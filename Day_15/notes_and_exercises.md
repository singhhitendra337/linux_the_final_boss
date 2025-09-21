# Day 15: Networking & Troubleshooting

## Notes
- **Why Networking Skills Matter:**
  - Networking is foundational for system connectivity, troubleshooting, and security.
  - Essential for DevOps, SRE, and cloud engineers.

- **Network Configuration & Tools:**
  - `ip a` or `ifconfig`: Show network interfaces
  - `ip route` or `route -n`: Show routing table
  - `ping host`: Test connectivity
  - `traceroute host`: Trace network path
  - `netstat -tuln` or `ss -tuln`: List open ports
  - `nslookup`, `dig`: DNS queries
  - `curl`, `wget`: HTTP requests
  - `scp`, `rsync`: File transfer
  - `nmcli`, `nmtui`: NetworkManager tools

- **Troubleshooting Steps:**
  - Check physical connections and interface status
  - Test local and remote connectivity
  - Check DNS resolution
  - Inspect firewall rules (`iptables`, `ufw`, `firewalld`)
  - Review logs (`/var/log/syslog`, `/var/log/messages`)

- **Best Practices:**
  - Document network configs and changes
  - Use version control for config files
  - Limit open ports and use firewalls
  - Monitor network traffic for anomalies
  - Use strong passwords and encryption for remote access

- **Diagram:**
  - ![Networking Diagram](https://upload.wikimedia.org/wikipedia/commons/3/3b/Network_troubleshooting.png)

## In-Depth Explanations & Scenarios
- **Network Layers & Protocols:**
  - OSI model: Physical, Data Link, Network, Transport, Session, Presentation, Application
  - TCP/IP stack: Link, Internet, Transport, Application
- **Common Protocols:**
  - TCP, UDP, ICMP, HTTP, HTTPS, FTP, SSH, DNS, DHCP
- **Real-World Example:**
  - Troubleshoot a web server: check interface, ping, traceroute, check firewall, test with `curl`, review logs.
- **Firewall Tools:**
  - `iptables`, `ufw`, `firewalld` for managing access
  - Example: `ufw allow 22/tcp`; `iptables -L -n`
- **Network Diagnostics:**
  - Use `tcpdump` or `wireshark` for packet capture and analysis
  - `nmap` for port scanning and security auditing
- **Best Practices (Expanded):**
  - Document all network changes
  - Use VLANs and segmentation for security
  - Regularly audit open ports and firewall rules
  - Use VPNs for secure remote access

## Sample Exercises
1. Display all network interfaces and their IP addresses.
2. Test connectivity to a remote server and trace the route.
3. List all open TCP/UDP ports on your system.
4. Query DNS records for a domain.
5. Transfer a file to another machine using `scp`.

## Solutions
1. `ip a` or `ifconfig`
2. `ping example.com`; `traceroute example.com`
3. `netstat -tuln` or `ss -tuln`
4. `nslookup example.com` or `dig example.com`
5. `scp file.txt user@host:/path/`

## Sample Interview Questions
1. How do you check network interface configuration?
2. How do you test and troubleshoot network connectivity?
3. What is the difference between TCP and UDP?
4. How do you check which process is using a specific port?
5. How do you perform a DNS lookup?
6. How do you secure remote access to a Linux server?
7. What is the purpose of a routing table?
8. How do you monitor network traffic?
9. How do you troubleshoot DNS issues?
10. How do you transfer files securely between servers?

## Interview Question Answers
1. Use `ip a` or `ifconfig` to view interface configs.
2. Use `ping`, `traceroute`, and check logs/firewall rules for troubleshooting.
3. TCP is connection-oriented and reliable; UDP is connectionless and faster but less reliable.
4. Use `netstat -tulnp` or `ss -tulnp` to see processes and ports.
5. Use `nslookup` or `dig` for DNS lookups.
6. Use SSH with key-based auth, disable root login, and use firewalls.
7. The routing table determines how packets are forwarded between networks.
8. Use `iftop`, `nload`, `tcpdump`, or `wireshark` to monitor traffic.
9. Check `/etc/resolv.conf`, test with `dig`, and verify DNS server reachability.
10. Use `scp` or `rsync` over SSH for secure file transfer.
