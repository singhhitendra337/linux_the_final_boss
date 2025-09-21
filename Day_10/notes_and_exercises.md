# Day 10: Environment Variables, Aliases & Shell Customization

## Notes
- **Why Customize Your Shell?**
  - Boosts productivity, reduces repetitive typing, and personalizes your workflow.
  - Essential for DevOps, SRE, and power users.

- **Environment Variables:**
  - Store configuration and session info (e.g., PATH, HOME, USER, SHELL).
  - View all: `printenv` or `env`
  - View one: `echo $PATH`
  - Set for session: `export VAR=value`
  - Set permanently: add to `~/.bashrc`, `~/.bash_profile`, or `~/.zshrc`
  - Remove: `unset VAR`

- **Common Environment Variables:**
  - `PATH`: Directories searched for executables
  - `HOME`: User's home directory
  - `USER`: Current username
  - `SHELL`: Default shell
  - `PS1`: Shell prompt format

- **Aliases:**
  - Shortcut for commands (e.g., `alias ll='ls -alF'`)
  - List all: `alias`
  - Remove: `unalias ll`
  - Make permanent: add to `~/.bashrc` or `~/.zshrc`

- **Shell Customization:**
  - Edit `~/.bashrc`, `~/.bash_profile`, or `~/.zshrc` for custom settings
  - Customize prompt: `PS1='\u@\h:\w$ '`
  - Add functions: e.g., `mkcd() { mkdir -p "$1" && cd "$1"; }`
  - Source file to apply changes: `source ~/.bashrc`

- **Best Practices:**
  - Keep customizations under version control (e.g., dotfiles repo)
  - Comment your aliases and functions
  - Avoid overriding critical commands (e.g., `rm`)
  - Test changes in a new shell before making permanent

- **Diagram:**
  - ![Shell Customization Diagram](https://upload.wikimedia.org/wikipedia/commons/6/6b/Bash_shell_customization.png)

## Sample Exercises
1. Add a directory to your PATH and verify it works.
2. Create an alias for a long command you use often.
3. Write a shell function to create and enter a directory in one step.
4. Change your shell prompt to show the current directory and username.
5. Make an environment variable permanent for all future sessions.

## Solutions
1. `export PATH="$PATH:/my/new/dir"`; add to `~/.bashrc` for permanence.
2. `alias gs='git status'`; add to `~/.bashrc` or `~/.zshrc`.
3. `mkcd() { mkdir -p "$1" && cd "$1"; }`; add to shell config file.
4. `export PS1='\u@\h:\w$ '`; add to shell config file.
5. Add `export VAR=value` to `~/.bashrc` or `~/.zshrc` and run `source ~/.bashrc`.

## Sample Interview Questions
1. What is the difference between a shell variable and an environment variable?
2. How do you make an environment variable available to all child processes?
3. How do you set a permanent alias?
4. What is the purpose of the PATH variable?
5. How do you customize your shell prompt?
6. How do you remove an environment variable?
7. What is the difference between `~/.bashrc` and `~/.bash_profile`?
8. How do you apply changes made to your shell configuration file?
9. How do you prevent accidental overwriting of important commands with aliases?
10. Why is it useful to keep your dotfiles under version control?

## Interview Question Answers
1. Shell variables are local to the shell; environment variables are inherited by child processes.
2. Use `export VAR=value` to make it available to child processes.
3. Add the alias to your shell config file (e.g., `~/.bashrc`) and reload the shell.
4. PATH tells the shell where to look for executable files.
5. Set the `PS1` variable in your shell config file.
6. Use `unset VAR` to remove an environment variable.
7. `~/.bashrc` is for interactive non-login shells; `~/.bash_profile` is for login shells.
8. Use `source ~/.bashrc` or restart the shell.
9. Avoid using common command names for aliases, or use `command <cmd>` to bypass aliases.
10. Version control allows you to back up, share, and restore your customizations easily.
