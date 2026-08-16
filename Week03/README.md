# Week 3 Portfolio Project — Enterprise Server Deployment and Operating System Installation

**Course:** ITEP 414 – System Administration and Maintenance
**Program:** BSIT
**Week:** 3

---

## Project Overview

This project simulates the role of a Junior System Administrator deploying the first Linux server for ABC Startup Solutions. It covers a complete Ubuntu Server installation, initial configuration, functional verification, a BIOS vs. UEFI comparison, a boot process flowchart, a Windows Server evaluation install, and an operating system comparison report.

## Learning Objectives

- Explain the purpose of an operating system in enterprise environments.
- Differentiate BIOS and UEFI firmware.
- Explain the stages of the computer boot process.
- Compare Ubuntu Server, Windows Server, and Rocky Linux.
- Install and configure Ubuntu Server in a virtual machine.
- Enable secure remote administration using SSH.
- Verify server functionality and document installation procedures.

## Virtual Machine Specifications

| Component | Value |
|---|---|
| Name | Ubuntu-Server-Week03 |
| RAM | 4096 MB (4 GB) |
| CPU | 2 Virtual Processors |
| Storage | 40 GB (VDI, dynamically allocated) |
| Network | NAT |
| OS | Ubuntu Server 26.04 LTS |

## Installation Summary

Ubuntu Server 26.04 LTS was installed via Oracle VirtualBox using the guided installer:

- **Language / Keyboard:** English, English (US)
- **Network:** DHCP, assigned `10.0.2.15/24`
- **Hostname:** `server01`
- **Disk partitioning:** Guided — entire 40 GB disk, LVM group (`ubuntu-vg`), ext4 file system, `/boot` (2 GB) + `/` (≈19 GB, LVM logical volume)
- **SSH:** OpenSSH Server installed and enabled
- **Additional packages:** None installed, per project instructions

Full step-by-step procedure is documented in `InstallationGuide.docx`.

## Configuration Summary

- Non-root administrative user account created during setup
- OpenSSH Server enabled with password authentication
- No additional snaps or optional packages installed

## Verification Results

| Task | Command | Result |
|---|---|---|
| Login | — | Successful login, Ubuntu 26.04 LTS confirmed |
| Hostname | `hostname` | `server01` |
| IP Address | `ip addr` | `10.0.2.15/24` (matches DHCP assignment) |
| Internet connectivity | `ping -c 4 google.com` | Successful |
| System update | `sudo apt update && sudo apt upgrade -y` | ~713 MB updated across 48+ packages; one minor transient error (see Challenges) |
| SSH service | `systemctl status ssh` | `active (running)` |

## BIOS vs. UEFI Highlights

UEFI has largely replaced BIOS in modern systems because it supports much larger disks via GPT partitioning (vs. BIOS's ~2.2 TB limit under MBR), offers Secure Boot for firmware-level malware protection, boots faster through 32/64-bit initialization instead of BIOS's legacy 16-bit mode, and provides a graphical, mouse-driven pre-boot interface. Full comparison table: `BIOS_vs_UEFI.pdf`.

## Boot Process Flowchart

Power On → BIOS/UEFI Initialization → Boot Device Detection → Boot Loader (GRUB) → Linux Kernel → init/systemd → Services Start → Login Prompt

See `diagrams/BootProcessFlowchart.png` and `diagrams/BootProcessFlowchart.pdf`.

## Windows Server Evaluation (Bring-Home)

Windows Server 2025 Standard Evaluation (Desktop Experience) was installed in a separate VM (4096 MB RAM, 2 CPUs, 50 GB storage) to support the OS comparison exercise. See `OSComparisonReport.docx` for the full Windows Server vs. Ubuntu Server vs. Rocky Linux comparison.

## Challenges Encountered

1. **Installer appeared to stall during "installing kernel" step**, with kernel watchdog "soft lockup" warnings in the log. Resolved by monitoring the log for continued activity and allowing the process to finish rather than restarting.
2. **403 Forbidden error** fetching an optional `linux-firmware-amd-misc` package during `apt upgrade`. Resolved by retrying `apt update`/`apt upgrade`; confirmed the package was non-critical (hardware firmware not required in a VM).

## Reflection

Deploying this Ubuntu Server from scratch reinforced how much of system administration is about verification, not just installation — a server isn't "done" until each configuration choice (hostname, network, SSH, updates) has actually been confirmed working, not just assumed. Working entirely from a fresh VM also made the guided LVM partitioning genuinely useful to understand rather than abstract, since I had to reason about how much space to leave unallocated for future growth.

The two issues I ran into — the kernel installation appearing to freeze, and the 403 error during package upgrades — were the most valuable part of the exercise. Both looked alarming at first, but neither turned out to be a real failure: the first was resolved by patiently confirming the log was still updating rather than assuming the VM had crashed, and the second was a transient mirror issue that retrying fixed. That distinction, between a genuine failure and a slow or noisy but healthy process, is exactly the kind of judgment a system administrator has to build.

Comparing Ubuntu Server against Windows Server and Rocky Linux also clarified why the "best OS" question doesn't have a single answer. Ubuntu's lightweight, free, cloud-friendly profile made sense for a startup like ABC Startup Solutions, but I can now see concretely why a Microsoft-centric enterprise would lean Windows Server, or why a regulated organization migrating off CentOS would choose Rocky Linux instead. Understanding BIOS vs. UEFI and the boot sequence in detail also connected concepts I previously only knew abstractly — the flowchart and comparison table made the actual order of operations tangible in a way that reading about it beforehand did not.

Overall, this project moved my understanding of server deployment from theoretical to practical: I can now install, configure, verify, and document a Linux server end-to-end, and I have firsthand experience choosing between real alternative platforms rather than just reading about them.

## References

- Canonical Ltd. (2026). *Ubuntu Server documentation*. https://ubuntu.com/server/docs
- Microsoft. (2026). *Windows Server 2025 Evaluation Center*. https://www.microsoft.com/evalcenter
- Oracle Corporation. *VirtualBox User Manual*. https://www.virtualbox.org/manual/
