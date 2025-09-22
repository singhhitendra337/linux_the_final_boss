# Day 08: File Management & Editors (nano, vi/vim)

## Learning Objectives
By the end of Day 8, you will:
- Master advanced file operations and management
- Use nano for simple text editing
- Understand vi/vim basics for system administration
- Manage file attributes and metadata
- Apply file compression and archiving techniques

**Estimated Time:** 3-4 hours

## Notes
- **Why File Management & Editors Matter:**
  - Essential for configuration management, log analysis, and system administration
  - Text editors are crucial for editing config files, scripts, and documentation
  - Understanding file attributes helps with troubleshooting and security

- **Advanced File Operations:**
  ```bash
  # File attributes
  stat filename                    # Detailed file information
  file filename                    # Determine file type
  wc filename                      # Word, line, character count
  wc -l filename                   # Line count only
  
  # File comparison
  diff file1 file2                 # Show differences
  diff -u file1 file2              # Unified diff format
  cmp file1 file2                  # Binary comparison
  
  # File timestamps
  touch -t 202301011200 file       # Set specific timestamp
  touch -r reference_file file     # Copy timestamp from reference
  ```

- **nano Editor (Beginner-Friendly):**
  ```bash
  nano filename                    # Open file in nano
  
  # Key shortcuts (shown at bottom)
  Ctrl+O                          # Save (WriteOut)
  Ctrl+X                          # Exit
  Ctrl+W                          # Search (Where Is)
  Ctrl+\                          # Search and replace
  Ctrl+K                          # Cut line
  Ctrl+U                          # Paste (UnCut)
  Ctrl+G                          # Help
  ```

- **vi/vim Editor (Advanced):**
  ```bash
  vi filename                      # Open file in vi
  vim filename                     # Open file in vim (enhanced vi)
  
  # Modes
  # Normal mode (default) - navigation and commands
  # Insert mode - text editing
  # Command mode - save, quit, search
  
  # Mode switching
  i                               # Enter insert mode
  Esc                             # Return to normal mode
  :                               # Enter command mode
  
  # Navigation (Normal mode)
  h, j, k, l                      # Left, down, up, right
  w, b                            # Next/previous word
  0, $                            # Beginning/end of line
  gg, G                           # First/last line
  
  # Editing (Normal mode)
  x                               # Delete character
  dd                              # Delete line
  yy                              # Copy line
  p                               # Paste
  u                               # Undo
  Ctrl+r                          # Redo
  
  # Command mode
  :w                              # Save
  :q                              # Quit
  :wq or :x                       # Save and quit
  :q!                             # Quit without saving
  /pattern                        # Search forward
  ?pattern                        # Search backward
  :%s/old/new/g                   # Replace all occurrences
  ```

- **File Attributes and Metadata:**
  ```bash
  # Extended attributes
  lsattr filename                  # List attributes
  chattr +i filename               # Make immutable
  chattr -i filename               # Remove immutable
  
  # File ACLs (Access Control Lists)
  getfacl filename                 # Get ACL
  setfacl -m u:user:rw filename    # Set user permissions
  ```

## Sample Exercises
1. Create a file using nano, add content, and save it.
2. Open a file in vim, navigate to a specific line, and make edits.
3. Compare two similar files and identify differences.
4. Use vim to search and replace text in a configuration file.
5. Set file attributes to make a file immutable.
6. Count lines, words, and characters in a text file.
7. Create a backup of a file with timestamp.
8. Use vim to edit multiple files simultaneously.

## Sample Interview Questions
1. What is the difference between nano and vim?
2. How do you save and exit in vim?
3. What are the different modes in vim?
4. How do you search and replace text in vim?
5. What is the purpose of the `stat` command?
6. How do you make a file immutable in Linux?
7. What is the difference between `diff` and `cmp`?
8. How do you count lines in a file?
9. How do you copy timestamps from one file to another?
10. What are file ACLs and how do you manage them?

## Solutions
1. **Create file with nano:**
   ```bash
   nano myfile.txt
   # Type content, Ctrl+O to save, Ctrl+X to exit
   ```

2. **Edit with vim:**
   ```bash
   vim filename
   :10                             # Go to line 10
   i                               # Enter insert mode
   # Make edits
   Esc                             # Exit insert mode
   :wq                             # Save and quit
   ```

3. **Compare files:**
   ```bash
   diff file1.txt file2.txt
   diff -u file1.txt file2.txt     # Unified format
   ```

4. **Search and replace in vim:**
   ```bash
   vim config.conf
   :%s/old_value/new_value/g       # Replace all occurrences
   :wq                             # Save and quit
   ```

5. **Make file immutable:**
   ```bash
   sudo chattr +i important.conf
   lsattr important.conf           # Verify attribute
   ```

6. **Count file content:**
   ```bash
   wc filename                     # Lines, words, characters
   wc -l filename                  # Lines only
   ```

7. **Backup with timestamp:**
   ```bash
   cp file.txt file.txt.$(date +%Y%m%d_%H%M%S)
   ```

8. **Edit multiple files in vim:**
   ```bash
   vim file1.txt file2.txt
   :next                           # Switch to next file
   :prev                           # Switch to previous file
   ```

## Interview Question Answers
1. **nano vs vim:** nano is beginner-friendly with on-screen help; vim is powerful but has steeper learning curve
2. **vim Save/Exit:** `:w` saves, `:q` quits, `:wq` saves and quits, `:q!` quits without saving
3. **vim Modes:** Normal (navigation/commands), Insert (text editing), Command (save/search/quit)
4. **vim Search/Replace:** `:%s/old/new/g` replaces all occurrences globally
5. **stat Command:** Shows detailed file information including size, permissions, timestamps, inode
6. **Immutable Files:** Use `chattr +i filename` to make file unchangeable, even by root
7. **diff vs cmp:** `diff` shows line-by-line differences in text files; `cmp` compares files byte-by-byte
8. **Count Lines:** `wc -l filename` or `cat filename | wc -l`
9. **Copy Timestamps:** `touch -r source_file target_file` copies timestamps
10. **File ACLs:** Extended permissions beyond standard rwx; managed with `getfacl` and `setfacl`

## Completion Checklist
- [ ] Can create and edit files using nano
- [ ] Understand basic vim navigation and editing
- [ ] Know how to save and exit both editors
- [ ] Can compare files and identify differences
- [ ] Understand file attributes and metadata
- [ ] Can use search and replace in text editors

## Key Commands Summary
```bash
# nano editor
nano filename                    # Open file
Ctrl+O, Ctrl+X                  # Save, exit

# vim editor
vim filename                     # Open file
i, Esc                          # Insert mode, normal mode
:w, :q, :wq                     # Save, quit, save & quit

# File operations
stat filename                    # File information
diff file1 file2                 # Compare files
wc -l filename                   # Count lines
touch -r ref_file target         # Copy timestamp
```

## Next Steps
Proceed to [Day 9: File Transfer](../Day_09/notes_and_exercises.md) to learn secure file transfer methods.
