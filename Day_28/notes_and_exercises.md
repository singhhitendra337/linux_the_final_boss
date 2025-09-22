# Day 28: Linux Containers & Namespaces Internals

## Learning Objectives
By the end of this day, you will:
- Understand Linux namespaces and their role in containerization
- Master cgroups for resource management and isolation
- Implement container security using capabilities and seccomp
- Build containers from scratch using low-level Linux features
- Troubleshoot container runtime issues at the kernel level

## Table of Contents
1. [Container Fundamentals](#container-fundamentals)
2. [Linux Namespaces Deep Dive](#linux-namespaces-deep-dive)
3. [Control Groups (cgroups)](#control-groups-cgroups)
4. [Linux Capabilities](#linux-capabilities)
5. [Container Security](#container-security)
6. [Building Containers from Scratch](#building-containers-from-scratch)
7. [Container Runtime Internals](#container-runtime-internals)
8. [Exercises](#exercises)
9. [Solutions](#solutions)
10. [Interview Questions](#interview-questions)

---

## Container Fundamentals

### What Are Containers?
Containers are lightweight, portable execution environments that:
- Share the host kernel
- Provide process isolation
- Have separate filesystem views
- Control resource usage
- Maintain security boundaries

### Container vs Virtual Machines
```
Virtual Machines:
┌─────────────────────────────────────┐
│           Application               │
├─────────────────────────────────────┤
│           Guest OS                  │
├─────────────────────────────────────┤
│           Hypervisor                │
├─────────────────────────────────────┤
│           Host OS                   │
├─────────────────────────────────────┤
│           Hardware                  │
└─────────────────────────────────────┘

Containers:
┌─────────────────────────────────────┐
│           Application               │
├─────────────────────────────────────┤
│        Container Runtime            │
├─────────────────────────────────────┤
│           Host OS                   │
├─────────────────────────────────────┤
│           Hardware                  │
└─────────────────────────────────────┘
```

### Linux Container Technologies
- **Namespaces**: Process isolation
- **cgroups**: Resource management
- **Capabilities**: Privilege control
- **seccomp**: System call filtering
- **AppArmor/SELinux**: Mandatory access control

---

## Linux Namespaces Deep Dive

### Types of Namespaces

#### 1. PID Namespace
```bash
# View current PID namespace
ls -l /proc/$$/ns/pid

# Create new PID namespace
sudo unshare --pid --fork --mount-proc bash
ps aux  # Shows only processes in this namespace

# Check PID from outside
echo $$  # Shows PID 1 inside namespace
```

#### 2. Network Namespace
```bash
# Create network namespace
sudo ip netns add container1

# List network namespaces
ip netns list

# Execute command in namespace
sudo ip netns exec container1 ip link show

# Create veth pair
sudo ip link add veth0 type veth peer name veth1
sudo ip link set veth1 netns container1

# Configure networking
sudo ip addr add 192.168.1.1/24 dev veth0
sudo ip link set veth0 up

sudo ip netns exec container1 ip addr add 192.168.1.2/24 dev veth1
sudo ip netns exec container1 ip link set veth1 up
sudo ip netns exec container1 ip link set lo up
```

#### 3. Mount Namespace
```bash
# Create mount namespace
sudo unshare --mount bash

# Changes only affect this namespace
mount -t tmpfs tmpfs /tmp
ls /tmp  # Empty in this namespace

# Exit and check
exit
ls /tmp  # Original content preserved
```

#### 4. UTS Namespace (hostname)
```bash
# Create UTS namespace
sudo unshare --uts bash

# Change hostname (only in this namespace)
hostname container-host
hostname  # Shows new hostname

# Exit and check
exit
hostname  # Original hostname preserved
```

#### 5. User Namespace
```bash
# Create user namespace
unshare --user --map-root-user bash
id  # Shows root inside namespace

# Check from outside (different terminal)
ps aux | grep bash  # Shows real user ID
```

#### 6. IPC Namespace
```bash
# Create IPC namespace
sudo unshare --ipc bash

# IPC objects are isolated
ipcs  # Shows empty IPC objects
```

### Namespace Management
```bash
# View all namespaces for a process
ls -l /proc/$$/ns/

# Enter existing namespace
nsenter -t <pid> -n -p -m bash

# Create multiple namespaces at once
unshare --pid --net --mount --uts --ipc --fork bash
```

---

## Control Groups (cgroups)

### cgroups v1 vs v2
```bash
# Check cgroup version
stat -fc %T /sys/fs/cgroup/
# tmpfs = v1, cgroup2fs = v2

# Mount points
mount | grep cgroup
```

### cgroups v1 Management
```bash
# Create cgroup
sudo mkdir /sys/fs/cgroup/memory/mycontainer
sudo mkdir /sys/fs/cgroup/cpu/mycontainer

# Set memory limit (100MB)
echo 104857600 | sudo tee /sys/fs/cgroup/memory/mycontainer/memory.limit_in_bytes

# Set CPU limit (50% of one core)
echo 50000 | sudo tee /sys/fs/cgroup/cpu/mycontainer/cpu.cfs_quota_us
echo 100000 | sudo tee /sys/fs/cgroup/cpu/mycontainer/cpu.cfs_period_us

# Add process to cgroup
echo $$ | sudo tee /sys/fs/cgroup/memory/mycontainer/cgroup.procs
echo $$ | sudo tee /sys/fs/cgroup/cpu/mycontainer/cgroup.procs
```

### cgroups v2 Management
```bash
# Create cgroup
sudo mkdir /sys/fs/cgroup/mycontainer

# Enable controllers
echo "+memory +cpu" | sudo tee /sys/fs/cgroup/cgroup.subtree_control

# Set limits
echo "100M" | sudo tee /sys/fs/cgroup/mycontainer/memory.max
echo "50000 100000" | sudo tee /sys/fs/cgroup/mycontainer/cpu.max

# Add process
echo $$ | sudo tee /sys/fs/cgroup/mycontainer/cgroup.procs
```

### Systemd cgroup Management
```bash
# Run with resource limits
systemd-run --scope -p MemoryMax=256M -p CPUQuota=50% stress --vm 1 --vm-bytes 512M

# View cgroup tree
systemd-cgls

# Monitor resource usage
systemd-cgtop
```

### cgroup Monitoring
```bash
# Memory usage
cat /sys/fs/cgroup/memory/mycontainer/memory.usage_in_bytes
cat /sys/fs/cgroup/memory/mycontainer/memory.stat

# CPU usage
cat /sys/fs/cgroup/cpu/mycontainer/cpuacct.usage
cat /sys/fs/cgroup/cpu/mycontainer/cpu.stat

# I/O statistics
cat /sys/fs/cgroup/blkio/mycontainer/blkio.throttle.io_service_bytes
```

---

## Linux Capabilities

### Understanding Capabilities
```bash
# View process capabilities
getcap /bin/ping
capsh --print

# View capabilities for running process
grep Cap /proc/$$/status

# Decode capability mask
capsh --decode=0000003fffffffff
```

### Common Capabilities
- **CAP_NET_ADMIN**: Network administration
- **CAP_SYS_ADMIN**: System administration
- **CAP_NET_BIND_SERVICE**: Bind to privileged ports
- **CAP_SETUID/SETGID**: Change user/group ID
- **CAP_DAC_OVERRIDE**: Bypass file permissions
- **CAP_KILL**: Send signals to processes

### Managing Capabilities
```bash
# Set file capabilities
sudo setcap cap_net_bind_service=+ep /usr/bin/python3

# Remove capabilities
sudo setcap -r /usr/bin/python3

# Run with specific capabilities
sudo capsh --caps="cap_net_admin+eip" --

# Drop all capabilities
capsh --drop=all --
```

---

## Container Security

### Security Principles
1. **Least Privilege**: Run with minimal required permissions
2. **Defense in Depth**: Multiple security layers
3. **Immutable Infrastructure**: Read-only filesystems
4. **Resource Isolation**: Prevent resource exhaustion
5. **Audit and Monitoring**: Track security events

### Container Hardening Techniques

#### 1. Non-root User
```bash
# Create non-root user in container
docker run --user 1000:1000 nginx

# In Dockerfile
USER 1000
```

#### 2. Read-only Filesystem
```bash
# Read-only root filesystem
docker run --read-only nginx

# With writable tmp
docker run --read-only --tmpfs /tmp nginx
```

#### 3. Capability Dropping
```bash
# Drop all capabilities
docker run --cap-drop=ALL nginx

# Add specific capabilities
docker run --cap-drop=ALL --cap-add=NET_BIND_SERVICE nginx
```

#### 4. seccomp Profiles
```bash
# Default seccomp profile
docker run --security-opt seccomp=default nginx

# Custom seccomp profile
docker run --security-opt seccomp=./custom-seccomp.json nginx

# No seccomp (dangerous)
docker run --security-opt seccomp=unconfined nginx
```

#### 5. AppArmor/SELinux
```bash
# AppArmor profile
docker run --security-opt apparmor=docker-default nginx

# SELinux context
docker run --security-opt label=level:s0:c100,c200 nginx
```

---

## Building Containers from Scratch

### Manual Container Creation
```bash
#!/bin/bash
# create_container.sh - Build container from scratch

set -e

# Configuration
CONTAINER_NAME="mycontainer"
ROOTFS="/tmp/container-rootfs"
CGROUP_NAME="container-demo"

# Create rootfs
mkdir -p $ROOTFS

# Install minimal system (Ubuntu/Debian)
sudo debootstrap --arch=amd64 focal $ROOTFS http://archive.ubuntu.com/ubuntu/

# Or copy existing system
# sudo rsync -av --exclude=/proc --exclude=/sys --exclude=/dev /bin /etc /lib* /sbin /usr $ROOTFS/

# Create essential directories
sudo mkdir -p $ROOTFS/{proc,sys,dev,tmp}

# Create cgroup
sudo mkdir -p /sys/fs/cgroup/memory/$CGROUP_NAME
sudo mkdir -p /sys/fs/cgroup/cpu/$CGROUP_NAME

# Set resource limits
echo "128M" | sudo tee /sys/fs/cgroup/memory/$CGROUP_NAME/memory.limit_in_bytes
echo "50000" | sudo tee /sys/fs/cgroup/cpu/$CGROUP_NAME/cpu.cfs_quota_us
echo "100000" | sudo tee /sys/fs/cgroup/cpu/$CGROUP_NAME/cpu.cfs_period_us

# Create network namespace
sudo ip netns add $CONTAINER_NAME

# Setup veth pair
sudo ip link add veth0 type veth peer name veth1
sudo ip link set veth1 netns $CONTAINER_NAME
sudo ip addr add 172.18.0.1/16 dev veth0
sudo ip link set veth0 up

# Configure container network
sudo ip netns exec $CONTAINER_NAME ip addr add 172.18.0.2/16 dev veth1
sudo ip netns exec $CONTAINER_NAME ip link set veth1 up
sudo ip netns exec $CONTAINER_NAME ip link set lo up
sudo ip netns exec $CONTAINER_NAME ip route add default via 172.18.0.1

# Start container
sudo cgexec -g memory,cpu:$CGROUP_NAME \
  ip netns exec $CONTAINER_NAME \
  unshare --pid --uts --ipc --mount --fork \
  chroot $ROOTFS /bin/bash -c "
    mount -t proc proc /proc
    mount -t sysfs sysfs /sys
    mount -t tmpfs tmpfs /tmp
    hostname $CONTAINER_NAME
    exec /bin/bash
  "
```

### Container Runtime Script
```bash
#!/bin/bash
# simple_runtime.sh - Minimal container runtime

function create_container() {
    local name=$1
    local image=$2
    local command=$3
    
    # Create container directory
    mkdir -p /var/lib/containers/$name
    
    # Extract image (simplified)
    tar -xf $image -C /var/lib/containers/$name
    
    # Create namespaces and cgroups
    setup_isolation $name
    
    # Start container process
    start_process $name "$command"
}

function setup_isolation() {
    local name=$1
    
    # Create cgroups
    mkdir -p /sys/fs/cgroup/memory/containers/$name
    mkdir -p /sys/fs/cgroup/cpu/containers/$name
    
    # Set default limits
    echo "512M" > /sys/fs/cgroup/memory/containers/$name/memory.limit_in_bytes
    echo "100000" > /sys/fs/cgroup/cpu/containers/$name/cpu.cfs_quota_us
    echo "100000" > /sys/fs/cgroup/cpu/containers/$name/cpu.cfs_period_us
}

function start_process() {
    local name=$1
    local command=$2
    local rootfs="/var/lib/containers/$name"
    
    # Start container with all isolation
    cgexec -g memory,cpu:containers/$name \
      unshare --pid --net --mount --uts --ipc --fork \
      chroot $rootfs /bin/sh -c "
        mount -t proc proc /proc
        mount -t tmpfs tmpfs /tmp
        hostname $name
        exec $command
      "
}

# Usage
create_container "test1" "alpine.tar" "/bin/sh"
```

---

## Container Runtime Internals

### Docker Architecture
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Docker CLI    │───▶│   Docker Daemon │───▶│   containerd    │
└─────────────────┘    └─────────────────┘    └─────────────────┘
                                                        │
                                                        ▼
                                               ┌─────────────────┐
                                               │      runc       │
                                               └─────────────────┘
                                                        │
                                                        ▼
                                               ┌─────────────────┐
                                               │ Linux Kernel    │
                                               │ (ns, cgroups)   │
                                               └─────────────────┘
```

### OCI Runtime Specification
```bash
# Create OCI bundle
mkdir -p /tmp/oci-bundle
cd /tmp/oci-bundle

# Generate config.json
runc spec

# Modify config for custom container
vim config.json

# Create container
runc create mycontainer

# Start container
runc start mycontainer

# List containers
runc list
```

### Container Lifecycle
1. **Create**: Set up namespaces and cgroups
2. **Start**: Execute container process
3. **Run**: Process execution
4. **Stop**: Send termination signal
5. **Delete**: Clean up resources

---

## Exercises

### Exercise 1: Namespace Exploration
**Objective**: Understand different types of namespaces and their isolation properties.

**Tasks**:
1. Create a container with all namespace types
2. Verify isolation for each namespace
3. Demonstrate communication between namespaces
4. Monitor namespace creation and destruction

### Exercise 2: cgroups Resource Management
**Objective**: Implement resource limits using cgroups.

**Tasks**:
1. Create memory and CPU limited environment
2. Test resource limits with stress testing
3. Monitor resource usage and enforcement
4. Compare cgroups v1 and v2 behavior

### Exercise 3: Container Security Hardening
**Objective**: Implement comprehensive container security.

**Tasks**:
1. Create hardened container configuration
2. Implement capability dropping
3. Configure seccomp profiles
4. Test security restrictions

### Exercise 4: Build Container Runtime
**Objective**: Create a minimal container runtime from scratch.

**Tasks**:
1. Implement namespace creation
2. Add cgroup management
3. Create filesystem isolation
4. Add network configuration

### Exercise 5: Container Troubleshooting
**Objective**: Debug container runtime issues.

**Tasks**:
1. Diagnose namespace problems
2. Troubleshoot cgroup limits
3. Debug network connectivity
4. Analyze security violations

---

## Solutions

### Solution 1: Namespace Exploration
```bash
# Create comprehensive namespace isolation
sudo unshare --pid --net --mount --uts --ipc --user --map-root-user --fork bash

# Verify PID namespace
ps aux  # Should show minimal processes
echo $$  # Should show PID 1

# Verify UTS namespace
hostname test-container
hostname  # Should show new hostname

# Verify mount namespace
mount -t tmpfs tmpfs /tmp
ls /tmp  # Should be empty

# From another terminal, check isolation
ps aux | grep bash  # Original processes visible
hostname  # Original hostname
ls /tmp  # Original content

# Network namespace verification
ip link show  # Should show only loopback

# IPC namespace verification
ipcs  # Should show no IPC objects
```

### Solution 2: cgroups Resource Management
```bash
# Create cgroup hierarchy
sudo mkdir -p /sys/fs/cgroup/memory/test-container
sudo mkdir -p /sys/fs/cgroup/cpu/test-container

# Set 256MB memory limit
echo 268435456 | sudo tee /sys/fs/cgroup/memory/test-container/memory.limit_in_bytes

# Set 50% CPU limit
echo 50000 | sudo tee /sys/fs/cgroup/cpu/test-container/cpu.cfs_quota_us
echo 100000 | sudo tee /sys/fs/cgroup/cpu/test-container/cpu.cfs_period_us

# Test memory limit
echo $$ | sudo tee /sys/fs/cgroup/memory/test-container/cgroup.procs
stress --vm 1 --vm-bytes 512M --timeout 30s  # Should be killed

# Test CPU limit
echo $$ | sudo tee /sys/fs/cgroup/cpu/test-container/cgroup.procs
stress --cpu 4 --timeout 30s  # Should use only 50% of one core

# Monitor usage
watch cat /sys/fs/cgroup/memory/test-container/memory.usage_in_bytes
watch cat /sys/fs/cgroup/cpu/test-container/cpuacct.usage
```

### Solution 3: Container Security Hardening
```bash
# Create hardened container script
cat > hardened_container.sh << 'EOF'
#!/bin/bash

# Create non-privileged user
sudo useradd -r -s /bin/false container-user

# Create secure rootfs
ROOTFS="/tmp/secure-container"
sudo mkdir -p $ROOTFS/{bin,lib,lib64,etc,tmp}

# Copy minimal binaries
sudo cp /bin/sh $ROOTFS/bin/
sudo cp /lib/x86_64-linux-gnu/libc.so.6 $ROOTFS/lib/
sudo cp /lib64/ld-linux-x86-64.so.2 $ROOTFS/lib64/

# Set up seccomp profile
cat > seccomp.json << 'SECCOMP'
{
    "defaultAction": "SCMP_ACT_ERRNO",
    "architectures": ["SCMP_ARCH_X86_64"],
    "syscalls": [
        {"names": ["read", "write", "exit", "exit_group"], "action": "SCMP_ACT_ALLOW"}
    ]
}
SECCOMP

# Start hardened container
sudo unshare --pid --net --mount --uts --ipc --fork \
  capsh --drop=all --user=container-user --chroot=$ROOTFS -- -c "
    mount -t proc proc /proc 2>/dev/null || true
    exec /bin/sh
  "
EOF

chmod +x hardened_container.sh
./hardened_container.sh
```

### Solution 4: Build Container Runtime
```bash
# Complete container runtime implementation
cat > container_runtime.py << 'EOF'
#!/usr/bin/env python3
import os
import sys
import subprocess
import json

class SimpleRuntime:
    def __init__(self):
        self.containers_dir = "/var/lib/simple-containers"
        os.makedirs(self.containers_dir, exist_ok=True)
    
    def create_cgroup(self, name, memory_limit="512M", cpu_limit="100000"):
        """Create cgroup for container"""
        cgroup_path = f"/sys/fs/cgroup/memory/containers/{name}"
        os.makedirs(cgroup_path, exist_ok=True)
        
        # Set memory limit
        with open(f"{cgroup_path}/memory.limit_in_bytes", "w") as f:
            if memory_limit.endswith("M"):
                limit = int(memory_limit[:-1]) * 1024 * 1024
            f.write(str(limit))
        
        # Set CPU limit
        cpu_path = f"/sys/fs/cgroup/cpu/containers/{name}"
        os.makedirs(cpu_path, exist_ok=True)
        with open(f"{cpu_path}/cpu.cfs_quota_us", "w") as f:
            f.write(cpu_limit)
        with open(f"{cpu_path}/cpu.cfs_period_us", "w") as f:
            f.write("100000")
    
    def create_network(self, name):
        """Create network namespace and veth pair"""
        subprocess.run(["ip", "netns", "add", name], check=True)
        subprocess.run(["ip", "link", "add", f"veth-{name}", "type", "veth", 
                       "peer", "name", f"veth-{name}-peer"], check=True)
        subprocess.run(["ip", "link", "set", f"veth-{name}-peer", "netns", name], check=True)
        
        # Configure host side
        subprocess.run(["ip", "addr", "add", "172.18.0.1/16", "dev", f"veth-{name}"], check=True)
        subprocess.run(["ip", "link", "set", f"veth-{name}", "up"], check=True)
        
        # Configure container side
        subprocess.run(["ip", "netns", "exec", name, "ip", "addr", "add", 
                       "172.18.0.2/16", "dev", f"veth-{name}-peer"], check=True)
        subprocess.run(["ip", "netns", "exec", name, "ip", "link", "set", 
                       f"veth-{name}-peer", "up"], check=True)
        subprocess.run(["ip", "netns", "exec", name, "ip", "link", "set", "lo", "up"], check=True)
    
    def run_container(self, name, rootfs, command):
        """Run container with full isolation"""
        container_dir = f"{self.containers_dir}/{name}"
        os.makedirs(container_dir, exist_ok=True)
        
        # Create isolation
        self.create_cgroup(name)
        self.create_network(name)
        
        # Start container
        cmd = [
            "cgexec", "-g", f"memory,cpu:containers/{name}",
            "ip", "netns", "exec", name,
            "unshare", "--pid", "--uts", "--ipc", "--mount", "--fork",
            "chroot", rootfs, "/bin/sh", "-c",
            f"mount -t proc proc /proc; mount -t tmpfs tmpfs /tmp; hostname {name}; exec {command}"
        ]
        
        subprocess.run(cmd)

if __name__ == "__main__":
    runtime = SimpleRuntime()
    if len(sys.argv) != 4:
        print("Usage: container_runtime.py <name> <rootfs> <command>")
        sys.exit(1)
    
    name, rootfs, command = sys.argv[1:4]
    runtime.run_container(name, rootfs, command)
EOF

chmod +x container_runtime.py
# Usage: sudo ./container_runtime.py test1 /path/to/rootfs /bin/sh
```

### Solution 5: Container Troubleshooting
```bash
# Container debugging toolkit
cat > debug_container.sh << 'EOF'
#!/bin/bash

function debug_namespaces() {
    local pid=$1
    echo "=== Namespace Information for PID $pid ==="
    ls -l /proc/$pid/ns/
    echo
    
    echo "=== Process Tree ==="
    pstree -p $pid
    echo
    
    echo "=== Network Namespace ==="
    nsenter -t $pid -n ip addr show
    echo
}

function debug_cgroups() {
    local pid=$1
    echo "=== cgroup Membership ==="
    cat /proc/$pid/cgroup
    echo
    
    echo "=== Memory Usage ==="
    local memory_cgroup=$(grep memory /proc/$pid/cgroup | cut -d: -f3)
    if [ -n "$memory_cgroup" ]; then
        cat /sys/fs/cgroup/memory$memory_cgroup/memory.usage_in_bytes
        cat /sys/fs/cgroup/memory$memory_cgroup/memory.limit_in_bytes
    fi
    echo
}

function debug_capabilities() {
    local pid=$1
    echo "=== Process Capabilities ==="
    grep Cap /proc/$pid/status
    echo
}

function debug_seccomp() {
    local pid=$1
    echo "=== Seccomp Status ==="
    grep Seccomp /proc/$pid/status
    echo
}

# Main debugging function
function debug_container() {
    local pid=$1
    
    if [ -z "$pid" ]; then
        echo "Usage: debug_container <pid>"
        return 1
    fi
    
    debug_namespaces $pid
    debug_cgroups $pid
    debug_capabilities $pid
    debug_seccomp $pid
}

# Usage
debug_container $1
EOF

chmod +x debug_container.sh
# Usage: ./debug_container.sh <container_pid>
```

---

## Interview Questions

### Basic Level

1. **Q**: What is the difference between containers and virtual machines at the kernel level?
   **A**: VMs virtualize hardware and run separate OS kernels, while containers share the host kernel and use Linux namespaces and cgroups for isolation. Containers have lower overhead but less isolation than VMs.

2. **Q**: Name the six types of Linux namespaces and their purposes.
   **A**: 
   - PID: Process ID isolation
   - Network: Network stack isolation
   - Mount: Filesystem mount points
   - UTS: Hostname and domain name
   - IPC: Inter-process communication
   - User: User and group ID mapping

3. **Q**: How do cgroups complement namespaces in containerization?
   **A**: Namespaces provide isolation (what processes can see), while cgroups provide resource control (what resources processes can use). Together they enable secure multi-tenancy.

4. **Q**: What are Linux capabilities and why are they important for container security?
   **A**: Capabilities divide root privileges into distinct units. Containers can run with specific capabilities instead of full root access, following the principle of least privilege.

### Intermediate Level

5. **Q**: Explain the difference between cgroups v1 and v2.
   **A**: cgroups v2 provides a unified hierarchy (single tree), better resource distribution, improved performance, and simplified interface. v1 has separate hierarchies per resource type.

6. **Q**: How would you troubleshoot a container that's consuming more memory than expected?
   **A**: Check cgroup memory statistics, use tools like `docker stats`, examine `/sys/fs/cgroup/memory/*/memory.stat`, look for memory leaks with `valgrind`, and analyze application memory usage patterns.

7. **Q**: What security measures should be implemented when running containers in production?
   **A**: Run as non-root user, use read-only filesystems, drop unnecessary capabilities, implement seccomp profiles, use AppArmor/SELinux, scan images for vulnerabilities, and implement resource limits.

8. **Q**: How do you create a custom network namespace and connect it to the host?
   **A**: Use `ip netns add`, create veth pairs with `ip link add`, move one end to the namespace, configure IP addresses, and set up routing between host and namespace.

### Advanced Level

9. **Q**: Describe the container lifecycle from creation to destruction at the kernel level.
   **A**: Create namespaces → Set up cgroups → Configure networking → Mount filesystems → Set capabilities → Apply seccomp → Start process → Monitor → Clean up resources.

10. **Q**: How would you implement a minimal container runtime?
    **A**: Create namespaces with `unshare`, set up cgroups for resource limits, configure networking with veth pairs, mount proc/sys filesystems, apply security policies, and exec the container process.

11. **Q**: Explain how Docker's architecture uses these Linux primitives.
    **A**: Docker daemon uses containerd → runc → Linux kernel. runc creates namespaces/cgroups, containerd manages container lifecycle, Docker daemon provides API and image management.

12. **Q**: What are the performance implications of different namespace types?
    **A**: PID namespace has minimal overhead, network namespace adds routing overhead, mount namespace affects filesystem operations, user namespace has mapping overhead. Choose based on isolation needs.

### Expert Level

13. **Q**: How do you optimize container performance for high-density deployments?
    **A**: Use cgroups v2, optimize memory overcommit, implement CPU pinning, use shared base layers, minimize namespace overhead, implement efficient networking (SR-IOV), and monitor resource contention.

14. **Q**: Describe advanced container security techniques beyond basic hardening.
    **A**: Implement gVisor/Kata containers for stronger isolation, use eBPF for runtime security monitoring, implement admission controllers, use signed images, and deploy runtime security tools like Falco.

15. **Q**: How do you handle container networking in complex multi-host environments?
    **A**: Implement overlay networks (VXLAN), use CNI plugins, configure service mesh, implement network policies, use load balancers, and consider performance implications of different networking models.

---

## Troubleshooting Guide

### Common Container Issues

1. **Namespace Creation Failures**
   - Check user permissions and capabilities
   - Verify kernel support for namespaces
   - Ensure sufficient system resources

2. **cgroup Limit Enforcement**
   - Verify cgroup hierarchy and permissions
   - Check for cgroup v1 vs v2 compatibility
   - Monitor for cgroup OOM events

3. **Network Connectivity Issues**
   - Verify veth pair configuration
   - Check routing tables and iptables rules
   - Ensure proper namespace network setup

4. **Security Violations**
   - Review capability requirements
   - Check seccomp profile compatibility
   - Verify AppArmor/SELinux policies

---

## Community and Support

- **Discord Community**: [Join our Discord](https://discord.gg/linux-learning) for container technology discussions
- **Google Group**: [Linux Containers Group](https://groups.google.com/g/linux-containers) for technical deep dives
- **YouTube Channel**: [Container Internals Tutorials](https://youtube.com/c/container-internals) for hands-on demonstrations
- **Container Labs**: Share your container experiments and runtime implementations

---

## Completion Checklist

- [ ] Understand all six Linux namespace types
- [ ] Master cgroups v1 and v2 management
- [ ] Implement Linux capabilities security
- [ ] Configure container networking from scratch
- [ ] Build a minimal container runtime
- [ ] Apply comprehensive security hardening
- [ ] Troubleshoot container runtime issues
- [ ] Complete all practical exercises
- [ ] Practice container debugging techniques
- [ ] Review and answer all interview questions
- [ ] Contribute to community container projects

---

**Next**: [Day 29 - Advanced Monitoring & Observability](../Day_29/notes_and_exercises.md)

