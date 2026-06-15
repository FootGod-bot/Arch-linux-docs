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

# 1.6 Verify the boot mode
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
Use a [partitioning tool](https://wiki.archlinux.org/title/Partitioning#Partitioning_tools) like [fdisk](https://wiki.archlinux.org/title/Fdisk) to modify partition tables. For example:
```zsh
fdisk /dev/the_disk_to_be_partitioned
```
> **Note**
> Take time to plan a long-term partitioning scheme to avoid risky and complicated conversion or re-partitioning procedures in the future.
> If you want to create any stacked block devices for [LVM](https://wiki.archlinux.org/title/Install_Arch_Linux_on_LVM), [system encryption](https://wiki.archlinux.org/title/Dm-crypt) or [RAID](https://wiki.archlinux.org/title/RAID), do it now.
> If the disk from which you want to boot already has an [EFI system partition](https://wiki.archlinux.org/title/EFI_system_partition#Check_for_an_existing_partition), do not create another one, but use the existing partition instead.
> [Swap](https://wiki.archlinux.org/title/Swap) space can be set on a [swap file](https://wiki.archlinux.org/title/Swap_file) for file systems supporting it. Alternatively, disk based swap can be avoided entirely by setting up [swap on zram](https://wiki.archlinux.org/title/Zram#Usage_as_swap) after installing the system.
