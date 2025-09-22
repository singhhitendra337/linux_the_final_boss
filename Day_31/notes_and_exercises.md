# Day 31: Advanced Troubleshooting & Kernel Interaction

## Notes
- **Approach:** Hypothesize → Observe → Isolate → Change one thing → Verify.

### Syscalls & Tracing
- `strace -p <pid>`; filter with `-e trace=network,file`
- `ltrace` for library calls

### Profiling
- `perf top`, `perf record -F 99 -g -p <pid>`; `perf report`
- Flamegraphs (conceptual overview)

### Filesystems & Inodes
- `lsof`, `ls -li`, `stat`, orphaned deletes (`(deleted)` in `/proc/<pid>/fd`)

### Kernel Interfaces
- `/proc`, `/sys` basics; `sysctl`
- Dmesg triage: `dmesg -T | less`

### eBPF (Intro)
- bcc/bpftrace examples (if available)
```
bpftrace -e 'kprobe:do_sys_open { printf("%s %s\n", comm, str(arg1)); }'
```

## Hands-on Labs
1. Use `strace` to debug a failing CLI that returns non-zero.
2. Use `perf` to locate CPU hotspots in a loop program.
3. Recover disk space from a rotated-but-held log file using `lsof`.

## Interview-style Questions
1. A process is stuck in D state. What does it mean and how do you debug it?
2. How do you find which process holds a deleted large file?
3. What signals would you try before `kill -9`, and why?

## Solutions (Brief)
1. Uninterruptible sleep (usually I/O wait); inspect with `iostat`, dmesg, block layer.
2. `lsof | grep deleted`; restart offending process or truncate via `/proc/<pid>/fd`.
3. `SIGTERM` for graceful shutdown, `SIGINT`, `SIGHUP` to reload; `SIGKILL` last resort.

