# Day 05: Basic Linux Commands for DevOps Engineers

## Notes
- **Why Learn Basic Linux Commands?**
  - Mastery of basic commands is essential for navigation, troubleshooting, and automation in any Linux environment.
  - These commands are used daily by DevOps, SRE, and system engineers.

- **Navigation & System Info:**
  - `pwd`: Print working directory
  - `ls`, `ls -l`, `ls -a`, `ls -lh`, `ls -R`: List files (long, all, human-readable, recursive)
  - `cd <dir>`: Change directory
  - `clear`: Clear the terminal screen
  - `whoami`: Show current user
  - `date`, `cal`: Show date and calendar
  - `history`: Show command history
  - `uname -a`, `uname -r`, `uname -m`: System/kernel info, kernel version, architecture

- **Working with Files & Directories:**
  - `touch <file>`: Create empty file
  - `mkdir <dir>`, `mkdir -p <dir/subdir>`: Create directory, create parent directories as needed
  - `cp <src> <dest>`, `cp -r <dir> <dest>`, `cp -i`, `cp -u`: Copy files/dirs, prompt before overwrite, update only if newer
  - `mv <src> <dest>`, `mv -i`: Move/rename, prompt before overwrite
  - `rm <file>`, `rm -r <dir>`, `rm -f`, `rm -rf`: Remove file, recursive, force, recursive+force
  - `rmdir <dir>`: Remove empty directory
  - `cat`, `less`, `more`: View file contents
  - `head -n <N>`, `tail -n <N>`, `tail -f <file>`: View first/last N lines, follow file (logs)

- **Searching & Utilities:**
  - `grep <pattern> <file>`, `grep -i`, `grep -r`, `grep -v`, `grep -n`: Search, ignore case, recursive, invert match, show line numbers
  - `find <path> -name <pattern>`, `find <path> -type f -size +10M`: Find files by name, type, size
  - `locate <pattern>`: Quickly find files (uses a database)
  - `cmp`, `diff`, `diff -u`: Compare files, unified diff
  - `bc`: Calculator
  - `alias`, `alias ll='ls -l'`: Create command shortcuts

- **Tab Completion & Command History:**
  - Use `Tab` to auto-complete commands and paths
  - Use `Up/Down` arrows to navigate command history

- **Best Practices:**
  - Use wildcards (`*`, `?`, `[]`) for flexible file operations
  - Use `man <command>` for help and documentation
  - Practice safe deletion: double-check before using `rm -rf`
  - Use aliases for frequently used or complex commands

## Sample Exercises
1. List all files, including hidden ones, in your home directory.
2. Create a directory, add a file, copy and rename it, then delete both.
3. View the first and last 10 lines of `/etc/passwd`.
4. Search for the word "root" in `/etc/passwd` using `grep`.
5. Create an alias to list files in long format and use it.

## Sample Interview Questions
1. What does the `pwd` command do?
2. How do you list all files, including hidden ones, in a directory?
3. What is the difference between `cat`, `less`, and `more`?
4. How do you create, copy, move, and delete files and directories in Linux?
5. How do you view the first and last 10 lines of a file?
6. What is the use of wildcards in file management?
7. How do you search for a file by name or content?
8. What is the difference between `cp` and `mv`?
9. How do you check disk usage of a directory?
10. How do you use tab completion and command history to improve efficiency?

## Interview Question Answers
1. `ls` lists files; `ls -l` shows details; `ls -a` shows hidden files; `ls -lh` shows human-readable sizes.
2. `cd` changes directories; `pwd` prints the current directory; `tree` visualizes directory structure.
3. `cp` copies files; `mv` moves/renames; `rm` removes files; use `-r` for directories.
4. `cat` displays file contents; `less`/`more` for paging; `head`/`tail` for first/last lines.
5. `find` searches for files by name, type, size, etc.; `grep` searches file contents by pattern.
6. Use `chmod` to change permissions, `chown` to change owner/group, `umask` to set default permissions.
7. `man` shows manual pages; `--help` gives command options; `info` provides detailed docs.
8. Use `history` to see previous commands; `!!` repeats last; `!n` repeats command n; `!string` repeats last starting with string.
9. Use `df -h` for disk usage, `du -sh` for directory size, `free -h` for memory usage.
10. Use pipes (`|`) to chain commands, redirects (`>`, `>>`, `<`) to control input/output, and wildcards (`*`, `?`) for pattern matching.

## Solutions
1. ls -a ~
2. mkdir testdir; touch testdir/file.txt; cp testdir/file.txt testdir/file2.txt; mv testdir/file2.txt testdir/file3.txt; rm testdir/file3.txt; rm -r testdir
3. head -10 /etc/passwd; tail -10 /etc/passwd
4. grep root /etc/passwd
5. alias ll='ls -l'; ll
