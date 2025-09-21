# Day 04: Linux Boot Process & Service Management

## Notes
- **What is the Linux Boot Process?**
  - The boot process is the sequence of steps the system takes to start up and become ready for use.
  - Understanding the boot process is crucial for troubleshooting startup issues and managing services.

- **Boot Process Steps:**
  1. **BIOS/UEFI:** Initializes hardware and loads the bootloader from disk.
  2. **Bootloader (GRUB/LILO):** Loads the Linux kernel into memory.
  3. **Kernel:** Initializes hardware, mounts root filesystem, starts the first process (init/systemd).
  4. **Init System (systemd, SysVinit, Upstart):** Launches system services and user sessions.

  ![Linux Boot Process](https://upload.wikimedia.org/wikipedia/commons/7/7c/Linux_Boot_Process.png)

- **Bootloader Details:**
  - GRUB (GRand Unified Bootloader) is the most common bootloader.
  - Allows selection of OS/kernel version at boot.
  - Configured via `/boot/grub/grub.cfg` or `/etc/default/grub`.

- **Init Systems:**
  - **systemd:** Modern, parallel service startup, used by most distros (Ubuntu, CentOS 7+, Debian, etc.).
  - **SysVinit:** Older, sequential startup using scripts in `/etc/init.d/`.
  - **Upstart:** Transitional system (Ubuntu 9.10–14.10).

- **Service Management with systemd:**
  - `systemctl status <service>`: Show service status
  - `systemctl start <service>`: Start a service
  - `systemctl stop <service>`: Stop a service
  - `systemctl restart <service>`: Restart a service
  - `systemctl enable <service>`: Enable service at boot
  - `systemctl disable <service>`: Disable service at boot
  - `systemctl list-units --type=service`: List all services
  - `journalctl -u <service>`: View logs for a service

- **Checking Boot Logs:**
  - `dmesg`: Kernel ring buffer (hardware and driver messages)
  - `journalctl -b`: View logs from current boot

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
1. The Linux boot process includes BIOS/UEFI, bootloader (GRUB), kernel loading, init/systemd, and user space startup.
2. The bootloader (e.g., GRUB) loads the kernel and initial RAM disk, then hands off control to the kernel.
3. The kernel initializes hardware, mounts the root filesystem, and starts the init process (PID 1).
4. `systemd` is the modern init system, managing services, sockets, and targets; alternatives include SysVinit and Upstart.
5. Use `systemctl status <service>`, `systemctl start/stop/restart <service>` to manage services.
6. `journalctl` shows logs for systemd services; `systemctl status` also displays recent logs.
7. Use `systemctl enable <service>` to start at boot, `systemctl disable <service>` to prevent auto-start.
8. Troubleshoot boot issues by checking logs (`journalctl`, `/var/log/boot.log`), using rescue mode, or reviewing kernel parameters.
9. `runlevel` shows the current runlevel; systemd uses targets instead (e.g., `multi-user.target`).
10. Use `systemctl list-units --type=service` to list all services; `systemctl is-enabled <service>` to check if enabled at boot.
