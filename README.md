# Bootstrap Arch

Goal of this repo is to get setup with Arch as quickly as possible. It uses a combination of markup via this README.md and ansible playbooks to guide the installation and automate areas where it is possible to automate.

Additional Resources for installing Arch:

- https://wiki.archlinux.org/index.php/installation_guide
- https://averagelinuxuser.com/a-step-by-step-arch-linux-installation-guide/

# Installation

## Initial setup

Start archiso from a formatted USB drive and run the following commands:

```shell
# Format drive into boot and os partitions:
#  - sdX1: 512MB Linux filesystem
#  - sdX2: *GB   Linux filesystem
cfdisk /dev/sdX

# Attach filesystems to partitions
mkfs.fat -F32 /dev/sdX1
mkfs.ext4 /dev/sdX2

# Mount os partition
mount /dev/sdX2 /mnt

# Install system-wide dependencies
system_deps="base base-devel linux linux-firmware"
bootstrap_deps="sudo vim zsh git ansible networkmanager"
boot_deps="grub efibootmgr"
pacstrap -i /mnt $system_deps $bootstrap_deps $boot_deps

# Generate fstab file for attaching devices at startup
genfstab -U -p /mnt >> /mnt/etc/fstab

# Change to system as root
arch-chroot /mnt /usr/bin/zsh

# Set root password
passwd
```
## Clone repository

```shell
cd /opt/
git clone https://github.com/logan-connolly/bootstrap-arch.git
cd bootstrap-arch
```
## Ansible playbooks

```shell
# Setup locale configuration
ansible-playbook ./playbooks/locale.yml

# Setup grub to be able to boot
ansible-playbook ./playbooks/grub.yml

# Create swap file
ansible-playbook ./playbooks/swap.yml

# Create user and groups
ansible-playbook ./playbooks/user.yml

# Add passwd for newly created user (archie)
passwd archie

# Exit out and login as non-root user
exit
```
## Install user dependencies

```shell
# Install paru
mkdir -p ~/.local/opt && cd ~/.local/opt
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si

# Install audio dependencies
paru -S pulseaudio pulseaudio-alsa

# Install X11 server
paru -S xorg xorg-xinit xorg-server

# Install video card relevant for your hardware
paru -S xf86-video-intel

# Optional: install guest utils if installing in virtualbox
paru -S virtualbox-guest-utils
```
## Add configuration

Now that there is a fresh install of Arch, you can add whatever desktop environment you want (i3, gnome, kde, etc.). I have detailed how I setup my environment in my [dotfiles](https://github.com/logan-connolly/dotfiles) if you are interested.
