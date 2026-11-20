# Bash Scripting Fundamentals – Lesson 15

## Project Title

**Linux Bash Scripting and Automation Fundamentals**

---

## Objective

The objective of this lesson was to understand Bash scripting and learn how to automate Linux commands using shell scripts.

The practical exercises focused on creating and executing Bash scripts, working with variables, accepting user input, implementing conditional statements, loops and functions, and building a Linux System Information and Monitoring Script.

---

## Lab Environment

| Component               | Details           |
|-------------------------|-------------------|
| Host Operating System   | Windows 11        |
| Server Machine          | Ubuntu Linux VM   |
| Virtualization Platform | Oracle VirtualBox |
| Shell                   | GNU Bash          |
| Text Editor             | Nano              |
| Practice Directory      | `~/bash_lab`      |

---

## 1. Introduction to Bash Scripting

Bash scripting is the process of writing multiple Linux commands in a file so they can execute automatically in sequence.

### Example

```bash
date
whoami
hostname
```

When these commands are placed inside a Bash script and executed, all three commands run sequentially.

### Applications of Bash Scripting

- Linux system administration
- Task automation
- Security monitoring
- Log analysis
- Backup operations
- Incident investigation

---

## 2. Understanding Bash Script Structure

A Bash script generally begins with a Shebang.

```bash
#!/bin/bash
```

### Shebang Explanation

| Component   | Purpose                              |
|-------------|--------------------------------------|
| `#!`        | Identifies the interpreter directive |
| `/bin/bash` | Specifies the Bash interpreter       |

### Comments in Bash

Comments begin with the `#` symbol and are ignored during script execution.

Example:

```bash
# This is a comment
```

---

## 3. Creating and Executing the First Bash Script

### Create a Practice Directory

```bash
mkdir -p ~/bash_lab
cd ~/bash_lab
```

### Create a Script

```bash
nano first_script.sh
```

Script content:

```bash
#!/bin/bash

# My first Bash script

echo "Welcome to Linux Administration!"
echo "-------------------------------"

date
whoami
hostname
```

### Give Execution Permission

```bash
chmod +x first_script.sh
```

### Execute the Script

```bash
./first_script.sh
```

### Alternative Execution Method

```bash
bash first_script.sh
```

**Key Learning:** A Bash script can be executed directly with execution permission or through the Bash interpreter.

---

## 4. Variables in Bash Scripts

Variables store information that can be reused throughout a script.

### Example

```bash
#!/bin/bash

name="Dhruv"
course="Cyber Security"

echo "My name is $name"
echo "My course is $course"
```

### Important Concepts

| Component | Meaning                     |
|-----------|-----------------------------|
| `name`    | Variable name               |
| `=`       | Assignment operator         |
| `"Dhruv"` | Stored value                |
| `$name`   | Accesses the variable value |

**Important:** Spaces must not be placed around the assignment operator.

Correct:

```bash
name="Dhruv"
```

Incorrect:

```bash
name = "Dhruv"
```

---

## 5. Taking User Input Using `read`

The `read` command allows a Bash script to accept input from the user.

### Example

```bash
#!/bin/bash

echo "Enter your name:"
read name

echo "Welcome, $name!"
```

### Using `read -p`

```bash
read -p "Enter your name: " name
```

This displays the prompt and accepts input on the same line.

### Hiding User Input

```bash
read -sp "Enter password: " password
echo
```

The `-s` option prevents the entered password from appearing on the terminal.

**Key Learning:** User input can be stored in variables and reused throughout a script.

---

## 6. Conditional Statements

Conditional statements allow scripts to make decisions based on specified conditions.

### Basic Structure

```bash
if [ condition ]; then
    # Commands if condition is true
else
    # Commands if condition is false
fi
```

### Practical Example

```bash
#!/bin/bash

read -p "Enter your age: " age

if [ "$age" -ge 18 ]; then
    echo "You are an adult."
else
    echo "You are a minor."
fi
```

### Conditional Keywords

| Keyword | Purpose                        |
|---------|--------------------------------|
| `if`    | Starts a condition             |
| `then`  | Executes commands when true    |
| `else`  | Executes commands when false   |
| `elif`  | Checks another condition       |
| `fi`    | Ends the conditional statement |

---

## 7. Comparison and Logical Operators

### Numeric Comparison Operators

| Operator | Meaning                  |
|----------|--------------------------|
| `-eq`    | Equal to                 |
| `-ne`    | Not equal to             |
| `-gt`    | Greater than             |
| `-lt`    | Less than                |
| `-ge`    | Greater than or equal to |
| `-le`    | Less than or equal to    |

### Logical Operators

| Operator | Meaning                                  |
|----------|------------------------------------------|
| `&&`     | AND — both conditions must be true       |
| `||`     | OR — at least one condition must be true |
| `!`      | NOT — reverses a condition               |

### Practical Example

```bash
#!/bin/bash

age=20
has_id="yes"

if [ "$age" -ge 18 ] && [ "$has_id" = "yes" ]; then
    echo "Entry allowed"
else
    echo "Entry denied"
fi
```

**Key Learning:** Logical operators allow multiple conditions to be evaluated together.

---

## 8. Loops in Bash Scripting

Loops execute commands repeatedly.

Two important loops were studied.

### A. For Loop

A `for` loop is useful when the values or number of iterations are known.

Example:

```bash
#!/bin/bash

for i in 1 2 3 4 5
do
    echo "Cyber Security Practice - $i"
done
```

### B. While Loop

A `while` loop continues executing as long as its condition remains true.

Example:

```bash
#!/bin/bash

count=1

while [ "$count" -le 5 ]
do
    echo "Count: $count"
    count=$((count + 1))
done
```

### Loop Keywords

| Keyword | Purpose                         |
|---------|---------------------------------|
| `for`   | Starts a for loop               |
| `in`    | Provides values to iterate over |
| `while` | Starts a condition-based loop   |
| `do`    | Begins the loop body            |
| `done`  | Ends the loop                   |

**Important:** A while loop must have a condition that eventually becomes false to prevent an infinite loop.

---

## 9. Functions in Bash

A function is a reusable block of commands that performs a specific task.

### Basic Function Structure

```bash
function_name() {
    # Commands go here
}
```

### Practical Example

```bash
#!/bin/bash

greeting() {
    echo "Welcome to Linux Administration!"
    echo "Keep learning Bash scripting."
}

greeting
```

### System Information Function

```bash
#!/bin/bash

system_info() {
    echo "===== SYSTEM INFORMATION ====="
    echo "Username: $(whoami)"
    echo "Hostname: $(hostname)"
    echo "Current Date: $(date)"
    echo "Disk Usage:"
    df -h /
}

system_info
```

### Command Substitution

Example:

```bash
echo "Username: $(whoami)"
```

The `$(command)` syntax executes a command and inserts its output into the surrounding command.

**Key Learning:** Functions help organize scripts, reduce repeated code and improve readability.

---

## 10. Final Practical Project – Linux System Information and Monitoring Script

### Project Objective

To develop a Bash script that automatically displays important information about a Linux system, including user details, network information, uptime, memory usage and disk usage.

### Create the Project Script

```bash
nano system_monitor.sh
```

### Script Content

```bash
#!/bin/bash

# Linux System Information and Monitoring

show_system_info() {
    echo "========== SYSTEM INFORMATION =========="
    echo "Username: $(whoami)"
    echo "Hostname: $(hostname)"
    echo "Date: $(date)"
    echo "IP Address: $(hostname -I)"
}

show_resource_usage() {
    echo
    echo "========== RESOURCE USAGE =========="
    echo "System Uptime:"
    uptime

    echo
    echo "Memory Usage:"
    free -h

    echo
    echo "Disk Usage:"
    df -h /
}

show_system_info
show_resource_usage
```

### Give Execution Permission

```bash
chmod +x system_monitor.sh
```

### Execute the Project

```bash
./system_monitor.sh
```

### Project Features

- Displays the current username.
- Displays the system hostname.
- Shows the current date and time.
- Displays assigned IP addresses.
- Shows system uptime.
- Displays RAM usage.
- Displays root filesystem disk usage.

### Concepts Implemented

| Concept              | Implementation         |
|----------------------|------------------------|
| Shebang              | `#!/bin/bash`          |
| Comments             | `# Linux System...`    |
| Functions            | `show_system_info()`   |
| Command substitution | `$(hostname)`          |
| Function calls       | `show_system_info`     |
| Linux commands       | `free`, `df`, `uptime` |

---

## Commands Practiced

| Command          | Purpose                             |
|------------------|-------------------------------------|
| `mkdir -p`       | Creates a directory                 |
| `cd`             | Changes the current directory       |
| `nano`           | Opens the Nano text editor          |
| `chmod +x`       | Adds execution permission           |
| `./script.sh`    | Executes a script directly          |
| `bash script.sh` | Executes a script using Bash        |
| `echo`           | Displays output                     |
| `read`           | Accepts user input                  |
| `date`           | Displays date and time              |
| `whoami`         | Displays the current username       |
| `hostname`       | Displays the system hostname        |
| `uptime`         | Displays system uptime              |
| `free -h`        | Displays memory usage               |
| `df -h /`        | Displays root filesystem disk usage |

---

## Cybersecurity Relevance

Bash scripting is useful in cybersecurity operations because it helps automate repetitive administrative and defensive tasks.

Practical applications include:

- Security log collection
- System information gathering
- Resource monitoring
- Backup automation
- Incident response preparation
- Security monitoring workflows

Bash scripting provides a foundation for developing automation tools used in Linux administration and SOC environments.

---

## Key Learning Points

- Bash scripts automate multiple Linux commands.
- The Shebang specifies the script interpreter.
- Variables store reusable information.
- The `read` command accepts user input.
- Conditional statements allow decision-making.
- Comparison and logical operators evaluate conditions.
- Loops automate repetitive operations.
- Functions organize reusable blocks of commands.
- Command substitution inserts command output into scripts.
- Linux monitoring commands can be combined into useful automation scripts.

---

## Result

Successfully learned Bash scripting fundamentals and created a Linux System Information and Monitoring Script.

The practical implementation combined variables, user input, conditional logic, loops, functions and Linux system commands to automate basic system information gathering.

---

# 📸 Screenshot Structure

The screenshots for this lab are available in the `Screenshots/` directory and are numbered in the order they were performed

---

**Author:** Dhruv Sudani

**Course:** Self-Learning Linux & Cybersecurity Homelab

**Module:** Linux Administration

**Lesson:** 15 – Bash Scripting Fundamentals