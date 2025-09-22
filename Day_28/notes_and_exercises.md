# Day 28: Linux Containers & Namespaces Internals

## Notes
- **Namespaces:** pid, net, mnt, user, ipc, uts → isolate resources.
- **cgroups:** limit/measure CPU, memory, I/O; v1 vs v2.
- **Capabilities:** fine-grained root privileges; drop what you don't need.

### Explore Namespaces
```
ls -l /proc/$$/ns
unshare --uts --ipc --mount --pid --net --fork --mount-proc bash
hostname container-1
ps -ef        # separate PID view
ip link       # separate network ns when provided
```

### cgroups quickstart
```
mount | grep cgroup
systemd-cgls
systemd-cgtop
```

### Docker mappings
- PID/user/net ns map to container isolation; cgroups set via `--memory`, `--cpus`.
- Security: run as non-root, read-only FS, drop capabilities, seccomp profiles.

## Hands-on Labs
1. Use `unshare` to build a minimal container shell with separate hostname and PID tree.
2. Limit a stress process with cgroups (or `systemd-run --scope -p MemoryMax=256M`).
3. Start an nginx container with `--read-only`, `--cap-drop=ALL`, user 1000.

## Interview-style Questions
1. Difference between VMs and containers at the kernel level?
2. How do namespaces and cgroups work together?
3. How do you harden a container at runtime?

## Solutions (Brief)
1. VMs virtualize hardware; containers share the kernel; isolation via ns/cgroups.
2. Namespaces isolate; cgroups control resources; together provide multi-tenant safety.
3. Non-root users, read-only FS, capability drops, seccomp, minimal images.

