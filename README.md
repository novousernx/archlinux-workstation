# Arch Linux Workstation

[Ansible](https://www.ansible.com/) playbook for configuring my main work
machine on top of a clean [Arch Linux](https://www.archlinux.org/) installation.

This configuration sets up a desktop environment based on
[i3-gaps](https://github.com/Airblader/i3), a popular fork of the
[i3](https://i3wm.org/) tiling window manager.

![screenshot](./screenshot.png)

**Note:** Since this is used in different hardware, it does not try to set up
hardware-specific drivers and configuration; these should be done manually
in each machine.

## Instructions

First, follow the
[official installation guide](https://wiki.archlinux.org/index.php/installation_guide)
to bootstrap the base system.

I also use
[full disk encryption](https://wiki.archlinux.org/index.php/Dm-crypt), as I
always need to keep some credentials and other sensitive information stored in
the local disk. This protects my files from being accessed in case my work
machine gets stolen.

### Pre-Installation

Make sure you have internet connectivity and run the following commands as
the `root` user to install the required software for the `ansible-playbook`
command to work:

#### Install the Required Packages

Run the following command as `root` to install the packages required to
complete the rest of the installation:

```sh
pacman -S ansible git git-lfs base-devel makepkg nano
```

### Create User for Running the Ansible Playbook

Running Ansible as root is probably a bad idea (I'm not sure it's even possible!),
so I recommend you creating a separate user for running this Ansible playbook.

Run the following commands as root to create a new `ansible` user:

```sh
# Create a 'ansible' group and user:
groupadd -g 2000 ansible
useradd -m -u 2000 -g ansible -G wheel ansible
passwd ansible <password>

# Make sure the ansible user is allowed to sudo:
EDITOR=nano visudo
```

Logout and then login as the new `ansible` user.

### Running the Ansible Playbook

Clone this playbook in your home directory, edit the `vars/custom.yml` file to
your liking and then run the following command to apply the Ansible playbook:

```sh
git clone https://github.com/danielfm/archlinux-workstation.git
cd archlinux-workstation
ansible-playbook playbook.yml -e "@vars/custom.yml"
```

Don't leave the machine unattended as you'll probably have to re-enter the user
password a few times throughout the process when installing the required packages.

#### Troubleshooting: AUR Issues

The AUR is community-maintained, so it's not that uncommon to find outdated
packages that cannot be installed (i.e. due to having mismatching validation
hash).

If you encounter any issues, you'll have to fix the affected packages by hand
before retrying the `ansible-playbook` command.

Some commands that might be useful:

```sh
# If you need to edit the PKGBUILD file to fix a validation SHA or download URL:
yay -S <package> --editmenu
```

### That's It!

If the `ansible-playbook` command finishes successfully, reboot the system to
access your new environment!
