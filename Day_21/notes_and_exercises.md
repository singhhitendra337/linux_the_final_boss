# Day 21: Volume Management (LVM, RAID, Snapshots, Resizing)

## Why Volume Management Matters
- Enables flexible, scalable, and reliable storage management.
- Essential for handling growing data, backups, and disaster recovery.

## Key Concepts
- LVM (Logical Volume Manager): PV, VG, LV, snapshots, resizing
- RAID (levels 0, 1, 5, 6, 10): redundancy, performance, fault tolerance
- Snapshots: point-in-time copies for backup and rollback
- Resizing: growing and shrinking volumes safely

## Common Commands
- `lsblk`, `fdisk`, `parted`, `pvcreate`, `vgcreate`, `lvcreate`, `lvextend`, `lvreduce`, `lvremove`, `vgextend`, `vgreduce`, `mdadm`, `mkfs`, `mount`, `umount`, `resize2fs`, `xfs_growfs`

## Practical Examples
### 1. Create and Use LVM
```bash
# Create physical volume
sudo pvcreate /dev/sdb
# Create volume group
sudo vgcreate vgdata /dev/sdb
# Create logical volume
sudo lvcreate -L 5G -n lvdata vgdata
# Format and mount
sudo mkfs.ext4 /dev/vgdata/lvdata
sudo mount /dev/vgdata/lvdata /mnt/data
```

### 2. Create RAID 1 with mdadm
```bash
sudo mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sdc /dev/sdd
sudo mkfs.ext4 /dev/md0
sudo mount /dev/md0 /mnt/raid1
```

### 3. Take and Restore LVM Snapshot
```bash
sudo lvcreate --size 500M --snapshot --name snap1 /dev/vgdata/lvdata
# Restore: mount or merge snapshot as needed
```

### 4. Resize Logical Volume
```bash
sudo lvextend -L +2G /dev/vgdata/lvdata
sudo resize2fs /dev/vgdata/lvdata
```

## Best Practices
- Always backup before resizing or modifying volumes.
- Monitor disk health and RAID status regularly.
- Use RAID for redundancy, not as a substitute for backups.
- Document all storage changes.

## Exercises
1. Create an LVM setup with a new disk, format, and mount it.
2. Set up a RAID 5 array and test disk failure recovery.
3. Take a snapshot of a logical volume and restore from it.
4. Resize a logical volume and verify data integrity.

## Solutions
### 1. LVM setup: Use `pvcreate`, `vgcreate`, `lvcreate`, `mkfs`, `mount`.
### 2. RAID 5: Use `mdadm` to create, simulate failure with `mdadm --fail`, recover with `mdadm --add`.
### 3. Snapshot: Use `lvcreate --snapshot`, mount, and restore as needed.
### 4. Resize: Use `lvextend`/`lvreduce` and `resize2fs`/`xfs_growfs`.

## Interview-Style Questions & Answers
1. **What is LVM and why use it?**
   - LVM provides flexible disk management, allowing resizing and snapshots.
2. **Difference between RAID 0, 1, 5, 6, 10?**
   - RAID 0: striping, no redundancy; RAID 1: mirroring; RAID 5/6: parity; RAID 10: mirror + stripe.
3. **How do you resize a logical volume?**
   - Use `lvextend`/`lvreduce` and resize the filesystem.
4. **How do you check RAID health?**
   - Use `cat /proc/mdstat` and `mdadm --detail`.
5. **What are LVM snapshots used for?**
   - Point-in-time backups and rollback.

---

Congratulations on completing the Mega Project and the entire Linux curriculum! This project brings together all your skills and prepares you for real-world DevOps and SRE roles.