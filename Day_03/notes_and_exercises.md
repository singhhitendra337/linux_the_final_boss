# Day 03: Linux Folder Structure & File Types

## Notes
- **Linux Filesystem Hierarchy**
  - The Linux filesystem is hierarchical, starting from the root directory `/`.
  - Each directory has a specific purpose and is standardized by the Filesystem Hierarchy Standard (FHS).

  ![Linux Filesystem Hierarchy](https://upload.wikimedia.org/wikipedia/commons/6/6f/FHS-standards.png)

- **Key Directories (Categorized):**
  - **Essential System Directories:**
    - `/bin`: Essential user binaries (ls, cp, mv)
    - `/sbin`: System binaries (administrative commands)
    - `/lib`, `/lib64`: Essential shared libraries
    - `/boot`: Boot loader files (kernel, initrd)
    - `/dev`: Device files
    - `/etc`: System configuration files
    - `/proc`: Virtual filesystem for process and kernel info
    - `/sys`: Virtual filesystem for system info
    - `/run`: Runtime variable data
  - **User Data & Home:**
    - `/home`: User home directories
    - `/root`: Home directory for root user
  - **Variable & Temporary Data:**
    - `/var`: Variable data (logs, mail, spool)
    - `/tmp`: Temporary files (writable by all users)
  - **Additional Software & User Programs:**
    - `/usr`: User programs, libraries, documentation
    - `/usr/bin`, `/usr/sbin`: Non-essential user/system binaries
    - `/usr/local`: Locally installed software
    - `/opt`: Optional/additional software
  - **Mount Points:**
    - `/mnt`: Temporary mount point for filesystems
    - `/media`: Removable media (USB, CD-ROM)
    - `/srv`: Data for services (web, ftp)

  ```
  /
  |-- bin
  |-- sbin
  |-- lib
  |-- lib64
  |-- boot
  |-- dev
  |-- etc
  |-- proc
  |-- sys
  |-- run
  |-- home
  |-- root
  |-- var
  |-- tmp
  |-- usr
  |-- usr/bin
  |-- usr/sbin
  |-- usr/local
  |-- opt
  |-- mnt
  |-- media
  |-- srv
  ```

- **File Types in Linux:**
  - Regular files: Text, binary, scripts, etc.
  - Directories: Folders containing files
  - **Symbolic links (symlinks):** Pointers to other files or directories. Created with `ln -s`. Can span filesystems. If the target is deleted, the symlink is broken (dangling link).
  - **Hard links:** Additional directory entries for a file. Created with `ln`. Share the same inode as the original file. Cannot span filesystems or link to directories. File is only deleted when all hard links are removed.
  - Device files: Represent hardware (block/character devices)
  - Named pipes (FIFOs): For inter-process communication
  - Sockets: For network communication

  | Feature         | Symbolic Link (symlink) | Hard Link           |
  |-----------------|------------------------|---------------------|
  | Command         | ln -s target linkname  | ln target linkname  |
  | Inode           | New inode              | Same as original    |
  | Cross-filesystem| Yes                    | No                  |
  | Directories     | Yes (with -s)          | No                  |
  | If target gone  | Dangling (broken)      | File remains        |

  **Diagram:**
  ```
  [file.txt] <--- hardlink.txt (same inode)
      ^
      |
  symlink.txt (points to file.txt, different inode)
  ```

- **Commands to Identify File Types:**
  - `ls -l`: Shows file type in the first character (-, d, l, c, b, p, s)
  - `file <filename>`: Describes file type
  - `stat <filename>`: Detailed file info

- **Navigating the Filesystem:**
  - `cd <dir>`: Change directory
  - `ls`: List directory contents
  - `pwd`: Print working directory
  - `tree`: Visualize directory structure (may need to install)

- **Absolute vs Relative Paths:**
  - Absolute: Starts from `/` (e.g., `/home/user/file.txt`)
  - Relative: Based on current directory (e.g., `../file.txt`)

- **Hidden Files:**
  - Files starting with `.` are hidden (e.g., `.bashrc`). Use `ls -a` to view.

- **Best Practices:**
  - Use symbolic links for flexibility (e.g., config files, shared resources), but avoid circular links.
  - Use hard links for backup/versioning within the same filesystem.
  - Do not modify files in `/bin`, `/sbin`, `/lib`, `/usr` unless necessary.
  - Store personal files in `/home/<username>`.

## Sample Exercises
1. List all directories in the root (`/`) and describe their purpose and category.
2. Use `tree` to visualize your home directory structure.
3. Find and display all hidden files in your home directory.
4. Explain the difference between `/etc/passwd` and `/etc/shadow`.
5. Create a symbolic link and a hard link for a file. Show the difference.
6. Use `file` and `stat` to inspect different file types.
7. Explain the difference between symbolic and hard links, and give practical use cases for each.

## Sample Interview Questions
1. Explain the Linux filesystem hierarchy and the purpose of key directories.
2. What is the difference between /bin and /usr/bin?
3. What are the different file types in Linux? How can you identify them?
4. Explain the difference between a symbolic link and a hard link. When would you use each?
5. How do you find all hidden files in a directory?
6. What is the purpose of the /proc and /sys directories?
7. How do you check the inode number of a file? Why is it important?
8. What are device files and where are they located?
9. What is the difference between absolute and relative paths?
10. How would you troubleshoot a broken symlink?

## Interview Question Answers
1. The Linux filesystem hierarchy starts at `/` and organizes files by function: `/bin` for binaries, `/etc` for configs, `/home` for user data, `/var` for variable data, etc.
2. `/bin` contains essential binaries for all users; `/usr/bin` has non-essential user binaries, often for applications installed after the base system.
3. File types: regular files, directories, symlinks, hard links, device files, pipes, sockets. Identify with `ls -l`, `file`, or `stat`.
4. Symbolic links point to a path and can cross filesystems; hard links are additional directory entries for the same inode. Use symlinks for configs/scripts, hard links for backup/versioning.
5. Use `ls -a` to list all files, then filter with `grep '^\.'` for hidden files.
6. `/proc` provides process and kernel info; `/sys` exposes kernel and hardware info for system management.
7. Use `ls -li` or `stat` to check inode numbers; inodes track file metadata and are key for hard links.
8. Device files represent hardware and are found in `/dev` (e.g., `/dev/sda` for disks).
9. Absolute paths start with `/` and are system-wide; relative paths are based on the current directory.
10. Use `ls -l` to check if a symlink is broken (shows red or 'No such file'); fix by recreating or correcting the target.

## Solutions
1. `ls -l /` ; see notes for directory purposes and categories
2. `tree ~`
3. `ls -a ~ | grep '^\.'`
4. `/etc/passwd` stores user account info; `/etc/shadow` stores encrypted passwords.
5. `ln -s file.txt symlink.txt` (symbolic); `ln file.txt hardlink.txt` (hard); use `ls -li` to see inode numbers.
6. `file file.txt` ; `stat file.txt` ; try on directories, links, devices, etc.
7. Symbolic links point to a file or directory path and can cross filesystems; hard links are additional directory entries for the same inode and cannot cross filesystems. Use symlinks for configs/scripts, hard links for backup/versioning.
