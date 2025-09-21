# Day 11: Pipes, Redirects, Wildcards, and Links

## Notes
- **Why These Tools Matter:**
  - Essential for chaining commands, automating tasks, and flexible file management in Linux.
  - Mastery is expected for DevOps, SRE, and system engineering roles.

- **Pipes (`|`):**
  - Pass output of one command as input to another.
  - Example: `cat file.txt | grep error | sort | uniq`

- **Redirects:**
  - `>`: Redirect output to file (overwrite)
  - `>>`: Redirect output to file (append)
  - `<`: Use file as input
  - `2>`: Redirect stderr
  - `2>&1`: Redirect stderr to stdout
  - Example: `ls > files.txt 2> errors.txt`

- **Wildcards (Globbing):**
  - `*`: Any number of characters
  - `?`: Single character
  - `[abc]`: Any one character in set
  - `[a-z]`: Any character in range
  - Example: `ls *.txt`, `rm file?.log`, `ls [a-c]*`

- **Links:**
  - **Hard Link:** `ln file1 file2` — Same inode, file exists until all links are deleted
  - **Symbolic Link (Symlink):** `ln -s target linkname` — Pointer to another file or directory
  - Use `ls -li` to view inodes and link types

- **Best Practices:**
  - Use pipes to build powerful one-liners
  - Redirect output to log files for troubleshooting
  - Use wildcards carefully to avoid accidental deletion
  - Prefer symlinks for configs/scripts; use hard links for backup/versioning

- **Diagram:**
  - ![Pipes and Redirects Diagram](https://upload.wikimedia.org/wikipedia/commons/4/4b/Unix_pipeline.svg)

## Sample Exercises
1. Use pipes to count the number of lines containing "error" in a log file.
2. Redirect both stdout and stderr of a command to a file.
3. List all files starting with "test" and ending with ".sh" in a directory.
4. Create a symbolic link and a hard link for a file, then show the difference.
5. Use wildcards to delete all `.tmp` files in a directory.

## Solutions
1. `grep error logfile.txt | wc -l`
2. `command > output.txt 2>&1`
3. `ls test*.sh`
4. `ln -s file.txt symlink.txt`; `ln file.txt hardlink.txt`; use `ls -li` to compare inodes.
5. `rm *.tmp`

## Sample Interview Questions
1. What is the difference between a pipe and a redirect?
2. How do you append output to a file instead of overwriting it?
3. What is the difference between a hard link and a symbolic link?
4. How do you use wildcards to match files?
5. How do you redirect both stdout and stderr to the same file?
6. What happens if you delete the original file for a symlink? For a hard link?
7. How do you find all files with a certain extension in a directory and its subdirectories?
8. What are the risks of using wildcards with `rm`?
9. How do you check the inode number of a file?
10. How do you use pipes to combine multiple commands?

## Interview Question Answers
1. A pipe (`|`) passes output from one command to another; a redirect (`>`, `>>`, `<`) sends output/input to or from files.
2. Use `>>` to append output to a file.
3. Hard links share the same inode and data; symlinks point to a path. Hard links can't cross filesystems; symlinks can.
4. Use `*`, `?`, and `[]` to match patterns in filenames.
5. Use `command > file 2>&1` to redirect both stdout and stderr.
6. Deleting the original file for a symlink breaks the link; for a hard link, the file remains accessible via other links.
7. Use `find . -name '*.ext'` to find files recursively.
8. Wildcards with `rm` can match more files than intended; always double-check with `ls` first.
9. Use `ls -li` or `stat filename` to check inode numbers.
10. Pipes allow you to chain commands, e.g., `cat file | grep pattern | sort | uniq`.