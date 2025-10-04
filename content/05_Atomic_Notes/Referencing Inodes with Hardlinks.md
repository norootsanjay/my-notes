- Points to an existing inode of the file.
- It can link to files on the same file system.
- Technically, the first filename of the file is already a hard link.
![[Screenshot 2025-08-23 at 10.15.57.png]]

```bash
ln [target] [hardlink]
```

- [f] ==Hard links cannot be created for directories.==