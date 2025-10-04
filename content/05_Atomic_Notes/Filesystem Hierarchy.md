- The Filesystem Hierarchy Standard defines the directory structure and directory contents in Unix-like OS.
- It provides a consistent and predictable location for a specific type of files and directories.

[![Linux Filesystem Hierarchy Structure (FHS)](https://tecadmin.net/wp-content/uploads/2022/06/linux-filesystem-hierarchy.png)

Here’s a **comprehensive guide to the Linux Filesystem Hierarchy** (based on the **FHS – Filesystem Hierarchy Standard**) that explains what each directory under `/` (root) is for:

---

# 🗂 Linux Filesystem Hierarchy

Everything in Linux starts at the **root directory `/`**. All files, devices, and mounts are placed somewhere under it.

---

## 🔹 Top-Level Directories under `/`

### 1. `/` – Root

- The top of the filesystem tree.
- Contains all other files and directories.
- It should be kept minimal to allow the system to boot even if other partitions are unmounted.

---

### 2. `/bin` – Essential User Binaries

- Contains ==basic command binaries== (executables) required by all users and needed for system boot/repair.
> [!Example]
`ls`, `cat`, `cp`, `mv`, `rm`, `echo`, `mkdir`, `bash`

---

### 3. `/sbin` – System Binaries

- Contains ==system administration binaries== (executables used by root).    
>[!Example]
 `ifconfig`, `reboot`, `mount`, `shutdown`, `fdisk`

---

### 4. `/boot` – Boot Loader Files

- Holds files needed to boot the system:
    - Linux kernel (`vmlinuz-*`)
    - Initrd/initramfs (`initrd.img-*`)
    - Bootloader config (GRUB → `grub/grub.cfg`)

---

### 5. `/dev` – Device Files

- Virtual directory managed by `udev`.
- Represents ==devices as files== (everything is treated as a file in Linux).
>[!Example]
 `/dev/sda` → first hard disk
`/dev/tty` → terminal
`/dev/null` → black hole for output


---

### 6. `/etc` – Configuration Files

- Contains ==system-wide configuration files==.
>[!Example]
`/etc/passwd` → user accounts
`/etc/fstab` → filesystem mounts
`/etc/ssh/sshd_config` → SSH server config


---

### 7. `/home` – User Home Directories

- Each user has a subdirectory under `/home`.
- Stores personal files, configurations, and documents.
>[!Example]
`/home/sanjay`


---

### 8. `/lib`, `/lib64` – Essential Shared Libraries

- Contains ==shared libraries== needed by programs in `/bin` and `/sbin`.
- Equivalent to `.dll` files in Windows.
> [!Example]
 `/lib/x86_64-linux-gnu/libc.so.6`


---

### 9. `/media` – Removable Media Mount Point

- Used to automatically mount ==removable devices== (USB, CD-ROM).
>[!Example]
`/media/usb`

---

### 10. `/mnt` – Temporary Mount Point

- Traditionally used by admins to manually mount filesystems.
> [!Example]
`mount /dev/sdb1 /mnt`


---

### 11. `/opt` – Optional / Add-on Software

- Third-party software packages are often installed here.
>[!Example]
`/opt/google/chrome`


---

### 12. `/proc` – Process Information (Virtual Filesystem)

- A virtual file system that provides kernel and process information.
>[!Example]
`/proc/cpuinfo` → CPU details
`/proc/meminfo` → memory usage
`/proc/1234/` → process with PID 1234
        

---

### 13. `/root` – Root User’s Home

- Home directory for the ==root user== (superuser).
>[!Example]
`/root/.bashrc`


---

### 14. `/run` – Runtime Data

- Contains volatile runtime data since last boot.
>[!Example]
`/run/sshd.pid` (PID files)

---

### 15. `/srv` – Service Data

- Contains ==data served by services== (like web or FTP).
>[!Example]
`/srv/www` for web server data


---

### 16. `/sys` – Kernel Information

- Virtual filesystem exposing ==kernel info and device tree==.
>[!Example]
`/sys/class/net/eth0/`


---

### 17. `/tmp` – Temporary Files

- Stores ==temporary files.==
- World-writable, usually cleared on reboot.
>[!Example]
`/tmp/somefile.tmp`
        

---

### 18. `/usr` – User Binaries & Read-Only Data

- Stands for ==Unix System Resources==.
- Often the largest directory, contains most user-level programs.
- Subdirectories:
    - `/usr/bin` → non-essential user binaries (`gcc`, `vim`, `python`)
    - `/usr/sbin` → non-essential system binaries
    - `/usr/lib` → libraries for `/usr/bin` and `/usr/sbin`
    - `/usr/share` → shared read-only data (icons, docs, man pages)


---

### 19. `/var` – Variable Data

- Contains files that ==change frequently==.
- Subdirectories:
    - `/var/log` → log files
    - `/var/mail` → user mail
    - `/var/spool` → printer/mail queues
    - `/var/tmp` → temp files (preserved between reboots)
        

---

# 🔑 Key Points to Remember

- **`/bin` vs `/usr/bin`**:  
    `/bin` has _essential_ commands (needed in single-user mode), `/usr/bin` has _non-essential_ user programs.
- **`/sbin` vs `/usr/sbin`**:  
    Same as above, but for system administration.
- **`/etc`** contains config, not binaries.
- **`/proc` & `/sys`** are _virtual filesystems_ (not real disk files).
- **`/tmp`** is cleared frequently → don’t store important files there.
    