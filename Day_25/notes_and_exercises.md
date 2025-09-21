# Day 25: System Hardening

## Why System Hardening Matters
- Reduces attack surface and risk of compromise.
- Essential for compliance, security, and production reliability.

## Key Concepts
- Principle of least privilege
- Disabling unused services and ports
- Secure SSH configuration
- File and directory permissions
- Auditing and logging
- Security tools: `fail2ban`, `auditd`, `AIDE`, `chkrootkit`, `rkhunter`
- Kernel hardening (sysctl, AppArmor/SELinux basics)

## Common Hardening Steps
- Remove/disable unnecessary packages and services
- Enforce strong password policies
- Configure firewalls (ufw, firewalld, iptables)
- Disable root SSH login, use SSH keys
- Set correct file permissions and ownership
- Enable and monitor system logs
- Regularly scan for rootkits and malware

## Practical Examples
### 1. Disable Unused Services
```bash
sudo systemctl disable --now avahi-daemon cups
```

### 2. Secure SSH
```bash
sudo sed -i 's/^#PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
sudo systemctl reload sshd
```

### 3. Set File Permissions
```bash
sudo chmod -R go-rwx /etc/ssh
```

### 4. Install and Configure Fail2ban
```bash
sudo apt install fail2ban
sudo systemctl enable --now fail2ban
```

## Best Practices
- Harden new systems before production use
- Document all changes
- Regularly review and update hardening measures
- Test hardening in staging before production

## Exercises
1. Harden SSH on a test server (disable root login, enforce key auth).
2. Remove and disable all unnecessary services.
3. Set up and test fail2ban for SSH brute-force protection.
4. Scan the system for rootkits and document findings.

## Solutions
### 1. Edit `/etc/ssh/sshd_config`, reload sshd.
### 2. Use `systemctl` and `apt remove`/`yum remove`.
### 3. Install fail2ban, check `/var/log/fail2ban.log`.
### 4. Use `chkrootkit`/`rkhunter` and review reports.

## Interview-Style Questions & Answers
1. **What is system hardening?**
   - The process of securing a system by reducing vulnerabilities and attack surface.
2. **How do you secure SSH access?**
   - Disable root login, use SSH keys, change default port, use fail2ban.
3. **What tools help with system hardening?**
   - `fail2ban`, `auditd`, `AIDE`, `chkrootkit`, `rkhunter`, firewalls.
4. **How do you audit system changes?**
   - Use logging, `auditd`, and regular file integrity checks.
5. **Why is least privilege important?**
   - Limits damage from compromised accounts or processes.
