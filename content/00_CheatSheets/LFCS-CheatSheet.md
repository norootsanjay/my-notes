
> [!Note] 
> A comprehensive reference for LFCS examination preparation.  
> Covers essential Linux system administration commands and concepts.

---

## Essential Commands

### Log in to Local Remote Graphical and Text Mode Consoles

| **Modes**                    | **Description**                                     |
| ---------------------------- | --------------------------------------------------- |
| Local Text Mode Console      | Press `Ctrl+Alt+F3` to `F6` (TTY3-TTY6)             |
| Local Graphical Mode Console | Press `Ctrl+Alt+F1` or `F2` (GUI on TTY1/TTY2)      |
| Remote Text Mode Login       | SSH connection to remote server                     |
| Remote Graphical Mode Login  | VNC, RDP, or X11 forwarding over SSH with `-X` flag |

```bash
# Remote text mode login
ssh username@192.168.1.100

# Remote graphical mode (X11 forwarding)
ssh -X username@192.168.1.100

# SSH with specific port
ssh -p 2222 username@192.168.1.100

# Switch to different TTY locally
# Ctrl+Alt+F3 (TTY3), Ctrl+Alt+F1 (back to GUI)
```

### Read and Use System Documentation

|**Command**|**Description**|**Example**|
|---|---|---|
|`--help`|Display brief help for a command|`ls --help`|
|`man`|Display comprehensive manual page for a command|`man journalctl`|
|`apropos`|Search man pages for keywords in their short description|`apropos director`|
|`man -k`|Same as apropos - searches man page descriptions|`man -k network`|
|`info`|Display detailed GNU info documentation|`info coreutils`|
|`whatis`|Display one-line description of a command|`whatis grep`|
|`type`|Show command type (alias, built-in, file)|`type cd`|
|`which`|Show full path of command executable|`which python3`|
|`whereis`|Locate binary, source, and man pages|`whereis nginx`|

### Create Delete Copy and Move Files and Directories

|**Command**|**Description**|**Example**|
|---|---|---|
|`pwd`|Display current working directory|`pwd`|
|`touch`|Create new empty file or update timestamp|`touch demo.txt`|
|`mkdir`|Create new directory|`mkdir demo/`|
|`mkdir -p`|Create nested directories|`mkdir -p project/src/main`|
|`cp`|Copy file|`cp file1.txt file2.txt`|
|`cp -r`|Copy directory recursively|`cp -r Folder1/ Folder2/`|
|`cp -p`|Copy preserving attributes|`cp -p config.conf backup.conf`|
|`mv`|Move or rename file/directory|`mv receipt.pdf Receipts/`|
|`rm`|Remove file|`rm oldfile.txt`|
|`rm -r`|Remove directory recursively|`rm -r old_project/`|
|`rm -f`|Force remove without confirmation|`rm -f *.tmp`|
|`rm -rf`|Force remove directory (use with caution!)|`rm -rf /tmp/cache/`|
|`rmdir`|Remove empty directory only|`rmdir empty_folder/`|
|`ls`|List directory contents|`ls -lah`|
|`tree`|Display directory structure as tree|`tree -L 2`|

### Create and Manage Hard and Soft Links

|**Command**|**Description**|**Example**|
|---|---|---|
|`ln`|Create hard link (same inode, same data)|`ln original.txt hardlink.txt`|
|`ln -s`|Create symbolic/soft link (pointer to file)|`ln -s /var/log/app.log ~/app.log`|
|`stat`|Display detailed file information and inode|`stat filename.txt`|
|`ls -li`|List files with inode numbers|`ls -li`|
|`readlink`|Display target of symbolic link|`readlink -f symlink`|
|`unlink`|Remove link (hard or soft)|`unlink linkfile`|

**Key Differences:**

- **Hard Link**: Points to same inode; survives if original deleted; cannot cross filesystems
- **Soft Link**: Points to pathname; breaks if original deleted; can cross filesystems

```bash
# Create hard link
ln /home/user/data.txt /backup/data.txt

# Create symbolic link
ln -s /usr/share/nginx/html /var/www/html

# Check inode numbers (hard links share same inode)
ls -li original.txt hardlink.txt

# Verify symbolic link
stat symlink.txt
```

### List Set and Change Standard File Permission

|**Command**|**Description**|**Example**|
|---|---|---|
|`ls -l`|List files with permissions|`ls -l`|
|`chmod`|Change file permissions|`chmod 755 script.sh`|
|`chmod u+x`|Add execute permission for user/owner|`chmod u+x file.sh`|
|`chmod g-w`|Remove write permission for group|`chmod g-w data.txt`|
|`chmod o=r`|Set others to read-only|`chmod o=r public.txt`|
|`chmod a+r`|Add read permission for all|`chmod a+r readme.md`|
|`umask`|Set default permission mask|`umask 022`|
|`groups`|Display user's group memberships|`groups username`|
|`chgrp`|Change group ownership|`chgrp developers project/`|
|`chown`|Change user ownership|`chown john file.txt`|
|`chown u:g`|Change both user and group ownership|`chown john:developers project/`|
|`chown -R`|Change ownership recursively|`chown -R www-data:www-data /var/www`|

**Permission Values:**

- `r` (read) = 4
- `w` (write) = 2
- `x` (execute) = 1

```bash
# Numeric: rwx = 7, rw- = 6, r-x = 5, r-- = 4
chmod 644 file.txt    # rw-r--r--
chmod 755 script.sh   # rwxr-xr-x
chmod 700 private/    # rwx------

# Symbolic
chmod u+x,g+x script.sh       # Add execute for user and group
chmod u=rwx,g=rx,o=r file.txt # Set specific permissions
```

### SUID SGID and Sticky Bit

|**Command**|**Description**|**Example**|
|---|---|---|
|`chmod 4755`|Set SUID bit (executes with owner's privileges)|`chmod 4755 /usr/bin/passwd`|
|`chmod 2775`|Set SGID bit (executes with group's privileges)|`chmod 2775 /shared/project/`|
|`chmod 6755`|Set both SUID and SGID|`chmod 6755 special_binary`|
|`chmod 1777`|Set sticky bit (only owner can delete files)|`chmod 1777 /tmp`|
|`chmod u+s`|Add SUID using symbolic notation|`chmod u+s binary`|
|`chmod g+s`|Add SGID using symbolic notation|`chmod g+s shared_folder/`|
|`chmod +t`|Add sticky bit using symbolic notation|`chmod +t public_folder/`|

**Special Permissions:**

- **SUID (4)**: File executes with owner's permissions (shown as `s` in user execute)
- **SGID (2)**: File executes with group's permissions; new files inherit directory group
- **Sticky Bit (1)**: Only file owner can delete their files in directory (common on /tmp)

```bash
# Find files with SUID
find / -perm -4000 2>/dev/null

# Find files with SGID
find / -perm -2000 2>/dev/null

# Find directories with sticky bit
find / -type d -perm -1000 2>/dev/null

# Example: Set SGID on shared folder
mkdir /shared/team
chmod 2775 /shared/team
chgrp developers /shared/team
```

### Search For Files

|**Command**|**Description**|**Example**|
|---|---|---|
|`find`|Search for files in directory hierarchy|`find /home -name "*.txt"`|
|`find -name`|Search by filename (case-sensitive)|`find . -name "config.conf"`|
|`find -iname`|Search by filename (case-insensitive)|`find /etc -iname "*.CONF"`|
|`find -type f`|Search for files only|`find /var/log -type f -name "*.log"`|
|`find -type d`|Search for directories only|`find /home -type d -name "backup"`|
|`find -size`|Search by file size|`find /tmp -size +100M`|
|`find -mtime`|Search by modification time (days)|`find /var -mtime -7`|
|`find -mmin`|Search by modification time (minutes)|`find /tmp -mmin -30`|
|`find -perm`|Search by permissions|`find / -perm -4000`|
|`find -user`|Search by owner|`find /home -user john`|
|`find -group`|Search by group|`find /var -group www-data`|
|`find -exec`|Execute command on found files|`find . -name "*.tmp" -exec rm {} \;`|
|`locate`|Fast search using database|`locate nginx.conf`|
|`updatedb`|Update locate database|`sudo updatedb`|

```bash
# Find files larger than 100MB
find /home -type f -size +100M

# Find files modified in last 24 hours
find /var/log -type f -mtime -1

# Find files with SUID permission
find /usr/bin -perm -4000

# Find and delete files older than 30 days
find /tmp -type f -mtime +30 -delete

# Find empty files
find /home -type f -empty

# Find files and change permissions
find /var/www -type f -exec chmod 644 {} \;
find /var/www -type d -exec chmod 755 {} \;

# Complex find with multiple conditions
find /var/log -type f -name "*.log" -size +10M -mtime +30
```

### Compare and Manipulate File Content

|**Command**|**Description**|**Example**|
|---|---|---|
|`cat`|Display file contents|`cat file.txt`|
|`cat > file`|Create file with input|`cat > newfile.txt`|
|`tac`|Display file in reverse (last line first)|`tac /var/log/syslog`|
|`tail`|Display last 10 lines of file|`tail /var/log/messages`|
|`tail -n 20`|Display last N lines|`tail -n 20 app.log`|
|`tail -f`|Follow file updates in real-time|`tail -f /var/log/apache2/error.log`|
|`head`|Display first 10 lines of file|`head /etc/passwd`|
|`head -n 5`|Display first N lines|`head -n 5 data.csv`|
|`sed`|Stream editor for text manipulation|`sed 's/old/new/g' file.txt`|
|`sed -i`|Edit file in-place|`sed -i 's/error/warning/' log.txt`|
|`cut`|Extract columns from text|`cut -d: -f1,3 /etc/passwd`|
|`cut -c`|Extract specific characters|`cut -c1-10 file.txt`|
|`uniq`|Remove duplicate adjacent lines|`sort file.txt \| uniq`|
|`uniq -c`|Count duplicate lines|`sort file.txt \| uniq -c`|
|`sort`|Sort lines alphabetically|`sort names.txt`|
|`sort -n`|Sort numerically|`sort -n numbers.txt`|
|`sort -r`|Sort in reverse order|`sort -r file.txt`|
|`sort -k`|Sort by specific column|`sort -k2 -t: /etc/passwd`|
|`diff`|Compare two files line by line|`diff file1.txt file2.txt`|
|`diff -u`|Unified diff format|`diff -u old.conf new.conf`|
|`wc`|Count lines, words, characters|`wc -l file.txt`|
|`tr`|Translate or delete characters|`tr 'a-z' 'A-Z' < file.txt`|
|`paste`|Merge lines from multiple files|`paste file1.txt file2.txt`|

```bash
# Replace text in file
sed 's/oldtext/newtext/g' input.txt > output.txt

# Delete lines matching pattern
sed '/pattern/d' file.txt

# Extract first column from CSV
cut -d',' -f1 data.csv

# Sort and remove duplicates
sort file.txt | uniq

# Count occurrences of each line
sort access.log | uniq -c | sort -rn

# Display differences side by side
diff -y file1.txt file2.txt
```

### Pagers and VI

|**Command**|**Description**|**Example**|
|---|---|---|
|`less`|View file with navigation (recommended)|`less /var/log/syslog`|
|`more`|Simple pager (forward only)|`more README.md`|
|`vim`|Advanced text editor|`vim config.conf`|
|`vi`|Basic text editor|`vi script.sh`|
|`nano`|User-friendly text editor|`nano file.txt`|

**Essential VIM Commands:**

- **Normal Mode:**
    - `i` - Insert mode (before cursor)
    - `a` - Insert mode (after cursor)
    - `o` - New line below
    - `dd` - Delete line
    - `yy` - Copy line
    - `p` - Paste
    - `/pattern` - Search forward
    - `?pattern` - Search backward
    - `n` - Next search result
    - `u` - Undo
    - `Ctrl+r` - Redo
    - `:w` - Save
    - `:q` - Quit
    - `:wq` or `:x` - Save and quit
    - `:q!` - Quit without saving
    - `:%s/old/new/g` - Replace all occurrences
    - `:set number` - Show line numbers

**Less Navigation:**

- `Space` or `f` - Forward one page
- `b` - Backward one page
- `/pattern` - Search forward
- `?pattern` - Search backward
- `q` - Quit

### Search file using grep

|**Command**|**Description**|**Example**|
|---|---|---|
|`grep`|Search for pattern in file|`grep "error" /var/log/syslog`|
|`grep -r`|Recursive search in directories|`grep -r "TODO" /home/project/`|
|`grep -ri`|Recursive case-insensitive search|`grep -ri "password" /etc/`|
|`grep -v`|Invert match (show non-matching lines)|`grep -v "^#" config.conf`|
|`grep -n`|Show line numbers|`grep -n "error" app.log`|
|`grep -c`|Count matching lines|`grep -c "Failed" auth.log`|
|`grep -l`|List files containing pattern|`grep -rl "function" *.py`|
|`grep -w`|Match whole words only|`grep -w "root" /etc/passwd`|
|`grep -wi`|Whole word case-insensitive|`grep -wi "error" *.log`|
|`grep -o`|Show only matching part|`grep -o "192\.168\.[0-9]*\.[0-9]*" log`|
|`grep -oi`|Show only matching part (case-insensitive)|`grep -oi "error\|warning" app.log`|
|`grep -A`|Show N lines after match|`grep -A 3 "error" log.txt`|
|`grep -B`|Show N lines before match|`grep -B 2 "critical" log.txt`|
|`grep -C`|Show N lines around match|`grep -C 5 "exception" app.log`|
|`grep -E`|Extended regex (same as egrep)|`grep -E "error\|fail" log.txt`|
|`egrep`|Extended grep with regex support|`egrep "^[0-9]+" numbers.txt`|
|`fgrep`|Fast grep (no regex, literal strings)|`fgrep "192.168.1.1" access.log`|

```bash
# Find all error lines in logs
grep -i "error" /var/log/syslog

# Search recursively excluding binary files
grep -r "password" /etc/ 2>/dev/null

# Find IP addresses
grep -E "[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}" access.log

# Search multiple patterns
grep -E "error|warning|critical" app.log

# Show only uncommented lines
grep -v "^#" /etc/ssh/sshd_config | grep -v "^$"
```

### Analyze Text Using Basic Regular Expression

|**Symbol**|**Description**|**Example**|
|---|---|---|
|`^`|Match beginning of line|`grep "^root" /etc/passwd`|
|`$`|Match end of line|`grep "bash$" /etc/passwd`|
|`.`|Match any single character|`grep "r..t" file.txt`|
|`*`|Match zero or more of previous character|`grep "go*gle" file.txt`|
|`+`|Match one or more of previous (extended regex)|`grep -E "go+gle" file.txt`|
|`?`|Match zero or one of previous (extended regex)|`grep -E "colou?r" file.txt`|
|`{n}`|Match exactly n occurrences|`grep -E "a{3}" file.txt`|
|`{n,m}`|Match between n and m occurrences|`grep -E "[0-9]{2,4}" file.txt`|
|`[]`|Match any character in brackets|`grep "[aeiou]" file.txt`|
|`[^]`|Match any character NOT in brackets|`grep "[^0-9]" file.txt`|
|`[a-z]`|Match range of characters|`grep "[A-Z]" file.txt`|
|`\|`|OR operator (alternation)|`grep -E "cat\|dog" file.txt`|
|`()`|Grouping|`grep -E "(error\|fail)(ed)?" log.txt`|
|`\`|Escape special character|`grep "\." file.txt`|

```bash
# Lines starting with digit
grep "^[0-9]" file.txt

# Lines ending with punctuation
grep "[.!?]$" file.txt

# Email addresses
grep -E "[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}" file.txt

# IP addresses
grep -E "^([0-9]{1,3}\.){3}[0-9]{1,3}$" ips.txt

# Match phone numbers (xxx-xxx-xxxx)
grep -E "[0-9]{3}-[0-9]{3}-[0-9]{4}" contacts.txt

# Lines with 3-5 consecutive vowels
grep -E "[aeiou]{3,5}" file.txt
```

### Archive Backup Compress Unpack and Un-compress files

|**Command**|**Description**|**Example**|
|---|---|---|
|`tar`|Archive utility|`tar cvf archive.tar files/`|
|`tar --list`|List contents of archive|`tar --list -f archive.tar`|
|`tar -tf`|List archive contents (short form)|`tar -tf archive.tar`|
|`tar tf`|List without dash (GNU tar allows)|`tar tf archive.tar`|
|`tar --create`|Create new archive|`tar --create -f backup.tar /data/`|
|`tar -cvf`|Create archive with verbose output|`tar -cvf backup.tar /home/user/`|
|`tar cf`|Create archive (minimal flags)|`tar cf backup.tar files/`|
|`tar --append`|Append files to existing archive|`tar --append -f archive.tar new.txt`|
|`tar -rvf`|Append files (short form)|`tar -rvf archive.tar newfile.txt`|
|`tar --extract`|Extract archive|`tar --extract -f archive.tar`|
|`tar -xvf`|Extract with verbose output|`tar -xvf backup.tar`|
|`tar xf`|Extract (minimal flags)|`tar xf archive.tar`|
|`tar -xvf -C`|Extract to specific directory|`tar -xvf backup.tar -C /restore/`|
|`tar -czf`|Create gzip compressed archive|`tar -czf backup.tar.gz /data/`|
|`tar -cjf`|Create bzip2 compressed archive|`tar -cjf backup.tar.bz2 /data/`|
|`tar -cJf`|Create xz compressed archive|`tar -cJf backup.tar.xz /data/`|
|`tar -xzf`|Extract gzip compressed archive|`tar -xzf backup.tar.gz`|
|`tar -xjf`|Extract bzip2 compressed archive|`tar -xjf backup.tar.bz2`|
|`tar -xJf`|Extract xz compressed archive|`tar -xJf backup.tar.xz`|

```bash
# Create tar archive
tar -cvf backup_$(date +%F).tar /home/user/documents/

# List archive contents
tar -tvf backup.tar

# Extract specific file from archive
tar -xvf backup.tar path/to/file.txt

# Create compressed archive
tar -czf website_backup.tar.gz /var/www/html/

# Extract to specific directory
tar -xzf backup.tar.gz -C /restore/

# Exclude files when creating archive
tar -czf backup.tar.gz --exclude='*.log' /var/log/
```

### Compress and Uncompress Files Optional

|**Command**|**Description**|**Example**|
|---|---|---|
|`gzip`|Compress file (replaces original)|`gzip largefile.txt`|
|`gzip -k`|Compress keeping original file|`gzip -k file.txt`|
|`gzip -9`|Maximum compression|`gzip -9 file.txt`|
|`gzip -l`|List compression information|`gzip -l file.txt.gz`|
|`gunzip`|Decompress gzip file|`gunzip file.txt.gz`|
|`bzip2`|Compress with bzip2 (better compression)|`bzip2 largefile.txt`|
|`bzip2 -k`|Compress keeping original|`bzip2 -k data.txt`|
|`bunzip2`|Decompress bzip2 file|`bunzip2 file.txt.bz2`|
|`xz`|Compress with xz (best compression)|`xz largefile.txt`|
|`xz -k`|Compress keeping original|`xz -k database.sql`|
|`unxz`|Decompress xz file|`unxz file.txt.xz`|
|`zip`|Create zip archive|`zip archive.zip file1 file2`|
|`zip -r`|Zip directory recursively|`zip -r backup.zip folder/`|
|`zip -e`|Create encrypted zip|`zip -e secure.zip data.txt`|
|`unzip`|Extract zip archive|`unzip archive.zip`|
|`unzip -l`|List zip contents|`unzip -l archive.zip`|
|`zcat`|View compressed file without extracting|`zcat file.txt.gz`|
|`zless`|Page through compressed file|`zless logfile.gz`|

**Compression Comparison:**

- **gzip**: Fast, moderate compression (~60-70%)
- **bzip2**: Slower, better compression (~70-80%)
- **xz**: Slowest, best compression (~80-90%)

```bash
# Compress file
gzip -k important.log

# Decompress
gunzip important.log.gz

# View compressed log without extracting
zcat /var/log/syslog.1.gz | grep error

# Create zip archive
zip -r project_backup.zip /home/user/project/

# Extract zip to specific directory
unzip archive.zip -d /restore/
```

### Back Up Files to a Remote System Optional

|**Command**|**Description**|**Example**|
|---|---|---|
|`rsync -a`|Archive mode sync (preserves attributes)|`rsync -a /source/ user@remote:/backup/`|
|`rsync -av`|Archive mode with verbose output|`rsync -av /data/ backup.example.com:/backups/`|
|`rsync -avz`|Archive with compression|`rsync -avz /home/ user@server:/home_backup/`|
|`rsync -avzP`|Archive with progress and partial resume|`rsync -avzP /large/ remote:/backup/`|
|`rsync --delete`|Delete files in destination not in source|`rsync -av --delete /src/ /dest/`|
|`rsync -e ssh`|Specify SSH for transport|`rsync -ave ssh /data/ user@host:/backup/`|
|`sudo dd`|Low-level disk cloning|`sudo dd if=/dev/sda of=/dev/sdb bs=64K status=progress`|
|`dd if=`|Input file/device|`sudo dd if=/dev/sda of=disk.img`|
|`dd of=`|Output file/device|`sudo dd if=backup.img of=/dev/sdb`|

```bash
# Sync local to remote with SSH
rsync -avz -e "ssh -p 2222" /home/user/ backup@server:/backups/user/

# Sync remote to local
rsync -avz user@server:/var/www/ /local/backup/

# Dry run (test without making changes)
rsync -avn --delete /source/ /destination/

# Exclude files
rsync -av --exclude='*.tmp' --exclude='cache/' /data/ /backup/

# Create disk image
sudo dd if=/dev/sda of=/mnt/backup/disk.img bs=4M status=progress

# Restore disk image
sudo dd if=/mnt/backup/disk.img of=/dev/sda bs=4M status=progress

# Backup with progress
rsync -ah --info=progress2 /source/ user@remote:/backup/
```

### Use Input Output Redirection

|**Operator**|**Description**|**Example**|
|---|---|---|
|`>`|Redirect stdout to file (overwrite)|`echo "text" > file.txt`|
|`>>`|Redirect stdout to file (append)|`echo "more" >> file.txt`|
|`<`|Redirect stdin from file|`wc -l < input.txt`|
|`<<`|Here document (multi-line input)|`cat << EOF > file.txt`|
|`<<<`|Here string (single line input)|`bc <<< "5 + 3"`|
|`\|`|Pipe stdout to next command|`ls -l \| grep ".txt"`|
|`2>`|Redirect stderr to file|`command 2> error.log`|
|`2>>`|Append stderr to file|`command 2>> error.log`|
|`&>`|Redirect both stdout and stderr|`command &> output.log`|
|`2>&1`|Redirect stderr to stdout|`command > output.log 2>&1`|
|`1>&2`|Redirect stdout to stderr|`echo "error" 1>&2`|
|`/dev/null`|Discard output|`command > /dev/null 2>&1`|
|`tee`|Write to file and stdout simultaneously|`ls \| tee list.txt`|
|`tee -a`|Append to file and stdout|`echo "log" \| tee -a logfile.txt`|

> [!Note]
> 
> - **STDIN (0)**: Standard Input
> - **STDOUT (1)**: Standard Output
> - **STDERR (2)**: Standard Error

```bash
# Save command output to file
ls -l /home > listing.txt

# Append to existing file
date >> log.txt

# Redirect errors to file
find / -name "*.conf" 2> errors.txt

# Redirect both stdout and stderr
make install > install.log 2>&1

# Discard errors
grep -r "pattern" /etc 2>/dev/null

# Save output and view simultaneously
dmesg | tee dmesg_output.txt

# Here document example
cat << EOF > config.txt
server=192.168.1.1
port=8080
EOF

# Pipe multiple commands
ps aux | grep nginx | awk '{print $2}' | xargs kill

# Redirect stdin from file
mysql database < backup.sql
```

### Working with SSL Certificates

|**Command**|**Description**|**Example**|
|---|---|---|
|`openssl help`|Display OpenSSL help|`openssl help`|
|`openssl version`|Show OpenSSL version|`openssl version -a`|
|`man openssl`|OpenSSL manual|`man openssl`|
|`man openssl req`|Manual for certificate request|`man openssl req`|
|`openssl req`|Create certificate request|`openssl req -new -key private.key -out request.csr`|
|`openssl req -x509`|Create self-signed certificate|`openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365`|
|`openssl x509`|Display/manipulate X.509 certificates|`openssl x509 -in cert.pem -text -noout`|
|`openssl x509 -in`|Read and display certificate|`openssl x509 -in certificate.crt -text -noout`|
|`openssl genrsa`|Generate RSA private key|`openssl genrsa -out private.key 2048`|
|`openssl rsa`|Process RSA keys|`openssl rsa -in private.key -check`|
|`openssl s_client`|SSL/TLS client for testing connections|`openssl s_client -connect google.com:443`|
|`openssl verify`|Verify certificate|`openssl verify -CAfile ca.crt certificate.crt`|

```bash
# Generate private key
openssl genrsa -out server.key 2048

# Generate certificate signing request (CSR)
openssl req -new -key server.key -out server.csr

# Create self-signed certificate
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes

# View certificate details
openssl x509 -in cert.pem -text -noout

# Test SSL connection
openssl s_client -connect example.com:443 -servername example.com

# Check certificate expiration
openssl x509 -in cert.pem -noout -enddate

# Convert certificate formats
openssl x509 -in cert.crt -outform PEM -out cert.pem

# Verify private key matches certificate
openssl x509 -noout -modulus -in cert.pem | openssl md5
openssl rsa -noout -modulus -in key.pem | openssl md5
```

### Git Operations

|**Command**|**Description**|**Example**|
|---|---|---|
|`git config --global user.name`|Set global username|`git config --global user.name "John"`|
|`git config --global user.email`|Set global email|`git config --global user.email "j@example.com"`|
|`git init`|Initialize new Git repository|`git init`|
|`git clone`|Clone remote repository|`git clone https://github.com/user/repo.git`|
|`git status`|Show working tree status|`git status`|
|`git add`|Add files to staging area|`git add file.txt`|
|`git add .`|Add all changes|`git add .`|
|`git commit -m`|Commit staged changes with message|`git commit -m "Fix bug"`|
|`git commit -am`|Add and commit tracked files|`git commit -am "Update docs"`|
|`git log`|Show commit history|`git log --oneline`|
|`git log --raw`|Show commit history with file changes|`git log --raw`|
|`git diff`|Show changes between commits/working tree|`git diff`|
|`git reset`|Unstage files|`git reset HEAD file.txt`|
|`git reset --hard`|Reset to specific commit (destructive)|`git reset --hard HEAD~1`|
|`git rm`|Remove file from repo|`git rm oldfile.txt`|
|`git mv`|Move/rename file|`git mv old.txt new.txt`|
|`git branch`|List/create branches|`git branch feature-x`|
|`git branch -d`|Delete branch|`git branch -d old-feature`|
|`git checkout`|Switch branches|`git checkout main`|
|`git checkout -b`|Create and switch to new branch|`git checkout -b feature-y`|
|`git checkout master`|Switch to master branch|`git checkout master`|
|`git merge`|Merge branch into current branch|`git merge feature-x`|
|`git pull`|Fetch and merge from remote|`git pull origin main`|
|`git fetch`|Download objects from remote|`git fetch origin`|
|`git push`|Push commits to remote|`git push origin main`|
|`git push -u`|Push and set upstream|`git push -u origin feature-x`|
|`git remote`|Manage remote repositories|`git remote -v`|
|`git remote -v`|Show remote URLs|`git remote -v`|
|`git remote add origin`|Add remote repository|`git remote add origin https://...`|
|`git stash`|Temporarily save changes|`git stash`|
|`git stash pop`|Restore stashed changes|`git stash pop`|
|`git tag`|Create tag|`git tag v1.0.0`|

```bash
# Configure Git
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Initialize repository
git init
git add .
git commit -m "Initial commit"

# Create and switch to branch
git checkout -b feature-authentication

# View commit history
git log --oneline --graph --all

# Undo last commit (keep changes)
git reset --soft HEAD~1

# Discard local changes
git checkout -- file.txt

# View differences
git diff HEAD~1 HEAD

# Merge branch
git checkout main
git merge feature-authentication

# Push to remote
git push origin main

# Pull latest changes
git pull origin main
```

#### Configuring SSH Keys

|**Command**|**Description**|**Example**|
|---|---|---|
|`ssh-keygen`|Generate SSH key pair|`ssh-keygen -t rsa -b 4096`|
|`ssh-keygen -t ed25519`|Generate Ed25519 key (recommended)|`ssh-keygen -t ed25519 -C "email@example.com"`|
|`ssh-copy-id`|Copy public key to remote server|`ssh-copy-id user@server`|
|`ssh-add`|Add private key to SSH agent|`ssh-add ~/.ssh/id_rsa`|
|`ssh-agent`|Start SSH agent|`eval $(ssh-agent)`|

```bash
# Generate SSH key pair
ssh-keygen -t ed25519 -C "your_email@example.com"

# Copy public key to remote server
ssh-copy-id user@192.168.1.100

# Test SSH connection
ssh -T git@github.com

# Add key to SSH agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# View public key
cat ~/.ssh/id_ed25519.pub
```

---

## Operations Deployment

### Boot Reboot and Shutdown a System Safely

|**Command**|**Description**|**Example**|
|---|---|---|
|`sudo systemctl reboot`|Reboot system immediately|`sudo systemctl reboot`|
|`sudo systemctl poweroff`|Power off system immediately|`sudo systemctl poweroff`|
|`sudo systemctl halt`|Halt system (stop CPU)|`sudo systemctl halt`|
|`sudo systemctl suspend`|Suspend system (sleep mode)|`sudo systemctl suspend`|
|`sudo systemctl hibernate`|Hibernate system (save to disk)|`sudo systemctl hibernate`|
|`sudo systemctl reboot --force`|Force reboot (skip shutdown scripts)|`sudo systemctl reboot --force`|
|`sudo shutdown now`|Shutdown immediately|`sudo shutdown now`|
|`sudo shutdown +5`|Shutdown in 5 minutes|`sudo shutdown +5`|
|`sudo shutdown 02:00`|Shutdown at 2:00 AM|`sudo shutdown 02:00`|
|`sudo shutdown -r now`|Reboot immediately|`sudo shutdown -r now`|
|`sudo shutdown -r +15`|Reboot in 15 minutes|`sudo shutdown -r +15`|
|`sudo shutdown -r 02:00`|Reboot at 2:00 AM|`sudo shutdown -r 02:00`|
|`sudo shutdown -r +1 'Scheduled'`|Reboot with message to logged-in users|`sudo shutdown -r +1 'Maintenance reboot'`|
|`sudo shutdown -c`|Cancel scheduled shutdown|`sudo shutdown -c`|
|`wall`|Send message to all logged-in users|`wall "System rebooting in 5 minutes"`|
|`who`|Show who is logged in|`who`|
|`last`|Show last logged in users|`last`|
|`uptime`|Show system uptime|`uptime`|

```bash
# Schedule shutdown at specific time
sudo shutdown 22:00 "System going down for maintenance"

# Reboot in 10 minutes with warning
sudo shutdown -r +10 "Scheduled reboot for updates"

# Cancel scheduled shutdown
sudo shutdown -c

# Check who's logged in before shutting down
who
w

# Immediate poweroff
sudo systemctl poweroff

# Check system uptime
uptime
```

### Boot or Change System into Different Operating Modes Optional

|**Target**|**Description**|**Use Case**|**Command Example**|
|---|---|---|---|
|graphical.target|Boots into a full graphical desktop environment|Standard desktop usage|`systemctl get-default`|
|multi-user.target|Boots into a text-based environment with network services|Server or low-resource environments|`sudo systemctl set-default multi-user.target`|
|rescue.target|Loads essential services with a root shell access|Administrative tasks in a minimal environment|`sudo systemctl rescue`|
|emergency.target|Boots with minimal system services; root FS is read-only|Critical troubleshooting when other services cause issues|`sudo systemctl emergency`|
|reboot.target|Reboots the system|Restart the system|`sudo systemctl isolate reboot.target`|
|poweroff.target|Powers off the system|Shutdown the system|`sudo systemctl isolate poweroff.target`|

|**Command**|**Description**|**Example**|
|---|---|---|
|`systemctl get-default`|Show current default target|`systemctl get-default`|
|`sudo systemctl set-default`|Set default boot target|`sudo systemctl set-default multi-user.target`|
|`sudo systemctl isolate`|Switch to different target immediately|`sudo systemctl isolate rescue.target`|
|`systemctl list-units --type=target`|List all available targets|`systemctl list-units --type=target`|
|`sudo systemctl rescue`|Enter rescue mode|`sudo systemctl rescue`|
|`sudo systemctl emergency`|Enter emergency mode|`sudo systemctl emergency`|

```bash
# Check current default target
systemctl get-default

# Change default to multi-user (no GUI)
sudo systemctl set-default multi-user.target

# Change default to graphical (with GUI)
sudo systemctl set-default graphical.target

# Switch to multi-user mode immediately
sudo systemctl isolate multi-user.target

# Switch to graphical mode immediately
sudo systemctl isolate graphical.target

# Enter rescue mode (single-user)
sudo systemctl rescue

# View all targets
systemctl list-units --type=target --all
```

### Use Scripting to Automate System Maintenance Tasks

|**Command**|**Description**|**Example**|
|---|---|---|
|`bash`|Execute bash script|`bash script.sh`|
|`./script.sh`|Execute script directly|`./script.sh`|
|`date`|Display current date and time|`date +"%Y-%m-%d %H:%M:%S"`|
|`date +%F`|Display date in YYYY-MM-DD format|`date +%F`|
|`echo`|Print text to stdout|`echo "Hello World"`|
|`read`|Read user input|`read -p "Enter name: " name`|
|`if`|Conditional statement|`if [ $? -eq 0 ]; then...; fi`|
|`for`|Loop through items|`for i in {1..5}; do...; done`|
|`while`|Loop while condition is true|`while true; do...; done`|
|`case`|Multiple condition branching|`case $var in ... esac`|
|`$?`|Exit status of last command|`echo $?`|
|`$1, $2`|Positional parameters (script arguments)|`echo $1`|
|`$#`|Number of arguments|`echo "Args: $#"`|
|`$@`|All arguments|`echo $@`|
|`$`|Current process ID|`echo $`|

#### Creating a Basic Script

```bash
#!/bin/bash
# backup_script.sh - Daily backup automation

# Log the date and time when the script was executed
echo "=== Backup started at $(date) ===" >> /var/log/backup.log

# Append the current Linux kernel version to the log
echo "Kernel: $(uname -r)" >> /var/log/backup.log

# Create backup
tar -czf /backups/home_backup_$(date +%F).tar.gz /home/user/ 2>> /var/log/backup.log

# Check if backup succeeded
if [ $? -eq 0 ]; then
    echo "Backup completed successfully" >> /var/log/backup.log
else
    echo "ERROR: Backup failed!" >> /var/log/backup.log
    exit 1
fi

# Remove backups older than 7 days
find /backups/ -name "home_backup_*.tar.gz" -mtime +7 -delete

echo "=== Backup finished at $(date) ===" >> /var/log/backup.log
```

**Make script executable and run:**

```bash
chmod +x backup_script.sh
./backup_script.sh
```

**Advanced Script Examples:**

```bash
#!/bin/bash
# system_monitor.sh - Monitor system resources

# Check disk usage
DISK_USAGE=$(df -h / | awk 'NR==2 {print $5}' | sed 's/%//')

if [ $DISK_USAGE -gt 80 ]; then
    echo "WARNING: Disk usage is at ${DISK_USAGE}%" | mail -s "Disk Alert" admin@example.com
fi

# Check memory usage
FREE_MEM=$(free | awk 'NR==2 {printf "%.0f", $3/$2 * 100}')

if [ $FREE_MEM -gt 90 ]; then
    echo "WARNING: Memory usage is at ${FREE_MEM}%" | logger -t MEMORY_ALERT
fi

# Check failed login attempts
FAILED_LOGINS=$(grep "Failed password" /var/log/auth.log | wc -l)

if [ $FAILED_LOGINS -gt 10 ]; then
    echo "WARNING: ${FAILED_LOGINS} failed login attempts detected" | mail -s "Security Alert" admin@example.com
fi
```

```bash
#!/bin/bash
# cleanup_script.sh - Clean temporary files

# Function to clean directory
cleanup_directory() {
    local dir=$1
    local days=$2
    echo "Cleaning files older than $days days in $dir"
    find "$dir" -type f -mtime +$days -delete
}

# Clean various directories
cleanup_directory "/tmp" 7
cleanup_directory "/var/tmp" 14
cleanup_directory "/var/log" 30

# Clear package cache
echo "Cleaning package cache..."
apt-get clean  # For Debian/Ubuntu
# yum clean all  # For RHEL/CentOS

echo "Cleanup completed at $(date)"
```

### Manage Startup Process and Services in Service Configurations

|**Systemd Units**|**Description**|**Example**|
|---|---|---|
|service|System services and daemons|`sshd.service`|
|socket|IPC or network socket|`docker.socket`|
|device|Device unit for udev|`dev-sda.device`|
|mount|Filesystem mount point|`home.mount`|
|automount|Automount point|`proc-sys-fs-binfmt_misc.automount`|
|target|Group of units (like runlevels)|`multi-user.target`|
|timer|Timer for scheduled tasks (like cron)|`backup.timer`|
|path|File or directory for path-based activation|`cups.path`|
|slice|Resource management group|`user.slice`|

|**Command**|**Description**|**Example**|
|---|---|---|
|`systemctl`|Control systemd system and services|`systemctl status`|
|`systemctl list-units`|List all active units|`systemctl list-units`|
|`systemctl list-units --type=service`|List all services|`systemctl list-units --type=service`|
|`systemctl list-unit-files`|List all unit files|`systemctl list-unit-files`|
|`systemctl cat`|Display unit file contents|`systemctl cat sshd.service`|
|`sudo systemctl status`|Show service status|`sudo systemctl status nginx`|
|`sudo systemctl start`|Start service|`sudo systemctl start apache2`|
|`sudo systemctl stop`|Stop service|`sudo systemctl stop mysql`|
|`sudo systemctl restart`|Restart service|`sudo systemctl restart network`|
|`sudo systemctl reload`|Reload service configuration|`sudo systemctl reload nginx`|
|`sudo systemctl enable`|Enable service at boot|`sudo systemctl enable sshd`|
|`sudo systemctl disable`|Disable service at boot|`sudo systemctl disable bluetooth`|
|`systemctl is-enabled`|Check if service is enabled|`systemctl is-enabled firewalld`|
|`systemctl is-active`|Check if service is active|`systemctl is-active sshd`|
|`sudo systemctl --now enable`|Enable and start service immediately|`sudo systemctl enable --now nginx`|
|`sudo systemctl --now disable`|Disable and stop service immediately|`sudo systemctl disable --now apache2`|
|`sudo systemctl mask`|Mask service (prevent from being started)|`sudo systemctl mask bluetooth`|
|`sudo systemctl unmask`|Unmask service|`sudo systemctl unmask bluetooth`|
|`systemctl list-dependencies`|Show unit dependencies|`systemctl list-dependencies sshd`|
|`systemctl show`|Show unit properties|`systemctl show nginx`|
|`sudo systemctl daemon-reload`|Reload systemd manager configuration|`sudo systemctl daemon-reload`|
|`journalctl -u`|View logs for specific service|`journalctl -u sshd`|
|`journalctl -f`|Follow journal in real-time|`journalctl -f`|
|`journalctl -xe`|Show detailed recent logs|`journalctl -xe`|

**Service Unit Status:**

|**UNIT**|**LOAD**|**ACTIVE**|**SUB**|**DESCRIPTION**|
|---|---|---|---|---|
|accounts-daemon.service|loaded|active|running|Accounts Service|
|alsa-restore.service|loaded|inactive|dead|Save/Restore Sound Card|
|alsa-state.service|loaded|active|running|Manage Sound Card State|
|apparmor.service|not-found|inactive|dead|apparmor.service|
|atd.service|loaded|active|running|Job spooling tools|
|auditd.service|loaded|active|running|Security Auditing Service|
|auth-rpcgss-module.service|loaded|inactive|dead|Kernel Module support|

```bash
# Check service status
systemctl status nginx

# Start and enable service
sudo systemctl enable --now docker

# Restart service after config change
sudo systemctl restart sshd

# Reload without restarting
sudo systemctl reload nginx

# View service logs
journalctl -u nginx -f

# List all failed services
systemctl --failed

# Check if service will start on boot
systemctl is-enabled httpd

# Mask service to prevent starting
sudo systemctl mask cups

# View service dependencies
systemctl list-dependencies multi-user.target
```

#### Components in Service file

**Location:** `/lib/systemd/system/` or `/etc/systemd/system/`

|**Components**|**Description**|**Common Directives**|
|---|---|---|
|`[Unit]`|General information about the unit|Description, After, Before, Requires, Wants|
|`[Service]`|Service-specific configuration|Type, ExecStart, ExecStop, Restart, User|
|`[Install]`|Installation information for enabling|WantedBy, RequiredBy, Alias|

**Example Service File:**

```ini
# /etc/systemd/system/webapp.service

[Unit]
Description=My Web Application
After=network.target auditd.service
Requires=network.target
Documentation=https://example.com/docs

[Service]
Type=simple
User=webapp
Group=webapp
WorkingDirectory=/opt/webapp
ExecStartPre=/bin/echo "Starting Web App"
ExecStart=/usr/bin/python3 /opt/webapp/app.py
ExecReload=/bin/kill -HUP $MAINPID
ExecStop=/bin/kill -TERM $MAINPID
Restart=on-failure
RestartSec=10
StandardOutput=journal
StandardError=journal
Environment="PORT=8080"
EnvironmentFile=/etc/webapp/config

[Install]
WantedBy=multi-user.target
```

### Create Systemd Services

**Step 1: Create the Application Script**

```bash
sudo vim /usr/local/bin/myapp.sh
```

```bash
#!/bin/bash
# myapp.sh - Sample application

echo "My app started at $(date)" | systemd-cat -t MyApp -p info

# Simulate application work
sleep 5

# Simulate crash
echo "My app crashed at $(date)" | systemd-cat -t MyApp -p err
exit 1
```

- `systemd-cat` sends messages directly to the system log (journald)
- The `-t` option attaches a tag (MyApp) to every log message
- The `-p` option sets the logging priority (using `info` for normal status and `err` for error)
- The `sleep 5` command simulates a delay of five seconds before logging a crash

**Step 2: Make Script Executable**

```bash
sudo chmod +x /usr/local/bin/myapp.sh
```

**Step 3: Read Service Documentation**

```bash
man systemd.service
man systemd.unit
```

Pre-existing services are typically stored in:

```bash
ls /lib/systemd/system/
ls /etc/systemd/system/
```

**Step 4: Create Service Unit File**

```bash
sudo vim /etc/systemd/system/myapp.service
```

```ini
[Unit]
Description=My Custom Application Service
After=network.target auditd.service
Documentation=man:myapp(1)

[Service]
Type=simple
ExecStartPre=/bin/echo "Systemd is preparing to start myapp"
ExecStart=/usr/local/bin/myapp.sh
KillMode=process
Restart=always
RestartSec=1
StandardOutput=journal
StandardError=journal
SyslogIdentifier=myapp

[Install]
WantedBy=multi-user.target
```

**Service Directives Explained:**

- `Type=simple` - The process started is the main process
- `KillMode=process` - Only kill the main process, not child processes
- `Restart=always` - Always restart regardless of exit status
- `RestartSec=1` - Wait 1 second before restarting
- `ExecStartPre` - Command to run before starting main process
- `ExecStart` - Main command to start the service
- `StandardOutput=journal` - Send stdout to journald
- `WantedBy=multi-user.target` - Enable for multi-user mode

**Service Types:**

- `simple` - Main process of the service (default)
- `forking` - Service forks and parent exits
- `oneshot` - Process exits before starting follow-up units
- `notify` - Service sends notification when ready
- `idle` - Delayed until all jobs are dispatched

**Step 5: Reload Systemd and Manage Service**

```bash
# Reload systemd to recognize new service
sudo systemctl daemon-reload

# Start the service
sudo systemctl start myapp.service

# Check service status
systemctl status myapp.service

# Enable service to start at boot
sudo systemctl enable myapp

# View service logs
journalctl -u myapp -f

# Stop service
sudo systemctl stop myapp.service

# Restart service
sudo systemctl restart myapp.service
```

**Advanced Service Example with Timer:**

```bash
# /etc/systemd/system/backup.service
[Unit]
Description=Backup Service
Wants=backup.timer

[Service]
Type=oneshot
ExecStart=/usr/local/bin/backup.sh
User=backup
Group=backup

[Install]
WantedBy=multi-user.target
```

```bash
# /etc/systemd/system/backup.timer
[Unit]
Description=Daily Backup Timer
Requires=backup.service

[Timer]
OnCalendar=daily
OnCalendar=*-*-* 02:00:00
Persistent=true

[Install]
WantedBy=timers.target
```

```bash
# Enable and start timer
sudo systemctl daemon-reload
sudo systemctl enable backup.timer
sudo systemctl start backup.timer

# Check timer status
systemctl list-timers
```

### Diagnose and Manage Processes

|**Command**|**Description**|**Example**|
|---|---|---|
|`ps`|Display current processes|`ps aux`|
|`ps -ef`|Display all processes|`ps -ef`|
|`ps aux`|BSD-style process listing|`ps aux \| grep nginx`|
|`top`|Real-time process monitoring|`top`|
|`htop`|Interactive process viewer|`htop`|
|`pgrep`|Find process ID by name|`pgrep nginx`|
|`pkill`|Kill process by name|`pkill firefox`|
|`kill`|Send signal to process|`kill -15 1234`|
|`kill -9`|Force kill process|`kill -9 1234`|
|`killall`|Kill all processes by name|`killall chrome`|
|`nice`|Run command with modified priority|`nice -n 10 command`|
|`renice`|Change priority of running process|`renice -n 5 -p 1234`|
|`jobs`|List background jobs|`jobs`|
|`bg`|Resume job in background|`bg %1`|
|`fg`|Bring job to foreground|`fg %1`|
|`&`|Run command in background|`long_command &`|
|`nohup`|Run command immune to hangups|`nohup script.sh &`|
|`screen`|Terminal multiplexer|`screen -S session_name`|
|`tmux`|Modern terminal multiplexer|`tmux new -s session_name`|

**Process Signals:**

- `SIGTERM (15)` - Graceful termination (default)
- `SIGKILL (9)` - Force kill (cannot be caught)
- `SIGHUP (1)` - Hangup (reload config)
- `SIGSTOP (19)` - Stop/pause process
- `SIGCONT (18)` - Continue stopped process


```bash
# Find and kill process
ps aux | grep nginx
kill 1234

# Kill all processes by name
killall firefox

# Find process by name and kill
pkill -f "python app.py"

# Monitor specific process
top -p 1234

# Run process in background
./long_script.sh &

# Run process with low priority
nice -n 19 backup_script.sh

# Change priority of running process
renice -n 10 -p 1234

# Keep process running after logout
nohup ./long_running_task.sh > output.log 2>&1 &
```

```bash
sleep 300 &
[1] 13868
jobs
[1]+  Running                 sleep 300 &
```

The `lsof` command lets you inspect which files or directories are in use by a process. First, obtain the PID of your bash shell:

```bash
pgrep -a bash
13536 bash
```

Then, list all open files for that process:

```bash
lsof -p 13536
COMMAND   PID USER     FD   TYPE DEVICE SIZE/OFF NODE NAME
bash    13536 jeremy    cwd    DIR  252,0     4096 786475 /home/jeremy
bash    13536 jeremy    rtd    DIR  252,0     4096     2 /
bash    13536 jeremy    txt    REG  252,0 1446024 262614 /usr/bin/bash
...
```

### Locate and Analyze System Log files

```bash
ls /var/log/
alternatives.log     landscape           nginx
apt                  lastlog             private
auth.log             dmesg.0             syslog
auth.log.1           dmesg.1.gz         syslog.1
auth.log.2.gz        dmesg.2.gz         syslog.2.gz
bootstrap.log        dpkg.log           ubuntu-advantage.log
btmp                 faillog            ubuntu-advantage.log.1
btmp.1               installer          unattended-upgrades
cloud-init.log       journal             wtmp
cloud-init-output.log kern.log           kern.log.1
dist-upgrade         kern.log.2.gz      dmesg
```

>[!Note]
>Most log files under `/var/log` are restricted to the root user. If you need to view these logs as a non-root user, consider switching to root using commands like `su --login` or `sudo --login`.

| **Command**                                    | **Description** | **Example** |
| ---------------------------------------------- | --------------- | ----------- |
| `grep -r 'ssh' /var/log`                       |                 |             |
| `less /var/log/auth.log`                       |                 |             |
| `taiil -F /vat/log/auth.log`                   |                 |             |
| `journalctl`                                   |                 |             |
| `journalctl -e`                                |                 |             |
| `journalctl -f`                                |                 |             |
| `journalctl -p`                                |                 |             |
| `journalctl -S <since_value> -U <until_value>` |                 |             |
| `journalctl -S 'string value`                  |                 |             |
| `journalctl -b`                                |                 |             |
| `last`                                         |                 |             |
| `lastlog`                                      |                 |             |

### Schedule Tasks to Run at a Set Date and Time

1. Cron Utility
2. Anacron
3. At Utility

#### Cron

```bash
cat /etc/crontab
SHELL=/bin/sh
# You can also override PATH; by default, newer versions inherit it.
# Example of job definition:
# ----------- minute (0 - 59)
# ----------- hour (0 - 23)
# ----------- day of month (1 - 31)
# ----------- month (1 - 12) OR jan,feb,mar,apr ...
# ----------- day of week (0 - 6) (Sunday=0 or 7) OR
#               sun,mon,tue,wed,thu,fri,sat
# * * * * * user-name command to be executed
35 6 * * * root /bin/some_command --some_options
```

```bash
crontab -e

35 6 * * * /usr/bin/touch test_passed            # Every day at 6:35 AM
0 3 * * 0 /usr/bin/touch test_passed              # Every Sunday at 3:00 AM
0 3 * * 7 /usr/bin/touch test_passed              # Alternative notation for Sunday
0 3 15 * * /usr/bin/touch test_passed             # On the 15th of every month at 3:00 AM
```

| **Command**  | **Description** | **Example** |
| ------------ | --------------- | ----------- |
| `crontab -l` |                 |             |
| `crontab -r` |                 |             |
| `crontab -e` |                 |             |

#### Anacron

```bash
sudo vim /etc/anacrontab
# See anacron(8) and anacrontab(5) for details.
# These entries replace cron's job entries.
1       5       cron.daily      run-parts --report /etc/cron.daily
7       10      cron.weekly     run-parts --report /etc/cron.weekly
@monthly 15      cron.monthly    run-parts --report /etc/cron.monthly
```
Syntax check:
```bash
anacron -T
```

#### At Utility

```bash
at '15:00'
warning: commands will be executed using /bin/sh
at> /usr/bin/touch file_created_by_at
at> <CTRL-D>
```

```bash
at 'August 20 2024'
at '2:30 August 20 2024'
at 'now + 30 minutes'
at 'now + 3 hours'
at 'now + 3 days'
at 'now + 3 weeks'
at 'now + 3 months'
atq
```

### Manage Software with the Package Manager

| **Command**                  | **Description**                                   | **Example**                     |
| ---------------------------- | ------------------------------------------------- | ------------------------------- |
| `apt update`                 | Refresh local package database from repositories  | `sudo apt update`               |
| `apt upgrade`                | Upgrade all installed packages to latest versions | `sudo apt upgrade`              |
| `apt list --upgradable`      | List packages that can be upgraded                | `apt list --upgradable`         |
| `apt full-upgrade`           | Upgrade with automatic dependency handling        | `sudo apt full-upgrade`         |
| `apt install`                | Install one or more packages                      | `sudo apt install nginx`        |
| `apt install -y`             | Install without confirmation prompt               | `sudo apt install -y nginx`     |
| `apt install <pkg1> <pkg2>`  | Install multiple packages                         | `sudo apt install vim git curl` |
| `dpkg --listfiles`           | List all files installed by a package             | `dpkg --listfiles nginx`        |
| `dpkg -L`                    | Short form of --listfiles                         | `dpkg -L nginx`                 |
| `dpkg --search`              | Find which package owns a file                    | `dpkg --search /usr/sbin/nginx` |
| `dpkg -S`                    | Short form of --search                            | `dpkg -S /usr/sbin/nginx`       |
| `apt show`                   | Display detailed package information              | `apt show nginx`                |
| `apt list --installed`       | List all installed packages                       | `apt list --installed`          |
| `apt list --installed <pkg>` | Check if specific package is installed            | `apt list --installed nginx`    |
| `dpkg -l`                    | List installed packages (alternative)             | `dpkg -l \| grep nginx`         |
| `dpkg -s`                    | Show package status                               | `dpkg -s nginx`                 |
| `apt search`                 | Search packages by name and description           | `apt search nginx`              |
| `apt search --names-only`    | Search package names only                         | `apt search --names-only nginx` |
| `apt-cache search`           | Alternative search command                        | `apt-cache search nginx`        |
| `apt-cache policy`           | Show package versions and sources                 | `apt-cache policy nginx`        |
| `apt remove`                 | Remove package (keeps config files)               | `sudo apt remove nginx`         |
| `apt purge`                  | Remove package and config files                   | `sudo apt purge nginx`          |
| `apt autoremove`             | Remove package and unused dependencies            | `sudo apt autoremove nginx`     |
| `apt autoremove`             | Remove orphaned dependencies                      | `sudo apt autoremove`           |
| `apt clean`                  | Remove all cached package files                   | `sudo apt clean`                |
| `apt autoclean`              | Remove old cached package files                   | `sudo apt autoclean`            |
| `du -sh /var/cache/apt`      | Check cache size                                  | `du -sh /var/cache/apt`         |
| `apt --fix-broken install`   | Fix broken dependencies                           | `sudo apt --fix-broken install` |
| `apt -f install`             | Short form of --fix-broken                        | `sudo apt -f install`           |
| `dpkg --configure -a`        | Configure unpacked packages                       | `sudo dpkg --configure -a`      |
| `apt-mark hold`              | Prevent package from being upgraded               | `sudo apt-mark hold nginx`      |
| `apt-mark unhold`            | Allow package to be upgraded again                | `sudo apt-mark unhold nginx`    |
| `apt-mark showhold`          | List held packages                                | `apt-mark showhold`             |

|Method|Removes Package|Removes Config|Removes Dependencies|
|---|---|---|---|
|`apt remove`|✓|✗|✗|
|`apt purge`|✓|✓|✗|
|`apt autoremove`|✓|✗|✓|
