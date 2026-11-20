# Environment & Shell Fundamentals – Lesson 14

## Project Title

**Understanding Linux Shells, Environment Variables and Bash Configuration**

---

## Objective

The objective of this lesson was to understand Linux shell environments, variables, environment variables, PATH configuration, aliases and Bash startup configuration files.

The practical exercises focused on customizing the Bash environment, creating command shortcuts and understanding how shell settings can be made persistent.

---

## Lab Environment

| Component               | Details           |
|-------------------------|-------------------|
| Host Operating System   | Windows 11        |
| Server Machine          | Ubuntu Linux VM   |
| Virtualization Platform | Oracle VirtualBox |
| Shell                   | GNU Bash          |
| User Environment        | Linux Terminal    |

---

## 1. Introduction to Shell Fundamentals

A Linux shell is a command-line interpreter that accepts user commands and communicates with the operating system.

### Difference Between Terminal, Shell and Environment

- **Terminal:** Interface used to enter commands.
- **Shell:** Interprets commands and requests their execution.
- **Environment:** Collection of variables and settings that influence running programs.

### Commands Practiced

```bash
echo $SHELL
ps -p $$ -o comm=
whoami
echo $HOME
echo $PWD
```

---

## 2. Shell Variables and Scope

Shell variables store information that can be accessed by commands within the current shell.

### Create a Shell Variable

```bash
student="Dhruv"
```

### Display Variable

```bash
echo $student
```

### Export a Variable

```bash
export student
```

Exporting a variable makes it available to child processes, such as a new Bash shell.

### Remove a Variable

```bash
unset student
```

**Key Learning:** Normal shell variables are not automatically inherited by child shells. Exported variables are inherited by child processes.

---

## 3. Environment Variables

Environment variables provide information and configuration settings to running processes.

### Display Environment Variables

```bash
printenv
```

### Display Specific Variables

```bash
printenv PATH
echo $HOME
echo $USER
echo $SHELL
echo $PWD
```

### Display PATH Directories Separately

```bash
echo $PATH | tr ':' '\n'
```

This command separates PATH entries and displays each directory on a new line.

### Locate Executable Commands

```bash
which ls
which bash
which python3
```

**Key Learning:** Environment variables influence how programs and processes operate.

---

## 4. PATH Variable Configuration

The PATH variable contains directories where the shell searches for executable commands.

### Create a Practice Directory

```bash
mkdir -p ~/shell_lab
```

### Create a Shell Script

```bash
nano ~/shell_lab/hello.sh
```

Script content:

```bash
#!/bin/bash
echo "Hello Dhruv! Custom PATH is working."
```

### Give Execution Permission

```bash
chmod +x ~/shell_lab/hello.sh
```

### Execute Using Absolute Path

```bash
~/shell_lab/hello.sh
```

### Add Custom Directory to PATH

```bash
export PATH="$PATH:$HOME/shell_lab"
```

### Execute Without Specifying the Path

```bash
hello.sh
```

The shell successfully located and executed the script through the updated PATH variable.

**Important:** This PATH modification was temporary and applied only to the current shell environment and its child processes.

---

## 5. Shell Aliases

An alias is a shortcut used to execute a longer command using a shorter name.

### Create an Alias

```bash
alias ll='ls -lah'
```

### Execute Alias

```bash
ll
```

### Display Existing Aliases

```bash
alias
```

### Remove an Alias

```bash
unalias ll
```

**Key Learning:** Aliases simplify frequently used commands and improve command-line productivity.

---

## 6. Linux Shell Configuration Files

Linux uses configuration files to manage shell settings and environment variables.

| Configuration File | Purpose                                   |
|--------------------|-------------------------------------------|
| ~/.bashrc          | Configures interactive Bash shells        |
| ~/.profile         | Configures the user's login environment   |
| /etc/environment   | Stores system-wide environment variables  |

### Inspect Configuration Files

```bash
ls -la ~/.bashrc ~/.profile
cat ~/.bashrc
cat ~/.profile
cat /etc/environment
```

### Hidden Files

Files beginning with a dot (`.`) are hidden by default in Linux.

Example:

```text
.bashrc
.profile
```

Display hidden files:

```bash
ls -la
```

---

## 7. Login and Non-Login Shells

### Check Current Shell Type

```bash
shopt -q login_shell && echo "Login shell" || echo "Non-login shell"
```

### Start a New Non-Login Bash Shell

```bash
bash
```

### Exit Current Shell

```bash
exit
```

### Start a Login Bash Shell

```bash
bash -l
```

### Verify Shell Type

```bash
shopt -q login_shell && echo "Login shell" || echo "Non-login shell"
```

### Difference

| Login Shell                                         | Non-Login Shell                       |
|-----------------------------------------------------|---------------------------------------|
| Starts a login session                              | Starts inside an existing session     |
| Reads login configuration files                     | Reads interactive shell configuration |
| Can load ~/.profile, ~/.bash_profile, ~/.bash_login | Usually loads ~/.bashrc               |

**Key Learning:** Different shell types use different startup configuration files.

---

## 8. Persistent Shell Configuration

A configuration added to `.bashrc` can automatically load whenever an interactive Bash shell starts.

### Open `.bashrc`

```bash
nano ~/.bashrc
```

Add the following configuration:

```bash
# My custom Linux configuration
alias myip='hostname -I'
```

### Save and Reload Configuration

```bash
source ~/.bashrc
```

### Execute Custom Alias

```bash
myip
```

The command displays the IP addresses assigned to the Linux machine.

### Verify Persistence

After closing and reopening the terminal:

```bash
myip
```

The alias continued working without manually creating it again.

**Key Learning:** The `source` command reloads a shell configuration file in the current shell without requiring a terminal restart.

---

## Commands Practiced

| Command       | Purpose                                            |
|---------------|----------------------------------------------------|
| `echo $SHELL` | Displays the default shell path                    |
| `whoami`      | Displays the current username                      |
| `printenv`    | Displays environment variables                     |
| `which`       | Locates executable commands                        |
| `export`      | Exports variables to child processes               |
| `unset`       | Removes a shell variable                           |
| `tr`          | Translates or transforms characters                |
| `chmod +x`    | Adds execution permission                          |
| `alias`       | Creates command shortcuts                          |
| `unalias`     | Removes an alias                                   |
| `shopt`       | Displays or changes Bash shell options             |
| `source`      | Executes a configuration file in the current shell |
| `nano`        | Opens the nano text editor                         |
| `bash -l`     | Starts a login Bash shell                          |

---

## Cybersecurity Relevance

Understanding Linux shell environments is useful in:

- Linux system administration
- SOC operations
- Security automation
- Bash scripting
- Incident response
- Server configuration and management

Shell configuration files are also relevant to security investigations because unauthorized startup commands or aliases can sometimes indicate persistence mechanisms or suspicious modifications.

---

## Key Learning Points

- A shell interprets and executes user commands.
- Shell variables can have different scopes.
- Exported variables are inherited by child processes.
- PATH determines where the shell searches for executable commands.
- Aliases simplify frequently used commands.
- Dotfiles are hidden configuration files.
- `.bashrc` manages interactive Bash settings.
- `.profile` helps configure login environments.
- `/etc/environment` stores system-wide environment variables.
- Login and non-login shells follow different startup procedures.
- Persistent aliases can be configured through `.bashrc`.

---

## Result

Successfully explored Linux shell environments, variable scope, environment variables, PATH configuration, command aliases and Bash startup configuration files.

Created a custom shell script, modified PATH temporarily, configured a persistent alias and verified its availability across new terminal sessions.

---

# 📸 Screenshot Structure

The screenshots for this lab are available in the `Screenshots/` directory and are numbered in the order they were performed

---

**Author:** Dhruv Sudani

**Course:** Self-Learning Linux & Cybersecurity Homelab

**Module:** Linux Administration

**Lesson:** 14 – Environment & Shell Fundamentals