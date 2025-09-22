# Day 27: Linux Performance Tuning & Optimization

## Notes
- **Goals:** Improve system performance and stability through measurement and targeted tuning.
- **Measure First:** Always baseline before tuning.

### CPU
- Tools: `top`, `htop`, `mpstat`, `pidstat`, `sar`, `perf stat`
- Affinity: `taskset -cp <cpus> <pid>`
- Scaling governor: `cpupower frequency-info`, `cpupower frequency-set -g performance`

### Memory
- Tools: `free -h`, `vmstat 1`, `smem`, `sar -r`, `/proc/meminfo`
- Overcommit: `sysctl vm.overcommit_memory=1`, `vm.overcommit_ratio`
- Swappiness: `sysctl vm.swappiness=10`
- Transparent Huge Pages (THP): `cat /sys/kernel/mm/transparent_hugepage/enabled`

### I/O & Filesystems
- Tools: `iostat -x 1`, `iotop`, `blktrace`, `fio`
- Schedulers: `cat /sys/block/sda/queue/scheduler` (mq-deadline, none, bfq)
- Readahead: `blockdev --getra /dev/sda`, `blockdev --setra 1024 /dev/sda`
- Mount options: `noatime`, `nodiratime`, `discard` (SSD), `commit=`, `barrier=` (careful)

### Network
- Tools: `ss -s`, `ss -tulnp`, `ethtool`, `sar -n DEV 1`, `ping`, `traceroute`
- TCP tuning (examples):
```
sysctl -w net.core.somaxconn=1024
sysctl -w net.ipv4.tcp_fin_timeout=15
sysctl -w net.ipv4.tcp_tw_reuse=1
sysctl -w net.core.netdev_max_backlog=4096
```

### Kernel Parameters (sysctl)
- Inspect: `sysctl -a | less`
- Persist: add to `/etc/sysctl.d/99-tuning.conf`, then `sysctl --system`

### Methodology
1) Baseline → 2) Identify bottleneck → 3) Change one variable → 4) Re-test → 5) Document

## Hands-on Labs
1. Measure CPU, memory, disk, and network for 5 minutes; save logs with `sar`.
2. Run `fio` read/write tests; change readahead and compare.
3. Benchmark web server QPS before/after `somaxconn` and backlog tuning.

## Interview-style Questions
1. How do you identify I/O bottlenecks? Which metrics matter?
2. Explain swappiness and its impact.
3. When would you change TCP backlog parameters?

## Solutions (Brief)
1. `iostat -x`, high `await`/`%util` → disk bottleneck; `fio` to isolate.
2. Lower swappiness reduces swapping; set via `vm.swappiness`.
3. High connection rates with SYN backlog drops; increase `somaxconn`/`netdev_max_backlog`.

