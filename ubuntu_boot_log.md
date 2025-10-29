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
---

## 🗓️ Day 2 — October 28, 2025
### Objective
Repair the GRUB bootloader on the external Ubuntu SSD and restore boot capability without affecting Windows 11.

### Actions
- Booted into **Ubuntu Live USB** to access the external SSD.
- Verified disk and partition structure using:

```text
```bash
sudo fdisk -l
```
- Output confirmed:

```text
/dev/sda1  →  Microsoft Reserved Partition  
/dev/sda2  →  EFI System (1 GB)  
/dev/sda3  →  Ubuntu Root (46 GB)  
/dev/sda4  →  Extended Storage (1.7 TB)
```
- Mounted Ubuntu partitions:

```text
sudo mount /dev/sda3 /mnt
sudo mount /dev/sda2 /mnt/boot/efi
```
- Attempted GRUB reinstall:

```text
sudo grub-install --target=x86_64-efi --efi-directory=/mnt/boot/efi --bootloader-id=Ubuntu_External --removable --recheck
```

- Initial error recieved:

```text
grub-install: error: failed to get canonical path of ‘/mnt/boot/efi’.
```
- Rechecked mount points and verified directories:

```text
ls /mnt/boot/efi
lsblk
```
- Confirmed both partitions correctly mounted, then bound system directories for chroot:

```text
for i in /dev /dev/pts /proc /sys /run; do sudo mount -B $i /mnt$i; done
sudo chroot /mnt
```
- Ran GRUB reinstall again:

```text
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=Ubuntu_External --recheck --removable
```
- Error log:

```text
grub-install: error: failed to get canonical path of '/boot/efi'.
```
- Verified EFI contents manually:

```text
ls /boot/efi/EFI/
```
- Observed existing directories:

```text
Boot  Microsoft  Ubuntu
```
- Cross-checked firmware entries:

```text
sudo efibootmgr
```
- Output:

```text
Boot0000* Windows Boot Manager
Boot0003* UEFI: Seagate Game Drive PS4
Boot0005* UEFI: Ubuntu
```
At this stage, EFI entries existed but GRUB was not initializing from the external SSD.


### Observations

Ubuntu EFI partition correctly mounted but GRUB path resolution continued to fail.  
`grub-install` could not locate the canonical mount directory even after binding /dev and /sys.  
Both `Ubuntu` and `Ubuntu_External` entries appeared in UEFI firmware, but only the Windows boot entry functioned.

### Notes

Created a clean mount sequence and confirmed partition UUIDs matched 
```text
/etc/fstab
```
Considered wiping and rebuilding EFI folder manually but deferred until confirmation of GUID structure.

Next step: clean up duplicate firmware entries and rebuild boot sequence.

Planned Day 3 tasks:

```text
bcdedit /enum firmware
bcdedit /delete {aad562f4-b409-11f0-9464-806e6f6e6963}
bcdedit /set {bootmgr} displaybootmenu yes
```
Goal for Day 3: ensure Ubuntu boots independently from SSD and add to Windows Boot Manager menu.


## 🗓️ Day 3 — October 29, 2025

### Objective
Finalize the dual-boot configuration by cleaning redundant firmware entries, linking Ubuntu’s EFI bootloader correctly, and confirming stable startup for both Windows and Ubuntu.

### Actions
- Booted into **Windows 11** to inspect firmware-level boot entries.
- Ran:

```text
```bash
bcdedit /enum firmware
```
- Output showed duplicate Ubuntu entries refencing the Sseagate SSD:

```text
identifier              {aad562f3-b409-11f0-9464-806e6f6e6963}
device                  partition=S:
description             UEFI: Seagate Game Drive PS4

identifier              {aad562f4-b409-11f0-9464-806e6f6e6963}
device                  partition=S:
path                    \EFI\ubuntu\shimx64.efi
description             Ubuntu
```
- Deleted redundant firmware entries:

```text
bcdedit /delete {aad562f3-b409-11f0-9464-806e6f6e6963}
bcdedit /delete {aad562f4-b409-11f0-9464-806e6f6e6963}
```
- Created a clean Ubuntu boot entry:

```text
bcdedit /create /d "Ubuntu External" /application BOOTSECTOR
```
- Then pointed it to the correct EFI partition and loader:

```text
bcdedit /set {<new_guid>} device partition=S:
bcdedit /set {<new_guid>} path \EFI\ubuntu\shimx64.efi
bcdedit /displayorder {<new_guid>} /addlast
bcdedit /set {bootmgr} displaybootmenu yes
bcdedit /timeout 10
```
- Rebooted and confirmed Windows Boot Manager displayed:

```text
Windows 11
Ubuntu External
```
Selected Ubuntu External → GRUB loaded successfully → Ubuntu booted from SSD.
Confirmed secondary boot path works independently when the external drive is connected.


### Observations

GRUB chain-loading is now stable.
Windows and Ubuntu both launch without errors.
If the external SSD is unplugged, the system defaults safely to Windows Boot Manager.
Reboot cycles between operating systems function without any EFI corruption.

### Notes

- Verified BIOS boot priorities — Ubuntu entry appears automatically when SSD is attached.

- Confirmed both EFI partitions are intact and properly recognized:

```text
sudo efibootmgr
```
- Output:

```text
Boot0000* Windows Boot Manager
Boot0002* Ubuntu External
```
- Ensured GRUB was set as the default loader for the external drive:

```text
sudo grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=Ubuntu
sudo update-grub
```
- Tested boot without USB installer present — both systems loaded independently.

### Outcome

✅ Dual-boot success achieved.

System behavior:

SSD plugged in: Presents menu with Windows 11 and Ubuntu External.

SSD unplugged: Boots directly into Windows without error.

No further GRUB errors, missing EFI paths, or inconsistent firmware entries.

Performance verified over multiple reboots.
Final configuration confirmed stable and reproducible.

### Reflections

This project demonstrated how UEFI, GRUB, and Windows Boot Manager coexist on separate physical drives.
By isolating EFI systems, both OSes remain independent yet connected through firmware-level chaining.
The process clarified advanced boot repair concepts essential for cybersecurity and system recovery.

Final system summary:

```text
Internal NVMe  →  Windows 11  
External SSD   →  Ubuntu 22.04  
Boot Mode      →  UEFI  
State          →  Fully operational dual-boot
```
