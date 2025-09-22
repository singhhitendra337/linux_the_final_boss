# Day 05: Basic Linux Commands for DevOps Engineers

## Learning Objectives
By the end of Day 5, you will:
- Master essential Linux navigation and file operations
- Use basic commands for system information and file management
- Understand command history and tab completion
- Create aliases for efficiency
- Apply best practices for safe file operations

**Estimated Time:** 3-4 hours

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



## Solutions
1. **List all files including hidden:**
   ```bash
   ls -la ~
   ls -a ~/    # Alternative
   ```

2. **File and directory operations:**
   ```bash
   mkdir testdir                           # Create directory
   touch testdir/file.txt                  # Create file
   cp testdir/file.txt testdir/file2.txt   # Copy file
   mv testdir/file2.txt testdir/file3.txt  # Rename/move file
   rm testdir/file3.txt                    # Delete file
   rm -r testdir                           # Delete directory
   ```

3. **View file content:**
   ```bash
   head -10 /etc/passwd    # First 10 lines
   tail -10 /etc/passwd    # Last 10 lines
   head -n 5 /etc/passwd   # First 5 lines
   tail -f /var/log/syslog # Follow log file
   ```

4. **Search with grep:**
   ```bash
   grep root /etc/passwd           # Find "root"
   grep -i ROOT /etc/passwd        # Case insensitive
   grep -n root /etc/passwd        # Show line numbers
   ```

5. **Create and use alias:**
   ```bash
   alias ll='ls -l'        # Create alias
   alias la='ls -la'       # Another useful alias
   ll                      # Use the alias
   alias                   # List all aliases
   ```

## Completion Checklist
- [ ] Can navigate filesystem using cd, ls, pwd
- [ ] Understand file operations: create, copy, move, delete
- [ ] Can view file contents with cat, less, head, tail
- [ ] Use grep for basic text searching
- [ ] Created and used command aliases
- [ ] Comfortable with tab completion and command history

## Key Commands Summary
```bash
# Navigation
pwd                     # Print working directory
ls -la                  # List all files with details
cd /path/to/directory   # Change directory

# File operations
touch filename          # Create empty file
mkdir dirname           # Create directory
cp source dest          # Copy file/directory
mv source dest          # Move/rename
rm filename             # Remove file
rm -r dirname           # Remove directory

# View files
cat filename            # Display entire file
less filename           # Page through file
head -n 10 filename     # First 10 lines
tail -f filename        # Follow file changes

# Search
grep pattern filename   # Search in file
find /path -name "*.txt" # Find files by name

# System info
whoami                  # Current user
date                    # Current date/time
uname -a                # System information
history                 # Command history
```

## Best Practices
- Always use `ls -la` to see hidden files and permissions
- Use `cp -i` and `mv -i` to prompt before overwriting
- Never use `rm -rf /` or similar dangerous commands
- Use tab completion to avoid typos
- Create aliases for frequently used commands
- Use `man command` to learn more about any command

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
1. **pwd:** Prints the current working directory path
2. **List Hidden Files:** Use `ls -a` to show all files including those starting with dot (.)
3. **File Viewers:** `cat` displays entire file; `less`/`more` allow paging through large files; `less` is more feature-rich
4. **File Operations:** `touch` creates, `cp` copies, `mv` moves/renames, `rm` deletes; use `-r` flag for directories
5. **File Portions:** `head -10 file` shows first 10 lines; `tail -10 file` shows last 10 lines
6. **Wildcards:** `*` matches any characters, `?` matches single character, `[]` matches character sets for flexible operations
7. **File Search:** `find` searches by name/attributes; `grep` searches file contents; `locate` uses database for fast name search
8. **cp vs mv:** `cp` creates copy leaving original; `mv` moves/renames file removing original
9. **Disk Usage:** `du -sh directory` shows directory size; `df -h` shows filesystem usage
10. **Efficiency:** Tab completion auto-completes commands/paths; arrow keys navigate command history; `!!` repeats last command

## Next Steps
Proceed to [Day 6: Advanced Linux Commands](../Day_06/notes_and_exercises.md) to learn powerful text processing tools like grep, awk, and sed.

