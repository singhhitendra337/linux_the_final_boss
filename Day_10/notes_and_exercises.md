# Day 10: Environment Variables, Aliases & Shell Customization

## Learning Objectives
By the end of Day 10, you will:
- Understand and manage environment variables
- Create and manage shell aliases
- Customize shell prompts and behavior
- Configure shell startup files
- Create useful shell functions

**Estimated Time:** 2-3 hours

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



## Sample Exercises
1. Add a directory to your PATH and verify it works.
2. Create an alias for a long command you use often.
3. Write a shell function to create and enter a directory in one step.
4. Change your shell prompt to show the current directory and username.
5. Make an environment variable permanent for all future sessions.

- **Shell Functions:**
  ```bash
  # Useful functions to add to ~/.bashrc
  mkcd() {
      mkdir -p "$1" && cd "$1"
  }
  
  backup() {
      cp "$1" "$1.backup.$(date +%Y%m%d_%H%M%S)"
  }
  
  extract() {
      case $1 in
          *.tar.gz) tar -xzf "$1" ;;
          *.zip) unzip "$1" ;;
          *.tar) tar -xf "$1" ;;
          *) echo "Unsupported format" ;;
      esac
  }
  ```

- **Shell Configuration Files:**
  - `~/.bashrc`: Interactive non-login shells (most terminal sessions)
  - `~/.bash_profile`: Login shells (SSH, console login)
  - `~/.zshrc`: Zsh configuration
  - `/etc/profile`: System-wide profile
  - `/etc/bash.bashrc`: System-wide bashrc

## Sample Exercises
1. Add a directory to your PATH and verify it works.
2. Create an alias for a long command you use often.
3. Write a shell function to create and enter a directory in one step.
4. Change your shell prompt to show the current directory and username.
5. Make an environment variable permanent for all future sessions.
6. Create a function to quickly backup files with timestamps.
7. Set up a custom prompt with colors and git branch information.

## Solutions
1. **Add to PATH:**
   ```bash
   export PATH="$PATH:/my/new/dir"
   echo 'export PATH="$PATH:/my/new/dir"' >> ~/.bashrc
   source ~/.bashrc
   ```

2. **Create alias:**
   ```bash
   alias ll='ls -alF'
   alias gs='git status'
   alias la='ls -la'
   echo "alias ll='ls -alF'" >> ~/.bashrc
   ```

3. **Shell function:**
   ```bash
   mkcd() { mkdir -p "$1" && cd "$1"; }
   echo 'mkcd() { mkdir -p "$1" && cd "$1"; }' >> ~/.bashrc
   ```

4. **Custom prompt:**
   ```bash
   export PS1='\u@\h:\w$ '
   # With colors:
   export PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]$ '
   ```

5. **Permanent environment variable:**
   ```bash
   echo 'export MYVAR="myvalue"' >> ~/.bashrc
   source ~/.bashrc
   echo $MYVAR
   ```

6. **Backup function:**
   ```bash
   backup() {
       cp "$1" "$1.backup.$(date +%Y%m%d_%H%M%S)"
       echo "Backup created: $1.backup.$(date +%Y%m%d_%H%M%S)"
   }
   ```

7. **Advanced prompt with git:**
   ```bash
   # Add to ~/.bashrc
   parse_git_branch() {
       git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/ (\1)/'
   }
   export PS1='\u@\h:\w\[\033[32m\]$(parse_git_branch)\[\033[00m\]$ '
   ```

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
1. **Variables:** Shell variables are local to current shell; environment variables are inherited by child processes
2. **Export Variables:** Use `export VAR=value` to make variable available to child processes
3. **Permanent Alias:** Add alias to `~/.bashrc` or `~/.zshrc` and reload shell with `source ~/.bashrc`
4. **PATH Purpose:** Tells shell which directories to search for executable commands
5. **Custom Prompt:** Set PS1 variable in shell config file with escape sequences for colors/info
6. **Remove Variable:** Use `unset VARNAME` to remove environment variable from current session
7. **Config Files:** `~/.bashrc` for interactive non-login shells; `~/.bash_profile` for login shells
8. **Apply Changes:** Use `source ~/.bashrc` or `. ~/.bashrc` to reload configuration without restarting shell
9. **Safe Aliases:** Avoid common command names, use `command cmd` to bypass aliases, or use full paths
10. **Version Control:** Allows backup, sharing, and restoration of customizations across systems

## Completion Checklist
- [ ] Understand difference between shell and environment variables
- [ ] Can create and manage aliases
- [ ] Know how to customize shell prompt
- [ ] Can write and use shell functions
- [ ] Understand shell configuration files
- [ ] Can make changes permanent

## Key Commands Summary
```bash
# Environment variables
export VAR=value                 # Set environment variable
echo $VAR                        # Display variable
unset VAR                        # Remove variable
printenv                         # List all variables

# Aliases
alias name='command'             # Create alias
alias                            # List all aliases
unalias name                     # Remove alias

# Shell configuration
source ~/.bashrc                 # Reload configuration
echo 'config' >> ~/.bashrc      # Add to config file
```

## Best Practices
- Keep customizations in version control (dotfiles repo)
- Comment your aliases and functions
- Test changes before making permanent
- Avoid overriding system commands
- Use meaningful names for variables and functions
- Backup configuration files before major changes

## Next Steps
Proceed to [Day 11: Pipes, Redirects, Wildcards, and Links](../Day_11/notes_and_exercises.md) to master command chaining and I/O redirection.
