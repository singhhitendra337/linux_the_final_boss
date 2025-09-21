# Day 19: Advanced Linux Storage & Filesystems (ext4, xfs, btrfs, quotas, tuning)

## Notes
- **Why Advanced Storage Matters:**
  - Modern systems require robust, flexible, and high-performance storage solutions.
  - Understanding filesystems, quotas, and tuning is essential for reliability and scalability.

- **Common Linux Filesystems:**
  - `ext4`: Default on most distros; journaling, large file support, stable.
  - `xfs`: High-performance, scalable, good for large files and parallel I/O.
  - `btrfs`: Advanced features (snapshots, compression, checksumming, subvolumes).
  - `vfat`, `ntfs`: For interoperability with Windows/external drives.

- **Creating & Managing Filesystems:**
  - `mkfs.ext4 /dev/sdX1` — Create ext4 filesystem
  - `mkfs.xfs /dev/sdX1` — Create xfs filesystem
  - `mkfs.btrfs /dev/sdX1` — Create btrfs filesystem
  - `mount /dev/sdX1 /mnt` — Mount filesystem
  - `umount /mnt` — Unmount
  - `/etc/fstab` — Persistent mounts

- **Filesystem Maintenance:**
  - `fsck /dev/sdX1` — Check and repair
  - `tune2fs` — Tune ext2/3/4 parameters (e.g., reserved blocks, labels)
  - `xfs_repair`, `xfs_growfs` — XFS repair and online resize
  - `btrfs scrub`, `btrfs balance`, `btrfs snapshot`

- **Quotas:**
  - Enforce disk usage limits per user/group
  - Enable in `/etc/fstab` with `usrquota,grpquota`
  - `quotacheck`, `edquota`, `quota`, `repquota` for management

- **Filesystem Tuning:**
  - Adjust mount options for performance: `noatime`, `data=writeback`, `barrier=0`
  - Use `iostat`, `iotop`, `dstat` for performance monitoring
  - Align partitions for SSDs, use TRIM (`fstrim`)

- **Best Practices:**
  - Choose the right filesystem for your workload
  - Regularly check and repair filesystems
  - Monitor disk usage and set quotas for multi-user systems
  - Document all changes and test before production

- **Diagram:**
  - ![Filesystem Diagram](https://upload.wikimedia.org/wikipedia/commons/2/2e/Gparted-partitions.png)

## In-Depth Explanations & Scenarios
- **ext4 vs. xfs vs. btrfs:**
  - ext4: General purpose, stable, widely supported
  - xfs: Best for large files, high concurrency
  - btrfs: Advanced features, but less mature for some workloads
- **Real-World Example:**
  - Migrating from ext4 to xfs for a database server to improve write performance
  - Using btrfs snapshots for fast backups and rollbacks
- **Troubleshooting:**
  - Use `dmesg`, `journalctl`, and filesystem-specific tools for errors

## Sample Exercises
1. Create and mount an ext4, xfs, and btrfs filesystem on loopback devices.
2. Set up user quotas on an ext4 filesystem and test enforcement.
3. Take a btrfs snapshot and restore from it.
4. Tune mount options for performance and measure the impact.
5. Repair a corrupted filesystem using the appropriate tool.

## Solutions
1. Use `dd` to create image files, `losetup` to attach, then `mkfs.*` and `mount`.
2. Enable quotas in `/etc/fstab`, run `quotacheck`, edit with `edquota`, test with `quota`.
3. `btrfs subvolume snapshot /mnt/data /mnt/snap1`; restore by mounting the snapshot.
4. Edit `/etc/fstab` to add options like `noatime`, remount, and use `iostat`/`dstat` to compare.
5. Use `fsck` for ext4, `xfs_repair` for xfs, `btrfs check` for btrfs.

## Sample Interview Questions
1. What are the main differences between ext4, xfs, and btrfs?
2. How do you create and mount a new filesystem?
3. How do you enable and manage disk quotas?
4. What are filesystem snapshots and why are they useful?
5. How do you check and repair filesystem errors?
6. What mount options can improve performance?
7. How do you monitor disk I/O performance?
8. How do you migrate data between filesystems?
9. What are the risks of disabling journaling or barriers?
10. How do you automate filesystem checks and maintenance?

## Interview Question Answers
1. ext4 is stable and general-purpose; xfs is high-performance for large files; btrfs offers advanced features like snapshots.
2. Use `mkfs.*` to create, `mount` to attach, and edit `/etc/fstab` for persistence.
3. Enable quotas in `/etc/fstab`, use `quotacheck`, `edquota`, and `quota` commands.
4. Snapshots capture the state of a filesystem at a point in time; useful for backups and rollbacks.
5. Use `fsck`, `xfs_repair`, or `btrfs check` depending on the filesystem.
6. Options like `noatime`, `data=writeback`, and `barrier=0` can improve performance but may risk data integrity.
7. Use `iostat`, `iotop`, `dstat`, and `sar` to monitor disk I/O.
8. Use `rsync`, `tar`, or `cp -a` to migrate data; test after migration.
9. Disabling journaling or barriers can improve speed but risks data loss on crashes.
10. Use cron jobs or systemd timers to schedule checks and maintenance scripts.
