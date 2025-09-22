# Day 07: Users, Groups & Permissions

## Learning Objectives
By the end of Day 7, you will:
- Understand Linux user and group concepts
- Create, modify, and delete users and groups
- Manage file ownership and permissions
- Configure special permissions (SUID, SGID, Sticky Bit)
- Apply security best practices for user management

**Estimated Time:** 3-4 hours

## Notes
- **Why User & Group Management Matters:**
  - User and group management is foundational for system security, access control, and multi-user collaboration.
  - Proper configuration prevents privilege escalation and accidental data exposure.

- **User Concepts:**
  - Each user has a unique UID (User ID) and a default group (GID).
  - System users (UID < 1000) vs. regular users (UID >= 1000).
  - Home directories: `/home/<username>`
  - User info stored in `/etc/passwd`, passwords in `/etc/shadow` (hashed, root-only access).

- **Group Concepts:**
  - Groups allow collective permission management.
  - Primary group: assigned at user creation; secondary groups: for additional access.
  - Group info in `/etc/group`.

- **User Management Commands:**
  - `adduser <username>` / `useradd <username>`: Add user (use `adduser` for interactive, `useradd` for scriptable)
    - Options: `-m` (create home), `-s` (shell), `-G` (secondary groups), `-u` (UID)
  - `passwd <username>`: Set/change password
  - `usermod`: Modify user
    - `-aG <group>` (append to group), `-L` (lock), `-U` (unlock), `-d` (change home), `-s` (change shell)
  - `deluser <username>` / `userdel <username>`: Remove user
    - Options: `--remove-home` (delete home dir)

- **Group Management Commands:**
  - `groupadd <groupname>`: Create group
  - `groupdel <groupname>`: Delete group
  - `gpasswd -a <user> <group>`: Add user to group
  - `gpasswd -d <user> <group>`: Remove user from group

- **Viewing Users & Groups:**
  - `id <username>`: Show UID, GID, groups
  - `groups <username>`: List groups
  - `getent passwd` / `getent group`: Query user/group database
  - `cat /etc/passwd`, `cat /etc/group`, `cat /etc/shadow`

- **Switching Users:**
  - `su - <username>`: Switch user (requires password)
  - `sudo -i` or `sudo -u <user> <cmd>`: Run command as another user

- **File Ownership & Permissions:**
  - `chown <user>:<group> <file>`: Change owner/group
  - `chmod <mode> <file>`: Change permissions (symbolic/numeric)
  - `chgrp <group> <file>`: Change group

- **Special Permissions:**
  - **SUID:** `chmod u+s file` — Run as file owner (e.g., `/usr/bin/passwd`)
  - **SGID:** `chmod g+s dir` — New files inherit group; on files, run as group
  - **Sticky Bit:** `chmod +t dir` — Only owner/root can delete (e.g., `/tmp`)

- **Locking/Unlocking Accounts:**
  - `usermod -L <user>`: Lock
  - `usermod -U <user>`: Unlock
  - `passwd -l <user>`: Lock password

- **Best Practices:**
  - Use strong, unique passwords; enforce password policies
  - Grant sudo/admin rights only as needed
  - Regularly audit `/etc/passwd`, `/etc/shadow`, `/etc/group`
  - Remove unused users/groups promptly
  - Never use 777 permissions on sensitive files



## Sample Exercises
1. Create a user `devops1` with home directory and bash shell, set a password, and add to `sudo` and `docker` groups.
2. Create a group `projectX` and add multiple users to it.
3. Change the default shell for a user to `/bin/zsh`.
4. Lock a user account and then unlock it.
5. Set SUID, SGID, and Sticky Bit on a test file/directory and demonstrate their effects.
6. List all users with UID >= 1000.
7. Remove a user and their home directory.
8. Change ownership and permissions of a file so only a specific group can read/write it.



## Solutions
1. **Create user with groups:**
   ```bash
   sudo adduser devops1                        # Interactive creation
   # OR
   sudo useradd -m -s /bin/bash devops1        # Command-line creation
   sudo passwd devops1                         # Set password
   sudo usermod -aG sudo,docker devops1       # Add to groups
   ```

2. **Create group and add users:**
   ```bash
   sudo groupadd projectX                      # Create group
   sudo usermod -aG projectX user1             # Add user1
   sudo usermod -aG projectX user2             # Add user2
   getent group projectX                       # Verify members
   ```

3. **Change user shell:**
   ```bash
   sudo usermod -s /bin/zsh username
   chsh -s /bin/zsh username                   # Alternative method
   ```

4. **Lock and unlock account:**
   ```bash
   sudo usermod -L username                    # Lock account
   sudo passwd -l username                     # Lock password only
   sudo usermod -U username                    # Unlock account
   sudo passwd -u username                     # Unlock password
   ```

5. **Special permissions:**
   ```bash
   chmod u+s testfile                          # Set SUID
   chmod g+s testdir                           # Set SGID
   chmod +t testdir                            # Set Sticky Bit
   ls -l testfile testdir                      # Verify permissions
   ```

6. **List users with UID >= 1000:**
   ```bash
   awk -F: '$3>=1000{print $1, $3}' /etc/passwd
   getent passwd | awk -F: '$3>=1000{print $1}'
   ```

7. **Remove user and home directory:**
   ```bash
   sudo userdel -r username                    # Remove user and home
   sudo deluser --remove-home username         # Alternative
   ```

8. **Set group ownership and permissions:**
   ```bash
   sudo chown :groupname filename              # Change group only
   chmod 660 filename                          # rw-rw----
   chmod g+w filename                          # Add group write
   ```

## Completion Checklist
- [ ] Understand UID/GID concepts and system vs regular users
- [ ] Can create, modify, and delete users and groups
- [ ] Know the difference between primary and secondary groups
- [ ] Understand file ownership and permission models
- [ ] Can set and identify special permissions (SUID, SGID, Sticky)
- [ ] Familiar with user account security practices

## Key Commands Summary
```bash
# User management
sudo adduser username               # Create user (interactive)
sudo useradd -m -s /bin/bash user  # Create user (command-line)
sudo passwd username               # Set password
sudo usermod -aG group username    # Add to group
sudo userdel -r username           # Delete user and home

# Group management
sudo groupadd groupname            # Create group
sudo gpasswd -a user group         # Add user to group
sudo gpasswd -d user group         # Remove user from group

# Information
id username                        # Show user/group IDs
groups username                    # Show user's groups
getent passwd                      # List all users
getent group                       # List all groups

# Permissions
chmod 755 file                     # Set permissions (numeric)
chmod u+x,g-w file                 # Set permissions (symbolic)
chown user:group file              # Change ownership
chgrp group file                   # Change group only
```

## Permission Reference
```
Numeric  Symbolic  Meaning
4        r         Read
2        w         Write
1        x         Execute

Common combinations:
755 = rwxr-xr-x   (executable files)
644 = rw-r--r--   (regular files)
600 = rw-------   (private files)
777 = rwxrwxrwx   (avoid - security risk)
```

## Security Best Practices
- Use strong passwords and enforce password policies
- Grant minimum necessary permissions
- Regularly audit user accounts and group memberships
- Remove unused accounts promptly
- Use sudo instead of root login
- Monitor special permissions (SUID/SGID files)
- Set appropriate umask values

## Sample Interview Questions
1. What is the difference between `useradd` and `adduser`?
2. How do you add a user to multiple groups at once?
3. What is the purpose of the `/etc/shadow` file?
4. How do you enforce password policies for users?
5. Explain SUID, SGID, and Sticky Bit with real-world examples.
6. How do you list all members of a group?
7. What happens if you delete a user but not their files?
8. How do you switch to another user without knowing their password?
9. How do you find all files owned by a specific user?
10. What are the security risks of improper user/group management?

## Interview Question Answers
1. `adduser` is interactive and user-friendly; `useradd` is lower-level and scriptable.
2. A user is an individual account; a group is a collection of users for permission management.
3. Use `usermod -aG group1,group2 username` to add a user to multiple groups.
4. `/etc/passwd` stores user info; `/etc/shadow` stores encrypted passwords.
5. Use `su - username` to switch users, or `sudo -i` for root.
6. `su` switches user context; `sudo` runs a single command with elevated privileges.
7. Use `chown` to change ownership, `chmod` to change permissions.
8. SUID runs as file owner, SGID as group, Sticky Bit restricts deletion; e.g., `/tmp` uses Sticky Bit.
9. Use `cat /etc/passwd` for users, `groups username` for groups.
10. `usermod -L username` locks, `usermod -U username` unlocks an account.

## Sample Interview Questions
1. What is the difference between `useradd` and `adduser`?
2. How do you add a user to multiple groups at once?
3. What is the purpose of the `/etc/shadow` file?
4. How do you enforce password policies for users?
5. Explain SUID, SGID, and Sticky Bit with real-world examples.
6. How do you list all members of a group?
7. What happens if you delete a user but not their files?
8. How do you switch to another user without knowing their password?
9. How do you find all files owned by a specific user?
10. What are the security risks of improper user/group management?

## Interview Question Answers
1. **useradd vs adduser:** `useradd` is low-level, scriptable; `adduser` is interactive, user-friendly wrapper
2. **Multiple Groups:** `usermod -aG group1,group2 username` adds user to multiple groups
3. **/etc/shadow:** Stores encrypted passwords and password policies, readable only by root for security
4. **Password Policies:** Use PAM modules, `chage` command, `/etc/login.defs` for password aging and complexity
5. **Special Permissions:** SUID runs as file owner (e.g., `passwd`); SGID inherits group (directories); Sticky bit restricts deletion (`/tmp`)
6. **Group Members:** `getent group groupname` or `grep groupname /etc/group`
7. **Orphaned Files:** Files remain but show numeric UID instead of username; can cause security issues
8. **User Switching:** Use `sudo -u username command` or `sudo su - username` with proper sudo privileges
9. **Find User Files:** `find / -user username 2>/dev/null` finds all files owned by user
10. **Security Risks:** Privilege escalation, unauthorized access, orphaned files, weak passwords, excessive permissions

## Next Steps
Proceed to [Day 8: File Management & Editors](../Day_08/notes_and_exercises.md) to learn advanced file operations and text editors.
