# Day 20: Basic Shell Scripting for DevOps & Linux Engineers

## Why Shell Scripting Matters
- Automates repetitive tasks, improves efficiency, and reduces manual errors.
- Essential for DevOps, SRE, and system administration roles.
- Forms the foundation for advanced automation and CI/CD pipelines.

## Key Concepts
- What is a shell script?
- Shebang (`#!/bin/bash`)
- Script permissions (`chmod +x script.sh`)
- Variables and quoting
- Reading user input (`read`)
- Conditionals (`if`, `elif`, `else`)
- Loops (`for`, `while`, `until`)
- Functions
- Exit codes
- Comments (`#`)

## Basic Shell Script Structure
```bash
#!/bin/bash
# This is a comment
echo "Hello, World!"
```

## Common Commands in Scripts
- `echo`, `read`, `date`, `pwd`, `ls`, `cat`, `grep`, `awk`, `sed`, `cut`, `head`, `tail`, `wc`, `sort`, `uniq`, `tr`, `basename`, `dirname`, `test`/`[ ]`

## Practical Examples
### 1. Hello World
```bash
#!/bin/bash
echo "Hello, World!"
```

### 2. Variables and User Input
```bash
#!/bin/bash
echo "Enter your name:"
read name
echo "Hello, $name!"
```

### 3. If-Else Example
```bash
#!/bin/bash
if [ -f /etc/passwd ]; then
  echo "/etc/passwd exists."
else
  echo "/etc/passwd not found."
fi
```

### 4. For Loop Example
```bash
#!/bin/bash
for i in 1 2 3; do
  echo "Number: $i"
done
```

### 5. Function Example
```bash
#!/bin/bash
say_hello() {
  echo "Hello, $1!"
}
say_hello "Linux Engineer"
```

## Best Practices
- Always use a shebang.
- Use `set -e` for safer scripts (exit on error).
- Quote variables: `"$var"`.
- Use functions for reusable code.
- Add comments for clarity.
- Test scripts in a safe environment.

## Exercises
1. Write a script to print the current date and time.
2. Write a script that takes a filename as input and checks if it exists.
3. Write a script to print numbers 1 to 10 using a loop.
4. Write a script to count the number of lines in a file.
5. Write a script that accepts a username and greets the user.

## Solutions
### 1. Print current date and time
```bash
#!/bin/bash
date
```

### 2. Check if file exists
```bash
#!/bin/bash
echo "Enter filename:"
read fname
if [ -f "$fname" ]; then
  echo "$fname exists."
else
  echo "$fname does not exist."
fi
```

### 3. Print numbers 1 to 10
```bash
#!/bin/bash
for i in {1..10}; do
  echo $i
done
```

### 4. Count lines in a file
```bash
#!/bin/bash
echo "Enter filename:"
read fname
if [ -f "$fname" ]; then
  wc -l < "$fname"
else
  echo "File not found."
fi
```

### 5. Greet user by name
```bash
#!/bin/bash
echo "Enter your name:"
read name
echo "Hello, $name!"
```

## Interview-Style Questions & Answers
1. **What is a shebang and why is it important?**
   - The shebang (`#!/bin/bash`) at the top of a script tells the system which interpreter to use to run the script.
2. **How do you make a script executable?**
   - Use `chmod +x script.sh` to make the script executable.
3. **How do you pass arguments to a shell script?**
   - Arguments are accessed as `$1`, `$2`, etc. Example: `./script.sh arg1 arg2`.
4. **What is the difference between `$*` and `$@`?**
   - Both represent all arguments, but `$@` preserves argument boundaries when quoted.
5. **How do you handle errors in shell scripts?**
   - Check exit codes (`$?`), use `set -e`, and add error handling logic.

---

This covers the basics of shell scripting. For advanced scripting (arrays, traps, debugging, etc.), see the upcoming advanced shell scripting series.
