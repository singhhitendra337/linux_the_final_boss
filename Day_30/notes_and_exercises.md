# Day 30: Advanced Security Hardening & Compliance

## Learning Objectives
By the end of this day, you will:
- Implement comprehensive Linux security hardening
- Master SELinux and AppArmor mandatory access controls
- Configure advanced auditing and compliance monitoring
- Apply security frameworks (CIS, STIG, NIST)
- Automate security compliance checking and reporting

## Table of Contents
1. [Security Hardening Fundamentals](#security-hardening-fundamentals)
2. [Access Control Systems](#access-control-systems)
3. [SELinux Deep Dive](#selinux-deep-dive)
4. [AppArmor Implementation](#apparmor-implementation)
5. [System Auditing](#system-auditing)
6. [Compliance Frameworks](#compliance-frameworks)
7. [Network Security](#network-security)
8. [Exercises](#exercises)
9. [Solutions](#solutions)
10. [Interview Questions](#interview-questions)

---

## Security Hardening Fundamentals

### Defense in Depth Strategy
```
┌─────────────────────────────────────────────────────────┐
│                 Physical Security                       │
├─────────────────────────────────────────────────────────┤
│                 Network Security                        │
├─────────────────────────────────────────────────────────┤
│                 Host Security                           │
├─────────────────────────────────────────────────────────┤
│                 Application Security                    │
├─────────────────────────────────────────────────────────┤
│                 Data Security                           │
└─────────────────────────────────────────────────────────┘
```

### Core Security Principles
- **Least Privilege**: Minimum necessary access
- **Defense in Depth**: Multiple security layers
- **Fail Secure**: Secure defaults when systems fail
- **Complete Mediation**: All access requests checked
- **Open Design**: Security through design, not obscurity

### Account Security Hardening
```bash
# Lock unused system accounts
for user in games news uucp proxy www-data backup list irc gnats; do
    usermod -L -s /bin/false $user 2>/dev/null
done

# Set password policies
cat > /etc/security/pwquality.conf << 'EOF'
minlen = 12
minclass = 3
maxrepeat = 2
maxclasrepeat = 2
rejectpalindrome
EOF

# Configure account lockout
cat > /etc/security/faillock.conf << 'EOF'
dir = /var/run/faillock
audit
silent
deny = 5
fail_interval = 900
unlock_time = 600
EOF

# Set password aging
chage -M 90 -m 7 -W 7 username
```

---

## Access Control Systems

### Discretionary Access Control (DAC)
```bash
# Advanced file permissions
setfacl -m u:user1:rw- /path/to/file
setfacl -m g:group1:r-- /path/to/file
getfacl /path/to/file

# Default ACLs for directories
setfacl -d -m u::rwx,g::r-x,o::--- /shared/directory

# Sticky bit and special permissions
chmod 1755 /tmp  # Sticky bit
chmod 2755 /shared  # SGID
chmod 4755 /usr/bin/sudo  # SUID
```

### Mandatory Access Control (MAC)
- **SELinux**: Type Enforcement, Role-Based Access Control
- **AppArmor**: Path-based access control
- **grsecurity**: Kernel hardening (third-party)

---

## SELinux Deep Dive

### SELinux Architecture
```
┌─────────────────┐    ┌─────────────────┐
│   Application   │    │   System Call   │
└─────────────────┘    └─────────────────┘
         │                       │
         ▼                       ▼
┌─────────────────────────────────────────┐
│           Linux Kernel                  │
│  ┌─────────────────────────────────┐    │
│  │        SELinux LSM              │    │
│  │  ┌─────────────────────────┐    │    │
│  │  │    Security Server      │    │    │
│  │  └─────────────────────────┘    │    │
│  └─────────────────────────────────┘    │
└─────────────────────────────────────────┘
```

### SELinux Configuration
```bash
# Check SELinux status
getenforce
sestatus

# Set SELinux mode
setenforce 1  # Enforcing
setenforce 0  # Permissive

# Permanent configuration
echo "SELINUX=enforcing" > /etc/selinux/config
echo "SELINUXTYPE=targeted" >> /etc/selinux/config

# Context management
ls -Z /etc/passwd
ps -eZ
id -Z

# File context management
restorecon -R /var/www/html
semanage fcontext -a -t httpd_exec_t "/opt/myapp/bin(/.*)?"
restorecon -R /opt/myapp/bin
```

### SELinux Policy Management
```bash
# View policy modules
semodule -l

# Boolean management
getsebool -a | grep httpd
setsebool -P httpd_can_network_connect on

# Port labeling
semanage port -l | grep http
semanage port -a -t http_port_t -p tcp 8080

# User mapping
semanage login -l
semanage user -l
```

### SELinux Troubleshooting
```bash
# Audit log analysis
ausearch -m avc -ts recent
grep "denied" /var/log/audit/audit.log

# Generate policy from denials
ausearch -m avc -ts recent | audit2allow -M mypolicy
semodule -i mypolicy.pp

# SELinux tools
sealert -a /var/log/audit/audit.log
setroubleshoot-server
```

---

## AppArmor Implementation

### AppArmor Profile Management
```bash
# Check AppArmor status
aa-status
apparmor_status

# Profile modes
aa-enforce /etc/apparmor.d/usr.sbin.nginx
aa-complain /etc/apparmor.d/usr.sbin.nginx
aa-disable /etc/apparmor.d/usr.sbin.nginx

# Generate profiles
aa-genprof /usr/sbin/nginx
aa-logprof
```

### Custom AppArmor Profile
```bash
# Create nginx profile
cat > /etc/apparmor.d/usr.sbin.nginx << 'EOF'
#include <tunables/global>

/usr/sbin/nginx {
  #include <abstractions/base>
  #include <abstractions/nameservice>
  #include <abstractions/web-data>

  capability dac_override,
  capability setuid,
  capability setgid,
  capability net_bind_service,

  /usr/sbin/nginx mr,
  /etc/nginx/** r,
  /var/log/nginx/** w,
  /var/lib/nginx/** rw,
  /run/nginx.pid w,
  /var/www/** r,

  # Network access
  network inet tcp,
  network inet6 tcp,

  # Signal handling
  signal (receive) set=(term, quit, usr1, usr2) peer=unconfined,
}
EOF

# Load and enforce profile
apparmor_parser -r /etc/apparmor.d/usr.sbin.nginx
aa-enforce /etc/apparmor.d/usr.sbin.nginx
```

---

## System Auditing

### auditd Configuration
```bash
# Configure auditd
cat > /etc/audit/auditd.conf << 'EOF'
log_file = /var/log/audit/audit.log
log_format = RAW
log_group = root
priority_boost = 4
flush = INCREMENTAL_ASYNC
freq = 50
num_logs = 5
disp_qos = lossy
dispatcher = /sbin/audispd
name_format = HOSTNAME
max_log_file = 50
max_log_file_action = ROTATE
space_left = 75
space_left_action = SYSLOG
verify_email = yes
action_mail_acct = root
admin_space_left = 50
admin_space_left_action = SUSPEND
disk_full_action = SUSPEND
disk_error_action = SUSPEND
use_libwrap = yes
tcp_listen_queue = 5
tcp_max_per_addr = 1
tcp_client_max_idle = 0
enable_krb5 = no
krb5_principal = auditd
EOF

# Audit rules
cat > /etc/audit/rules.d/audit.rules << 'EOF'
# Delete all existing rules
-D

# Buffer size
-b 8192

# Failure mode (0=silent, 1=printk, 2=panic)
-f 1

# Monitor file access
-w /etc/passwd -p wa -k identity
-w /etc/group -p wa -k identity
-w /etc/shadow -p wa -k identity
-w /etc/sudoers -p wa -k privilege_escalation

# Monitor system calls
-a always,exit -F arch=b64 -S adjtimex,settimeofday -k time_change
-a always,exit -F arch=b64 -S clock_settime -F a0=0x0 -k time_change

# Monitor network configuration
-w /etc/hosts -p wa -k network_config
-w /etc/network/ -p wa -k network_config

# Monitor login/logout events
-w /var/log/lastlog -p wa -k logins
-w /var/run/faillock/ -p wa -k logins

# Monitor privilege escalation
-w /bin/su -p x -k privilege_escalation
-w /usr/bin/sudo -p x -k privilege_escalation

# Make rules immutable
-e 2
EOF

# Restart auditd
systemctl restart auditd
```

### Audit Log Analysis
```bash
# Search audit logs
ausearch -k identity
ausearch -ui 1000
ausearch -m USER_LOGIN
ausearch -ts today -te now

# Generate reports
aureport --summary
aureport --auth
aureport --file
aureport --user

# Real-time monitoring
autail -f
```

---

## Compliance Frameworks

### CIS Benchmarks Implementation
```bash
# Install CIS-CAT tool (example)
wget https://workbench.cisecurity.org/files/cis-cat-full.zip
unzip cis-cat-full.zip

# Run CIS assessment
./CIS-CAT.sh -a -b benchmarks/CIS_Ubuntu_Linux_20.04_LTS_Benchmark_v1.1.0-xccdf.xml

# Manual CIS controls
# 1.1.1.1 Ensure mounting of cramfs filesystems is disabled
echo "install cramfs /bin/true" >> /etc/modprobe.d/CIS.conf

# 1.1.1.2 Ensure mounting of freevxfs filesystems is disabled
echo "install freevxfs /bin/true" >> /etc/modprobe.d/CIS.conf

# 1.4.1 Ensure permissions on bootloader config are configured
chown root:root /boot/grub/grub.cfg
chmod og-rwx /boot/grub/grub.cfg

# 1.5.1 Ensure core dumps are restricted
echo "* hard core 0" >> /etc/security/limits.conf
echo "fs.suid_dumpable = 0" >> /etc/sysctl.conf
```

### STIG Compliance
```bash
# STIG hardening script
cat > stig_hardening.sh << 'EOF'
#!/bin/bash

# V-38437: Automated file system mounting must be disabled
systemctl disable autofs

# V-38439: The system must provide automated support for account lock
echo "auth required pam_faillock.so preauth audit silent deny=3 unlock_time=604800 fail_interval=900" >> /etc/pam.d/system-auth

# V-38443: The /etc/gshadow file must be owned by root
chown root:root /etc/gshadow

# V-38445: Audit log files must be owned by root
chown root:root /var/log/audit/audit.log

# V-38448: The /etc/gshadow file must be group-owned by root
chgrp root /etc/gshadow

# V-38450: The /etc/passwd file must be owned by root
chown root:root /etc/passwd
EOF

chmod +x stig_hardening.sh
```

### Lynis Security Auditing
```bash
# Install Lynis
wget https://downloads.cisofy.com/lynis/lynis-3.0.8.tar.gz
tar xzf lynis-3.0.8.tar.gz
cd lynis

# Run security audit
./lynis audit system

# Generate report
./lynis audit system --report-file /tmp/lynis-report.dat

# Custom Lynis profile
cat > /etc/lynis/custom.prf << 'EOF'
# Custom Lynis profile
skip-test=AUTH-9262
skip-test=AUTH-9264
config:ssh_daemon_options_file:/etc/ssh/sshd_config
config:pam_directory:/etc/pam.d
EOF
```

---

## Network Security

### Firewall Configuration
```bash
# iptables rules
cat > /etc/iptables/rules.v4 << 'EOF'
*filter
:INPUT DROP [0:0]
:FORWARD DROP [0:0]
:OUTPUT ACCEPT [0:0]

# Allow loopback
-A INPUT -i lo -j ACCEPT

# Allow established connections
-A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# Allow SSH (rate limited)
-A INPUT -p tcp --dport 22 -m conntrack --ctstate NEW -m recent --set
-A INPUT -p tcp --dport 22 -m conntrack --ctstate NEW -m recent --update --seconds 60 --hitcount 4 -j DROP
-A INPUT -p tcp --dport 22 -j ACCEPT

# Allow HTTP/HTTPS
-A INPUT -p tcp --dport 80 -j ACCEPT
-A INPUT -p tcp --dport 443 -j ACCEPT

# Log dropped packets
-A INPUT -m limit --limit 5/min -j LOG --log-prefix "iptables denied: " --log-level 7

COMMIT
EOF

# Apply rules
iptables-restore < /etc/iptables/rules.v4
```

### Network Hardening
```bash
# Kernel network parameters
cat > /etc/sysctl.d/99-network-security.conf << 'EOF'
# IP Spoofing protection
net.ipv4.conf.default.rp_filter = 1
net.ipv4.conf.all.rp_filter = 1

# Ignore ICMP redirects
net.ipv4.conf.all.accept_redirects = 0
net.ipv6.conf.all.accept_redirects = 0

# Ignore send redirects
net.ipv4.conf.all.send_redirects = 0

# Disable source packet routing
net.ipv4.conf.all.accept_source_route = 0
net.ipv6.conf.all.accept_source_route = 0

# Log Martians
net.ipv4.conf.all.log_martians = 1

# Ignore ICMP ping requests
net.ipv4.icmp_echo_ignore_all = 1

# Ignore Directed pings
net.ipv4.icmp_echo_ignore_broadcasts = 1

# Disable IPv6 if not needed
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
EOF

sysctl --system
```

---

## Exercises

### Exercise 1: SSH Hardening
**Objective**: Implement comprehensive SSH security.

**Tasks**:
1. Configure key-based authentication
2. Disable root login and password authentication
3. Implement connection rate limiting
4. Configure SSH audit logging

### Exercise 2: SELinux Policy Creation
**Objective**: Create custom SELinux policy for application.

**Tasks**:
1. Install application in enforcing mode
2. Analyze AVC denials
3. Create custom policy module
4. Test policy effectiveness

### Exercise 3: System Auditing Setup
**Objective**: Implement comprehensive system auditing.

**Tasks**:
1. Configure auditd with custom rules
2. Monitor file system changes
3. Track privilege escalation attempts
4. Generate compliance reports

### Exercise 4: CIS Benchmark Implementation
**Objective**: Apply CIS security controls.

**Tasks**:
1. Run CIS assessment baseline
2. Implement Level 1 controls
3. Document exceptions and rationale
4. Verify compliance improvements

### Exercise 5: Automated Security Scanning
**Objective**: Implement automated security monitoring.

**Tasks**:
1. Set up Lynis automated scanning
2. Configure security alerting
3. Create remediation playbooks
4. Implement continuous compliance monitoring

---

## Solutions

### Solution 1: SSH Hardening
```bash
# SSH configuration
cat > /etc/ssh/sshd_config << 'EOF'
Port 2222
Protocol 2
HostKey /etc/ssh/ssh_host_rsa_key
HostKey /etc/ssh/ssh_host_ecdsa_key
HostKey /etc/ssh/ssh_host_ed25519_key

PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys

MaxAuthTries 3
MaxSessions 2
MaxStartups 2

ClientAliveInterval 300
ClientAliveCountMax 2

AllowUsers admin devops
DenyUsers root

Ciphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com
MACs hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com
KexAlgorithms curve25519-sha256@libssh.org,diffie-hellman-group16-sha512

LogLevel VERBOSE
SyslogFacility AUTH

UsePAM yes
X11Forwarding no
AllowTcpForwarding no
AllowAgentForwarding no
PermitTunnel no
EOF

systemctl restart sshd
```

### Solution 2: SELinux Policy Creation
```bash
# Generate policy from audit logs
ausearch -m avc -ts recent | audit2allow -M myapp_policy

# Review and customize policy
cat myapp_policy.te

# Compile and install
semodule -i myapp_policy.pp

# Verify policy
semodule -l | grep myapp
```

### Solution 3: System Auditing Setup
```bash
# Comprehensive audit rules
cat > /etc/audit/rules.d/comprehensive.rules << 'EOF'
# System integrity
-w /etc/passwd -p wa -k identity
-w /etc/group -p wa -k identity
-w /etc/shadow -p wa -k identity
-w /etc/sudoers -p wa -k privilege

# Network configuration
-w /etc/hosts -p wa -k network
-w /etc/network/ -p wa -k network

# System administration
-w /var/log/lastlog -p wa -k logins
-w /var/run/faillock/ -p wa -k logins

# Privilege escalation
-a always,exit -F arch=b64 -S execve -F euid=0 -F auid>=1000 -F auid!=4294967295 -k privilege_escalation
EOF

systemctl restart auditd
```

---

## Interview Questions

### Basic Level

1. **Q**: What is the difference between DAC and MAC?
   **A**: DAC (Discretionary Access Control) allows resource owners to control access. MAC (Mandatory Access Control) enforces system-wide security policies that users cannot override.

2. **Q**: How do you check if SELinux is enabled and in what mode?
   **A**: Use `getenforce` for current mode, `sestatus` for detailed status, and check `/etc/selinux/config` for permanent configuration.

3. **Q**: What are the main components of an audit rule?
   **A**: Watch path (-w), permissions (-p), key (-k), and optional filters. Example: `-w /etc/passwd -p wa -k identity`

### Intermediate Level

4. **Q**: How do you troubleshoot SELinux denials?
   **A**: Check audit logs with `ausearch -m avc`, use `sealert` for analysis, generate policies with `audit2allow`, and test in permissive mode first.

5. **Q**: Explain the CIS benchmark levels.
   **A**: Level 1 provides basic security with minimal impact on functionality. Level 2 provides enhanced security but may impact performance or usability.

### Advanced Level

6. **Q**: How do you implement automated compliance monitoring?
   **A**: Use tools like Lynis, OpenSCAP, or custom scripts with configuration management systems. Implement continuous scanning, alerting, and remediation workflows.

---

## Community and Support

- **Discord Community**: [Join our Discord](https://discord.gg/linux-learning) for security discussions
- **Google Group**: [Linux Security Group](https://groups.google.com/g/linux-security) for compliance topics
- **YouTube Channel**: [Security Hardening Tutorials](https://youtube.com/c/linux-security)

---

## Completion Checklist

- [ ] Understand security hardening principles
- [ ] Master SELinux and AppArmor configuration
- [ ] Implement comprehensive system auditing
- [ ] Apply compliance frameworks (CIS, STIG)
- [ ] Configure network security controls
- [ ] Complete all practical exercises
- [ ] Review and answer all interview questions

---

**Next**: [Day 31 - Advanced Troubleshooting & Kernel Interaction](../Day_31/notes_and_exercises.md)

