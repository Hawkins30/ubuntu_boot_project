# 🧩 Ubuntu External Boot Recovery Project

![GitHub last commit](https://img.shields.io/github/last-commit/Hawkins30/ubuntu_boot_project)
![GitHub repo size](https://img.shields.io/github/repo-size/Hawkins30/ubuntu_boot_project)
![GitHub issues](https://img.shields.io/github/issues/Hawkins30/ubuntu_boot_project)

---

## 📘 Overview
This project documents the process of diagnosing and repairing a dual-boot configuration involving **Windows 11** and **Ubuntu (External SSD)** after bootloader corruption.

The goal was to restore a functional boot environment for both systems and understand how **UEFI**, **GRUB**, and **BCDedit** interact across devices.

---

## ⚙️ System Context

| Component | Details |
|------------|----------|
| **Laptop** | Samsung Notebook (UEFI firmware) |
| **Primary OS** | Windows 11 |
| **Secondary OS** | Ubuntu 22.04 (installed on external Seagate SSD) |
| **Storage** | Seagate Game Drive PS4 (1.8 TB) |
| **Boot Type** | UEFI / GPT |
| **Tools Used** | `bcdedit`, `mountvol`, `efibootmgr`, `fdisk`, `grub-install`, `chroot`, WSL |

---

## 🧠 Objectives
- Restore Ubuntu boot functionality from external SSD  
- Maintain Windows 11 bootloader integrity  
- Investigate and document EFI / GRUB configuration  
- Build a long-term reference for troubleshooting future setups  

---

## 🧩 Key Actions Performed
- Identified partitions using `fdisk -l` and `lsblk`
- Mounted EFI and root partitions manually
- Attempted GRUB reinstall using `grub-install`
- Repaired Windows Boot Manager using `bcdedit`
- Used `efibootmgr` to verify and clean EFI entries
- Created GitHub documentation from within WSL

---

## 📓 Logs & Session Notes
Detailed logs of the process are available here:
- [`notes/2025-10-28-boot-session.md`](notes/2025-10-28-boot-session.md)

---

## 🧰 Next Steps
- [ ] Test external Ubuntu boot using UEFI boot override  
- [ ] Rebuild GRUB configuration if required  
- [ ] Automate detection and repair of EFI entries via script  
- [ ] Add Python-based diagnostic tool (future idea)  

---

## 🧾 Credits
Developed and documented by **Alex Hawkins**  
📅 Project Started: October 2025  
📂 Repository: [github.com/Hawkins30/ubuntu_boot_project](https://github.com/Hawkins30/ubuntu_boot_project)

---
> “Every failed boot teaches you something the BIOS never will.”

