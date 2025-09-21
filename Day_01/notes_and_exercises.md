# Day 01: Linux Fundamentals & Core Concepts

## Notes
- **What is Linux?**
  - Linux is a free, open-source, Unix-like operating system kernel created by Linus Torvalds in 1991.
  - It forms the core of many operating systems called "Linux distributions" (distros).
  - Used in servers, desktops, embedded systems, supercomputers, IoT, and mobile devices (Android).

  ![Linux Ecosystem Diagram](https://upload.wikimedia.org/wikipedia/commons/1/1b/Linux_Distribution_Timeline.svg)

- **Linux vs Windows:**
  - Linux is open-source; Windows is proprietary.
  - Linux uses a hierarchical file system; Windows uses drive letters.
  - Linux is case-sensitive; Windows is not.
  - Linux supports multiple users natively and is preferred for servers.
  - Package management and software installation differ greatly.

  | Feature         | Linux                | Windows           |
  |-----------------|----------------------|-------------------|
  | Source          | Open Source          | Proprietary       |
  | File System     | Hierarchical (/)     | Drive Letters (C:)|
  | Case Sensitivity| Yes                  | No                |
  | Users           | Multi-user           | Single-user focus |
  | Package Mgmt    | apt, yum, etc.       | .exe, .msi        |

- **Core Components of Linux:**
  - **Kernel:** The core that manages hardware, memory, processes, and system calls.
  - **Shell:** Command-line interface to interact with the OS (bash, zsh, sh, etc.).
  - **File System:** Organizes data in a hierarchical structure; everything is a file.
  - **Processes:** Running instances of programs, managed by the kernel.
  - **Init System:** Manages system startup and services (systemd, SysVinit, Upstart).

  ```
  +-------------------+
  |   Applications    |
  +-------------------+
  |      Shell        |
  +-------------------+
  |      Kernel       |
  +-------------------+
  |     Hardware      |
  +-------------------+
  ```

- **Popular Linux Distributions:**
  - Ubuntu, Debian, CentOS, Fedora, Red Hat, Arch, Kali, Alpine, SUSE, etc.
  - Each distro has its own package manager (apt, yum, dnf, pacman, zypper).

  ![Linux Distros](https://upload.wikimedia.org/wikipedia/commons/4/4f/Linux_Distribution_Timeline.png)

- **Open Source Philosophy & GNU Project:**
  - Linux is licensed under GPL; source code is freely available.
  - GNU provides essential tools and utilities (coreutils, bash, gcc, etc.).
  - Community-driven development and contributions.

- **Linux in DevOps, SRE, System, and Cloud Roles:**
  - Most servers, cloud VMs, and containers run Linux.
  - Essential for automation, scripting, CI/CD, monitoring, and troubleshooting.
  - Used in Kubernetes, Docker, AWS, GCP, Azure, and more.

- **Setting Up Your First Linux Environment:**
  - Options: Install on bare metal, use a VM (VirtualBox, VMware), WSL (Windows Subsystem for Linux), or cloud instance (AWS EC2, GCP Compute Engine).
  - Download ISO from official distro site, create bootable USB, or use cloud console.
  - Recommended: Start with Ubuntu or CentOS for beginners.

  ![Linux Setup Options](https://upload.wikimedia.org/wikipedia/commons/3/35/VirtualBox_logo.png)

- **Basic Linux Commands:**
  - `pwd`: Print working directory
  - `ls`: List files and directories
  - `cd`: Change directory
  - `echo`: Print text to terminal
  - `clear`: Clear terminal screen
  - `exit`: Close terminal session
  - `man <command>`: Show manual page for a command
  - `whoami`: Show current user
  - `uname -a`: Show system/kernel info
  - `history`: Show command history
  - `date`, `cal`: Show date and calendar

  ```
  $ pwd
  /home/user
  $ ls
  Documents  Downloads  Pictures
  $ cd Documents
  $ echo Hello World
  Hello World
  ```

- **Best Practices:**
  - Always check official documentation and man pages.
  - Practice using the terminal daily.
  - Use tab completion and command history for efficiency.
  - Keep your system updated with the package manager.

## Sample Exercises
1. List five differences between Linux and Windows from a system engineer's perspective.
2. Name and describe the function of four core components of Linux.
3. Set up a Linux VM, WSL, or cloud instance and open a terminal.
4. Run the following commands and note their output: `pwd`, `ls`, `cd ~`, `echo Hello World`, `clear`, `exit`, `whoami`, `uname -a`, `man ls`.
5. Find and list the version of your Linux distribution.

## Solutions
1. Linux is open source, case-sensitive, uses a hierarchical file system, supports multiple users, and has different package management.
2. Kernel (manages hardware), Shell (command interpreter), File System (organizes data), Init System (manages startup/services).
3. [Step-by-step setup guide or screenshots can be added here.]
4. 
   - `pwd`: Prints current directory
   - `ls`: Lists files in directory
   - `cd ~`: Changes to home directory
   - `echo Hello World`: Prints 'Hello World'
   - `clear`: Clears the terminal screen
   - `exit`: Closes the terminal session
   - `whoami`: Shows current user
   - `uname -a`: Shows kernel and system info
   - `man ls`: Shows manual for `ls` command
5. `lsb_release -a` or `cat /etc/os-release` (shows distro version)

## Interview Question Answers
1. Linux is an open-source, Unix-like operating system kernel; it manages hardware and system resources.
2. The kernel is the core of the OS, managing hardware and processes; a distribution includes the kernel plus userland tools, package managers, and more.
3. Popular distributions include Ubuntu (user-friendly), CentOS (enterprise), Fedora (cutting-edge), Debian (stable), and Arch (minimalist, rolling release).
4. The Linux kernel is the core; distributions package the kernel with software, tools, and package management.
5. Use `uname -a` to see kernel version; `lsb_release -a` or `/etc/os-release` for distribution info.
6. Open source means the source code is freely available to use, modify, and distribute.
7. Linux is widely used in servers, embedded systems, cloud, supercomputers, and mobile (Android).
8. The GNU project provides essential userland tools (shell, compilers, utilities) that work with the Linux kernel.
9. The shell is a command-line interface for interacting with the OS; common shells include bash, zsh, and sh.
10. Linux is preferred for servers due to stability, security, flexibility, and cost-effectiveness.
