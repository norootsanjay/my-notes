
During the creation of the file system, space is reserved for inodes. This space cannot be used for anything else.
```bash
df -ih
```

<u>But, if your application uses many small files:</u>
- This can be reached.
- Processes or applications crash or cannot be started.
- OS crash.****************

<u>To solve this:</u>
- Remove files that are no longer used.
- Compress multiple files into an archive.
- Recreate the filesystem with higher inode limits.
- Store data on additional drives and mount them.