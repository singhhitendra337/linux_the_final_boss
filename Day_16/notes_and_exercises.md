# Day 16: Security, Firewalls & Hardening

## Notes
- **Why Security Matters:**
  - Protects systems from unauthorized access, data loss, and attacks.
  - Essential for compliance, uptime, and trust.

- **User & File Security:**
  - Use strong, unique passwords; enforce password policies (`/etc/login.defs`, `chage`)
  - Limit sudo/root access; use `sudo` logs (`/var/log/auth.log`)
  - Set correct file permissions (`chmod`, `chown`, `umask`)
  - Use ACLs for fine-grained access

- **Network Security:**
  - Close unused ports; use `netstat`, `ss`, `lsof` to audit
  - Use firewalls: `ufw` (simple), `iptables` (advanced), `firewalld` (zones)
  - Example: `ufw enable`, `ufw allow 22/tcp`, `iptables -L -n`
  - Disable unused services (`systemctl disable <service>`)

- **System Hardening:**
  - Keep system/packages updated (`apt update && apt upgrade`)
  - Remove unnecessary software (`apt purge`, `yum remove`)
  - Use security tools: `fail2ban`, `auditd`, `AppArmor`, `SELinux`
  - Monitor logs for suspicious activity (`journalctl`, `/var/log/auth.log`)
  - Set up automatic security updates

- **Encryption:**
  - Encrypt sensitive data at rest (`gpg`, `openssl`, LUKS/dm-crypt)
  - Use SSH keys for remote access; disable password login
  - Use VPNs for secure remote connections

- **Best Practices:**
  - Principle of least privilege
  - Regularly audit users, groups, and permissions
  - Use multi-factor authentication (MFA) where possible
  - Backup configs and document security policies

- **Diagram:**
  - ![Security Diagram](https://upload.wikimedia.org/wikipedia/commons/3/3b/Linux_security_model.png)

## In-Depth Explanations & Scenarios
- **Firewall Configuration:**
  - `ufw`: Simple, e.g., `ufw allow 80/tcp`, `ufw deny 23`
  - `iptables`: Advanced, e.g., `iptables -A INPUT -p tcp --dport 22 -j ACCEPT`
  - `firewalld`: Zone-based, e.g., `firewall-cmd --add-service=ssh --permanent`
- **SELinux & AppArmor:**
  - Mandatory access control systems; restrict what processes can do
  - Use `getenforce`, `setenforce`, `aa-status`, `aa-complain`
- **Real-World Example:**
  - Harden a web server: close all ports except 80/443, use fail2ban, enforce strong permissions, monitor logs

## Sample Exercises
1. Set up a firewall to allow only SSH and HTTP/HTTPS traffic.
2. Enforce password aging and complexity for all users.
3. Find and disable all unused services on your system.
4. Encrypt a file using GPG and decrypt it.
5. Set up fail2ban to protect SSH from brute-force attacks.

## Solutions
1. `ufw allow 22,80,443/tcp; ufw enable` or use `iptables`/`firewalld` equivalents
2. Edit `/etc/login.defs` and use `chage -M 90 -m 7 -W 14 username`
3. `systemctl list-unit-files | grep enabled`; `systemctl disable <service>`
4. `gpg -c file.txt`; `gpg file.txt.gpg`
5. Install `fail2ban`, configure `/etc/fail2ban/jail.local`, restart service

## Sample Interview Questions
1. How do you secure SSH access on a Linux server?
2. What is the difference between a firewall and SELinux/AppArmor?
3. How do you audit user and group permissions?
4. How do you set up automatic security updates?
5. What is the principle of least privilege?
6. How do you encrypt data at rest on Linux?
7. How do you monitor for unauthorized access attempts?
8. What are the risks of running services as root?
9. How do you check for open ports and running services?
10. How do you recover from a security breach?

## Interview Question Answers
1. Use SSH keys, disable root login, change default port, use firewalls, enable fail2ban.
2. Firewalls filter network traffic; SELinux/AppArmor restrict process capabilities.
3. Use `find`, `ls -lR`, `getfacl`, and audit logs to review permissions.
4. Use `unattended-upgrades` (Debian/Ubuntu) or `yum-cron` (RHEL/CentOS).
5. Grant only the minimum permissions needed for users/services.
6. Use `gpg`, `openssl`, or LUKS/dm-crypt for encryption.
7. Monitor `/var/log/auth.log`, use `auditd`, and set up alerts.
8. Root services can be exploited for full system access; use unprivileged users where possible.
9. Use `netstat`, `ss`, `systemctl`, and `ps` to check open ports/services.
10. Isolate affected systems, analyze logs, restore from backups, patch vulnerabilities, and review security policies.
