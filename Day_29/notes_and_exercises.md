# Day 29: Advanced Monitoring & Observability

## Notes
- **Three pillars:** Logs, Metrics, Traces
- **Linux focus:** journald/rsyslog, node metrics, tracing syscalls/perf

### Logs
- `journalctl -xe`, `journalctl -u <svc> -f`
- Structured logs (JSON) and rotation: `logrotate`

### Metrics
- System: `sar`, `vmstat`, `iostat`, `mpstat`, `pidstat`
- Exporters: node_exporter concept (CPU, mem, FS, network)

### Tracing & Profiling
- `strace -p <pid>` syscalls; `ltrace` libraries
- `perf top`, `perf record/report` flame data
- eBPF (intro): `bpftrace -l`, `bpftrace -e 'tracepoint:syscalls:sys_enter_openat { printf("%s\n", comm); }'`

## Hands-on Labs
1. Capture and filter service logs with `journalctl` and rotate them.
2. Collect 10 minutes of performance metrics using `sar` and summarize.
3. Use `strace` and `perf` to triage a CPU-hot process.

## Interview-style Questions
1. When do you choose `strace` vs `perf`?
2. How do you detect memory pressure using only Linux tools?
3. What are key metrics for web service SLOs at host level?

## Solutions (Brief)
1. `strace` = syscall behavior; `perf` = CPU profiling and hotspots.
2. `vmstat si/so`, `sar -r`, `pressure-stall-information` (if PSI enabled), OOM logs.
3. CPU, memory, disk I/O latency, network errors, file descriptor usage, saturation.

