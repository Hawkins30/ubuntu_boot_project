# Ubuntu Boot Repair Log

## Overview
This project documents the troubleshooting and repair process for setting up and recovering Ubuntu from an external drive on a Windows 11 laptop (via UEFI dual boot).  
All steps were performed manually using the command line, GRUB repair commands, and EFI boot management tools.

## Process Summary
- Created and configured EFI boot entries using `bcdedit` and `efibootmgr`.
- Mounted Linux partitions and attempted GRUB reinstalls.
- Diagnosed errors such as:
  - `grub-install: error: failed to get canonical path of '/cow'`
  - `shimx64.efi missing or contains errors`
- Verified partitions using `lsblk` and `fdisk -l`.
- Adjusted BIOS settings to enable UEFI and detect external drives.
- Rebuilt boot entries for Ubuntu and Windows Boot Manager.

## Tools Used
- Windows Command Prompt (`bcdedit`, `mountvol`)
- Ubuntu Live Environment
- `efibootmgr`, `grub-install`
- WSL (Windows Subsystem for Linux)
- Git for version control and documentation

## Repository Purpose
This repository serves as a detailed record of:
1. Boot repair attempts for Ubuntu.
2. EFI bootloader configuration and recovery.
3. Lessons learned for future external-drive installations.

---

**Author:** Alex Hawkins  
**GitHub:** [@Hawkins30](https://github.com/Hawkins30)  
**Date:** October 2025
