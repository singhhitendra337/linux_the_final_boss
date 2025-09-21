# Day 06: Advanced Linux Commands (grep, awk, sed, find, xargs, etc.)

## Notes
- **Why Learn Advanced Linux Commands?**
  - These commands are essential for text processing, automation, and efficient system administration.
  - Mastery of these tools is expected in DevOps, SRE, and system engineering interviews.

- **grep (Global Regular Expression Print):**
  - Search for patterns in files.
  - `grep 'pattern' file.txt` — Basic search
  - `grep -i` (ignore case), `-r` (recursive), `-v` (invert match), `-n` (show line numbers), `-A/B/C N` (context lines)
  - `egrep` is equivalent to `grep -E` (extended regex)

- **awk:**
  - Powerful text processing and reporting tool.
  - `awk '{print $1}' file.txt` — Print first column
  - `awk -F: '{print $1, $3}' /etc/passwd` — Custom field separator
  - Useful for column extraction, calculations, and filtering

- **sed (Stream Editor):**
  - Edit text in streams/files non-interactively.
  - `sed 's/old/new/g' file.txt` — Replace text
  - `sed -n '5,10p' file.txt` — Print lines 5 to 10
  - `sed -i` — Edit file in place

- **find:**
  - Search for files and directories.
  - `find /path -name '*.log'` — Find files by name
  - `find /path -type f -size +10M` — Find files by type/size
  - `find /path -mtime -1` — Modified in last 24 hours
  - Combine with `-exec` or `xargs` for actions

- **xargs:**
  - Build and execute command lines from input.
  - `find . -name '*.log' | xargs rm` — Delete all .log files
  - `echo 'one two three' | xargs mkdir` — Create multiple directories

- **cut, sort, uniq, tr:**
  - `cut -d: -f1 /etc/passwd` — Extract fields
  - `sort file.txt` — Sort lines
  - `uniq file.txt` — Remove duplicates
  - `tr 'a-z' 'A-Z'` — Translate characters

- **Pipes (`|`):**
  - Chain commands together for powerful one-liners
  - Example: `cat file.txt | grep error | sort | uniq -c | sort -nr`

- **Best Practices:**
  - Use `man` pages and `--help` for command options
  - Test commands on sample files before running on important data
  - Combine tools for efficient data processing

## Sample Exercises
1. Search for the word "error" in all `.log` files in `/var/log` (case-insensitive).
2. Print the second column of `/etc/passwd` using `awk`.
3. Replace all occurrences of "foo" with "bar" in a file using `sed`.
4. Find all files larger than 100MB in your home directory.
5. Count the number of unique lines in a file.

## Sample Interview Questions
1. What is the difference between `grep` and `egrep`?
2. How do you use `awk` to process columns in a text file?
3. Give an example of using `sed` to replace text in a file.
4. How do you find all files modified in the last 24 hours?
5. What is the use of `xargs`? Give a practical example.
6. How do you combine `find` and `grep` to search for content in files?
7. What is the difference between `cut` and `awk`?
8. How do you sort and remove duplicate lines from a file?
9. How do you use `tr` for string manipulation?
10. How do you use pipes to chain multiple commands?

## Interview Question Answers
1. `grep` uses basic regex by default; `egrep` (or `grep -E`) uses extended regex, allowing more complex patterns.
2. Use `awk '{print $2}' file.txt` to print the second column; you can specify field separators with `-F`.
3. `sed 's/old/new/g' file.txt` replaces all occurrences of 'old' with 'new' in a file.
4. `find . -mtime -1` finds files modified in the last 24 hours.
5. `xargs` builds and executes command lines from input; e.g., `find . -name '*.log' | xargs rm` deletes all .log files.
6. `find . -type f -exec grep 'pattern' {} +` searches for content in files found by `find`.
7. `cut` extracts columns by delimiter; `awk` is more powerful for pattern matching and processing.
8. `sort file.txt | uniq` sorts and removes duplicate lines from a file.
9. `tr 'a-z' 'A-Z'` translates lowercase to uppercase; `tr` is used for character-level string manipulation.
10. Pipes (`|`) chain commands, passing output of one as input to the next for complex processing.
