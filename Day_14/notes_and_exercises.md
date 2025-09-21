# Day 14: System Monitoring & Log Management

## Notes
- **Why Monitoring & Logs Matter:**
  - Proactive monitoring prevents outages and enables fast troubleshooting.
  - Log management is essential for auditing, compliance, and root cause analysis.

- **System Monitoring Tools:**
  - `top`, `htop`: Real-time process/resource monitoring
  - `vmstat`: Virtual memory stats
  - `iostat`: CPU and disk I/O stats
  - `free -h`: Memory usage
  - `uptime`: System load
  - `dstat`, `sar`: Advanced stats (may need install)
  - `df -h`, `du -sh`: Disk usage
  - `watch`: Repeatedly run a command

- **Log Management Tools:**
  - `journalctl`: View systemd logs
  - `tail -f /var/log/syslog` or `/var/log/messages`: Live log monitoring
  - `grep`, `awk`, `less`, `more`: Log searching and filtering
  - `logrotate`: Automated log rotation and cleanup

- **Best Practices:**
  - Set up alerts for resource thresholds
  - Rotate and archive logs regularly
  - Monitor key metrics (CPU, memory, disk, network)
  - Use centralized logging for large environments
  - Secure log files (permissions, access control)

- **Diagram:**
  - ![Monitoring Diagram](https://upload.wikimedia.org/wikipedia/commons/2/2b/Monitoring_stack.png)

## In-Depth Explanations & Scenarios
- **Key Metrics to Monitor:**
  - CPU: Load average, per-core usage
  - Memory: Used, free, cache, swap
  - Disk: Usage, I/O, latency
  - Network: Bandwidth, errors, dropped packets
- **Real-World Example:**
  - Use `top`/`htop` for live troubleshooting; `sar` for historical data; `journalctl` for systemd logs.
- **Centralized Monitoring:**
  - Tools: Nagios, Zabbix, Prometheus, Grafana, ELK stack
  - Aggregate logs from multiple servers for alerting and analysis.
- **Log Rotation Deep Dive:**
  - `logrotate` config: `/etc/logrotate.conf`, `/etc/logrotate.d/`
  - Set retention, compression, and post-rotate actions.
- **Best Practices (Expanded):**
  - Set up alerting for critical metrics (CPU, disk, memory, network)
  - Regularly review and prune old logs
  - Secure logs with proper permissions and encryption if needed

## Sample Exercises
1. Monitor CPU and memory usage in real time.
2. Find the top 5 memory-consuming processes.
3. Watch disk usage grow in a directory.
4. View and filter system logs for errors.
5. Set up log rotation for a custom log file.

## Solutions
1. `top` or `htop`
2. `ps aux --sort=-%mem | head -6`
3. `watch -n 2 df -h /mydir`
4. `journalctl | grep error` or `grep error /var/log/syslog`
5. Edit `/etc/logrotate.conf` or add a config in `/etc/logrotate.d/`

## Sample Interview Questions
1. What tools do you use to monitor system resources?
2. How do you find and troubleshoot high CPU usage?
3. How do you monitor logs in real time?
4. What is log rotation and why is it important?
5. How do you set up alerts for resource usage?
6. How do you secure log files?
7. What is the difference between `top` and `htop`?
8. How do you monitor disk I/O?
9. How do you centralize logs from multiple servers?
10. How do you automate log cleanup?

## Interview Question Answers
1. Use `top`, `htop`, `vmstat`, `iostat`, `free`, `df`, and `sar` for monitoring.
2. Use `top`/`htop` to identify processes, then investigate with `ps`, `strace`, or logs.
3. Use `tail -f` or `journalctl -f` for real-time log monitoring.
4. Log rotation archives old logs and prevents disk space issues; managed by `logrotate`.
5. Use monitoring tools with alerting (Nagios, Prometheus) or scripts with `mail`/`sendmail`.
6. Restrict permissions, use secure storage, and audit access to log files.
7. `htop` is interactive and user-friendly; `top` is standard and available everywhere.
8. Use `iostat`, `dstat`, or `sar` to monitor disk I/O.
9. Use centralized logging solutions (ELK stack, rsyslog, syslog-ng).
10. Use `logrotate` to automate log cleanup and rotation.
