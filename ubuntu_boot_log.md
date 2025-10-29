# Ubuntu External Boot Setup Log

## Overview
This project documents the process of setting up Ubuntu to boot externally from a hard drive alongside Windows 11.

---

## 🗓️ Day 1 — October 27, 2025

### Objective
Boot Ubuntu externally from an SSD connected via USB, while maintaining the existing Windows 11 installation on the internal drive.  
Investigation began after the initial external boot attempt failed.

### Actions
- Booted into BIOS and confirmed both internal and external drives were detected.  
- Attempted boot from **UEFI: Seagate Game Drive PS4** — failed with:

```text
File: \EFI\ubuntu\shimx64.efi
Status: 0xc000007b
```
- Booted into Windows and ran:

```text
bcdedit /enum firmware
```
- Found duplicate {Ubuntu} entries and missing canonical path references. 
- Verified the external SSD contained:

```text
/EFI/ubuntu/
```
  but lacked complete GRUB files.
- Checked disk layout using Disk Management in Windows to confirm partitions.
- Confirmed Ubuntu's EFI partition existed but was not properly configured.
- Additional inspection in PowerShell:

```text
Get-Disk | Select Number, FriendlyName, PartitionStyle
```
- Result showed external SSD:

```text
Partition 1 — EFI System (FAT32)
Partition 2 — Ubuntu Root (EXT4)
```
- Compared GUIDs for EFI entries:

```text
bcdedit /enum firmware
```
- Output snippet:

```text
identifier              {bootmgr}
device                  partition=\Device\HarddiskVolume1
path                    \EFI\Microsoft\Boot\bootmgfw.efi

identifier              {aad562f4-b409-11f0-9464-806e6f6e6963}
device                  partition=S:
description             UEFI: Seagate Game Drive PS4
```

### Observations

Ubuntu EFI partition present but incomplete.  
Firmware boot order defaulted to Windows Boot Manager.  
Selecting the external drive manually still failed to load GRUB.  
EFI folder structure suggested GRUB had been partially overwritten or unlinked.

### Notes

Prepared an Ubuntu Live USB installer to access a repair environment.
Documented partition identifiers:
```text
/dev/sda2 → EFI Partition  
/dev/sda3 → Ubuntu Root Partition
```
Planned to reinstall GRUB manually via terminal commands during day 2.
Preliminary fix plan outline:

```text
sudo mount /dev/sda3 /mnt
sudo mount /dev/sda2 /mnt/boot/efi
sudo grub-install --target=x86_64-efi --efi-directory=/mnt/boot/efi --bootloader-id=Ubuntu
update-grub
```
