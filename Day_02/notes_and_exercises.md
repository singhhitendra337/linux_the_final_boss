# Day 02: Virtualization & Setting Up Linux (VM, WSL, Cloud)

## Notes
- **What is Virtualization?**
  - Virtualization allows you to run multiple operating systems on a single physical machine by creating virtual machines (VMs).
  - Hypervisors manage VMs. Two types:
    - Type 1 (bare-metal): Runs directly on hardware (e.g., VMware ESXi, Microsoft Hyper-V)
    - Type 2 (hosted): Runs on top of an existing OS (e.g., VirtualBox, VMware Workstation)

  ![Virtualization Diagram](https://upload.wikimedia.org/wikipedia/commons/3/3b/Virtual_machine.svg)

- **Why Use Virtualization?**
  - Safe environment for learning, testing, and development
  - Snapshots and easy recovery
  - Resource isolation and efficient hardware use

- **Popular Virtualization Tools:**
  - VirtualBox (free, cross-platform)
  - VMware Workstation/Player
  - KVM (Linux native)
  - Parallels (Mac)

- **Setting Up a Linux VM:**
  1. Download a Linux ISO (Ubuntu, CentOS, etc.) from the official website.
  2. Install VirtualBox or your preferred hypervisor.
  3. Create a new VM, allocate resources (CPU, RAM, disk), and attach the ISO.
  4. Boot the VM and follow the installation prompts.
  5. Install Guest Additions/Tools for better integration.

- **WSL (Windows Subsystem for Linux):**
  - Lets you run Linux directly on Windows without a VM.
  - Install from Microsoft Store (Ubuntu, Debian, etc.).
  - Use `wsl --install` on Windows 10/11.

- **Cloud Linux Instances:**
  - AWS EC2, Google Compute Engine, Azure VMs
  - Launch a VM from the cloud console, choose OS, size, and region
  - SSH into your instance for remote access

- **Best Practices:**
  - Allocate enough resources for smooth operation
  - Use snapshots/backups before making major changes
  - Secure remote access (use SSH keys, disable password login)
  - Keep your VM and host OS updated

## Sample Exercises
1. Set up a Linux VM using VirtualBox or VMware and install Ubuntu or CentOS.
2. Install and launch WSL on a Windows machine.
3. Launch a cloud Linux instance (AWS, GCP, or Azure) and connect via SSH.
4. Take a snapshot of your VM and restore it.
5. Compare Type 1 and Type 2 hypervisors with examples.

## Sample Interview Questions
1. What is virtualization and why is it important in modern IT?
2. Explain the difference between Type 1 and Type 2 hypervisors with examples.
3. How would you set up a Linux VM on your local machine? What are the key steps?
4. What is WSL and how does it differ from a traditional VM?
5. How do you connect to a cloud Linux instance securely?
6. What are the advantages of using snapshots in virtualization?
7. What are some best practices for securing a cloud VM?
8. How do you allocate resources efficiently for a VM?
9. What is the role of the bootloader in a VM?
10. How can you automate the provisioning of VMs in the cloud?

## Interview Question Answers
1. Virtualization allows multiple OS instances on one physical machine, improving resource utilization and isolation.
2. VMs emulate hardware and run full OSes; containers share the host OS kernel and are more lightweight.
3. Use VirtualBox, VMware, or KVM for VMs; Docker or LXC for containers.
4. WSL lets you run Linux on Windows without a VM, using a compatibility layer.
5. Cloud providers offer on-demand Linux VMs; you can use AWS EC2, Azure VMs, or Google Compute Engine.
6. Use `ssh user@host` to connect to a remote Linux machine securely.
7. ISO images are bootable disk images used to install OSes; mount or burn to USB to use.
8. Use `scp` or `rsync` to transfer files between host and VM.
9. Snapshots save the state of a VM for easy rollback.
10. Use `sudo` to run commands as root/admin; it's safer than logging in as root directly.

## Solutions
1. [Step-by-step screenshots or video guide can be added.]
2. `wsl --install` (Windows 10/11), then launch Ubuntu from Start menu.
3. Use cloud console to launch, then `ssh user@public-ip` to connect.
4. Use VirtualBox/VMware snapshot feature; restore from snapshot menu.
5. Type 1: VMware ESXi (runs on hardware); Type 2: VirtualBox (runs on host OS).
