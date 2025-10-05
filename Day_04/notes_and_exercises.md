# Day 04: Linux Boot Process & Service Management

## Learning Objectives
By the end of Day 4, you will:
- Understand the complete Linux boot process
- Manage services using systemctl
- View and analyze boot logs
- Configure services to start at boot
- Troubleshoot boot and service issues

**Estimated Time:** 1 hour

## Notes
- **What is the Linux Boot Process?**
  - The boot process is the sequence of steps the system takes to start up and become ready for use.
  - Understanding the boot process is crucial for troubleshooting startup issues and managing services.
  <img width="800" height="800" alt="0213-linux-boot-process-explained" src="https://github.com/user-attachments/assets/75e328e4-52f5-4e4b-a0d2-a680d87ce03c" />
1. Power & Reset Vector  
   System receives stable power; CPU jumps to the architecture-defined reset vector and begins executing firmware code.
2. Firmware (BIOS or UEFI)  
   Performs POST, minimal hardware initialization, enumerates bootable devices, chooses a boot entry (boot order / NVRAM).
3. Disk Structure & Loader Entry  
   - Legacy BIOS path: Read MBR (LBA 0) → tiny bootstrap → load GRUB core.  
   - UEFI path: Read GPT, mount/scan ESP (FAT32), load EFI executable (e.g. `grubx64.efi` or shim chain).
4. GRUB2 Bootloader  
   Loads modules, parses `grub.cfg`, presents menu (if enabled), selects kernel + initramfs, constructs kernel command line.
5. Kernel Load & Decompression  
   Kernel image is copied into RAM, decompressed, establishes CPU modes (protected/long), sets up early memory management & interrupts.
6. Initramfs (Early User Space)  
   Temporary cpio archive with minimal user space tools: loads storage/crypto drivers, assembles RAID/LVM, unlocks LUKS, locates real root.
7. Switch to Real Root Filesystem  
   Root filesystem is mounted; `switch_root` (or older `pivot_root`) transitions from initramfs to the actual root; initramfs is freed.
8. Init Process (PID 1: systemd)  
   `systemd` starts as PID 1, parses dependency graph, mounts additional filesystems, activates sockets, timers, and services in parallel.
9. Services & Targets Reached  
   Network stack, logging, display manager (if graphical), and other units come online; default target (`multi-user.target` or `graphical.target`) is achieved.
10. User Interaction  
    TTY login prompts (`agetty`), SSH access, and/or graphical greeter become available—the system is ready for workload and users.

---

## Detailed Phase Explanations (Optional)

### 1. Power & Reset Vector
- Hardware initializes base electrical conditions.
- CPU fetches first instruction at fixed physical address.
- Corruption here = pre-boot failure (no firmware banner, no beeps).

### 2. Firmware (BIOS vs UEFI)
| Aspect | BIOS (Legacy) | UEFI (Modern) |
|--------|---------------|---------------|
| Execution Mode | 16‑bit real mode | 32/64‑bit native |
| Disk Interface | INT 13h | Drivers + EFI protocols |
| Partition Scheme | MBR only | GPT (with protective MBR) |
| Secure Boot | Not available | Supported |
| Extensibility | Minimal | Shell, diagnostics, network stack |
| Boot Entries | Sequential scan | NVRAM-managed entries |
Tasks: POST, memory sizing, basic device presence, choose boot entry.

### 3. Disk Structure & Loader Entry
- **MBR Layout (512 bytes)**: 446 boot code + 64 partition table + 0x55AA signature.
- **GPT Advantages**: Redundancy (primary + backup headers), CRC32 integrity, large partition count.
- **ESP**: Dedicated FAT32 partition (usually 100–500 MB) containing EFI executables (`/EFI/<vendor>/*.efi`).

### 4. GRUB2 Bootloader
Responsibilities:
- Build boot menu (multi-OS or multi-kernel).
- Load kernel (`/boot/vmlinuz-*`) + initramfs (`/boot/initramfs-*`).
- Pass kernel parameters (command line).
Key Files:
- `/etc/default/grub` → human-editable defaults.
- Generated config: `/boot/grub2/grub.cfg` (BIOS / many RPM distros) or `/boot/efi/EFI/<distro>/grub.cfg` (some UEFI layouts).
Common Parameters (view with `cat /proc/cmdline`):
| Param | Purpose |
|-------|---------|
| `root=UUID=` | Select real root filesystem |
| `ro` / `rw` | Initial root mount mode |
| `quiet` / `splash` | Reduce console noise / show splash image |
| `systemd.unit=<target>` | Boot directly into a specific target |
| `rd.luks.uuid=` | Identify encrypted device for early unlock |
| `rd.lvm.lv=` | Specify LVM LV path |
| `panic=SECONDS` | Auto-reboot after kernel panic |
| `console=ttyS0,115200` | Serial console (servers/headless) |

### 5. Kernel Load & Decompression
Sequence:
1. Decompress kernel to RAM.
2. Initialize memory management (page tables, zones).
3. Bring up interrupts, timers, CPU cores (SMP).
4. Initialize essential buses (PCI enumeration).
5. Register base drivers (others deferred).
6. Jump into initramfs `/init`.

### 6. Initramfs (Early User Space)
Contains:
- BusyBox or minimal utilities.
- Storage/crypto modules not built into kernel.
- Scripts for: RAID assembly (`mdadm`), LVM activation, LUKS unlock (`cryptsetup`), filesystem detection (UUID/LABEL).
Inspection:
```bash
lsinitrd /boot/initramfs-$(uname -r).img | less    # dracut-based
```
Rebuild:
- RHEL/Fedora: `dracut -f`
- Debian/Ubuntu: `update-initramfs -u`
Failure Symptoms:
- Dropped to `(initramfs)` shell.
- “Cannot find root device” / “Unable to mount root fs”.

### 7. Switch Root
- `switch_root` replaces the temporary initramfs root with the real root, freeing RAM.
- Mounts from `/etc/fstab` proceed via systemd mount units.
- If encrypted: unlock → map → mount → switch.

### 8. PID 1 (systemd)
Core Roles:
- Dependency resolution graph (units with `Requires=`, `Wants=`, `Before=`/`After=`).
- Parallel startup (faster than sequential SysVinit).
- Activation methods (socket, bus, path, timer).
- Supervision (restarts, failure isolation).
- Integrated logging via `journald`.
Primary Unit Types:
`service`, `socket`, `mount`, `automount`, `timer`, `path`, `target`, `device`, `swap`, `slice`, `scope`.

### 9. Services & Targets
Runlevel Mapping (for legacy reference):
| Legacy Runlevel | systemd Target | Meaning |
|-----------------|----------------|---------|
| 0 | `poweroff.target` | Halt system |
| 1 | `rescue.target` | Single-user (maintenance) |
| 3 | `multi-user.target` | Non-graphical multi-user |
| 5 | `graphical.target` | Multi-user + GUI |
| 6 | `reboot.target` | Reboot |
| S/Emergency | `emergency.target` | Minimal root shell |
Default target symlink: `/etc/systemd/system/default.target`.

### 10. User Interaction
- TTY login: `agetty` spawns `/bin/login`.
- GUI: Display manager (GDM/SDDM/LightDM) started as a service.
- Remote: `sshd.service` often comes online before graphics (headless administration).

---

- **Service Management with systemd:**
  ```bash
  # Service control
  systemctl status <service>     # Show service status
  systemctl start <service>      # Start a service
  systemctl stop <service>       # Stop a service
  systemctl restart <service>    # Restart a service
  systemctl reload <service>     # Reload config without restart
  
  # Boot management
  systemctl enable <service>     # Enable service at boot
  systemctl disable <service>    # Disable service at boot
  systemctl is-enabled <service> # Check if enabled
  
  # Information
  systemctl list-units --type=service        # List all services
  systemctl list-units --state=failed        # List failed services
  systemctl list-unit-files --type=service   # List all service files
  
  # Logs
  journalctl -u <service>        # View logs for service
  journalctl -u <service> -f     # Follow logs in real-time
  journalctl -b                  # Boot logs
  ```

- **Checking Boot Logs:**
  ```bash
  # Kernel messages
  dmesg                    # Kernel ring buffer
  dmesg | grep -i error    # Filter for errors
  dmesg -T                 # Human-readable timestamps
  
  # System logs
  journalctl -b            # Current boot logs
  journalctl -b -1         # Previous boot logs
  journalctl --list-boots  # List all boots
  journalctl -p err        # Error priority and above
  journalctl --since "1 hour ago"  # Recent logs
  ```

- **Best Practices:**
  - Only enable necessary services at boot
  - Regularly check service status and logs
  - Secure the bootloader with a password if needed
  - Keep kernel and initramfs updated

## Sample Exercises
1. List and describe each step of the Linux boot process.
2. Check the status of the `ssh` (or `sshd`) service and restart it.
3. Enable a service to start at boot and then disable it.
4. View the kernel boot messages and identify any errors.
5. List all running services and their status.

## Solutions
1. **Boot Process Steps:**
   - **BIOS/UEFI:** Hardware initialization, POST, bootloader location
   - **Bootloader (GRUB):** Loads kernel and initramfs into memory
   - **Kernel:** Hardware detection, driver loading, root filesystem mount
   - **Init (systemd):** Service startup, target achievement, user space

2. **SSH Service Management:**
   ```bash
   systemctl status ssh      # or sshd on RHEL/CentOS
   systemctl restart ssh
   systemctl status ssh      # Verify restart
   ```

3. **Service Boot Configuration:**
   ```bash
   systemctl enable nginx    # Enable at boot
   systemctl is-enabled nginx # Check status
   systemctl disable nginx   # Disable at boot
   ```

4. **Boot Message Analysis:**
   ```bash
   dmesg | grep -i "error\|fail\|warn"
   journalctl -b -p err
   ```

5. **Service Listing:**
   ```bash
   systemctl list-units --type=service --state=running
   systemctl list-units --type=service --state=failed
   ```

## Completion Checklist
- [ ] Understand the boot process stages
- [ ] Can manage services with systemctl commands
- [ ] Know how to enable/disable services at boot
- [ ] Can view and analyze boot logs
- [ ] Understand systemd vs SysVinit differences
- [ ] Can troubleshoot basic service issues

## Key Commands Summary
```bash
# Service management
systemctl status|start|stop|restart <service>
systemctl enable|disable <service>
systemctl list-units --type=service

# Logs and diagnostics
journalctl -u <service>
dmesg
systemd-analyze

# Boot analysis
journalctl -b
systemd-analyze blame
```

## Troubleshooting Tips
- **Service won't start:** Check `systemctl status` and `journalctl -u service`
- **Boot issues:** Use rescue mode, check `journalctl -b`
- **Slow boot:** Use `systemd-analyze blame` to identify bottlenecks
- **Failed services:** `systemctl list-units --state=failed`

## Sample Interview Questions
1. Explain the complete Linux boot process, step by step.
2. What is the difference between BIOS and UEFI?
3. What is the role of the bootloader, and how can you troubleshoot bootloader issues?
4. Compare systemd, SysVinit, and Upstart. Why is systemd preferred in modern distributions?
5. How do you check if a service is enabled to start at boot? How do you enable/disable it?
6. What is the difference between `systemctl stop` and `systemctl disable`?
7. How can you view logs for a specific service?
8. What would you do if a critical service fails to start during boot?
9. How can you secure the bootloader?
10. What is the purpose of the `dmesg` command?

## Interview Question Answers
1. **Boot Process:** BIOS/UEFI → Bootloader (GRUB) → Kernel loading → Init system (systemd) → User space services
2. **BIOS vs UEFI:** BIOS is legacy firmware with 16-bit mode; UEFI is modern with 32/64-bit, faster boot, secure boot, and GUI
3. **Bootloader:** GRUB loads kernel and initramfs; troubleshoot with rescue mode, check `/boot/grub/grub.cfg`, use `grub-install`
4. **Init Systems:** systemd is parallel, faster, dependency-based; SysVinit is sequential, script-based; Upstart was transitional
5. **Service Boot Management:** `systemctl is-enabled service`, `systemctl enable/disable service`
6. **Stop vs Disable:** `stop` halts running service; `disable` prevents auto-start at boot
7. **Service Logs:** `journalctl -u service`, `systemctl status service` shows recent logs
8. **Failed Service:** Check `systemctl status`, `journalctl -u service`, use rescue mode, check dependencies
9. **Secure Bootloader:** Set GRUB password, enable secure boot, restrict physical access
10. **dmesg Purpose:** Shows kernel ring buffer messages, hardware detection, driver loading, boot errors


## Next Steps
Proceed to [Day 5: Basic Linux Commands for DevOps Engineers](../Day_05/notes_and_exercises.md) to learn essential command-line tools.
