# Day 12: Compression, Archiving, and Backups

## Notes
- **Why Compression & Backups Matter:**
  - Save disk space, speed up transfers, and protect data from loss.
  - Essential for system admins, DevOps, and SREs.

- **Compression Tools:**
  - `gzip file` — Compresses to file.gz; `gunzip file.gz` decompresses
  - `bzip2 file` — Compresses to file.bz2; `bunzip2 file.bz2` decompresses
  - `xz file` — Compresses to file.xz; `unxz file.xz` decompresses
  - `zip file.zip file1 file2` — Create zip archive; `unzip file.zip` extracts

- **Archiving Tools:**
  - `tar -cvf archive.tar files/` — Create tar archive
  - `tar -xvf archive.tar` — Extract tar archive
  - `tar -czvf archive.tar.gz files/` — Create compressed tarball
  - `tar -xzvf archive.tar.gz` — Extract compressed tarball

- **Backup Tools:**
  - `rsync -av source/ dest/` — Sync files/directories
  - `cp -a source/ dest/` — Archive copy
  - `dd if=/dev/sda of=/backup.img` — Disk image (use with caution)
  - `cron` — Schedule regular backups

- **Best Practices:**
  - Automate backups with scripts/cron
  - Store backups offsite or in the cloud
  - Test restores regularly
  - Use checksums (`md5sum`, `sha256sum`) to verify integrity

- **Diagram:**
  - ![Backup Diagram](https://upload.wikimedia.org/wikipedia/commons/7/7e/Backup_cycle.png)

## In-Depth Explanations & Scenarios
- **Compression Algorithms:**
  - `gzip`: Fast, widely used, moderate compression. Best for logs and text files.
  - `bzip2`: Slower, higher compression. Good for source code, text.
  - `xz`: Slowest, best compression. Used for large archives, package distribution.
- **Archiving vs. Compression:**
  - Archiving (`tar`) combines files; compression (`gzip`, `bzip2`, `xz`) reduces size. Use together for `.tar.gz`, `.tar.bz2`, `.tar.xz`.
- **Backup Strategies:**
  - Full backup: All data, slowest, safest.
  - Incremental: Only changed files since last backup, faster, needs all increments to restore.
  - Differential: Changed files since last full backup, faster restore than incremental.
- **Real-World Example:**
  - Automate daily home directory backup with `tar` and `cron`, store on external drive/cloud.
- **Restoring Backups:**
  - Always test restores! Use `tar -tzf` to list archive contents before extracting.
- **Cloud & Remote Backups:**
  - Use `rsync` with SSH for remote backups. Consider tools like `rclone` for cloud storage.
- **Best Practices (Expanded):**
  - Encrypt sensitive backups (`gpg`, `openssl`).
  - Keep multiple backup copies (3-2-1 rule: 3 copies, 2 media, 1 offsite).
  - Document backup/restore procedures for disaster recovery.

## Sample Exercises
1. Compress and decompress a file using gzip and bzip2.
2. Create a tar archive of a directory and extract it.
3. Use rsync to backup your home directory to another location.
4. Schedule a daily backup using cron.
5. Verify the integrity of a backup file using checksums.

## Solutions
1. `gzip file.txt`; `gunzip file.txt.gz`; `bzip2 file.txt`; `bunzip2 file.txt.bz2`
2. `tar -czvf backup.tar.gz mydir/`; `tar -xzvf backup.tar.gz`
3. `rsync -av ~/ /backup/home_backup/`
4. `crontab -e` and add: `0 2 * * * rsync -av ~/ /backup/home_backup/`
5. `md5sum backup.tar.gz`; `sha256sum backup.tar.gz`

## Sample Interview Questions
1. What is the difference between compression and archiving?
2. How do you create and extract a compressed tarball?
3. What are the advantages of using rsync for backups?
4. How do you automate backups in Linux?
5. How do you verify the integrity of a backup?
6. What is the risk of using `dd` for disk backups?
7. How do you exclude files from a tar or rsync backup?
8. What is the difference between gzip, bzip2, and xz?
9. How do you restore a single file from a tar archive?
10. Why is it important to test your backups?

## Interview Question Answers
1. Compression reduces file size; archiving combines multiple files into one. Tools like `tar` can do both.
2. Use `tar -czvf archive.tar.gz files/` to create, `tar -xzvf archive.tar.gz` to extract.
3. `rsync` is fast, incremental, and can resume transfers; ideal for backups.
4. Use `cron` to schedule scripts or commands for automated backups.
5. Use `md5sum` or `sha256sum` to generate and verify checksums.
6. `dd` can overwrite disks if used incorrectly; always double-check source and destination.
7. Use `--exclude` with `tar` or `rsync` to skip files/directories.
8. `gzip` is fast, `bzip2` compresses better but slower, `xz` offers best compression but slowest.
9. Use `tar -xvf archive.tar file.txt` to extract a single file.
10. Untested backups may be corrupt or incomplete; always verify restores.
