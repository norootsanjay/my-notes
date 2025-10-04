Devices are also files. Through this file, we can enable access to the underlying hardware without knowing its technical details. *Eg: Hardware*
*Everything is a stream of bytes.*
> [!Note]
> A Device in Linux refers to any hardware component or peripheral that interacts with the system. 
> Devices are essential for performing input/output (I/O) operations, such as reading data from a disk, sending output to a printer, or receiving input from a keyboard.
> Devices act as an interface between the OS and the Hardware.



Linux abstracts devices using a unified model, where most devices are represented as **files** in the `/dev` directory. This abstraction allows users and programs to interact with devices using standard file operations like ==read==, ==write==, and ==open==.

- Represented as a special type of file in the `/dev` directory.
- Accessed using standard file I/O operations, even though it may represent a physical piece of hardware.

Devices can be broadly categorized into two types:
1. **Block Devices**.
2. **Character Devices**.

---

# Character Device

Character devices handle data one character (byte) at a time. They are typically used for devices where data is streamed sequentially, such as keyboards, mice, or serial ports.
- We gain unbuffered access to the hardware.
- [f] Data is accessed sequentially.
>[!Example]
>1. Keyboards (`/dev/input/eventX`)
>2. Mice (`/dev/input/mouseX`)
>3. Serial ports (`/dev/ttyS0`)
>4. Virtual terminals (`/dev/tty1`)

```bash
sudo cat /dev/input/event0
```
This will display raw input events from the keyboard.

# Block Device

Block devices handle data in fixed-size chunks called **blocks**. They are typically used for storage devices where data is read/written in large, structured units.
## Characteristics of Block Devices:
- Data is accessed randomly (non-sequentially).
>[!Example]
>1. Hard drives (`/dev/sda`, `/dev/sdb`)
>2. Solid-state drives (SSDs)
>3. USB drives
>4. CD/DVD drives

The first hard drive in a Linux system is usually represented as `/dev/sda`. You can interact with it using tools like `dd` or `fdisk`:
```bash
sudo fdisk -l /dev/sda
```
![[Screenshot 2025-08-23 at 22.06.27.png]]

# Special Device Files

In addition to block and character devices, Linux also uses special device files for specific purposes:

## a) Pseudo-Devices

These are virtual devices that don’t correspond to physical hardware but provide useful functionality:
- `/dev/null`: A "black hole" device that discards all data written to it.
- `/dev/zero`: Provides an endless stream of null bytes (`\0`).
- `/dev/random` and `/dev/urandom`: Provide random numbers for cryptographic purposes.

## b) TTY Devices

TTY (teletype) devices represent terminal sessions:
- `/dev/tty`: The current terminal.
- `/dev/tty1`, `/dev/tty2`: Virtual consoles.
- `/dev/pts/X`: Pseudo-terminal devices for remote or GUI-based terminal sessions.

## c) Loop Devices

Loop devices allow you to mount files as if they were block devices:
- Example: Mounting an ISO file as a virtual disk:

 ---
 
![[Screenshot 2025-08-27 at 01.17.19.png]]