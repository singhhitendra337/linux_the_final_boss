# Day 13: Process Management & Scheduling (cron, at, anacron)

## Notes
- **Why Scheduling Matters:**
  - Automates repetitive tasks, backups, monitoring, and maintenance.
  - Essential for DevOps, SRE, and system admins.

- **Process Management Recap:**
  - `ps`, `top`, `htop`, `kill`, `nice`, `renice`, `jobs`, `fg`, `bg` (see Day 8)

- **Job Scheduling Tools:**
  - `cron`: Schedule recurring tasks
    - Edit with `crontab -e`
    - Syntax: `* * * * * command` (min, hour, day, month, weekday)
    - List: `crontab -l`; Remove: `crontab -r`
    - System-wide: `/etc/crontab`, `/etc/cron.*`
  - `at`: Schedule one-time tasks
    - `at 10:00` (then enter commands, Ctrl+D to finish)
    - List: `atq`; Remove: `atrm <job#>`
  - `anacron`: Run periodic jobs not guaranteed to run at set times (good for laptops)
    - Config: `/etc/anacrontab`

- **Crontab Examples:**
  - `0 2 * * * /usr/bin/backup.sh` — Run daily at 2am
  - `*/5 * * * * /usr/bin/check.sh` — Every 5 minutes

- **Best Practices:**
  - Use absolute paths in cron jobs
  - Redirect output to log files for troubleshooting
  - Test jobs manually before scheduling
  - Document scheduled tasks

- **Diagram:**
  - ![Cron Diagram](https://upload.wikimedia.org/wikipedia/commons/6/6b/Cron_table.png)

## In-Depth Explanations & Scenarios
- **Cron Syntax Deep Dive:**
  - Format: `min hour day month weekday command`
  - Special strings: `@reboot`, `@daily`, `@hourly`, `@weekly`, `@monthly`, `@yearly`
- **Environment in Cron:**
  - Cron jobs run with minimal environment. Always use full paths for commands and files.
- **Real-World Example:**
  - Schedule log rotation, database dumps, or system updates with cron.
- **at vs. cron vs. anacron:**
  - `at` for one-time jobs (e.g., shutdown at 2am), `cron` for recurring, `anacron` for periodic jobs on laptops/desktops.
- **Monitoring & Troubleshooting:**
  - Check `/var/log/cron` or `/var/log/syslog` for job output/errors.
  - Use `MAILTO` in crontab to get job results by email.
- **Best Practices (Expanded):**
  - Use lock files or `flock` to prevent overlapping jobs.
  - Document all scheduled jobs in version control.
  - Test scripts interactively before scheduling.

## Sample Exercises
1. Schedule a script to run every day at midnight using cron.
2. Schedule a one-time job to run 10 minutes from now using `at`.
3. List all current user's cron jobs.
4. Set up an anacron job to run a script daily.
5. Redirect cron job output to a log file.

## Solutions
1. `crontab -e` and add: `0 0 * * * /path/to/script.sh`
2. `echo "/path/to/script.sh" | at now + 10 minutes`
3. `crontab -l`
4. Edit `/etc/anacrontab` and add: `1 5 myjob /path/to/script.sh`
5. `0 0 * * * /path/to/script.sh > /var/log/script.log 2>&1`

## Sample Interview Questions
1. What is the difference between cron, at, and anacron?
2. How do you edit and list cron jobs for a user?
3. What is the crontab syntax?
4. How do you schedule a job to run every Monday at 5am?
5. How do you redirect output from a cron job?
6. What are the risks of running scripts as root in cron?
7. How do you prevent overlapping cron jobs?
8. How do you troubleshoot a cron job that isn't running?
9. Where are system-wide cron jobs defined?
10. How do you schedule a job to run only once?

## Interview Question Answers
1. `cron` schedules recurring jobs, `at` schedules one-time jobs, `anacron` runs periodic jobs that may be missed.
2. Use `crontab -e` to edit, `crontab -l` to list jobs for the current user.
3. `* * * * * command` (min, hour, day, month, weekday).
4. `0 5 * * 1 command` runs every Monday at 5am.
5. Add `> /path/to/log 2>&1` to the cron job line.
6. Scripts as root can be dangerous; restrict permissions and validate scripts.
7. Use lock files or tools like `flock` to prevent overlap.
8. Check cron logs (`/var/log/cron`), permissions, and script paths.
9. System-wide jobs: `/etc/crontab`, `/etc/cron.d/`, `/etc/cron.daily/`, etc.
10. Use `at` for one-time jobs.
