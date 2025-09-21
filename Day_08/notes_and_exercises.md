# Day 08: Linux Process Management (ps, top, kill, nice, jobs, etc.)

## Notes
- **Why Process Management Matters:**
  - Managing processes is essential for system stability, troubleshooting, and performance tuning.
  - Understanding process states, priorities, and signals is key for DevOps and SRE roles.

- **Process Concepts:**
  - Every running program is a process with a unique PID (Process ID).
  - Parent/child relationships: processes can spawn other processes.
  - Foreground vs. background processes; interactive vs. daemon processes.

- **Viewing Processes:**
  - `ps aux` — List all processes with details (user, PID, CPU, MEM, command)
  - `ps -ef` — Alternative format
  - `pgrep <pattern>` — Find PIDs by name
  - `pstree` — Visualize process hierarchy
  - `top` — Real-time process monitor (press `h` for help)
    - `htop` — Enhanced, interactive version (if installed)

- **Controlling Processes:**
  - `kill <PID>` — Send signal to process (default: SIGTERM)
  - `kill -9 <PID>` — Force kill (SIGKILL)
  - `pkill <pattern>` — Kill by name
  - `killall <name>` — Kill all by name
  - `xkill` — Click to kill a GUI process

- **Process Priorities:**
  - `nice -n <value> <command>` — Start with priority (lower value = higher priority, range: -20 to 19)
  - `renice <value> -p <PID>` — Change priority of running process

- **Background & Job Control:**
  - `&` — Run command in background: `sleep 60 &`
  - `jobs` — List background jobs
  - `fg %1` — Bring job to foreground
  - `bg %1` — Resume job in background
  - `Ctrl+Z` — Suspend foreground job
  - `disown` — Remove job from shell's job table

- **Signals:**
  - `kill -l` — List all signals
  - Common: SIGTERM (15), SIGKILL (9), SIGHUP (1), SIGSTOP (19), SIGCONT (18)

- **Monitoring & Debugging:**
  - `strace -p <PID>` — Trace system calls
  - `lsof -p <PID>` — List open files by process
  - `ps -o pid,ppid,cmd,%mem,%cpu -p <PID>` — Custom output

- **Best Practices:**
  - Avoid using `kill -9` unless necessary (no cleanup)
  - Monitor resource usage with `top`/`htop` for troubleshooting
  - Use job control for long-running or background tasks
  - Regularly audit running processes for security

- **Diagram:**
  - ![Process Tree Diagram](https://upload.wikimedia.org/wikipedia/commons/6/6e/Process_tree.png)

## Sample Exercises
1. List all processes for a specific user.
2. Start a process in the background and bring it to the foreground.
3. Find and kill all processes named `python`.
4. Change the priority of a running process.
5. Display a process tree for the system.
6. Monitor CPU and memory usage of processes in real time.
7. Suspend a process and then resume it in the background.
8. List all open files for a given process.

## Sample Interview Questions
1. What is the difference between `kill` and `kill -9`?
2. How do you change the priority of a running process?
3. What is the purpose of the `jobs`, `fg`, and `bg` commands?
4. How do you find the parent process of a given PID?
5. What is the difference between `ps aux` and `top`?
6. How do you monitor all processes started by a specific user?
7. What is a zombie process and how do you handle it?
8. How do you list all signals that can be sent to a process?
9. What is the effect of `disown`?
10. How do you debug a misbehaving process?

## Solutions
1. `ps -u username` — Lists all processes for a specific user.
2. `sleep 100 &`, then `fg %1` — Starts a process in the background and brings it to the foreground.
3. `pkill python` or `killall python` — Finds and kills all processes named `python`.
4. `renice -n 5 -p <PID>` — Changes the priority of a running process.
5. `pstree` or `ps -ejH` — Displays a process tree for the system.
6. `top` or `htop` — Monitors CPU and memory usage of processes in real time.
7. `Ctrl+Z`, then `bg %1` — Suspends a process and resumes it in the background.
8. `lsof -p <PID>` — Lists all open files for a given process.

## Interview Question Answers
1. `kill` sends a signal (default SIGTERM) allowing cleanup; `kill -9` (SIGKILL) forcefully terminates without cleanup.
2. Use `renice -n <value> -p <PID>` to change the priority of a running process.
3. `jobs` lists background jobs; `fg` brings a job to the foreground; `bg` resumes a suspended job in the background.
4. Use `ps -o ppid= -p <PID>` or `ps -fp <PID>` to find the parent process.
5. `ps aux` is a snapshot of all processes; `top` is a real-time, interactive process monitor.
6. `ps -u <username>` or `pgrep -u <username>` to monitor all processes started by a user.
7. A zombie process is a completed process not yet reaped by its parent; handled by killing/restarting the parent or using `wait`.
8. `kill -l` lists all signals that can be sent to a process.
9. `disown` removes a job from the shell's job table, so it won't be affected by shell exit.
10. Use `strace`, `lsof`, `top`, logs, and check resource usage to debug a misbehaving process.
