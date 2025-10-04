# Log in to Local, Remote, Graphical, and Text Mode Consoles

1. Local Text Mode Console
2. Local Graphical Mode Console
3. Remote Text Mode Login
4. Remote Graphical Mode Login

> [!Common Terms]
A **Console** is the ==display screen== where Linux outputs text and accepts commands.
A **terminal** emulator is a ==graphical application that replicates console== functionality within a window

![[Pasted image 20250928010425.png]]

Remote graphical connections are available, though set up differently. Administrators may configure VNC (Virtual Network Computing) or RDP (Remote Desktop Protocol) for remote GUI access. For VNC, use a compatible client like VNC Viewer or RealVNC. For RDP, open the Remote Desktop Connection application on Windows and log in with your credentials.
Remote text-based sessions use OpenSSH, which runs the SSH daemon (sshd) to provide secure, encrypted remote login—making it a preferred alternative to older, unsecured methods like Telnet
![[Pasted image 20250928010549.png]]

---

# Read and Use System Documentation

```bash
journalctl --help
```

```bash
man journalctl
```

## Distinguishing Similar Man Pages

Sometimes, two man pages may share the same command name. For example, there is both a command and a programming function for `printf`. The manual categorizes these pages into sections. You can observe this by checking the man page for `man` itself:

![The image shows a dark-themed terminal interface with the text "Manual Pages With man Command" at the top and two words, "printf" and "printf()" displayed in the center.](https://kodekloud.com/kk-media/image/upload/v1752881266/notes-assets/images/Linux-Foundation-Certified-System-Administrator-LFCS-Read-and-Use-System-Documentation/manual-pages-man-command-printf.jpg)

To view the manual page for the printf command itself, specify section 1:

```
$ man 1 printf
```

For the printf function (used in programming), specify section 3:

```
$ man 3 printf
```

During online exams, the Linux Foundation allows the use of both `man` and the `--help` option, so remember to utilize them if you forget a command option.

```
$ man man
$ man 1 printf
$ man 3 printf
```
![[Screenshot 2025-09-29 at 20.16.27.png]]

## Using apropos to Find Commands

The ==apropos== command searches the man page database for keywords in their short descriptions.
If you encounter an error, it might be because the mandb database hasn’t been created or updated. You can generate or update it manually with:

```bash
sudo mandb
```

```bash
apropos director
```

---

# Create Delete Copy and Move Files and Directories

This guide demonstrates how to manage files and directories on Linux by creating, deleting, copying, and moving them. Before diving into the commands, ensure you understand these key concepts:

1. File system tree structure
2. Absolute paths
3. Relative paths

## Listing Files and Directories

To list files and directories—including hidden files (those starting with a dot)—use the `ls -la` command. The `-a` flag stands for "all" and the `-l` flag provides a detailed, long listing format. Here are some examples:

```
$ ls -la
$ ls -a
```

To list files for a specific directory, include the directory path:

```
$ ls /var/log
$ ls -l /var/log
```

The command `ls -l /var/log` displays detailed information such as file permissions, ownership, and modification dates. Combining options is also possible:

```
$ ls -ahl
total 76K
drwx------ 16 aaron aaron  4.0K Nov  1 17:57 .
drwxr-xr-x  7 root  root   70 Oct 26 16:54 ..
-rw-------  1 aaron aaron  5.0K Nov  1 17:56 .bash_history
-rw-r--r--  1 aaron aaron   18 Jul 27 09:21 .bash_logout
-rw-r--r--  1 aaron aaron  141 Jul 27 09:21 .bash_profile
-rw-r--r--  1 aaron aaron  376 Jul 27 09:21 .bashrc
drwxr-xr-x  2 aaron aaron    6 Oct 19 00:11 Desktop
drwxr-xr-x  3 aaron aaron   25 Oct 23 18:15 Documents
drwxr-xr-x  2 aaron aaron    6 Oct 19 00:11 Downloads
drwxr-xr-x  2 aaron aaron    6 Oct 19 00:11 Music
drwxr-xr-x  2 aaron aaron    2 Oct 19 00:11 Pictures
-rw-rw-r--  1 aaron aaron   36 Oct 28 20:06 testfile
```

Tip

Using combined options like `ls -ahl` makes it quicker to view comprehensive file information.

## Understanding the File System Tree

Linux organizes files and directories in what’s known as a file system tree. In this inverted hierarchy, the root is at the top and branches (subdirectories) and leaves (files) extend downward. The root directory is represented by a forward slash (`/`), and it forms the base for other essential directories such as `home`, `var`, and `etc`.

## Absolute and Relative Paths

### Absolute Paths

Absolute paths start from the root directory (`/`) and specify the complete location of a file or directory. For example, consider the following absolute path leading to a file named `Invoice.pdf`:

/home/aaron/Documents/Invoice.pdf

![The image shows a directory structure diagram illustrating an absolute path from the root directory to a file named "Invoice.pdf" located in "/home/aaron/Documents".](https://kodekloud.com/kk-media/image/upload/v1752881234/notes-assets/images/Linux-Foundation-Certified-System-Administrator-LFCS-Create-Delete-Copy-and-Move-Files-and-Directories/directory-structure-absolute-path-invoice.jpg)

### Relative Paths

Relative paths are defined in relation to your current working directory. To display your current directory, use the `pwd` command:

```
$ pwd
```

By default, you usually start in your home directory (e.g., `/home/Aaron` or `/home/Jane`). Changing directories can be done using the `cd` command:

```
$ cd /var/log   # Uses an absolute path
$ cd ..         # Moves up one directory level
```

Here, `..` refers to the parent directory. For example, if you are in `/home/Aaron`, executing `cd ..` will take you to `/home`.

#### Relative Path Examples

Assume your current directory is `/home/Aaron`:

- To access a file inside the `Documents` subdirectory:
    
    ```
    $ Documents/Invoice.pdf
    ```
    
- To access a file in the current directory:
    
    ```
    $ Invoice.pdf
    ```
    
- To access a file located one directory above:
    
    ```
    $ ../Invoice.pdf
    ```
    

You can chain `..` to move up multiple levels (e.g., `../../Invoice.pdf` moves up two levels).

Additional directory commands:

- Switch to the root directory:
    
    ```
    $ cd /
    ```
    
- Return to your previous directory:
    
    ```
    $ cd -
    ```
    
- Return to your home directory:
    
    ```
    $ cd
    ```
    

## Creating Files and Directories

To create a new file, use the `touch` command. For example, to create `receipt.pdf` in the current directory:

```
$ touch receipt.pdf
```

You can create a file in another location using an absolute or relative path:

```
$ touch /home/Jane/receipt.pdf
$ touch ../Jane_receipt.pdf
```

To create a new directory, use the `mkdir` command:

```
$ mkdir receipts
```

## Copying Files and Directories

The `cp` command copies files. You provide a source file and specify a destination. For instance, to copy `receipt.pdf` to the `receipts` directory:

```
$ cp receipt.pdf receipts/
```

Adding a trailing slash (i.e., `receipts/`) specifies that the destination is a directory. To copy and rename a file simultaneously:

```
$ cp receipt.pdf receipts/receipt_copy.pdf
```

To copy an entire directory with its contents, use the `-r` (recursive) flag:

```
$ cp -r Receipts/ BackupOfReceipts/
```

Important

Ensure the destination directory (e.g., `BackupOfReceipts/`) does not already exist when using recursive copying, as behavior may vary across systems.

## Moving and Renaming Files and Directories

The `mv` command is used for moving files between directories or renaming them. Here are some examples:

- To move a file into another directory:
    
    ```
    $ mv Receipt.pdf Receipts/
    ```
    
- To rename a file:
    
    ```
    $ mv receipt.pdf old_receipt.pdf
    ```
    
- To rename a directory:
    
    ```
    $ mv receipts old_receipts
    ```
    

Note that you don't need a recursive flag when moving directories; `mv` handles them by default.

## Deleting Files and Directories

To delete files, use the `rm` command. For example, to remove `Invoice.pdf`:

```
$ rm Invoice.pdf
```

To remove a directory and all its contents, apply the `-r` flag:

```
$ rm -r invoices
```

---

# Create and Manage Hard Links

In this article, we'll explore how Linux manages hard links and why they are beneficial for efficient file management. Understanding hard links requires a basic knowledge of file systems and inodes.

## File System Basics

Imagine a Linux system used by two distinct users, Aaron and Jane. Each user logs in with their own credentials, which provide personalized desktops, settings, and file directories.

Suppose Aaron takes a picture of the family dog and saves it as:

/home/aaron/pictures/family_dog.jpg

To simulate the file creation, we use the following command, which writes a description (acting as the file's content) into the file:

```
$ echo "Picture of Milo the dog" > Pictures/family_dog.jpg
```

When you inspect the file with the `stat` command, it shows details that include the inode number:

```
$ stat Pictures/family_dog.jpg
  File: Pictures/family_dog.jpg
  Size: 49              Blocks: 8          IO Block: 4096   regular file
Device: fd00h/64768d Inode: 52946177   Links: 1
Access: (0640/-rw-r-----)  Uid: ( 1000/ aaron)   Gid: ( 1005/ family)
Access: 2021-10-27 16:33:18.949749912 -0500
Modify: 2021-10-27 14:41:19.202778881 -0500
Change: 2021-10-27 16:33:18.851749919 -0500
Birth: 2021-10-26 13:37:17.980969655 -0500
```

In Linux, every file is represented by an inode—a data structure that stores metadata (like permissions, modification times, and data block locations). While the inode number is the technical reference, we use the file name (in this case, _family_dog.jpg_) to map to that inode. Notice the output indicates "Links: 1", meaning there is a single hard link (the original file name) associated with the inode.

## Creating and Using Hard Links

Hard links allow you to reference the same data from different locations without duplicating file content. This is especially useful if you want to share data without unnecessarily consuming additional disk space.

Consider Jane, who has her own pictures directory at /home/jane/pictures. Instead of copying _family_dog.jpg_ from Aaron's directory, which duplicates the file data, you can create a hard link. This avoids the overhead of duplicating thousands of high-resolution images.

While the typical copy command might be:

```
$ cp -r /home/aaron/Pictures/ /home/jane/Pictures/
```

you can create a hard link using the following syntax:

```
$ ln /home/aaron/Pictures/family_dog.jpg /home/jane/Pictures/family_dog.jpg
```

After creating the hard link, both file paths reference the same inode. Running the `stat` command now will show the file has two hard links:

```
$ stat Pictures/family_dog.jpg
File: Pictures/family_dog.jpg
Size: 49         Blocks: 8          IO Block: 4096   regular file
Device: fd00h/64768d Inode: 52946177  Links: 2
Access: (0640/-rw-r-----)  Uid: ( 1000/ aaron)   Gid: ( 1005/ family)
Access: 2021-10-27 16:33:18.949749912 -0500
Modify: 2021-10-27 14:41:19.20278881 -0500
Change: 2021-10-27 16:33:18.851749919 -0500
Birth: 2021-10-26 13:37:17.980969655 -0500
```

Persistence of Data

If one user deletes their reference (hard link) to the file, the data remains accessible through the other link. The file data is only removed when the last hard link is deleted.

### Deleting Hard Links

For example, if Aaron removes his file:

```
$ rm /home/aaron/Pictures/family_dog.jpg
```

Jane still has access via her hard link. However, if Jane then deletes the file as well:

```
$ rm /home/jane/Pictures/family_dog.jpg
```

the filesystem marks the data blocks as free, and from the user's perspective, the file is gone.

### Managing Permissions with Hard Links

Since hard links share the same inode, any permission changes on one link are reflected on all links. To ensure both Aaron and Jane have correct access to the file, you might add them to the same group (for example, "family") and adjust the file's permissions accordingly:

```
$ usermod -a -G family aaron
$ usermod -a -G family jane
$ chmod 660 /home/aaron/Pictures/family_dog.jpg
```

These permission changes apply to all hard links referencing the inode, ensuring consistent access.

## Limitations of Hard Links

There are a few limitations to be aware of when working with hard links:

1. Hard links can only be created for files, not directories.
2. Hard links must reside on the same file system; you cannot create a hard link from one file system to another.

![The image is a diagram explaining the limitations of hard links, showing that hard links can only be created for files, not folders, and must be on the same filesystem.](https://kodekloud.com/kk-media/image/upload/v1752881235/notes-assets/images/Linux-Foundation-Certified-System-Administrator-LFCS-Create-and-Manage-Hard-Links/hard-links-limitations-diagram.jpg)

---

# Create and Manage Soft Links

In this article, we explore how Linux handles soft links (also known as symbolic links). Soft links in Linux work similarly to the shortcuts you might find on a Windows desktop. For instance, when you install a program on Windows, a shortcut is added to your desktop that points to the actual executable stored elsewhere (such as "C:\Program Files\MyCoolApp\application.exe"). When you double-click the shortcut, it launches the program even though the executable is not directly stored on your desktop.

```
C:\Program Files\MyCoolApp\application.exe
```

Unlike hard links, which directly reference an inode, soft links are files that contain a path to another file or directory. Essentially, they are text files holding the address where the target file or directory is located.

To create a soft link, you add the `-s` option to the `ln` command. The basic syntax is:

```
# ln -s path_to_target_file path_to_link_file
```

- "path_to_target_file" is the location of the file or directory that the soft link will reference.
- "path_to_link_file" is the name (and optionally, the location) of the new soft link.

For example, to create a symbolic link for a family dog picture, use the following commands:

```
$ ln -s /home/aaron/Pictures/family_dog.jpg family_dog_shortcut.jpg
$ ls -l
lrwxrwxrwx. 1 aaron aaron family_dog_shortcut.jpg -> /home/aaron/Pictures/family_dog.jpg
```

In the output of `ls -l`, the leading "l" indicates that the file is a soft link. It also displays the path that the soft link points to. If the target path is lengthy, `ls -l`might not show the entire path. In these cases, you can use the `readlink`command to view the complete link destination:

```
$ readlink family_dog_shortcut.jpg
/home/aaron/Pictures/family_dog.jpg
```

Note

Although the soft link appears to have full permission bits (rwx), these permissions are not actually enforced. Instead, the permissions of the destination file or directory determine access rights.

For example, if you attempt to redirect output to a soft link that points to a protected file (such as `/etc/fstab`), the operation will be denied:

```
$ ln -s /home/aaron/Pictures/family_dog.jpg family_dog_shortcut.jpg
$ ls -l
lrwxrwxrwx. 1 aaron aaron family_dog_shortcut.jpg -> /home/aaron/Pictures/family_dog.jpg
$ readlink family_dog_shortcut.jpg
/home/aaron/Pictures/family_dog.jpg
$ echo "Test" >> fstab_shortcut
bash: fstab_shortcut: Permission denied
```

Using an absolute path in a soft link (e.g., `/home/aaron/Pictures/family_dog.jpg`) means that if the directory name (like "aaron") changes in the future, the link will break. A broken link is typically displayed in red when you use `ls -l`.

To prevent this issue, consider creating a soft link with a relative path if you are working within the same directory structure. This method ensures that when the soft link is accessed, it correctly redirects to the intended file relative to the current directory.

Soft links can also be created for directories or for files and directories located on different file systems.

![The image is a diagram explaining soft links, showing how they can link to files and folders, including across different filesystems. It includes visual representations of files and folders with arrows indicating the links.](https://kodekloud.com/kk-media/image/upload/v1752881236/notes-assets/images/Linux-Foundation-Certified-System-Administrator-LFCS-Create-and-Manage-Soft-Links/soft-links-diagram-files-folders.jpg)

---

# List Set and Change Standard File Permissions

In this article, we explore how to list, set, and modify standard file permissions in Linux. Mastering file permissions is crucial for managing file and directory ownership effectively.

When you run the following command:

```
$ ls -l
```

you may see output similar to this, which shows that each file or directory is owned by a particular user:

```
-rw-r----- 1 aaron family 49 Oct 27 14:41 family_dog.jpg
```

In the example above, the file "family_dog.jpg" is owned by the user "aaron" and associated with the group "family". Only the file owner or the superuser (root) can change its permissions.

## Changing the Group Owner with chgrp

To change the group of a file or directory, use the `chgrp` command. The syntax is as follows:

```bash
$ chgrp group_name file/directory
```

For example, to change the file's group to "sudo", execute:

```
$ chgrp sudo family_dog.jpg
```

After running this command and listing the file details using `ls -l`, you will see the group updated to "sudo". Note that you can only change the group to one that you are a member of. To display your current groups, run:

```
$ groups
aaron sudo family
```

Note

Only the root user can change the file group to any group available on the system.

## Changing the User Owner with chown

To change the user owner of a file or directory, use the `chown` command with the syntax below:

```
$ sudo chown new_owner file/directory
```

For example, to change the ownership of "family_dog.jpg" to "jane", use:

```
$ sudo chown jane family_dog.jpg
```

After executing `ls -l`, you will observe that the file's owner is now "jane". Only the root user has the privileges to change the file owner.

You can also modify both the owner and group simultaneously using:

```
$ sudo chown aaron:family family_dog.jpg
```

This command resets the owner to "aaron" and the group to "family". Here is a sequence of commands demonstrating changing ownership and group:

```
$ ls -l
-rw-r----- 1 aaron family 49 Oct 27 14:41 family_dog.jpg
$ chgrp sudo family_dog.jpg
$ ls -l
-rw-r----- 1 aaron sudo 49 Oct 27 14:41 family_dog.jpg
$ sudo chown jane family_dog.jpg
$ ls -l
-rw-r----- 1 jane sudo 49 Oct 27 14:41 family_dog.jpg
$ sudo chown aaron:family family_dog.jpg
$ ls -l
-rw-r----- 1 aaron family 49 Oct 27 14:41 family_dog.jpg
```

## Understanding ls -l Output and Permissions

The `ls -l` command output provides detailed file information, including permissions:

- The first character indicates the entry type:
    
    - A dash (-) represents a regular file.
    - A "d" signifies a directory.
    - An "l" denotes a symbolic link.
- The next nine characters are divided into three groups of three:
    
    - The first trio pertains to the user (owner).
    - The second trio is for the group.
    - The third trio applies to others.

For example:

```
$ ls -l
-rwxrwxrwx. 1 aaron family 49 Oct 27 14:41 family_dog.jpg
```

In this listing:

- "rwx" for the owner means the owner can read, write, and execute.
- "rwx" for the group grants identical permissions.
- "rwx" for others provides full access to all users.

### Permissions for Files vs. Directories

For files:

- "r" (read) allows the content to be viewed.
- "w" (write) permits modifications.
- "x" (execute) enables running the file as a program or script.

For directories:

- "r" allows listing the directory’s contents.
- "w" permits creating or deleting files within.
- "x" allows entering the directory using the `cd` command.

For instance, to list files in the "Pictures" directory or create a new subdirectory:

```
$ ls Pictures/
$ mkdir Pictures/Family
```

## How Permissions Are Evaluated

When accessing a file, Linux evaluates permissions in the following order:

1. If the user is the file owner, user permissions apply.
2. If not, and the user is a member of the file’s group, group permissions apply.
3. Otherwise, the "others" permissions are enforced.

Consider the following output:

```
(aaron)$ ls -l
-r--rw---- 1 aaron family 49 family_dog.jpg
```

Even though "aaron" is in the "family" group (which has read and write permissions), the file displays the owner’s permissions (r--), meaning Aaron can only read the file. Attempting to append text as Aaron results in:

```
(aaron)$ echo "Add this content to file" >> family_dog.jpg
bash: family_dog.jpg: Permission denied
```

However, if another user, such as "jane" (a member of the "family" group), accesses the file:

```
(aaron)$ su jane
(jane)$ echo "Add this content to file" >> family_dog.jpg
(jane)$ cat family_dog.jpg
Picture of Milo the dog
```

If the user is neither the owner nor a member of the file’s group, the "others" permissions are applied.

## Changing File Permissions with chmod

To modify file or directory permissions, use the `chmod` command:

```
$ chmod permissions file/directory
```

There are two primary methods to specify permissions:

### Using the Plus (+) and Minus (–) Signs

You can add permissions with `+` and remove them with `-`.

- To add write permission for the owner:
    
    ```
    $ chmod u+w family_dog.jpg
    ```
    
    Suppose the file initially shows:
    
    ```
    $ ls -l
    -r--rw----. 1 aaron family 49 Oct 27 14:41 family_dog.jpg
    ```
    
    After applying the command:
    
    ```
    $ ls -l
    -rw-rw----. 1 aaron family 49 Oct 27 14:41 family_dog.jpg
    ```
    
- To remove permissions, for example, to remove read permission for others:
    
    ```
    $ chmod o-r family_dog.jpg
    ```
    
    This command ensures that only the owner and group have read access to the file.
    

### Using the Equal (=) Operator

You can set permissions to exact values using the equal sign. For instance, to set group permissions to read-only:

```
$ chmod g=r family_dog.jpg
```

This command sets group permissions to exactly "r--", even if write or execute permissions were previously set. To remove all permissions for a group, use:

```
$ chmod g= family_dog.jpg
```

### Combining Permission Changes

You can combine changes for the user (u), group (g), and others (o) in a single command. For example, to grant the owner read and write permissions, set the group to read-only, and remove all permissions for others:

```
$ chmod u+rw,g=r,o= family_dog.jpg
```

## Setting Permissions with Octal Notation

The `chmod` command also accepts octal values for specifying permissions. First, view the file’s current permissions with the `stat` command:

```
$ stat family_dog.jpg
File: family_dog.jpg
Size: 49            Blocks: 8          IO Block: 4096   regular file
Device: fd00h/64768d Inode: 52946177    Links: 1
Access: (0640/-rw-r-----)  Uid: ( 1000/ aaron)   Gid: (  27/ sudo)
```

In the output above, "0640" represents the file permissions:

- Owner (6): read (4) + write (2)
- Group (4): read (4)
- Others (0): no permissions

To set the permissions to 640, run:

```
$ chmod 640 family_dog.jpg
```

### Understanding the Octal Calculation

Permissions can be visualized in binary:

- For the owner, "rw-" translates to 110 in binary (6 in octal).
- For the group, "r--" translates to 100 in binary (4 in octal).
- For others, "---" translates to 000 in binary (0 in octal).

A more common octal permission setting is 755, which means:

- Owner: 7 (rwx, or 111 in binary)
- Group: 5 (r-x, or 101 in binary)
- Others: 5 (r-x, or 101 in binary)

Similarly, 777 means full permissions (read, write, and execute) for all.

Below is an image that illustrates the conversion of binary file permissions to octal values:

![The image illustrates octal file permissions, showing the conversion of binary to decimal values, with a specific example of "rw-r-----" corresponding to the octal number 640.](https://kodekloud.com/kk-media/image/upload/v1752881255/notes-assets/images/Linux-Foundation-Certified-System-Administrator-LFCS-List-Set-and-Change-Standard-File-Permissions/octal-file-permissions-binary-decimal.jpg)

Another image further explains the octal permission notation used in Unix-like systems:

![The image illustrates octal permissions in a Unix-like system, showing how read, write, and execute permissions are converted to numerical values.](https://kodekloud.com/kk-media/image/upload/v1752881256/notes-assets/images/Linux-Foundation-Certified-System-Administrator-LFCS-List-Set-and-Change-Standard-File-Permissions/octal-permissions-unix-system-diagram.jpg)

---

# SUID SGID and Sticky Bit

In Unix/Linux systems, managing permissions is critical to maintaining security and efficient resource access. In this article, we explore three special permissions—SUID, SGID, and the Sticky Bit—that allow controlled elevation of privileges and help manage collaborative environments.

>[!Overview]
Understanding these permissions ensures that applications can safely operate with elevated privileges without compromising system integrity.

## SUID (Set User ID)

- [f] A special permission that allows ==users to run an executable with the permission of the permissions of the executable's owner.==

SUID is a special permission that, when applied to an executable file, enables the process to run with the file owner's privileges instead of those of the user who launched it. 
This feature is particularly useful when an application requires access to restricted resources. For example, if Emily develops a reports application that needs to access files under `/usr/local/reports`, she can allow John to run the application without granting him unfettered access to her directory.

![The image illustrates the concept of SUID, showing how users Emily and John can run an executable called "filereports" with the permissions of the executable's owner, allowing access to a directory at "/usr/local/reports."](https://kodekloud.com/kk-media/image/upload/v1752881267/notes-assets/images/Linux-Foundation-Certified-System-Administrator-LFCS-SUID-SGID-and-Sticky-Bit/suid-executable-permissions-users.jpg)

### Demonstration of SUID

Below is a step-by-step demonstration of setting and verifying the SUID bit:

```bash
# Create the test file
touch suidfile


# Check default permissions
ls -l suidfile
# Output: -rw-rw-r--  1 jeremy jeremy 0 May 8 01:22 suidfile
```

To set the SUID bit, which is represented by a leading digit of 4 in the permission mode, execute:

```bash
chmod 4664 suidfile
ls -l suidfile
```

Notice that the execute bit for the owner may be displayed as a capital "S" when it is not enabled. Including the execute permission (for example, using `4764`) will show a lowercase "s" instead.

## SGID (Set Group ID)

- [f] Applies to ==both executables and directories==.

SGID works similarly to SUID but applies to the group ownership of an executable or directory. For executables, SGID allows any user running the file to do so with the file's group privileges. When applied to a directory, any new file or directory created inherits the group's ownership, which is invaluable for collaborative work environments.

For instance, if a reports application is associated with the reports group, both Emily and John can access executable files, and newly created files inside the directory will automatically inherit the reports group.

![The image illustrates the concept of SGID (Set Group ID) permissions, showing how it applies to both executables and directories, with two users accessing a file within a directory.](https://kodekloud.com/kk-media/image/upload/v1752881269/notes-assets/images/Linux-Foundation-Certified-System-Administrator-LFCS-SUID-SGID-and-Sticky-Bit/sgid-permissions-executables-directories.jpg)

### Demonstration of SGID

Follow these simple steps to set the SGID bit on a file:

```bash
# Create the file
touch sgidfile


# Check the default file permissions
ls -l sgidfile
# Expected output: -rw-rw-r-- 1 jeremy jeremy 0 May 8 01:25 sgidfile
```

To set SGID without granting execute permissions for the group:

```bash
chmod 2664 sgidfile
ls -l sgidfile
```

If the execute permission is also required (thus displaying a lowercase "s"), use:

```
chmod 2674 sgidfile
ls -l sgidfile
```

## Combining SUID and SGID

Combining SUID and SGID on a single file is straightforward. Since SUID is represented by 4 and SGID by 2, the combined digit is 6. For example, to apply both on a file called `both`:

```bash
touch both
chmod 6664 both
ls -l both
```

To efficiently locate files using these special permissions, use the `find` command:

```bash
# Find files with the SUID bit set
find . -perm /4000


# Find files with the SGID bit set
find . -perm /2000


# Find files with either SUID or SGID (or both) set
find . -perm /6000
```

## Sticky Bit

- [f] Controls file deletion

The Sticky Bit is a special permission applied primarily to directories to control file deletion. When set, it restricts file deletion within the directory so that only the file owner, the directory owner, or the superuser can delete or rename files. This is especially beneficial for shared directories where multiple users have write access but should not be able to remove files created by others.

### Demonstration of the Sticky Bit

Creating a directory with a Sticky Bit is illustrated below:

```bash
# Set the Sticky Bit using a shorthand command
chmod 1777 stickydir/
ls -ld stickydir/
# Expected output: drwxrwxrwt 2 jeremy jeremy 4096 May 8 01:29 stickydir/
```

In the permission output, a lowercase "t" signifies that the Sticky Bit is active along with the execute permission. If the execute permission is revoked (for example, by setting mode `1666`), the indicator changes to an uppercase "T":

```
chmod 1666 stickydir/
ls -ld stickydir/
```

>[!Interpreting the Sticky Bit]
A lowercase "t" denotes that the Sticky Bit is set and execute permission is enabled, while an uppercase "T" indicates that only the Sticky Bit is set.

---

# Search for Files

Linux systems typically organize files in a structured manner—for example, SSH configuration files are usually found in `/etc/ssh`, while log files are maintained in `/var/log`. Even if you often know the file location, search commands can be invaluable for unexpected cases and complex queries.

Let's dive into some common scenarios.

## Finding Specific File Types

Imagine you have a website and need to find all JPEG images stored in `/usr/share`. The `find` command can easily list all files ending in `.jpg`:

![The image shows a directory structure diagram with a root directory containing subdirectories: usr, var, and etc, each with further subdirectories like share, log, and ssh.](https://kodekloud.com/kk-media/image/upload/v1752881269/notes-assets/images/Linux-Foundation-Certified-System-Administrator-LFCS-Search-for-Files/directory-structure-root-usr-var-etc.jpg)

```bash
$ find /usr/share/ -name '*.jpg'
1.jpg 2.jpg 3.jpg
```

## Searching by File Size

In another scenario, if your server hosting virtual machines is nearly out of disk space, you might need to identify unusually large files. Although this example does not contain files larger than 20 GB, you can modify the search to find files over a specified size. For example, to search for files larger than 10 megabytes in `/lib64`:

```bash
$ find /lib64/ -size +10M
large-file.txt
```

## Searching by Modification Time

When you update an application, you might want to identify which files were modified in the last minute. You can use the `-mmin` flag to achieve this:

```bash
$ find /dev/ -mmin -1
abc.txt
```

Using the `find` command in these examples shows how powerful it is for locating files based on a variety of parameters.

## Basic Usage

To search for a file by name within a specific directory, use the `-name` parameter. For example:

```bash
$ find /bin -name file1.txt
```

If no path is specified, `find` defaults to searching in the current directory. Always place the search path before the search parameters. An incorrect command such as:

```bash
$ find -name file1.txt /bin
```

may yield unexpected results. Think of it like entering your room before looking for your keys.

- [f] Always specify the search path before your parameters to ensure accurate search results.

## Detailed Search Parameters

### Case Sensitivity

The `-name` option is case sensitive. For example, searching for "felix" will not match a file named "Felix". Use `-iname` for a case-insensitive search:

```bash
$ find -name felix
$ find -iname felix
```

### Wildcard Patterns

Wildcards let you search for files matching a particular pattern. To find all files that begin with a lowercase "f":

```bash
$ find -name "f*"
```

The asterisk (\*) acts as a wildcard matching zero or more characters.

### Time-Based Searches

Search for files based on their modification time using the `-mmin` option, which is measured in minutes. Examples include:

- `-mmin 5`: Finds files modified exactly 5 minutes ago.
- `-mmin -5`: Finds files modified in the last 5 minutes.
- `-mmin +5`: Finds files modified more than 5 minutes ago.

Be aware that if it is currently 12:05, `-mmin 5` targets files modified at 12:01. To include files modified at 12:00, use `-mmin +5`.

For searches based on days, the `-mtime` option is used. For instance:

```bash
$ find -mtime 2
```

- `-mtime 0` matches files modified in the last 24 hours.
- `-mtime 1` matches files modified between 24 and 48 hours ago.

Important

File modification time measures content changes, not the file creation time.

### Change Time Versus Modification Time

Linux differentiates between modification time (when file contents change) and change time (when file metadata, such as permissions, are altered). The `find`command can help locate files that recently had their metadata changed—a useful feature if you're troubleshooting permission issues.

### Size-Based Searches

To search for files based on file size, the `-size` option is used. For example, to find files that are exactly 512 KB:

```bash
$ find -size 512k
```

Here, suffixes indicate the following:

- No suffix: bytes
- K: kilobytes
- M: megabytes
- G: gigabytes (note that M and G must be uppercase)

To search for files greater than or less than a specific size, prefix the size with a `+`or `-` sign respectively:

```bash
$ find -size +512k  # Files greater than 512 KB
$ find -size -512k  # Files less than 512 KB
```

### Combining Multiple Parameters

You can combine multiple parameters to narrow your search. For example, to find files that start with the letter "f" and are exactly 512 KB:

```bash
$ find -name "f*" -size 512k
```

The parameters are combined using an implicit AND operator. To use an OR operator, you can insert `-o` between conditions.

### Negation in Searches

To exclude files that meet a certain condition, use the NOT operator (`-not` or `\!`). For instance, to find all files that do not start with the letter "f":

```bash
$ find -not -name "f*"
$ find \! -name "f*"
```

>[!Attention]
Remember that in the Bash shell, the exclamation mark (!) has a special meaning. Escaping it with a backslash (i.e., `\!`) ensures it is interpreted literally.

### Permission-Based Searches

The `-perm` option lets you search for files based on their file permissions. For example, to find files with exactly 664 permissions (user: read/write, group: read/write, others: read):

```bash
$ find -perm 664  # Exact match for 664 permissions
```

To find files that have at least these permissions, precede the value with a hyphen:

```bash
$ find -perm -664
```

Alternatively, use a slash (`/`) to match any of the listed permissions:

```bash
$ find -perm /664
```

Here are a few more examples:

- To find files that only the owner can read and write (600):
    
    ```bash
    $ find -perm 600
    ```
    
- To locate files where the owner has at least execute permission:
    
    ```bash
    $ find -perm -100
    ```
    
- To find files that others are not allowed to read:
    
    ```bash
    $ find \! -perm -o=r
    ```
    

Files that cannot be read by anyone will be excluded from the search results.

# Compare and Manipulate File Content

In this article, we explore essential Linux command-line tools for comparing and manipulating file content. Because Linux relies heavily on text—whether for SSH sessions, configuration files, or log files—mastering these commands will improve your efficiency in managing and troubleshooting your system.

## Viewing File Content

To inspect a small file quickly, use the `cat` command with the filename. For example:

```
$ cat /home/users.txt
user1
user2
user3
user4
user5
user6
```

If you prefer to see the contents in reverse order (from bottom to top), the `tac`command is available:

```
$ tac /home/users.txt
user6
user5
user4
user3
user2
user1
```

For longer files, such as logs, it is often more practical to view only a portion of the file. The `tail` command displays the last 10 lines by default, which is useful for checking the most recent log entries. Conversely, the `head` command shows the beginning of a file.

For instance, consider a log file with 10 lines (keep in mind that empty lines might also count):

```
$ head /var/log/apt/term.log
Log started: 2024-03-11 04:41:37
Selecting previously unselected package libyaml2:amd64.
(Readi ng database ... 118768 files and directories currently installed.)
Preparing to unpack .../libyaml2_2.1.0-3ubuntu0.22.04.1_amd64.deb ...
Unpacking libyaml2:amd64 (2.1.0-3ubuntu0.22.04.1) ...
Selecting previously unselected package libvirt0:amd64.
Preparing to unpack .../libvirt0_8.0.0-1ubuntu7.8_amd64.deb ...
Unpacking libvirt0:amd64 (8.0.0-1ubuntu7.8) ...
Selecting previously unselected package libvirt-clients.
```

You can also control the number of lines displayed by using the `-n` option with both `tail` and `head`.

## Automating Text Replacement with SED

Editing multiple instances manually in large files can be error-prone and time-consuming. The Stream Editor (SED) automates search and replace tasks efficiently. For example, if a file listing user details has the country "Canada" misspelled as "canda", you can preview the correction with:

```
$ sed 's/canda/canada/g' userinfo.txt
```

Let's break down the command:

- `s/canda/canada/g`: The substitute command where `canda` is replaced with `canada` globally on each line.
- Single quotes ensure Bash does not interpret special characters.
- The `-g` flag replaces all occurrences in each line.

Once you're satisfied with the preview, apply the change in-place:

```
$ sed -i 's/canda/canada/g' userinfo.txt
```

Note

Always back up your files before performing in-place edits with `sed -i`.

It is important to quote the expression correctly to prevent Bash from misinterpreting special characters such as the asterisk. Both single and double quotes can be used:

```
$ sed "s/canda/canada/g" userinfo.txt
```

## Extracting Data with Cut

The `cut` command is ideal for extracting specific columns from a file. For example, to extract the first column—which often contains names—from a space-separated file, use:

```
$ cut -d ' ' -f 1 userinfo.txt
```

Here, `-d ' '` sets the delimiter to a space, while `-f 1` specifies that the first field should be extracted.

If the file is comma-separated, simply adjust the delimiter. For instance, to extract the third field (which could represent country names) and save the output to `countries.txt`, run:

```
$ cut -d ',' -f 3 userinfo.txt > countries.txt
```

In this command, the redirection operator (`>`) saves the extracted output to a new file.

## Removing Duplicate Entries

After extracting data—like a list of countries—you might encounter duplicate entries. The `uniq` command removes duplicates from adjacent lines. For example:

```
$ uniq countries.txt
usa
canada
usa
canada
```

To remove duplicates effectively, sort the file first so that similar lines are adjacent, then pipe the output to `uniq`:

```
$ sort countries.txt | uniq
canada
usa
```

Piping (`|`) is a powerful technique that allows you to pass the output from one command directly into another for further processing.

## Comparing Files with Diff

When system upgrades or configuration changes modify files, comparing the old and new versions is crucial. The `diff` command highlights these differences. Consider the following example:

```
$ diff file1 file2
1c1
< only exists in file 1
---
> only exists in file 2
4c4
< only exists in file 1
---
> only exists in file 2
```

In this output, the notation `1c1` indicates that line 1 of `file1` differs from line 1 of `file2`. The `<` symbol shows content from `file1`, while `>` represents content from `file2`.

For more context, use the `-c` option:

```
$ diff -c file1 file2
*** file1	2021-10-28 20:39:43.083264406 -0500
--- file2	2021-10-28 20:40:02.900262846 -0500
**************
** 1,4 ****
! only exists in file 1
  identical line 2
  identical line 3
! only exists in file 1
--- 1,4 ----
! only exists in file 2
  identical line 2
  identical line 3
! only exists in file 2
```

For a side-by-side visual comparison, use the `-y` option:

```
$ diff -y file1 file2
only exists in file 1        | only exists in file 2
identical line 2            | identical line 2
identical line 3            | identical line 3
only exists in file 1
```

Alternatively, you can use `sdiff` for a similar side-by-side comparison:

```
$ sdiff file1 file2
only exists in file 2
identical line 2
identical line 3
exists in file 2
```

---

# Pagers and VI

In this guide, we explore the usage of pagers and the powerful Vim text editor. First, we discuss two popular pagers—Less and More—detailing how to view and navigate through text files in the terminal. Then, we introduce Vim, covering its core functionality, essential commands, and editing techniques.

---

## Using the Less Pager

Pagers like Less allow you to view and navigate through large text files directly in the terminal. Less provides advanced features such as scrolling and searching, making it a popular choice for system log inspections and file viewing.

To open a file using Less, enter the following command (here, we inspect the system log):

```
sudo less /var/log/syslog
```

When the file is open, you will typically see the filename highlighted in the lower left corner. Navigation is easy with the arrow keys for scrolling up and down.

Below is an example snippet from a syslog as displayed by Less:

```
2024-05-07T00:34:30.893981+00:00 kodekloud kernel: Linux version 6.8.0-31-generic (buildd@lcy02-amd64-0080) (x86_64-linux-gnu-gcc-13 (Ubuntu 13.2.0-23ubuntu4) 13.2.0, GNU ld (GNU Binutils for Ubuntu) 2.42) # 31-Ubuntu SMP PREEMPT_DYNAMIC Sat Apr 20 00:40:06 UTC 2024 (Ubuntu 6.8.0-31.31-generic 6.8.1)
2024-05-07T00:34:30.894695+00:00 kodekloud kernel: Command line: BOOT_IMAGE=/vmlinuz-6.8.0-31-generic root=/dev/mapper/ubuntu--vg-ubuntu--lv ro
2024-05-07T00:34:30.894698+00:00 kodekloud kernel: KERNEL supported cpus:
2024-05-07T00:34:30.894699+00:00 kodekloud kernel:  Intel GenuineIntel
2024-05-07T00:34:30.894699+00:00 kodekloud kernel:  AMD AuthenticAMD
2024-05-07T00:34:30.894699+00:00 kodekloud kernel:  Hygon HygonGenuine
2024-05-07T00:34:30.894699+00:00 kodekloud kernel:  Centaur CentaurHauls
2024-05-07T00:34:30.894699+00:00 kodekloud kernel:  zhaoxin Shanghai
2024-05-07T00:34:30.894700+00:00 kodekloud kernel: BIOS-provided physical RAM map:
[...]
/var/log/syslog
```

You can scroll further using the arrow keys. For example, this snippet demonstrates additional navigation:

```
2024-05-07T00:34:30.894695+00:00 kodekloud kernel: Command line: BOOT_IMAGE=/vmlinuz-6.8.0-31-generic ro
2024-05-07T00:34:30.894698+00:00 kodekloud kernel: KERNEL supported cpus:
2024-05-07T00:34:30.894699+00:00 kodekloud kernel: Intel GenuineIntel
2024-05-07T00:34:30.894700+00:00 kodekloud kernel: AMD AuthenticAMD
2024-05-07T00:34:30.894699+00:00 kodekloud kernel: Hygon HygonGenuine
2024-05-07T00:34:30.894699+00:00 kodekloud kernel: Centaur CentaurHauls
2024-05-07T00:34:30.894701+00:00 kodekloud kernel: zhaoxin Shanghai
2024-05-07T00:34:30.894703+00:00 kodekloud kernel: BIOS-provided physical RAM map:
2024-05-07T00:34:30.894704+00:00 kodekloud kernel: BIOS-e820: [mem 0x0000000000000000-0x000000000009fbf]
2024-05-07T00:34:30.894706+00:00 kodekloud kernel: BIOS-e820: [mem 0x000000000009fc00-0x000000000009fff]
2024-05-07T00:34:30.894709+00:00 kodekloud kernel: BIOS-e820: [mem 0x00000000000e0000-0x0000000000effff]
2024-05-07T00:34:30.894709+00:00 kodekloud kernel: BIOS-e820: [mem 0x0000000000100000-0x000000026a6fffff]
```

Tip

Remember to use the arrow keys in Less for smooth navigation through your log files. For more extensive documents, Less is highly recommended over other simpler pagers.

### Searching Within Less

Less includes powerful search functionality. To search within a file:

1. Press the slash (/) key.
2. Type the desired search term (e.g., "system") and press Enter.
3. Less highlights the matching terms, and you can navigate through occurrences by pressing `N` for the next match or `Shift + N` for the previous match.

Below is a sample of search results highlighting various system messages:

```
2024-05-07T00:34:30.894818+00:00 kodekloud systemd[1]: Finished lvm2-monitor.service - Monitoring of LV M2 mirrors, snapshots etc. using dmeventd or progress polling.
2024-05-07T00:34:30.894822+00:00 kodekloud systemd[1]: Finished systemd-remount-fs.service - Remount Root and Kernel File Systems.
2024-05-07T00:34:30.894825+00:00 kodekloud systemd[1]: Mounted sys-fs-fuse-connections.mount - FUSE Control File System.
2024-05-07T00:34:30.894830+00:00 kodekloud systemd[1]: Activating swap swap.img.swap - /swap.img...
2024-05-07T00:34:30.894836+00:00 kodekloud systemd[1]: Starting multipathd.service - Device-Mapper Multipath Device Controller...
2024-05-07T00:34:30.894843+00:00 kodekloud systemd[1]: systemd-hwdb-update.service - Rebuild Hardware Database was skipped because no trigger condition checks were met.
2024-05-07T00:34:30.894847+00:00 kodekloud systemd-modules-load[379]: Inserted module 'dm_multipath'
2024-05-07T00:34:30.894855+00:00 kodekloud systemd[1]: Starting systemd-journal-flush.service - Flush Journal to Persistent Storage...
2024-05-07T00:34:30.894862+00:00 kodekloud systemd[1]: systemd-pstore.service - Platform Persistent Storage Archival was skipped because of an unmet condition check (ConditionDirectoryNotEmpty=/sys/fs/pstore).
2024-05-07T00:34:30.894867+00:00 kodekloud systemd[1]: Starting systemd-random-seed.service - Load/Save Random Seed.
2024-05-07T00:34:30.894862+00:00 kodekloud systemd[1]: systemd-tpm2-setup.service - TPM2 SRK Setup was skipped because of an unmet condition check (ConditionSecurity-measured-uki).
2024-05-07T00:34:30.894867+00:00 kodekloud systemd[1]: Activated swap swap.img.swap - /swap.img.
2024-05-07T00:34:30.894871+00:00 kodekloud systemd[1]: Finished systemd-modules-load.service - Load Kernel Modules.
2024-05-07T00:34:30.894874+00:00 kodekloud systemd[1]: Mounted sys-kernel-config.mount - Kernel Configuration File System.
2024-05-07T00:34:30.894878+00:00 kodekloud systemd[1]: Reached target swap.target - Swaps.
```

By default, searches are case sensitive. To perform a case-insensitive search, append "\c" to your search term (e.g., `/example\c`):

```
2024-05-07T00:34:30.894818+00:00 kodekloud systemd[1]: Finished lvm2-monitor.service - Monitoring of LV M2 mirrors, snapshots etc. using dmeventd or progress polling.
2024-05-07T00:34:30.894822+00:00 kodekloud systemd[1]: Finished systemd-remount-fs.service - Remount Root and Kernel File Systems.
2024-05-07T00:34:30.894825+00:00 kodekloud systemd[1]: Mounted sys-fs-fuse-connections.mount - FUSE Control File System.
2024-05-07T00:34:30.894830+00:00 kodekloud systemd[1]: Activating swap swap.img.swap - /swap.img...
2024-05-07T00:34:30.894836+00:00 kodekloud systemd[1]: Starting multipathd.service - Device-Mapper Multipath Device Controller...
2024-05-07T00:34:30.894843+00:00 kodekloud systemd[1]: systemd-hwdb-update.service - Rebuild Hardware Database was skipped because no trigger condition checks were met.
2024-05-07T00:34:30.894847+00:00 kodekloud systemd-modules-load[379]: Inserted module 'msr'
2024-05-07T00:34:30.894855+00:00 kodekloud systemd[1]: systemd-journal-flush.service - Flush Journal to Persistent Storage...
2024-05-07T00:34:30.894861+00:00 kodekloud systemd[1]: systemd-pstore.service - Platform Persistent Storage Archival was skipped because of an unmet condition check (ConditionDirectoryNotEmpty=/sys/fs/pstore).
2024-05-07T00:34:30.894867+00:00 kodekloud systemd[1]: Starting systemd-random-seed.service - Load/Save Random Seed...
2024-05-07T00:34:30.894872+00:00 kodekloud systemd[1]: systemd-tpm2-setup.service - TPM2 SRK Setup was skipped because of an unmet condition check (ConditionSecurity=measured-uki).
2024-05-07T00:34:30.894878+00:00 kodekloud systemd[1]: Reached target swap.target - Swaps.
```

To navigate backwards through matches, press uppercase `N`. When you are ready to exit Less, simply press `Q`.

---

## Using the More Pager

While Less offers many features, the More pager is a simpler alternative with a more straightforward interface. More is effective for quick file views.

To open a file with More, run the following command:

```
more /path/to/your/file
```

When you view a file with More, a progress indicator (like "More (2%)") appears at the bottom of the screen. Use the spacebar to advance through the file page by page.

Below is a sample output from More:

```
2024-05-07T00:34:30.894971+00:00 kodekloud systemd[1]: plymouth-start.service - Show Plymouth Boot Screen
2024-05-07T00:34:30.894974+00:00 kodekloud systemd[1]: Started systemd-ask-password-console.path - Dispatch Password Requests to Console Directory Watch.
2024-05-07T00:34:30.894980+00:00 kodekloud systemd[1]: systemd-ask-password-plymouth.path - Forward Password Requests to Plymouth Directory Watch was skipped because of an unmet condition check (ConditionPathExists=/run/plymouth/pid).
2024-05-07T00:34:30.894983+00:00 kodekloud systemd[1]: Reached target cryptsetup.target - Local Encrypted Volumes.
2024-05-07T00:34:30.894987+00:00 kodekloud (udev-worker)[456]: dm-0: Process '/usr/bin/unshare -m /usr/bin/snap auto-import --mount=/dev/dm-0' failed with exit code 1.
2024-05-07T00:34:30.894992+00:00 kodekloud (udev-worker)[451]: sda: Process '/usr/bin/unshare -m /usr/bin/snap auto-import --mount=/dev/sda' failed with exit code 1.
2024-05-07T00:34:30.894995+00:00 kodekloud lvm[522]: PV /dev/sda3 online, VG ubuntu-vg is complete.
2024-05-07T00:34:30.894996+00:00 kodekloud lvm[522]: VG ubuntu-vg finished
2024-05-07T00:34:30.895000+00:00 kodekloud (udev-worker)[467]: sda2: Process '/usr/bin/unshare -m /usr/bin/snap auto-import --mount=/dev/sda2' failed with exit code 1.
2024-05-07T00:34:30.895008+00:00 kodekloud (udev-worker)[451]: sda1: Process '/usr/bin/unshare -m /usr/bin/snap auto-import --mount=/dev/sda1' failed with exit code 1.
2024-05-07T00:34:30.895012+00:00 kodekloud (udev-worker)[473]: sr0: Process '/usr/bin/unshare -m /usr/bin/snap auto-import --mount=/dev/sr0' failed with exit code 1.
2024-05-07T00:34:30.895019+00:00 kodekloud (udev-worker)[450]: sda3: Process '/usr/bin/unshare -m /usr/bin/snap auto-import --mount=/dev/sda3' failed with exit code 1.
2024-05-07T00:34:30.895020+00:00 kodekloud systemd[1]: Found device dev-disk-by\x2duuid-18157e24\x2d3de7\x24d2b2\x2d9dc0\x2d21d01162db644.service - File System Check on /dev/disk/by-uuid/18157e24-3d
--More-- (2%)
```

To exit More, press the `Q` key.

---

## Introduction to Vim

Vim, short for "Vi Improved," is a powerful text editor designed for efficiency. When you start Vim, a welcome screen appears, providing basic information and usage tips:

```
VIM - Vi IMproved
version 9.1.16
by Bram Moolenaar et al.
Modified by team+vim@tracker.debian.org
Vim is open source and freely distributable


Become a registered Vim user!
type :help register<Enter> for information


type :q<Enter> to exit
type :help<Enter> or <F1> for on-line help
type :help version9<Enter> for version info
```

### Basic Vim Modes

Vim is a mode-based editor, meaning it operates in different modes for specific tasks:

- **Insert Mode:** Press the `i` key to enter Insert mode and start editing your file.
- **Command Mode:** Press the `Esc` key to switch back to Command mode. In this mode, you can run commands for saving, quitting, or navigating within the file.

Remember

Always press `Esc` to ensure you are in Command mode before executing commands.

### Creating, Saving, and Quitting Files in Vim

To create or edit a file, run:

```
vim test.txt
```

While in Command mode, use the following commands:

- To save your changes, type `:w` and press Enter.
- To save and exit, type `:wq` after pressing `Esc` if necessary.
- To exit without saving, type `:q!`.

To open an existing file, simply enter:

```
vim example.txt
```

### Navigating and Searching in Vim

Movement in Vim is efficient with both arrow keys and command shortcuts:

- Use `h`, `j`, `k`, and `l` for left, down, up, and right navigation respectively.
- To search for text, press `/` followed by your search query (e.g., `/example`) and press Enter.
- For a case-insensitive search, append `\c` to the query (e.g., `/example\c`).
- To jump to a specific line, type `:10` (where 10 is the line number).

### Copying, Pasting, and Cutting Text

Vim offers quick commands for text manipulation:

- Copy (yank) a line: Press `yy`.
- Paste the copied line: Press `p` (repeat to paste multiple times).
- Cut (delete) a line: Press `dd`, and then paste where appropriate using `p`.

These simple commands help streamline your workflow without reliance on a mouse.

---

# Search File Using Grep

In this lesson, you'll learn how to use the grep utility to search for specific text within files. Grep is particularly useful when working with large files containing thousands of lines, as it helps you quickly filter and locate the information you need.

## Basic grep Usage

The general syntax for the grep command is:

grep [options] [search pattern] [file]

For example, to search for the term "password" inside the SSH daemon configuration file, run:

```
$ grep 'password' /etc/ssh/sshd_config
#PermitRootLogin prohibit-password
# To disable tunneled clear text passwords, change to no here!
# Change to yes to enable challenge-response passwords (beware issues with
# the setting of "PermitRootLogin without-password".
```

Tip

Always enclose your search pattern in single quotes to prevent Bash from misinterpreting special characters, such as asterisks used in more complex search patterns.

## Case Sensitivity

By default, grep performs a case-sensitive search. This means that searching for 'password' will only match lowercase occurrences. Searching for 'Password' (with an uppercase P) produces different results.

Consider the following examples:

```
$ grep 'password' /etc/ssh/sshd_config
#PermitRootLogin prohibit-password
# To disable tunneled clear text passwords, change to no here!
# Change to yes to enable challenge-response passwords (beware issues with
# PasswordAuthentication. Depending on your PAM configuration,
# PAM authentication, then enable this but set PasswordAuthentication
```

```
$ grep 'Password' /etc/ssh/sshd_config
#PasswordAuthentication yes
#PermitEmptyPasswords no
# PasswordAuthentication. Depending on your PAM configuration,
# PAM authentication, then enable this but set PasswordAuthentication
```

To perform a case-insensitive search, use the `-i` option:

```
$ grep -i 'password' /etc/ssh/sshd_config
#PermitRootLogin prohibit-password
# To disable tunneled clear text passwords, change to no here!
#PasswordAuthentication yes
#PermitEmptyPasswords no
# Change to yes to enable challenge-response passwords (beware issues with
# PasswordAuthentication. Depending on your PAM configuration,
# the setting of "PermitRootLogin without-password".
# PAM authentication, then enable this but set PasswordAuthentication
```

## Recursive Search in Directories

Grep allows you to search through all files within a directory and its subdirectories using the `-r` (recursive) option. For example:

```
$ grep -r 'password' /etc/
/etc/fwupd/redfish.conf:# The username and password to the Redfish service
grep: /etc/sudoers.d/README: Permission denied
grep: /etc/ssl/private: Permission denied
/etc/ssl/openssl.cnf:# input_password = secret
/etc/ssl/openssl.cnf:# output_password = secret
/etc/ssl/openssl.cnf:challengePassword        = A challenge password
/etc/cloud/cloud.cfg: - set-passwords
```

The matched files are highlighted by default (using color) to help you quickly identify the search term. You might also see "Permission denied" messages for files your user does not have permission to read.

Combine recursive search with case-insensitivity using both `-r` and `-i` options:

```
$ grep -ri 'password' /etc/
/etc/fwupd/redfish.conf:# The username and password to the Redfish service
/etc/fwupd/redfish.conf:#Password=
/etc/fwupd/remotes.d/lvsf-testing.conf:#Password=
grep: /etc/sudoers.d/README: Permission denied
grep: /etc/ssl/private: Permission denied
/etc/ssl/openssl.cnf:# Passwords for private keys if not present they will be prompted for
/etc/ssl/openssl.cnf:  input_password = secret
/etc/ssl/openssl.cnf:  output_password = secret
/etc/ssl/openssl.cnf:challengePassword    = A challenge password
/etc/ssl/openssl.cnf:challengePassword_min  = 4
/etc/ssl/openssl.cnf:challengePassword_max  = 20
/etc/ssl/openssl.cnf:[pbm] # Password-based protection for Insta CA
/etc/cloud/cloud.cfg: - set-passwords
```

## Using sudo for Restricted Files

If you need to search through files that are only accessible by the administrator (root), prepend the grep command with sudo. Note that this might disable colored output, so the `--color` option can be used to force it:

```
$ sudo grep -ri 'password' --color /etc/
/etc/fwupd/redfish.conf:# The username and password to the Redfish service
/etc/fwupd/redfish.conf:#Password=
/etc/fwupd/remotes.d/lvfs-testing.conf:#Password=
/etc/ssl/openssl.cnf:# Passwords for private keys if not present they will be prompted for
/etc/ssl/openssl.cnf:# input_password = secret
/etc/ssl/openssl.cnf:# output_password = secret
/etc/ssl/openssl.cnf:challengePassword        = A challenge password
/etc/ssl/openssl.cnf:challengePassword_min    = 4
/etc/ssl/openssl.cnf:challengePassword_max    = 20
/etc/ssl/openssl.cnf:[pbm] # Password-based protection for Insta CA
/etc/cloud/cloud.cfg:  - set-passwords
```

Important

When using sudo with grep, be aware that forcing colored output is necessary if you want to visualize matches in color.

## Inverting the Search

If you need to display lines that do not contain a specific pattern, use the `-v`option. This inverts the search results to exclude the matched pattern.

## Matching Whole Words

Sometimes it's important to match only the exact word "password" rather than substrings (like "PasswordAuthentication" or "passwords"). The `-w` option restricts grep to whole word matches. Compare the outputs below:

```
$ grep -i 'password' /etc/ssh/sshd_config
#PermitRootLogin prohibit-password
# To disable tunneled clear text passwords, change to no here!
#PasswordAuthentication yes
#PermitEmptyPasswords no
# Change to yes to enable challenge-response passwords (beware issues with
# PasswordAuthentication. Depending on your PAM configuration,
# the setting of "PermitRootLogin without-password".
```

```
$ grep -wi 'password' /etc/ssh/sshd_config
#PermitRootLogin prohibit-password
# the setting of "PermitRootLogin without-password".
```

Notice that using the `-w` option excludes lines where "password" is part of a longer word.

## Displaying Only the Matched Portions

By default, grep prints the entire line containing a match. If you need to extract just the text that matches your pattern, use the `-o` option. This is useful when you want to isolate specific data.

The following example compares two approaches:

```
$ grep -i 'password' /etc/ssh/sshd_config
#PermitRootLogin prohibit-password
# To disable tunneled clear text passwords, change to no here!
#PasswordAuthentication yes
#PermitEmptyPasswords no
# Change to yes to enable challenge-response passwords (beware issues with
# PasswordAuthentication. Depending on your PAM configuration,
# the setting of "PermitRootLogin without-password".
# PAM authentication, then enable this but set PasswordAuthentication
```

```
$ grep -oi 'password' /etc/ssh/sshd_config
password
password
Password
Password
password
Password
password
Password
```

Using the `-o` option outputs only the matching portions of each line, simplifying further data processing.

---

# Analyze Text Using Basic Regular Expressions


![The image contains text related to regular expressions, including an IP address, a decimal number, and conditions for an integer \( x \) that is greater than 3 and less than 8.](https://kodekloud.com/kk-media/image/upload/v1752881231/notes-assets/images/Linux-Foundation-Certified-System-Administrator-LFCS-Analyze-Text-Using-Basic-Regular-Expressions/regular-expressions-ip-decimal-conditions.jpg)

We'll begin with simple examples and gradually progress to more complex expressions. Regular expressions are built using various operators such as the caret (^), dollar sign ($), period (.), asterisk (\*), plus sign (+), braces ({}), question mark (?), vertical pipe (|), brackets (\[\]), and parentheses (()). Each operator has a distinct function that helps tailor your search.

---

>[!RegEx]
>- ^ : The line begins with.
>- $ : The line ends with.
>- . : Match any one character.
>- \* : Match the previous element 0 or more times.
>- \+ : Match the previous element one or more times.
>- {} : Previous element can match "this many times".
>- ? : Makes the previous element optional.
>- | : Match one thing or the other.
>- \[\] : Ranges or sites.
>- () : Subexpressions.
>- \[^\] : Negated range or sets.
## Matching Commented Lines in Linux Configuration Files

In Linux configuration files, lines starting with a pound sign (#) are interpreted as comments. Although these lines are ignored by the system, they provide valuable context and documentation for humans. To search for these commented lines, you can build a regex that matches lines beginning with a pound sign by placing the caret operator (^) at the start of the pattern.

>[!Tip]
Use the caret operator (^) to ensure that your search starts at the beginning of the line.

For example, to list all lines that start with a pound sign:

```bash
grep '^#' /etc/login.defs
```

If you want to display only non-commented lines, combine this with grep's invert option (-v):

```bash
grep -v '^#' /etc/login.defs
```

The output might look like this:

```
MAIL_DIR           /var/mail
FAILLOG_ENAB       yes
LOG_UNKFAIL_ENAB   no
LOG_OK_LOGINS      no
SYSLOG_SU_ENAB     yes
SYSLOG_SG_ENAB     yes
FTMP_FILE          /var/log/btmp
SU_NAME            su
HUSHLOGIN_FILE     .hushlogin
```

This approach is very effective for filtering out cluttered comments in large files.

Similarly, to search for lines that start exactly with the letters "PASS," use:

```bash
grep '^PASS' /etc/login.defs
```

---

## The Caret (^) and Dollar Sign ($) Operators

Sometimes you may need to change a setting that's currently set to a specific value (for example, seven days). A naive search using:

```
$ grep '7' /etc/login.defs
```

might inadvertently match other instances of the digit 7. To refine the search, anchor the pattern to the end of the line using the dollar sign ($) if you know the variable value occurs last on the line:

```
$ grep -w '7$' /etc/login.defs
```

This ensures that only lines ending with the digit 7 are captured. Additionally, to target lines ending with the word "mail," you can use:

```
$ grep 'mail$' /etc/login.defs
```

Remember, the caret (^) specifies the start, while the dollar sign ($) specifies the end of the line.

---

## The Dot (.) Operator and Wildcards

The period (.) is a wildcard character in regex that matches any single character. For example, the pattern `C.T` would match strings such as "cat," "cut," "CRT," "C1T," or "C#T," whereas it wouldn't match "CT" because there must be exactly one character between C and T. Similarly, `C..T` ensures there are exactly two characters between C and T.

To match whole words rather than sub-strings within larger words, leverage grep’s `-w` option:

```
$ grep -r 'c.t' /etc/
```

For a recursive search with whole word matching, use:

```
$ grep -wr 'c.t' /etc/
```

---

## The Asterisk (\*) and Plus (+) Operators

The asterisk (\*) specifies that the preceding element can occur zero, one, or many times. For example, consider the pattern that matches "let" followed by zero or more "T" characters using `let*`. This pattern can match "LE," "LET," "LETT," and so on.

A recursive search example with the asterisk:

```
$ grep -r 'let*' /etc/
```

Be aware that the asterisk can also make the preceding character optional. For instance, the pattern `0*` will match lines regardless of whether the digit 0 is present:

```
$ grep -r '0*' /etc/
```

To search for lines where the digit 0 appears one or more times, use the plus operator (+). Note that grep’s Basic Regular Expressions (BRE) require you to escape the plus sign:

```
$ grep -r '0\+' /etc/
```

Using Extended Regular Expressions with grep's `-E` option eliminates the need for escaping the plus sign.

Remember

When working with grep, consider using the `-E` option for Extended Regular Expressions to simplify your patterns and avoid confusion with escaped characters.

---

# Extended Regular Expressions

Extended regular expressions (ERE) simplify pattern matching by reducing the need for escaping special characters. When using ERE with grep (via the -E option or its alias, egrep), most special characters are interpreted as regex operators by default. You only need to escape them when you want them treated as literal characters.

>[!Tip]
Use ERE with grep by using the uppercase -E flag or the egrep command to simplify your regular expressions and avoid common pitfalls with escaping characters.

## Basic Usage with grep

Consider a command that searches for one or more occurrences of the digit zero in files under the `/etc/` directory:

```
$ grep -Er '0+' /etc/
```

This command uses the `+` operator to match one or more zeros. Equivalently, you could use:

```
$ egrep -r '0+' /etc/
```

In both cases, the command highlights lines in various `/etc/` files where the pattern is found.

## Matching Specific Repetitions

To find strings containing at least three consecutive zeros, you can use the curly bracket syntax:

```
$ egrep -r '0{3,}' /etc/
```

Here, `{3,}` specifies a minimum repetition of three, with no upper limit.

If you want to search for a string beginning with `1` followed by up to three zeros, the pattern is:

```
$ egrep -r '10{,3}' /etc/
```

This regex also matches the case where no zero follows the digit `1`. To match exactly three zeros, omit the comma and second number:

```
$ egrep -r '0{3}' /etc/
```

## Optional Characters with the Question Mark

The question mark operator (`?`) makes the preceding element optional (i.e., it can appear once or not at all). For example, to find lines containing either "disable" or "disabled," you might use:

```
$ egrep -r 'disable?d?' /etc/
```

Be cautious—this expression may also match portions of longer words (like "disables"). To match whole words exactly, consider using the `-w` option with grep or an alternation operator:

```
$ egrep -r 'enabled|disabled' /etc/
```

For broader matching that handles case variations, add the `-i` option for a case-insensitive search.

## Ranges and Sets

Ranges allow you to specify a set of characters between two endpoints. For example:

- `[a-z]` matches any lowercase letter.
- `[0-9]` matches any digit.

Sets allow matching one character from a list. To search for either "cat" or "cut":

```
$ egrep -r 'c[au]t' /etc/
```

This pattern checks for the letters `a` or `u` in the middle of "c?t," effectively matching both "cat" and "cut."

## Combining Regex Patterns: Matching Device Files

When matching configuration entries for device files (e.g., `/dev/sda1` or `/dev/twa0`), the naive use of `.*` might be too broad:

```
$ egrep -r '/dev/.*' /etc/
```

Instead, you can be more specific by matching a forward slash followed by any number of lowercase letters:

```
$ egrep -r '/dev/[a-z]*' /etc/
```

To include trailing digits, adjust the pattern:

```
$ egrep -r '/dev/[a-z]*[0-9]' /etc/
```

This matches only device names ending in a digit. To accommodate both cases (with or without a trailing digit):

```
$ egrep -r '/dev/[a-z]*[0-9]?' /etc/
```

For multi-segment device names (like `/dev/tty0p0`), group the pattern for letters and an optional digit, then allow repetition:

```
$ egrep -r '/dev/([a-z]*[0-9]?)+'
```

If uppercase letters are also possible in device names, extend the character class:

```
$ egrep -r '/dev/(([a-zA-Z])*[0-9]?)+' /etc/
```

This pattern effectively matches various formats, including `/dev/ttyS0`.

## Using the Negation Operator

Inside square brackets, the caret (^) negates a set. For example, to search for the string "https" that is not immediately followed by a colon:

```
$ egrep -r 'https[^:]' /etc/
```

Similarly, you can refine your pattern to match "http" not followed by certain characters by excluding them in the character set.

For example, to find lines where a forward slash is immediately followed by a character that is not a lowercase letter:

```
$ egrep -r '/[^a-z]' /etc/
```

This command will return lines where the character following `/` does not fall within the lowercase alphabet.

## Practical Considerations and Further Resources

Regular expressions provide a powerful, precise method for text searching and manipulation. By mastering regex operators, ranges, sets, and grouping, you can craft expressions tailored to your needs.
