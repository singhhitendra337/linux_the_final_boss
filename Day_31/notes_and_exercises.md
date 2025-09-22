# Day 31: Advanced Troubleshooting & Kernel Interaction

## Learning Objectives
By the end of this day, you will:
- Master advanced Linux troubleshooting methodologies
- Use kernel interfaces for deep system analysis
- Implement eBPF for custom monitoring and debugging
- Troubleshoot complex system performance issues
- Interact directly with kernel subsystems for problem resolution

## Table of Contents
1. [Troubleshooting Methodology](#troubleshooting-methodology)
2. [System Call Tracing](#system-call-tracing)
3. [Performance Profiling](#performance-profiling)
4. [Kernel Interfaces](#kernel-interfaces)
5. [Advanced eBPF Techniques](#advanced-ebpf-techniques)
6. [Filesystem Troubleshooting](#filesystem-troubleshooting)
7. [Network Debugging](#network-debugging)
8. [Exercises](#exercises)
9. [Solutions](#solutions)
10. [Interview Questions](#interview-questions)

---

## Troubleshooting Methodology

### Systematic Approach
```
1. DEFINE the problem
   ↓
2. GATHER information
   ↓
3. HYPOTHESIZE root cause
   ↓
4. TEST hypothesis
   ↓
5. IMPLEMENT solution
   ↓
6. VERIFY fix
   ↓
7. DOCUMENT findings
```

### Problem Classification
- **Performance Issues**: Slow response, high resource usage
- **Availability Issues**: Service failures, crashes
- **Functional Issues**: Incorrect behavior, data corruption
- **Security Issues**: Unauthorized access, privilege escalation

### Information Gathering Tools
```bash
# System overview
uname -a
uptime
who
last

# Resource utilization
top -b -n1
free -h
df -h
iostat 1 5

# Process analysis
ps aux --sort=-%cpu | head -20
ps aux --sort=-%mem | head -20
lsof | wc -l

# Network status
ss -tuln
netstat -i
ip route show
```

---

## System Call Tracing

### Advanced strace Usage
```bash
# Comprehensive tracing
strace -f -e trace=all -o trace.log -p <pid>

# Filter by system call categories
strace -e trace=file -p <pid>        # File operations
strace -e trace=network -p <pid>     # Network operations
strace -e trace=process -p <pid>     # Process management
strace -e trace=memory -p <pid>      # Memory operations
strace -e trace=signal -p <pid>      # Signal handling

# Time analysis
strace -T -p <pid>                   # Show time spent in syscalls
strace -r -p <pid>                   # Show relative timestamps
strace -tt -p <pid>                  # Show absolute timestamps

# Statistical analysis
strace -c -p <pid>                   # Summary statistics
strace -C -p <pid>                   # Like -c but also regular output

# Advanced filtering
strace -e trace=!clock_gettime,gettimeofday -p <pid>  # Exclude noisy calls
strace -e trace=openat -e status=successful -p <pid>  # Only successful calls
```

### strace Analysis Scripts
```bash
# Analyze strace output
cat > analyze_strace.sh << 'EOF'
#!/bin/bash
TRACE_FILE="$1"

if [ ! -f "$TRACE_FILE" ]; then
    echo "Usage: $0 <strace_output_file>"
    exit 1
fi

echo "=== Top System Calls ==="
grep -o '^[a-z_]*(' "$TRACE_FILE" | sort | uniq -c | sort -nr | head -10

echo "\n=== Failed System Calls ==="
grep " = -1 " "$TRACE_FILE" | head -10

echo "\n=== Slow System Calls (>100ms) ==="
grep "<[0-9]\+\.[0-9]\+>" "$TRACE_FILE" | awk '$NF > 0.1 {print}' | head -10

echo "\n=== File Access Patterns ==="
grep -E '(open|read|write)' "$TRACE_FILE" | cut -d'"' -f2 | sort | uniq -c | sort -nr | head -10

echo "\n=== Network Activity ==="
grep -E '(socket|connect|bind|listen|accept)' "$TRACE_FILE" | wc -l
EOF

chmod +x analyze_strace.sh
```

### ltrace for Library Calls
```bash
# Library call tracing
ltrace -f -e malloc,free -p <pid>    # Memory allocation
ltrace -e 'printf*' -p <pid>         # Printf family functions
ltrace -c -p <pid>                   # Statistical summary
ltrace -T -p <pid>                   # Show time spent

# Custom library tracing
ltrace -e '@libssl.so*' -p <pid>     # SSL library calls
ltrace -e '@libc.so*' -p <pid>       # C library calls
```

---

## Performance Profiling

### Advanced perf Usage
```bash
# CPU profiling
perf record -g -F 99 -p <pid> sleep 30
perf report --stdio
perf report --tui

# Event-based profiling
perf record -e cycles,instructions,cache-misses -p <pid> sleep 30
perf record -e cpu-clock,task-clock -p <pid> sleep 30

# System-wide profiling
perf record -a -g sleep 10
perf top -a

# Memory profiling
perf record -e cache-misses,cache-references -p <pid> sleep 30
perf record -e page-faults,minor-faults,major-faults -p <pid> sleep 30

# I/O profiling
perf record -e block:block_rq_issue,block:block_rq_complete -a sleep 30
```

### Flame Graph Generation
```bash
# Install FlameGraph tools
git clone https://github.com/brendangregg/FlameGraph
cd FlameGraph

# Generate flame graph
perf record -F 99 -g -p <pid> sleep 30
perf script | ./stackcollapse-perf.pl | ./flamegraph.pl > flamegraph.svg

# Interactive flame graph
perf record -F 99 -g -p <pid> sleep 30
perf script | ./stackcollapse-perf.pl | ./flamegraph.pl --title="CPU Flame Graph" > cpu_flamegraph.svg
```

### Custom Performance Analysis
```bash
# Performance analysis script
cat > perf_analysis.sh << 'EOF'
#!/bin/bash
PID="$1"
DURATION="${2:-30}"
OUTPUT_DIR="/tmp/perf_analysis_$(date +%Y%m%d_%H%M%S)"

mkdir -p "$OUTPUT_DIR"

echo "Starting performance analysis for PID $PID (${DURATION}s)"

# CPU profiling
echo "Collecting CPU profile..."
perf record -g -F 99 -p $PID -o "$OUTPUT_DIR/cpu.data" sleep $DURATION &
CPU_PID=$!

# Memory events
echo "Collecting memory events..."
perf record -e cache-misses,page-faults -p $PID -o "$OUTPUT_DIR/memory.data" sleep $DURATION &
MEM_PID=$!

# I/O events
echo "Collecting I/O events..."
perf record -e syscalls:sys_enter_read,syscalls:sys_enter_write -p $PID -o "$OUTPUT_DIR/io.data" sleep $DURATION &
IO_PID=$!

wait $CPU_PID $MEM_PID $IO_PID

# Generate reports
echo "Generating reports..."
perf report -i "$OUTPUT_DIR/cpu.data" --stdio > "$OUTPUT_DIR/cpu_report.txt"
perf report -i "$OUTPUT_DIR/memory.data" --stdio > "$OUTPUT_DIR/memory_report.txt"
perf report -i "$OUTPUT_DIR/io.data" --stdio > "$OUTPUT_DIR/io_report.txt"

echo "Analysis complete. Results in: $OUTPUT_DIR"
EOF

chmod +x perf_analysis.sh
```

---

## Kernel Interfaces

### /proc Filesystem Deep Dive
```bash
# Process information
cat /proc/<pid>/status        # Process status
cat /proc/<pid>/cmdline       # Command line
cat /proc/<pid>/environ       # Environment variables
cat /proc/<pid>/limits        # Resource limits
cat /proc/<pid>/maps          # Memory mappings
cat /proc/<pid>/fd/           # File descriptors
cat /proc/<pid>/net/          # Network information

# System information
cat /proc/cpuinfo            # CPU information
cat /proc/meminfo            # Memory information
cat /proc/loadavg            # Load average
cat /proc/uptime             # System uptime
cat /proc/version            # Kernel version
cat /proc/modules            # Loaded modules

# Performance counters
cat /proc/stat               # System statistics
cat /proc/diskstats          # Disk statistics
cat /proc/net/dev            # Network device statistics
cat /proc/interrupts         # Interrupt statistics
```

### /sys Filesystem Exploration
```bash
# Device information
ls /sys/class/net/           # Network devices
ls /sys/class/block/         # Block devices
ls /sys/class/thermal/       # Thermal zones

# Hardware information
cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor
cat /sys/class/thermal/thermal_zone0/temp
cat /sys/block/sda/queue/scheduler

# Power management
cat /sys/power/state
echo mem > /sys/power/state  # Suspend to RAM
```

### sysctl Kernel Parameters
```bash
# View all parameters
sysctl -a | less

# Specific parameter groups
sysctl -a | grep vm          # Virtual memory
sysctl -a | grep net         # Network
sysctl -a | grep kernel      # Kernel

# Runtime changes
sysctl vm.swappiness=10
sysctl net.ipv4.ip_forward=1

# Persistent changes
echo "vm.swappiness = 10" >> /etc/sysctl.d/99-custom.conf
sysctl --system
```

### dmesg Analysis
```bash
# View kernel messages
dmesg -T                     # Human-readable timestamps
dmesg -l err,crit,alert,emerg # Error messages only
dmesg -f daemon              # Daemon facility only
dmesg -w                     # Follow new messages

# Filter by subsystem
dmesg | grep -i usb
dmesg | grep -i network
dmesg | grep -i memory
dmesg | grep -i cpu

# Analyze boot messages
dmesg | grep -E "(error|fail|warn)"
```

---

## Advanced eBPF Techniques

### System Monitoring with eBPF
```bash
# Process monitoring
cat > process_monitor.bt << 'EOF'
#!/usr/bin/env bpftrace

BEGIN {
    printf("Monitoring process events...\n");
}

tracepoint:sched:sched_process_exec {
    printf("EXEC: %s (PID %d) executed %s\n", 
           comm, pid, str(args->filename));
}

tracepoint:sched:sched_process_exit {
    printf("EXIT: %s (PID %d) exited with code %d\n", 
           comm, pid, args->code);
}

tracepoint:sched:sched_process_fork {
    printf("FORK: %s (PID %d) forked child %d\n", 
           comm, pid, args->child_pid);
}
EOF

chmod +x process_monitor.bt
sudo ./process_monitor.bt
```

### File System Monitoring
```bash
# File I/O latency tracking
cat > file_io_latency.bt << 'EOF'
#!/usr/bin/env bpftrace

kprobe:vfs_read,
kprobe:vfs_write {
    @start[tid] = nsecs;
    @op[tid] = func;
}

kretprobe:vfs_read,
kretprobe:vfs_write {
    $duration = nsecs - @start[tid];
    $op = @op[tid];
    
    delete(@start[tid]);
    delete(@op[tid]);
    
    @latency_ms = hist($duration / 1000000);
    
    if ($duration > 100000000) {  // > 100ms
        printf("SLOW I/O: %s %s %d ms\n", 
               comm, 
               $op == "vfs_read" ? "READ" : "WRITE",
               $duration / 1000000);
    }
}

interval:s:30 {
    printf("\n=== I/O Latency Distribution (ms) ===\n");
    print(@latency_ms);
    clear(@latency_ms);
}
EOF

chmod +x file_io_latency.bt
sudo ./file_io_latency.bt
```

### Network Debugging with eBPF
```bash
# TCP connection tracking
cat > tcp_monitor.bt << 'EOF'
#!/usr/bin/env bpftrace

kprobe:tcp_v4_connect {
    $sk = (struct sock *)arg0;
    $inet = (struct inet_sock *)$sk;
    
    printf("TCP CONNECT: %s -> %s:%d\n",
           comm,
           ntop(AF_INET, $inet->inet_daddr),
           $inet->inet_dport);
    
    @connections[comm] = count();
}

kprobe:tcp_close {
    printf("TCP CLOSE: %s\n", comm);
}

kprobe:tcp_retransmit_skb {
    printf("TCP RETRANSMIT: %s\n", comm);
    @retransmits[comm] = count();
}

interval:s:10 {
    printf("\n=== Connection Summary ===\n");
    print(@connections);
    
    printf("\n=== Retransmission Summary ===\n");
    print(@retransmits);
    
    clear(@connections);
    clear(@retransmits);
}
EOF

chmod +x tcp_monitor.bt
sudo ./tcp_monitor.bt
```

### Custom eBPF Tools
```bash
# Memory allocation tracker
cat > memory_tracker.bt << 'EOF'
#!/usr/bin/env bpftrace

kprobe:__kmalloc {
    @allocs[comm] = sum(arg0);
    @alloc_count[comm] = count();
}

kprobe:kfree {
    @frees[comm] = count();
}

interval:s:5 {
    printf("\n=== Memory Allocations (bytes) ===\n");
    print(@allocs, 10);
    
    printf("\n=== Allocation Counts ===\n");
    print(@alloc_count, 10);
    
    printf("\n=== Free Counts ===\n");
    print(@frees, 10);
    
    clear(@allocs);
    clear(@alloc_count);
    clear(@frees);
}
EOF

chmod +x memory_tracker.bt
sudo ./memory_tracker.bt
```

---

## Filesystem Troubleshooting

### Inode and File Handle Issues
```bash
# Check inode usage
df -i

# Find large directories
du -sh /* | sort -hr | head -10

# Find files with many hard links
find / -type f -links +1 2>/dev/null

# Identify deleted but open files
lsof | grep "(deleted)"
lsof +L1  # Files with link count less than 1

# Check file system errors
dmesg | grep -i "ext4\|xfs\|btrfs"
fsck -n /dev/sda1  # Read-only check
```

### Disk Space Recovery
```bash
# Find large files
find / -type f -size +100M 2>/dev/null | head -20

# Find old files
find / -type f -mtime +30 -size +10M 2>/dev/null

# Recover space from deleted but open files
cat > recover_space.sh << 'EOF'
#!/bin/bash

echo "=== Deleted but open files ==="
lsof | grep "(deleted)" | while read line; do
    pid=$(echo $line | awk '{print $2}')
    fd=$(echo $line | awk '{print $4}' | sed 's/[a-z]$//')
    size=$(ls -la /proc/$pid/fd/$fd 2>/dev/null | awk '{print $5}')
    
    if [ "$size" -gt 1048576 ]; then  # > 1MB
        echo "PID: $pid, FD: $fd, Size: $size bytes"
        echo "Command: $(ps -p $pid -o comm=)"
        echo "To recover: truncate /proc/$pid/fd/$fd --size 0"
        echo "Or restart process: kill -HUP $pid"
        echo "---"
    fi
done
EOF

chmod +x recover_space.sh
./recover_space.sh
```

### File System Performance
```bash
# I/O statistics
iostat -x 1 10

# Per-process I/O
iotop -a
pidstat -d 1 10

# File system cache statistics
cat /proc/meminfo | grep -E "(Cached|Buffers|Dirty)"

# Clear file system caches (use with caution)
echo 1 > /proc/sys/vm/drop_caches  # Page cache
echo 2 > /proc/sys/vm/drop_caches  # Dentries and inodes
echo 3 > /proc/sys/vm/drop_caches  # All caches
```

---

## Network Debugging

### Advanced Network Analysis
```bash
# Connection states
ss -tuln
ss -o state established
ss -i  # Show internal TCP information

# Network statistics
cat /proc/net/netstat
cat /proc/net/snmp

# Interface statistics
cat /proc/net/dev
ip -s link show

# Routing analysis
ip route get 8.8.8.8
traceroute -n 8.8.8.8

# ARP table
ip neigh show
arp -a
```

### Packet Capture and Analysis
```bash
# tcpdump examples
tcpdump -i eth0 -n host 192.168.1.100
tcpdump -i eth0 -n port 80
tcpdump -i eth0 -n -c 100 -w capture.pcap

# Network troubleshooting script
cat > network_debug.sh << 'EOF'
#!/bin/bash

TARGET="$1"
if [ -z "$TARGET" ]; then
    echo "Usage: $0 <target_host>"
    exit 1
fi

echo "=== Network Debugging for $TARGET ==="

echo "\n--- Ping Test ---"
ping -c 4 $TARGET

echo "\n--- Traceroute ---"
traceroute -n $TARGET

echo "\n--- DNS Resolution ---"
nslookup $TARGET

echo "\n--- Port Scan (common ports) ---"
nmap -p 22,80,443,3389 $TARGET

echo "\n--- Local Network Configuration ---"
ip addr show
ip route show

echo "\n--- Active Connections ---"
ss -tuln | grep -E ":(80|443|22|3389)"
EOF

chmod +x network_debug.sh
```

---

## Exercises

### Exercise 1: System Call Analysis
**Objective**: Debug application performance using system call tracing.

**Tasks**:
1. Trace system calls for a slow application
2. Identify performance bottlenecks
3. Analyze file access patterns
4. Generate performance recommendations

### Exercise 2: Kernel Performance Profiling
**Objective**: Use perf to identify CPU hotspots.

**Tasks**:
1. Profile CPU-intensive application
2. Generate flame graphs
3. Identify optimization opportunities
4. Measure improvement after optimization

### Exercise 3: eBPF Monitoring Tool
**Objective**: Create custom monitoring with eBPF.

**Tasks**:
1. Write bpftrace script for specific monitoring need
2. Track system events in real-time
3. Generate alerts for anomalous behavior
4. Create dashboard for monitoring data

### Exercise 4: Filesystem Troubleshooting
**Objective**: Diagnose and resolve filesystem issues.

**Tasks**:
1. Identify disk space issues
2. Find and resolve inode exhaustion
3. Recover space from deleted open files
4. Optimize filesystem performance

### Exercise 5: Network Performance Debugging
**Objective**: Troubleshoot network connectivity and performance.

**Tasks**:
1. Diagnose network connectivity issues
2. Analyze packet loss and latency
3. Identify network bottlenecks
4. Implement network performance optimizations

---

## Solutions

### Solution 1: System Call Analysis
```bash
# Comprehensive syscall analysis
strace -f -T -e trace=all -o app_trace.log -p <pid> &
STRACE_PID=$!
sleep 60
kill $STRACE_PID

# Analyze results
cat > analyze_syscalls.sh << 'EOF'
#!/bin/bash
TRACE_FILE="app_trace.log"

echo "=== Top 10 System Calls ==="
grep -o '^[a-z_]*(' "$TRACE_FILE" | sort | uniq -c | sort -nr | head -10

echo "\n=== Slow System Calls (>10ms) ==="
grep "<[0-9]\+\.[0-9]\+>" "$TRACE_FILE" | awk '$NF > 0.01 {print}' | head -10

echo "\n=== Failed System Calls ==="
grep " = -1 " "$TRACE_FILE" | head -10

echo "\n=== File Access Summary ==="
grep -E 'open.*=' "$TRACE_FILE" | grep -v ENOENT | wc -l
echo "Successful file opens"

grep -E 'read.*=' "$TRACE_FILE" | awk '{sum+=$NF} END {print "Total bytes read: " sum}'
grep -E 'write.*=' "$TRACE_FILE" | awk '{sum+=$NF} END {print "Total bytes written: " sum}'
EOF

chmod +x analyze_syscalls.sh
./analyze_syscalls.sh
```

### Solution 2: Kernel Performance Profiling
```bash
# Complete performance profiling
perf record -g -F 99 -p <pid> sleep 60

# Generate comprehensive report
perf report --stdio > cpu_profile.txt

# Create flame graph
perf script | stackcollapse-perf.pl | flamegraph.pl > cpu_flamegraph.svg

# Memory profiling
perf record -e cache-misses,cache-references -p <pid> sleep 60
perf report --stdio > memory_profile.txt

# Analysis summary
echo "CPU Profile: cpu_profile.txt"
echo "Flame Graph: cpu_flamegraph.svg"
echo "Memory Profile: memory_profile.txt"
```

### Solution 3: eBPF Monitoring Tool
```bash
# Comprehensive system monitor
cat > system_monitor.bt << 'EOF'
#!/usr/bin/env bpftrace

BEGIN {
    printf("Starting comprehensive system monitoring...\n");
}

// Process events
tracepoint:sched:sched_process_exec {
    @process_starts[comm] = count();
}

// File operations
tracepoint:syscalls:sys_enter_openat {
    @file_opens[comm] = count();
}

// Network connections
kprobe:tcp_v4_connect {
    @tcp_connects[comm] = count();
}

// Memory allocations
kprobe:__kmalloc {
    @memory_allocs[comm] = sum(arg0);
}

// Report every 30 seconds
interval:s:30 {
    time();
    printf("\n=== Process Starts ===\n");
    print(@process_starts, 5);
    clear(@process_starts);
    
    printf("\n=== File Opens ===\n");
    print(@file_opens, 5);
    clear(@file_opens);
    
    printf("\n=== TCP Connections ===\n");
    print(@tcp_connects, 5);
    clear(@tcp_connects);
    
    printf("\n=== Memory Allocations (bytes) ===\n");
    print(@memory_allocs, 5);
    clear(@memory_allocs);
    
    printf("\n" . "="*50 . "\n");
}
EOF

chmod +x system_monitor.bt
sudo ./system_monitor.bt
```

### Solution 4: Filesystem Troubleshooting
```bash
# Comprehensive filesystem analysis
cat > fs_troubleshoot.sh << 'EOF'
#!/bin/bash

echo "=== Filesystem Troubleshooting Report ==="
echo "Generated: $(date)"

echo "\n=== Disk Usage ==="
df -h

echo "\n=== Inode Usage ==="
df -i

echo "\n=== Large Files (>100MB) ==="
find / -type f -size +100M 2>/dev/null | head -10

echo "\n=== Deleted but Open Files ==="
lsof | grep "(deleted)" | head -10

echo "\n=== Directory Sizes ==="
du -sh /* 2>/dev/null | sort -hr | head -10

echo "\n=== Filesystem Errors ==="
dmesg | grep -i "ext4\|xfs\|btrfs" | tail -10

echo "\n=== I/O Statistics ==="
iostat -x 1 1
EOF

chmod +x fs_troubleshoot.sh
./fs_troubleshoot.sh
```

### Solution 5: Network Performance Debugging
```bash
# Network performance analysis
cat > network_perf_debug.sh << 'EOF'
#!/bin/bash

TARGET="${1:-8.8.8.8}"

echo "=== Network Performance Analysis for $TARGET ==="

echo "\n--- Connectivity Test ---"
ping -c 10 $TARGET | tail -1

echo "\n--- Traceroute Analysis ---"
traceroute -n $TARGET

echo "\n--- Bandwidth Test ---"
iperf3 -c $TARGET -t 10 2>/dev/null || echo "iperf3 server not available"

echo "\n--- Local Network Statistics ---"
ss -i | grep -A 5 "ESTAB"

echo "\n--- Network Interface Statistics ---"
cat /proc/net/dev | grep -v "lo:"

echo "\n--- TCP Statistics ---"
cat /proc/net/netstat | grep TcpExt

echo "\n--- Active Connections by State ---"
ss -s
EOF

chmod +x network_perf_debug.sh
./network_perf_debug.sh
```

---

## Interview Questions

### Basic Level

1. **Q**: A process is stuck in D state. What does this mean and how do you debug it?
   **A**: D state is uninterruptible sleep, usually waiting for I/O. Debug with `iostat`, check `dmesg` for hardware errors, examine `/proc/<pid>/stack`, and investigate storage subsystem.

2. **Q**: How do you find which process holds a deleted large file?
   **A**: Use `lsof | grep "(deleted)"` or `lsof +L1`. The file descriptor is still open, preventing space reclamation. Restart the process or truncate via `/proc/<pid>/fd/<fd>`.

3. **Q**: What signals would you try before `kill -9`?
   **A**: SIGTERM (15) for graceful shutdown, SIGINT (2) for interrupt, SIGHUP (1) for reload. SIGKILL (9) is last resort as it cannot be caught or ignored.

4. **Q**: How do you analyze system call patterns for a slow application?
   **A**: Use `strace -T -c -p <pid>` for timing and statistics, filter with `-e trace=file,network`, and analyze output for bottlenecks and failed calls.

### Intermediate Level

5. **Q**: Explain the difference between perf and strace for performance analysis.
   **A**: perf profiles CPU usage and hardware events with low overhead, showing where time is spent. strace traces system calls with higher overhead, showing what the application is doing.

6. **Q**: How do you use eBPF for custom monitoring without impacting performance?
   **A**: eBPF runs in kernel space with minimal overhead, use bpftrace for quick scripts, implement sampling for high-frequency events, and use maps for efficient data collection.

7. **Q**: What kernel interfaces would you check for memory pressure investigation?
   **A**: `/proc/meminfo` for memory stats, `/proc/pressure/memory` for PSI, `/proc/vmstat` for VM statistics, and `dmesg` for OOM killer messages.

8. **Q**: How do you troubleshoot high system load with low CPU usage?
   **A**: Check I/O wait with `iostat`, examine process states with `ps aux`, look for D state processes, check storage performance, and investigate network I/O.

### Advanced Level

9. **Q**: Describe advanced techniques for debugging kernel panics.
    **A**: Enable kdump, analyze crash dumps with crash utility, check hardware logs, examine kernel logs before panic, use kernel debugging options, and analyze stack traces.

10. **Q**: How do you implement custom kernel tracing for specific subsystems?
    **A**: Use ftrace with function tracer, create custom tracepoints, implement eBPF programs for specific kernel functions, and use SystemTap for complex tracing scenarios.

11. **Q**: Explain techniques for debugging memory leaks in kernel space.
    **A**: Use kmemleak detector, implement custom eBPF memory tracking, analyze /proc/slabinfo, use KASAN for debugging, and trace allocation/deallocation patterns.

12. **Q**: How do you troubleshoot performance issues in containerized environments?
    **A**: Check cgroup limits, analyze container-specific metrics, use nsenter for namespace debugging, monitor host-level resources, and consider container runtime overhead.

### Expert Level

13. **Q**: Describe advanced eBPF techniques for production debugging.
    **A**: Implement CO-RE (Compile Once Run Everywhere), use BTF for portability, implement complex data structures with maps, use tail calls for program chaining, and handle verifier constraints.

14. **Q**: How do you debug complex distributed system performance issues?
    **A**: Implement distributed tracing, correlate metrics across systems, use eBPF for network analysis, implement custom monitoring for specific protocols, and analyze cross-system dependencies.

15. **Q**: Explain kernel bypass techniques and their debugging challenges.
    **A**: DPDK, SPDK, io_uring bypass kernel networking/storage stacks. Debug with specialized tools, monitor hardware directly, analyze application-specific metrics, and understand hardware interactions.

---

## Troubleshooting Cheat Sheet

### Quick Diagnostics
```bash
# System overview
uptime && free -h && df -h

# Process analysis
ps aux --sort=-%cpu | head -10
ps aux --sort=-%mem | head -10

# I/O analysis
iostat -x 1 5
iotop -a

# Network analysis
ss -tuln
netstat -i

# Memory analysis
cat /proc/meminfo | grep -E "(MemFree|MemAvailable|Cached|Buffers)"
```

---

## Community and Support

- **Discord Community**: [Join our Discord](https://discord.gg/linux-learning) for troubleshooting discussions
- **Google Group**: [Linux Troubleshooting Group](https://groups.google.com/g/linux-troubleshooting) for complex issues
- **YouTube Channel**: [Advanced Linux Debugging](https://youtube.com/c/linux-debugging) for video tutorials
- **Kernel Debugging**: Share your custom eBPF tools and debugging techniques

---

## Completion Checklist

- [ ] Master systematic troubleshooting methodology
- [ ] Use advanced system call tracing techniques
- [ ] Implement performance profiling with perf
- [ ] Navigate kernel interfaces effectively
- [ ] Create custom eBPF monitoring tools
- [ ] Troubleshoot filesystem and network issues
- [ ] Complete all practical exercises
- [ ] Practice real-world debugging scenarios
- [ ] Review and answer all interview questions
- [ ] Contribute to community debugging resources

---

**Congratulations!** You have completed the Advanced Linux Track. You now have expert-level knowledge in Linux performance tuning, containerization, observability, security hardening, and advanced troubleshooting techniques.

