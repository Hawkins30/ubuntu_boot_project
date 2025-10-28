# Ubuntu External Boot Setup Log

## Overview
This project documents the process of setting up Ubuntu to boot externally from a hard drive alongside Windows 11.

---

## Day 1 — Boot Configuration Attempts (Oct 28, 2025)

### Objectives
- Make Ubuntu boot only when the external drive is plugged in.
- Keep Windows 11 as the default when the drive is not connected.

### Steps Taken
1. Accessed BIOS and disabled Secure Boot.
2. Verified EFI boot entries using `efibootmgr`.
3. Mounted partitions in Ubuntu live environment (`/dev/sda2` for EFI, `/dev/sda3` for root).
4. Attempted multiple GRUB reinstalls and EFI boot manager edits.
5. Edited Windows Boot Manager using `bcdedit` to detect and launch Ubuntu.
6. Tested boot behavior across configurations.

### Issues Encountered
- EFI variables not supported when using live USB.
- GRUB installation failed multiple times due to missing canonical paths.
- Ubuntu boot entries appeared in Windows Boot Manager but failed to launch.
- Received repeated “shimx64.efi missing” error during boot attempts.

### Next Steps
- Recreate GRUB from within the installed Ubuntu environment (not Live USB).
- Confirm EFI partition mount consistency.
- Clean redundant Windows Boot Manager entries.
- Finalize documentation for GitHub.

---

## Notes
This log will continue tracking configuration changes, GRUB reinstalls, and progress towards stable dual-boot functionality.

