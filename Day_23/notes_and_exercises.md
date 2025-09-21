# Day 23: Linux Interview Questions & Real-World Scenarios

## Notes
- **Why Interview Prep Matters:**
  - Prepares you for technical interviews and real-world troubleshooting.
  - Covers practical scenarios, command usage, and best practices.

- **Common Interview Topics:**
  - Filesystem hierarchy, permissions, process management, networking, scripting, troubleshooting, security, automation

- **Real-World Scenarios:**
  - Troubleshoot a slow server: check CPU, memory, disk, network, logs
  - Recover from accidental file deletion: use backups, `extundelete`, `testdisk`
  - Secure a public web server: firewall, permissions, updates, monitoring
  - Automate user onboarding: scripts, templates, version control
  - Migrate data between servers: `rsync`, `scp`, downtime planning

- **Best Practices:**
  - Always document changes and configs
  - Use version control for scripts and configs
  - Test in staging before production
  - Monitor and alert on key metrics
  - Backup before making major changes

- **Diagram:**
  - ![Interview Scenarios Diagram](https://upload.wikimedia.org/wikipedia/commons/2/2b/Monitoring_stack.png)

## Sample Exercises
1. Given a server with high load, outline your troubleshooting steps.
2. Write a script to automate user creation and home directory setup.
3. Secure a Linux server for public internet exposure.
4. Plan a backup and restore strategy for a production database.
5. Migrate a web application to a new server with minimal downtime.

## Solutions
1. Check `top`, `htop`, `vmstat`, `iostat`, `df`, logs, and running processes; identify and resolve bottlenecks.
2. Use a shell script with `useradd`, `passwd`, and directory setup commands.
3. Enable firewall, disable root SSH login, use SSH keys, keep system updated, monitor logs.
4. Use `mysqldump`/`pg_dump` for backups, automate with cron, test restores, store offsite.
5. Use `rsync` for data sync, plan DNS cutover, test on staging, schedule during low-traffic hours.

## Sample Interview Questions
1. How do you troubleshoot a server with high CPU usage?
2. How do you recover a deleted file on Linux?
3. How do you secure SSH access?
4. How do you automate repetitive tasks?
5. How do you monitor system health?
6. How do you migrate data between servers?
7. How do you handle failed disk or hardware?
8. How do you set up a new user with specific permissions?
9. How do you roll back a failed deployment?
10. How do you document and share your work with a team?

## Interview Question Answers
1. Use `top`, `htop`, check running processes, logs, and resource usage; identify and resolve the cause.
2. Restore from backup, or use tools like `extundelete`/`testdisk` if possible.
3. Use SSH keys, disable root login, change default port, use firewalls, enable fail2ban.
4. Use shell scripts, cron jobs, and automation tools (Ansible, Puppet).
5. Use monitoring tools (`top`, `htop`, `nagios`, `prometheus`), set up alerts.
6. Use `rsync`, `scp`, or backup/restore tools; plan for minimal downtime.
7. Use RAID, LVM, and have spares; monitor SMART data; replace failed disks promptly.
8. Use `useradd`, set permissions with `chmod`/`chown`, assign to groups.
9. Use version control, backups, and rollback scripts; test before deploying.
10. Use README files, wikis, and version control systems.
