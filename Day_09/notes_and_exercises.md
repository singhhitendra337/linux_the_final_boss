# Day 09: File Transfer & Remote Access (SSH, SCP, SFTP, rsync, NFS, Samba)

## Notes
- **Why File Transfer & Remote Access Matter:**
  - Essential for system administration, automation, backups, and collaboration.
  - Secure and efficient file transfer is a core DevOps/SRE skill.

- **SSH (Secure Shell):**
  - Secure remote login and command execution.
  - `ssh user@host` — Connect to remote host
  - `ssh -i key.pem user@host` — Use private key
  - `ssh -p 2222 user@host` — Custom port
  - `ssh -L local_port:remote_host:remote_port user@host` — Port forwarding
  - `ssh-copy-id user@host` — Copy public key for passwordless login

- **SCP (Secure Copy):**
  - Copy files securely between hosts.
  - `scp file.txt user@host:/path/` — Upload file
  - `scp user@host:/path/file.txt .` — Download file
  - `scp -r dir/ user@host:/path/` — Recursive copy
  - `scp -P 2222 ...` — Custom port

- **SFTP (SSH File Transfer Protocol):**
  - Interactive file transfer over SSH.
  - `sftp user@host` — Start SFTP session
  - Commands: `put`, `get`, `ls`, `cd`, `mget`, `mput`, `exit`

- **rsync:**
  - Fast, incremental file transfer and synchronization.
  - `rsync -avz source/ user@host:/dest/` — Archive, verbose, compress
  - `rsync -e ssh ...` — Use SSH for transfer
  - `rsync --delete ...` — Delete files not in source
  - `rsync -n ...` — Dry run

- **NFS (Network File System):**
  - Share directories over network (Linux/Unix)
  - `/etc/exports` — NFS server config
  - `mount -t nfs server:/share /mnt` — Mount NFS share
  - `showmount -e server` — List exports

- **Samba (SMB/CIFS):**
  - Share files between Linux and Windows
  - `smbclient //server/share -U user` — Access share
  - `mount -t cifs //server/share /mnt -o user=user` — Mount SMB share
  - Config: `/etc/samba/smb.conf`

- **Best Practices:**
  - Use SSH keys, disable password login for security
  - Use `rsync` for large or repeated transfers
  - Limit NFS/Samba access to trusted networks
  - Monitor and audit remote access logs
  - Use strong passwords and encryption

- **Diagram:**
  - ![File Transfer Diagram](https://upload.wikimedia.org/wikipedia/commons/3/3a/SSH_architecture_diagram.png)

## Sample Exercises
1. Set up SSH key-based authentication between two machines.
2. Transfer a directory recursively to a remote server using `scp`.
3. Synchronize two directories using `rsync` and verify changes.
4. Mount an NFS share on a client machine.
5. Access a Samba share from Linux and Windows.
6. Use SFTP to upload and download files interactively.
7. Perform a dry run with `rsync` before actual transfer.
8. Restrict SSH access to a specific user.

## Sample Interview Questions
1. What is the difference between SCP and SFTP?
2. How do you set up passwordless SSH login?
3. How does `rsync` differ from `scp`?
4. What are the security risks of NFS and how do you mitigate them?
5. How do you mount a Samba share on Linux?
6. How do you restrict SSH access to certain users or IPs?
7. What is the purpose of `ssh-copy-id`?
8. How do you troubleshoot failed SSH connections?
9. How do you automate file transfers securely?
10. What are the advantages of using `rsync` for backups?

## Interview Question Answers
1. SCP is a simple, non-interactive file transfer; SFTP is interactive and allows more operations.
2. Generate a key with `ssh-keygen`, then copy with `ssh-copy-id user@host`.
3. `rsync` syncs files and directories efficiently, only transferring changes; `scp` copies files as-is.
4. NFS is not encrypted and can be vulnerable; mitigate with firewalls, exports restrictions, and root_squash.
5. Use `mount -t cifs //server/share /mnt -o user=user` to mount a Samba share.
6. Edit `/etc/ssh/sshd_config` to set `AllowUsers` or use firewall rules.
7. `ssh-copy-id` copies your public key to a remote host for passwordless login.
8. Check SSH logs, use `ssh -v`, and verify network/firewall settings to troubleshoot.
9. Use `rsync` with SSH, or scripts with key-based auth for secure automation.
10. `rsync` is fast, incremental, supports resume, and is ideal for backups.

## Solutions
1. `ssh-keygen`, then `ssh-copy-id user@host`
2. `scp -r dir/ user@host:/path/`
3. `rsync -avz source/ user@host:/dest/`, then check with `rsync -n ...`
4. `mount -t nfs server:/share /mnt`
5. `smbclient //server/share -U user` or `mount -t cifs ...`
6. `sftp user@host`, then `put`/`get`
7. `rsync -n -avz ...`
8. Edit `/etc/ssh/sshd_config` (AllowUsers) and restart SSH
