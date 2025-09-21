# Day 07: Users, Groups & Permissions (Advanced)

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

- **Diagram:**
  - ![User/Group/Permission Diagram](https://upload.wikimedia.org/wikipedia/commons/6/6a/Unix_file_permissions_reference.png)

## Sample Exercises
1. Create a user `devops1` with home directory and bash shell, set a password, and add to `sudo` and `docker` groups.
2. Create a group `projectX` and add multiple users to it.
3. Change the default shell for a user to `/bin/zsh`.
4. Lock a user account and then unlock it.
5. Set SUID, SGID, and Sticky Bit on a test file/directory and demonstrate their effects.
6. List all users with UID >= 1000.
7. Remove a user and their home directory.
8. Change ownership and permissions of a file so only a specific group can read/write it.

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

## Solutions
1. `adduser devops1` (or `useradd -m -s /bin/bash devops1`), `passwd devops1`, `usermod -aG sudo,docker devops1`
2. `groupadd projectX`, `usermod -aG projectX user1`, `usermod -aG projectX user2`
3. `usermod -s /bin/zsh username`
4. `usermod -L username`, `usermod -U username`
5. `chmod u+s testfile`, `chmod g+s testdir`, `chmod +t testdir`
6. `awk -F: '$3>=1000{print $1}' /etc/passwd`
7. `userdel -r username`
8. `chown :groupname filename`, `chmod 660 filename`
