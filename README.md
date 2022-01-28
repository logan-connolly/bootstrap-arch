# Prerequisite commands

```shell
# Format drive (2 partitions)
cfdisk /dev/sdX

# Attach filesystems
mkfs.fat -F32 /dev/sdX1
mkfs.ext4 /dev/sdX2

# Mount partitions
mount /dev/sdX2 /mnt

# Install dependencies
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

# Clone bootstrap-arch

```shell
cd /opt/
git clone https://github.com/logan-connolly/bootstrap-arch.git
cd bootstrap-arch
```

# Root Playbooks

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

# Setup Dotfiles

```shell
# Install paru
mkdir -p ~/.local/opt && cd ~/.local/opt
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si

# Install user deps
audio_deps="pulseaudio pulseaudio-alsa"
server_deps="xorg xorg-xinit xorg-server"
video_deps="xf86-video-intel"
virtual_deps="virtualbox-guest-utils"
desktop_deps="i3"
sudo pacman -S $audio_deps $server_deps $video_deps $virtual_deps

# Create ssh key and copy public key to github
ssh-keygen -t ed25519

# Setup dotfiles
mkdir -p ~/projects && cd ~/projects
git clone https://github.com/logan-connolly/dotfiles.git
cd dotfiles
make setup
```
