# Day 30: Advanced Security Hardening & Compliance

## Notes
- **Goal:** Reduce attack surface and enforce least privilege.

### Accounts & Auth
- Lock unused accounts, enforce key-based SSH, `PermitRootLogin no`.
- PAM basics; password policies; `faillock`, `chage`.

### Discretionary vs Mandatory Access Control
- DAC: Unix perms + ACLs
- MAC: SELinux/AppArmor

### SELinux (RHEL-family)
```
getenforce
setenforce 1
sestatus
ausearch -m avc -ts recent | audit2allow
```

### AppArmor (Debian/Ubuntu)
```
aa-status
aa-enforce /etc/apparmor.d/usr.sbin.nginx
```

### Auditing & Compliance
- `auditd`, rules, and reports
- CIS/STIG benchmarks (concepts), `lynis` for auditing

### Network & Services
- Minimize listening services; firewall (`ufw`, `firewalld`, `nftables`)
- Kernel hardening: disable uncommon protocols; sysctl hardening

## Hands-on Labs
1. Harden SSH: disable root login, restrict ciphers, set IdleTimeout.
2. Enable SELinux/AppArmor in enforcing mode and review denials.
3. Write auditd rule to watch `/etc/ssh/sshd_config` changes.

## Interview-style Questions
1. Difference between SELinux types, roles, and booleans?
2. Steps to harden SSH on a production server?
3. How do you prove compliance during an audit?

## Solutions (Brief)
1. Types label objects/processes; roles tie to users; booleans toggle policy features.
2. Key-based auth, disable root/password, strong ciphers/MACs, rate-limit, fail2ban.
3. auditd logs, configuration baselines, automated reports, change management records.

