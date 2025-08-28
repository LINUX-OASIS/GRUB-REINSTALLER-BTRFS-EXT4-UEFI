# ✨ BTRFS Subvolume Converter ✨

[![License](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Shellcheck](https://img.shields.io/badge/Shellcheck-Passing-brightgreen.svg)](#)
[![Compatibility](https://img.shields.io/badge/Compatibility-Debian%20%7C%20Ubuntu%20%7C%20Mint-orange.svg)](#compatibility)
[![Made with](https://img.shields.io/badge/Made%20with-Bash%20&%20Whiptail-lightgrey.svg)](#)

![Danger](https://img.shields.io/badge/DANGER-CRITICAL%20FILESYSTEM%20OPERATION-red.svg)

A powerful shell script with a friendly terminal UI to convert a standard Ubuntu/Debian BTRFS installation into a proper subvolume layout (`@` for root, `@home` for home). This is the layout required by tools like Timeshift and Snapper to enable system snapshots and rollbacks.

---

## ⚠️ WARNING: CRITICAL OPERATION ⚠️

> This script performs major, irreversible changes to your filesystem structure. It moves **ALL** of your system files. While it includes multiple safety checks, a power failure or unexpected error during the operation could result in an **UNBOOTABLE SYSTEM AND DATA LOSS**.
>
> ### **YOU MUST HAVE A COMPLETE BACKUP OF YOUR DATA BEFORE PROCEEDING!**
>
 > This script **MUST** be run from an environment *outside* of the target operating system. You cannot convert the same OS you are currently booted into. Use one of the following methods:
 > *   Boot from a **Live USB/ISO** (e.g., the Ubuntu or Debian installer).
 > *   Boot into a **separate Linux installation** on another partition or drive.

---

## 🐧 Compatibility

This script is designed and tested for Debian-based Linux distributions that use the `apt` package manager, including:

*   **Ubuntu** (All flavors)
*   **Debian**
*   **Linux Mint**
*   Other Debian/Ubuntu derivatives

While the core tools (`btrfs-progs`, `util-linux`) are universal, the dependency installation helper is geared towards `apt`.

## ⚙️ Dependencies

The script relies on a few key packages to function.

*   `btrfs-progs` - The userspace tools for managing BTRFS filesystems.
*   `whiptail` - For displaying dialog boxes and the user interface.
*   `util-linux` - Provides the `lsblk` command.

The script will attempt to check for and install any missing dependencies when you run it.

## 🚀 Usage

Running the script is simple, but please follow these steps carefully. **Crucially, you must execute these commands from an environment outside the partition you intend to convert (e.g., a Live USB or a different Linux OS).**

1.  **Clone the Repository**

    ```bash
    git clone https://github.com/your-username/BTRFS-SUBVOLUME-CONVERTER.git
    cd BTRFS-SUBVOLUME-CONVERTER
    ```

2.  **Make the Script Executable**

    ```bash
    chmod +x custom-BTRFS-CONVERT-UBUNTU-INSTALL-TO-SUBVOLUME-INSTALL-CRITICAL.sh
    ```

3.  **Run with `sudo`**

    The script requires root privileges to mount partitions and move system files.

    ```bash
    sudo ./custom-BTRFS-CONVERT-UBUNTU-INSTALL-TO-SUBVOLUME-INSTALL-CRITICAL.sh
    ```

4.  **Follow the On-Screen Instructions**

    The script will guide you through selecting the correct BTRFS partition and will ask for confirmation multiple times before making any changes.

    
    *(Suggestion: Replace this with a screenshot or GIF of the script in action!)*

5.  **Reboot**

    Once the script completes successfully, reboot your system. Your computer should now boot using the new `@` and `@home` subvolume layout.

## 🛠️ How It Works (The Tech Sparkle)

For those curious about the magic behind the curtain, here is the step-by-step process:

1.  **Identify BTRFS Partition**: The script uses `lsblk` to find all BTRFS-formatted partitions on your system.
2.  **User Selection**: It presents the found partitions in a `whiptail` menu, allowing you to safely select the target for conversion.
3.  **Temporary Mount**: It mounts the top-level of the BTRFS partition (subvolume ID 5) to a temporary directory (e.g., `/tmp/btrfs_mount_...`). This gives it access to the entire filesystem as it currently exists.
4.  **Safety Checks**:
    *   It verifies that subvolumes named `@` or `@home` do not already exist, to prevent re-running on an already converted system.
    *   It requires multiple, explicit user confirmations before proceeding with the file operations.
5.  **Create Subvolumes**: It creates two new, empty subvolumes on the top level of the partition:
    *   `@` (for the root filesystem `/`)
    *   `@home` (for user data `/home`)
6.  **Move Files**: This is the critical step.
    *   It moves every file and directory from the top-level root into the new `@` subvolume.
    *   It then moves the contents of the original `/home` directory (now at `/@/home/`) into the new `@home` subvolume.
7.  **Update `fstab`**: The script intelligently edits the `/etc/fstab` file (now located inside the `@` subvolume) to tell the Linux kernel about the new layout.
    *   It changes the root (`/`) mount entry to use `subvol=@`.
    *   It adds or modifies the `/home` mount entry to use `subvol=@home`.
8.  **Cleanup**: It unmounts the temporary partition and removes the temporary directory.
9.  **Done!**: The system is now ready to be rebooted into its new, snapshot-friendly configuration.

## 📜 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🙏 Acknowledgments

*   Inspired by the many guides and forum posts from the Arch Linux and Ubuntu communities on BTRFS best practices.
