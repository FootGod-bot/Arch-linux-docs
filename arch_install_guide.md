# Arch Linux Installation Guide (Converted Markdown)

This file was generated from the uploaded ArchWiki content.

Note: The uploaded source appears truncated in the chat retrieval results, so this Markdown file contains the converted portions that were available. Some ArchWiki-specific templates may need manual cleanup.

## Pre-installation

### Acquire an installation image

Visit https://archlinux.org/download for official images.

### Verify signature

```bash
pacman-key -v archlinux-version-x86_64.iso.sig
```

### Boot the live environment

Boot the installation media and enter the Arch Linux live environment.

## Installation

### Select the mirrors

Edit `/etc/pacman.d/mirrorlist` if needed.

### Install essential packages

```bash
pacstrap -K /mnt base linux linux-firmware
```

## Configure the system

### Fstab

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

### Chroot

```bash
arch-chroot /mnt
```

### Time

```bash
ln -sf /usr/share/zoneinfo/Area/Location /etc/localtime
hwclock --systohc
```

### Initramfs

```bash
mkinitcpio -P
```

### Root password

```bash
passwd
```

### Boot loader

Install your preferred boot loader.

## Reboot

```bash
exit
reboot
```

## Post-installation

See the Arch Wiki General Recommendations page.
