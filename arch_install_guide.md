This file was based from the ArchWiki. It is modified to include instructions for grub, and if you follow along exactly you will end up with a working arch install. I set this up inside a virtual machine in proxmox, but it should work the same in a real pc.

# Arch Linux Installation Guide
This document is a guide for installing [Arch Linux](https://wiki.archlinux.org/title/Arch_Linux) using the live system booted from an installation medium made from an official installation image. The installation medium provides accessibility features which are described on the page [Install Arch Linux with accessibility options](https://wiki.archlinux.org/title/Install_Arch_Linux_with_accessibility_options). For alternative means of installation, see [Category:Installation process](https://wiki.archlinux.org/title/Category:Installation_process).  

Before installing, it would be advised to view the [FAQ](https://wiki.archlinux.org/title/FAQ). For conventions used in this document, see [Help:Reading](https://wiki.archlinux.org/title/Help:Reading). In particular, code examples may contain placeholders (formatted in italics) that must be replaced manually.  

This guide is kept concise and you are advised to follow the instructions in the presented order per section. For more detailed instructions, see the respective [ArchWiki](https://wiki.archlinux.org/title/ArchWiki) articles or the various programs' [man pages](https://wiki.archlinux.org/title/Man_page), both linked from this guide. For interactive help, the [IRC channel](https://wiki.archlinux.org/title/IRC_channel) and the [forums](https://bbs.archlinux.org/) are also available.  

Arch Linux should run on any x86_64-compatible machine with a minimum of 512 MiB RAM, though more memory is needed to boot the live system for installation.[1] A basic installation should take less than 2 GiB of disk space. As the installation process needs to retrieve packages from a remote repository, this guide assumes a working internet connection is available.  

# 1. Pre-installation
## 1.1. Acquire an installation image
Visit [https://archlinux.org/download](https://archlinux.org/download) for official images, and, depending on how you want to boot, acquire the ISO file or a netboot image, and the respective PGP signature.

## 1.2. Verify signature
It is recommended to verify the image signature before use, especially when downloading from an HTTP mirror, where downloads are generally prone to be intercepted to [serve malicious images](https://www2.cs.arizona.edu/stork/packagemanagersecurity/attacks-on-package-managers.html).  

Download the ISO PGP signature from [https://archlinux.org/download/#checksums](https://archlinux.org/download/#checksums) to the ISO directory and follow the instructions there to verify it.  

Alternatively, from an existing Arch Linux installation run:  
```zsh
pacman-key -v archlinux-version-x86_64.iso.sig
```
> **Note**
>
> The signature itself could be manipulated if it is downloaded from a mirror site, instead of from [archlinux.org](https://archlinux.org/download/). In this case, ensure that the public key, which is used to decode the signature, is signed by another, trustworthy key. The gpg command will output the fingerprint of the public key.
>
> Another method to verify the authenticity of the signature is to ensure that the public key's fingerprint is identical to the key fingerprint of the [Arch Linux developer](https://archlinux.org/people/developers/) who signed the ISO-file. See [Wikipedia:Public-key cryptography](https://en.wikipedia.org/wiki/Public-key_cryptography) for more information on the public-key process to authenticate keys.

## 1.3 Prepare an installation medium
Use a tool like [Rufus](https://rufus.ie), [Balena Etcher](https://etcher.balena.io), Or some other tool to put your iso on your usb.

## 1.4 Boot the live environment
> **Note**
>
> Arch Linux installation images do not support Secure Boot. You will need to [disable Secure Boot](https://wiki.archlinux.org/title/Unified_Extensible_Firmware_Interface/Secure_Boot#Disabling_Secure_Boot) to boot the installation medium. If desired, [Secure Boot](https://wiki.archlinux.org/title/Secure_Boot) can be set up after completing the installation.
Point the current boot device to the one which has the Arch Linux installation medium. Typically it is achieved by pressing a key during the [POST](https://en.wikipedia.org/wiki/Power-on_self_test) phase, as indicated on the splash screen. Refer to your motherboard's manual for details.  

When the installation medium's boot loader menu appears:

- If you used the ISO, select **Arch Linux install medium** and press Enter to enter the installation environment.
- If you used the Netboot image, choose a geographically close mirror from the **Mirror** menu, then select **Boot Arch Linux** and press Enter.

> **Tip**
>
> The ISO uses [systemd-boot](https://wiki.archlinux.org/title/Systemd-boot) for UEFI and [syslinux](https://wiki.archlinux.org/title/Syslinux) for BIOS booting. Use respectively `e` or `Tab` to enter the [boot parameters](https://wiki.archlinux.org/title/Kernel_parameters#Boot_loader_configuration).
> The Netboot image uses iPXE and the boot parameters can be specified in the Boot options menu.
> See [/usr/share/doc/mkinitcpio-archiso/README.bootparams](https://gitlab.archlinux.org/archlinux/mkinitcpio/mkinitcpio-archiso/blob/master/docs/README.bootparams) for a list.
>
> A common example of manually defined boot parameter would be the font size. For better readability on HiDPI screens—when they are not already recognized as such—using `fbcon=font:TER16x32` can help. [See HiDPI#Linux console (tty)](https://wiki.archlinux.org/title/HiDPI#Linux_console_(tty)) for a detailed explanation.

You will be logged in on the first [virtual console](https://en.wikipedia.org/wiki/Virtual_console) as the root user, and presented with a [Zsh](https://wiki.archlinux.org/title/Zsh) shell prompt.

To switch to a different console—for example, to view this guide with [Lynx](https://lynx.invisible-island.net/lynx_help/Lynx_users_guide.html) alongside the installation—use the `Alt + arrow` [keyboard shortcut](https://wiki.archlinux.org/title/Linux_console#Keyboard_shortcuts).

To [edit](https://wiki.archlinux.org/title/Textedit) configuration files, [mcedit(1)](https://man.archlinux.org/man/mcedit.1), [nano](https://wiki.archlinux.org/title/Nano#Usage), and [vim](https://wiki.archlinux.org/title/Vim#Usage) are available. See [pkglist.x86_64.txt](https://geo.mirror.pkgbuild.com/iso/latest/arch/pkglist.x86_64.txt) for a list of the packages included in the installation medium.

yeah you definitely want Wi-Fi before SSH, otherwise it’s kinda useless

here’s a clean drop-in section you can place right before your SSH one:

---

## Connect to Wi-Fi (Live ISO)

If you are not using Ethernet, you must connect to Wi-Fi before continuing.

---

### Start iwd (wireless tool)

The Arch ISO uses `iwd` for wireless connections.

```zsh id="wifi01"
iwctl
```

You will enter an interactive prompt.

---

### Find your wireless device

Inside `iwctl`:

```zsh id="wifi02"
device list
```

Look for something like `wlan0`.

---

### Scan for networks

```zsh id="wifi03"
station wlan0 scan
station wlan0 get-networks
```

Replace `wlan0` if your device name is different.

---

### Connect to Wi-Fi

```zsh id="wifi04"
station wlan0 connect YOUR_WIFI_NAME
```

It will ask for the password if required.

---

### Exit iwd

```zsh id="wifi05"
exit
```

---

### Test connection

```zsh id="wifi06"
ping archlinux.org
```

If you get replies, you're online.

---

### Note

> If Wi-Fi does not show up, make sure your adapter is not blocked:

```zsh
rfkill unblock all
```

## Set up SSH support (optional, recommended)

This allows you to remotely access the Arch live environment over the network. Useful for pasting commands from this guide.

---

### Install OpenSSH in the live ISO

The Arch ISO includes SSH by default, But it dosent hurt to double-check:

```zsh id="iso_ssh01"
pacman -Sy openssh
```

---

### Set a root password (required for SSH login)

SSH will not allow login without a password set. This password is temorary and is only used during the setup phase, so feel free to use 1 as your password:

```zsh id="iso_ssh02"
passwd
```

---

### Start the SSH service

Enable and start the SSH daemon:

```zsh id="iso_ssh03"
systemctl start sshd
```

Check status:

```zsh id="iso_ssh04"
systemctl status sshd
```

---

### Find your IP address

```zsh id="iso_ssh05"
ip a
```

Look for your active interface (like `eth0` or `wlan0`) and an `inet` address (example: `192.168.x.x`).

---

### Connect from another device

From Linux/macOS:

```zsh id="iso_ssh06"
ssh root@your_ip_address
```

From Windows:

* PowerShell `ssh root@ip`
* or PuTTY

---

### Important note

> The Arch ISO runs entirely in RAM. Any SSH setup is temporary and will reset after reboot.


## 1.5 Set the console keyboard layout and font
The default console keymap is [US](https://en.wikipedia.org/wiki/File:KB_United_States-NoAltGr.svg). Available layouts can be listed with:
```zsh
localectl list-keymaps
```
To set the keyboard layout, pass its name to [loadkeys(1)](https://man.archlinux.org/man/loadkeys.1). For example, to set a [German](https://en.wikipedia.org/wiki/File:KB_Germany.svg) keyboard layout:
```zsh
loadkeys de-latin1
```
[Console fonts](https://wiki.archlinux.org/title/Console_fonts) are located in `/usr/share/kbd/consolefonts/` and can likewise be set with [setfont(8)](https://man.archlinux.org/man/setfont.8) omitting the path and file extension. For example, to use one of the largest fonts suitable for [HiDPI screens](https://wiki.archlinux.org/title/HiDPI#Linux_console_(tty)), run:
```zsh
setfont ter-132b
```

## 1.6 Verify the boot mode
To verify the boot mode, check the [UEFI bitness](https://wiki.archlinux.org/title/Unified_Extensible_Firmware_Interface#UEFI_firmware_bitness):
```zsh
cat /sys/firmware/efi/fw_platform_size
```
- If the command returns 64, the system is booted in UEFI mode and has a 64-bit x64 UEFI.
- If the command returns 32, the system is booted in UEFI mode and has a 32-bit IA32 UEFI. While this is supported, it will limit the boot loader choice to those that support mixed mode booting.
- If it returns No such file or directory, the system may be booted in [BIOS](https://en.wikipedia.org/wiki/BIOS) (or [CSM](https://en.wikipedia.org/wiki/Compatibility_Support_Module)) mode.
If the system did not boot in the mode you desired (UEFI vs BIOS), refer to your motherboard's manual.

## 1.7 Connect to the internet

To set up a network connection in the live environment, go through the following steps:

1. Ensure your [network interface](https://wiki.archlinux.org/title/Network_interface) is listed and enabled, for example with [ip-link(8)](https://man.archlinux.org/man/ip-link.8):
  ```zsh
  ip link
  ```
2. For wireless and WWAN, make sure the card is not blocked with [rfkill](https://wiki.archlinux.org/title/Rfkill).
3. Connect to the network:
   - Ethernet—plug in the cable.
   - Wi-Fi—authenticate to the wireless network using [iwctl](https://wiki.archlinux.org/title/Iwctl).
   - Mobile broadband modem—connect to the mobile network with the [mmcli](https://wiki.archlinux.org/title/Mmcli) utility.
4. Configure your network connection:
   - [DHCP](https://wiki.archlinux.org/title/DHCP): dynamic IP address and DNS server assignment (provided by [systemd-networkd](https://wiki.archlinux.org/title/Systemd-networkd) and [systemd-resolved](https://wiki.archlinux.org/title/Systemd-resolved)) should work out of the box for Ethernet, WLAN, and WWAN network interfaces.
   - Static IP address: follow [Network configuration#Static IP address](https://wiki.archlinux.org/title/Network_configuration#Static_IP_address).
5. The connection may be verified with [ping](https://wiki.archlinux.org/title/Ping)
   ```zsh
   ping ping.archlinux.org
   ```

> **Note**
> In the installation image, [systemd-networkd](https://wiki.archlinux.org/title/Systemd-networkd), [systemd-resolved](https://wiki.archlinux.org/title/Systemd-resolved), [iwd](https://wiki.archlinux.org/title/Iwd) and [ModemManager](https://wiki.archlinux.org/title/ModemManager) are preconfigured and enabled by default. That will not be the case for the installed system.


## 1.8 Update the system clock
The live system needs accurate time to prevent package signature verification failures and TLS certificate errors. The [systemd-timesyncd](https://wiki.archlinux.org/title/Systemd-timesyncd) service is enabled by default in the live environment and time will be synchronized automatically once a connection to the internet is established.

Use [timedatectl(1)](https://man.archlinux.org/man/timedatectl.1) to ensure the system clock is synchronized:
```zsh
timedatectl
```


## 1.9 Partition the disks
When recognized by the live system, disks are assigned to a block device such as /dev/sda, /dev/nvme0n1 or /dev/mmcblk0. To identify these devices, use somthing like lsblk.
```zsh
lsblk
```
Results ending in `rom`, `loop` or `airootfs` may be ignored. `mmcblk*` devices ending in `rpmb`, `boot0` and `boot1` can be ignored.
> **Note**
> If the disk does not show up, [make sure the disk controller is not in RAID mode](https://wiki.archlinux.org/title/Partitioning#Drives_are_not_visible_when_firmware_RAID_is_enabled).


> Tip
Check that your NVMe drives and Advanced Format hard disk drives are using the [optimal logical sector size](https://wiki.archlinux.org/title/Advanced_Format) before partitioning.
The following [partitions](https://wiki.archlinux.org/title/Partition) are required for a chosen device:

- One partition for the [root directory](https://en.wikipedia.org/wiki/Root_directory) /.
- For booting in [UEFI](https://wiki.archlinux.org/title/UEFI) mode: an [EFI system partition](https://wiki.archlinux.org/title/EFI_system_partition).
Use a [partitioning tool](https://wiki.archlinux.org/title/Partitioning#Partitioning_tools) like [fdisk](https://wiki.archlinux.org/title/Fdisk) to modify partition tables.
> **Note**
> Take time to plan a long-term partitioning scheme to avoid risky and complicated conversion or re-partitioning procedures in the future.
> If you want to create any stacked block devices for [LVM](https://wiki.archlinux.org/title/Install_Arch_Linux_on_LVM), [system encryption](https://wiki.archlinux.org/title/Dm-crypt) or [RAID](https://wiki.archlinux.org/title/RAID), do it now.
> If the disk from which you want to boot already has an [EFI system partition](https://wiki.archlinux.org/title/EFI_system_partition#Check_for_an_existing_partition), do not create another one, but use the existing partition instead.
> [Swap](https://wiki.archlinux.org/title/Swap) space can be set on a [swap file](https://wiki.archlinux.org/title/Swap_file) for file systems supporting it. Alternatively, disk based swap can be avoided entirely by setting up [swap on zram](https://wiki.archlinux.org/title/Zram#Usage_as_swap) after installing the system.

## 1.9.1 Partitioning with fdisk

`fdisk` is used to create and manage partitions on your disk. First, identify your disk:

```zsh
lsblk
```

Look for something like `/dev/sda` or `/dev/nvme0n1`. **If you choose the wrong disk, you will permanently lose all data on that disk.**

---

### Clear the drive (optional but recommended)

Before partitioning, you can remove existing filesystem signatures:

```zsh
wipefs -a /dev/YOUR-DRIVE-HERE
```

> **WARNING**
> This is irreversible. Double-check the disk before running it.

---

### Start fdisk

```zsh
fdisk /dev/YOUR-DRIVE-HERE
```

Inside `fdisk`, each command is entered one at a time. You can always press:

* `p` → view current partitions
* `q` → quit without saving
* `w` → write changes and exit

---

### UEFI with GPT layout (recommended)

This setup creates:

* 1 GiB EFI System Partition
* 4 GiB swap partition
* Remaining space as root (`/`)

Inside `fdisk`:

### 1. Create GPT table

```
g
```

---

### 2. EFI partition (1 GiB)

```
n
[enter]
[enter]
+1G
```

Set type:

```
t
1
```

---

### 3. Swap partition (4 GiB or more)

```
n
[enter]
[enter]
+4G
```

Set type:

```
t
[enter]
19
```

---

### 4. Root partition (rest of disk)

```
n
[enter]
[enter]
[enter]
```

Set type:

```
t
[enter]
23
```

---

### 5. Write changes

```
w
```

---

### Resulting layout (UEFI GPT)

| Mount point   | Partition                 | Type                  | Size         |
| ------------- | ------------------------- | --------------------- | ------------ |
| /boot or /efi | /dev/efi_system_partition | EFI System Partition  | 1 GiB        |
| [SWAP]        | /dev/swap_partition       | Linux swap            | ≥ 4 GiB      |
| /             | /dev/root_partition       | Linux x86-64 root (/) | rest of disk |

---

### BIOS with MBR layout

Start fdisk the same way:

```zsh
fdisk /dev/YOUR-DRIVE-HERE
```

Inside `fdisk`:

---

### 1. Create MBR table

```
o
```

---

### 2. Swap partition (4 GiB or more)

```
n
[enter]
[enter]
+4G
```

Set type:

```
t
19
```

---

### 3. Root partition (rest of disk)

```
n
[enter]
[enter]
[enter]
```

---

### 4. Write changes

```
w
```

---

### Resulting layout (BIOS MBR)

| Mount point | Partition           | Type       | Size         |
| ----------- | ------------------- | ---------- | ------------ |
| [SWAP]      | /dev/swap_partition | Linux swap | ≥ 4 GiB      |
| /           | /dev/root_partition | Linux      | rest of disk |




## 1.10 Format the partitions
Once the partitions have been created, each newly created partition must be formatted with an appropriate [file system](https://wiki.archlinux.org/title/File_system). See [File systems#Create a file system](https://wiki.archlinux.org/title/File_systems#Create_a_file_system) for details.

For example, to create an [Ext4](https://wiki.archlinux.org/title/Ext4) file system on `/dev/root_partition`, run:
```zsh
mkfs.ext4 /dev/root_partition
```
If you created a partition for [swap](https://wiki.archlinux.org/title/Swap), initialize it with [mkswap(8)](https://man.archlinux.org/man/mkswap.8):
```zsh
mkswap /dev/swap_partition
```
> **Note**
> For stacked block devices replace `/dev/*_partition` with the appropriate block device path.


If you created an EFI system partition, [format](https://wiki.archlinux.org/title/EFI_system_partition#Format_the_partition) it to FAT32 using [mkfs.fat(8)](https://man.archlinux.org/man/mkfs.fat.8).


> **WARNING**
> Only format the EFI system partition if you created it during the partitioning step. If there already was an EFI system partition on disk beforehand, reformatting it can destroy the boot loaders of other installed operating systems.
```zsh
mkfs.fat -F 32 /dev/efi_system_partition
```

## 1.11 Mount the file systems
Mount the root volume to `/mnt`. For example, if the root volume is `/dev/root_partition`:
```zsh
mount /dev/root_partition /mnt
```

Create any remaining mount points under `/mnt` (such as `/mnt/boot` for `/boot`) and mount the volumes in their corresponding hierarchical order.

> **Tip**
> Run [mount(8)](https://man.archlinux.org/man/mount.8) with the --mkdir option to create the specified mount point. Alternatively, create it using [mkdir(1)](https://man.archlinux.org/man/mkdir.1) beforehand.


For UEFI systems, mount the EFI system partition. For example:
```zsh
mount --mkdir /dev/efi_system_partition /mnt/boot
```
If you created a [swap](https://wiki.archlinux.org/title/Swap) volume, enable it with [swapon(8)](https://man.archlinux.org/man/swapon.8):
```zsh
swapon /dev/swap_partition
```
[genfstab(8)](https://man.archlinux.org/man/genfstab.8) will later detect mounted file systems and swap space.

# 2 Installation
## 2.1 Select the mirrors
Packages to be installed must be downloaded from [mirror servers](https://wiki.archlinux.org/title/Mirrors), which are defined in `/etc/pacman.d/mirrorlist`. The higher a mirror is placed in the list, the more priority it is given when downloading a package.

On the live system, all HTTPS mirrors are enabled (i.e. uncommented). The topmost worldwide mirror should be fast enough for most people, but you may still want to inspect the file to see if it is satisfactory. If it is not, [edit](https://wiki.archlinux.org/title/Textedit) the file accordingly, and move the geographically closest mirrors to the top of the list, although other criteria should be taken into account. Alternatively, you can use [reflector](https://wiki.archlinux.org/title/Reflector) to create a mirrorlist file based on various criteria.

This file will later be copied to the new system by pacstrap, so it is worth getting right.


## 2.2 Install essential packages

After mounting the partitions, install the base system into `/mnt`. Only the `base` package is strictly required, but this guide includes everything needed for a working system.

No configuration from the live ISO is carried over except `/etc/pacman.d/mirrorlist`.

---

## Install the base system

```zsh
pacstrap -K /mnt base linux linux-firmware
```

---

## Full install (recommended setup for this guide)

This includes everything needed for boot, networking, and GRUB later:

```zsh
pacstrap -K /mnt base linux linux-firmware networkmanager nano grub efibootmgr sudo
```

---

### Notes

* add `intel-ucode` or `amd-ucode` depending on your cpu
* `networkmanager` is required for internet after install
* `grub` + `efibootmgr` are required for bootloader setup later
* Everything else can be installed after first boot


# 3 Configure the system
## 3.1 Fstab
To get needed file systems (like the one used for the boot directory `/boot`) mounted on startup, generate an [fstab](https://wiki.archlinux.org/title/Fstab) file with [persistent block device naming](https://wiki.archlinux.org/title/Persistent_block_device_naming) using [genfstab(8)](https://man.archlinux.org/man/genfstab.8). For example, reference file systems by their [UUIDs](https://wiki.archlinux.org/title/UUID) with `-U`:
```zfs
genfstab -U /mnt >> /mnt/etc/fstab
```
Check the resulting `/mnt/etc/fstab` file, and [edit](https://wiki.archlinux.org/title/Textedit) it in case of errors.

## 3.2 Chroot
To directly interact with the new system's environment, tools, and configurations for the next steps as if you were booted into it, [change root](https://wiki.archlinux.org/title/Change_root) into the new system:
```zsh
arch-chroot /mnt
```

> **Note**
> Some [systemd](https://wiki.archlinux.org/title/Systemd) tools such as [hostnamectl(1)](https://man.archlinux.org/man/hostnamectl.1), [localectl(1)](https://man.archlinux.org/man/localectl.1) and [timedatectl(1)](https://man.archlinux.org/man/timedatectl.1) cannot be used inside a [chroot(1)](https://man.archlinux.org/man/chroot.1), as they require an active [dbus](https://wiki.archlinux.org/title/Dbus) connection. [\[2\]](https://github.com/systemd/systemd/issues/798#issuecomment-126568596)

## 3.3 Time
For human convenience (e.g. showing the correct local time or handling Daylight Saving Time), set the [time zone](https://wiki.archlinux.org/title/Time_zone):
```zfs
ln -sf /usr/share/zoneinfo/Area/Location /etc/localtime
```
Run [hwclock(8)](https://man.archlinux.org/man/hwclock.8) to generate `/etc/adjtime`:
```zfs
hwclock --systohc
```
This command assumes the hardware clock is set to [UTC](https://en.wikipedia.org/wiki/UTC). See [System time#Time standard](https://wiki.archlinux.org/title/System_time#Time_standard) for details.

To prevent clock drift and ensure accurate time, set up [time synchronization](https://wiki.archlinux.org/title/Time_synchronization) using a [Network Time Protocol](https://en.wikipedia.org/wiki/Network_Time_Protocol) (NTP) client such as [systemd-timesyncd](https://wiki.archlinux.org/title/Systemd-timesyncd).

## 3.4 Localization
To use the correct region and language specific formatting (like dates, currency, decimal separators), [edit](https://wiki.archlinux.org/title/Textedit) `/etc/locale.gen` and uncomment the UTF-8 [locales](https://wiki.archlinux.org/title/Locale) you will be using. Generate the locales by running:

```zsh
locale-gen
```
[Create](https://wiki.archlinux.org/title/Create) the [locale.conf(5)](https://man.archlinux.org/man/locale.conf.5) file, and [set the LANG variable](https://wiki.archlinux.org/title/Locale#Setting_the_system_locale) accordingly:

```zsh
/etc/locale.conf
```
```zsh
LANG=en_US.UTF-8
```
If you [set the console keyboard layout](https://wiki.archlinux.org/title/Installation_guide#Set_the_console_keyboard_layout_and_font), make the changes persistent in [vconsole.conf(5)](https://man.archlinux.org/man/vconsole.conf.5):
```zsh
/etc/vconsole.conf
```
```zsh
KEYMAP=de-latin1
```


## 3.5 Network configuration

To assign a consistent, identifiable name to your system (especially useful in a networked environment), create the hostname file:

```zsh id="net01"
/etc/hostname
yourhostname
```

---

### Enable networking

If you installed `networkmanager`, enable it so networking works after reboot:

```zsh id="net02"
systemctl enable NetworkManager
```

---

### Optional check

You can verify your hostname file exists:

```zsh id="net03"
cat /etc/hostname
```

## 3.6 Initramfs
Creating a new initramfs is usually not required, because [mkinitcpio](https://wiki.archlinux.org/title/Mkinitcpio) was run on installation of the [kernel](https://wiki.archlinux.org/title/Kernel) package with pacstrap.

For [LVM](https://wiki.archlinux.org/title/Install_Arch_Linux_on_LVM#Adding_mkinitcpio_hooks), [system encryption](https://wiki.archlinux.org/title/Dm-crypt) or [RAID](https://wiki.archlinux.org/title/RAID#Configure_mkinitcpio), modify [mkinitcpio.conf(5)](https://man.archlinux.org/man/mkinitcpio.conf.5) and recreate the initramfs image. If you have [changed the default console keymap](https://wiki.archlinux.org/title/Installation_guide#Localization), only recreating the initramfs is required:

```zfs
mkinitcpio -P
```
## 3.7 Root password
Set a secure [password](https://wiki.archlinux.org/title/Password) for the [root user](https://en.wikipedia.org/wiki/Superuser) to allow performing administrative actions:
```zsh
passwd
```

## 3.8 User Setup

Create a regular user account for daily use. It is not recommended to use the root account for normal tasks.

---

### Create a new user

Replace `yourusername` with your desired username:

```zsh id="usr01"
useradd -m -G wheel yourusername
```

---

### Set a password for the user

```zsh id="usr02"
passwd yourusername
```

---

### Enable sudo access (wheel group)

Edit the sudoers file:

```zsh id="usr04"
EDITOR=nano visudo
```

Uncomment this line:

```
%wheel ALL=(ALL:ALL) ALL
```

---

### Set root password (if not already set)

```zsh id="usr05"
passwd
```


## 3.9 Boot Loader

Install and configure GRUB for both UEFI and BIOS systems.

---

### UEFI systems

Install GRUB to the EFI system partition:

```zsh id="grub02"
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
```

Generate config:

```zsh id="grub03"
grub-mkconfig -o /boot/grub/grub.cfg
```

---

### BIOS systems

Install GRUB to the disk (replace `/dev/sdX` with your disk, not a partition):

```zsh id="grub04"
grub-install --target=i386-pc /dev/sdX
```

Generate config:

```zsh id="grub05"
grub-mkconfig -o /boot/grub/grub.cfg
```
