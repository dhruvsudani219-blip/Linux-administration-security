# Linux Administration & Security Fundamentals

## Complete Learning Programme — 15 Lessons

**Author:** Dhruv Sudani
**Course:** B.Sc. Cyber Security
**Learning Track:** Linux Administration & Security
**Total Lessons:** 15
**Platform:** Ubuntu Linux 24.04 LTS
**Virtualization:** Oracle VirtualBox
**Host Operating System:** Windows 11
**Programme Status:** Completed

---

# 1. Project Overview

This programme was designed to develop practical Linux administration and security skills through hands-on exercises using Ubuntu Linux.

The curriculum covers essential Linux administration concepts, including user management, permissions, process monitoring, service management, networking, firewall configuration, storage management, log analysis, backup and recovery, task automation, and Bash scripting.

The primary focus was not simply learning Linux commands, but understanding how Linux systems operate, how administrators manage them, and how security controls help protect them.

This programme establishes a foundation for further cybersecurity training through platforms such as TryHackMe and Hack The Box.

---

# 2. Prerequisites

The following basic knowledge and resources were required:

- Basic computer and operating system knowledge.
- Familiarity with Windows 11.
- Basic networking concepts, including IP addresses and subnetting.
- Understanding of virtual machines and virtualization.
- Oracle VirtualBox installed.
- Ubuntu Linux 24.04 / 26.04 LTS virtual machine.
- Access to a Linux terminal.
- Basic understanding of files, directories, and command-line operations.

**Hardware Environment:**

- Windows 11 host machine.
- Ubuntu Linux virtual machine.
- VirtualBox networking configured for practical exercises.

---

# 3. Programme Objectives

The primary objectives were:

1. Understand Linux operating system fundamentals.
2. Develop practical command-line administration skills.
3. Manage Linux users, groups, and permissions.
4. Monitor processes and system services.
5. Understand Linux networking and troubleshooting.
6. Configure and review firewall rules.
7. Manage storage, backups, and recovery.
8. Monitor system logs and identify suspicious activities.
9. Automate repetitive administrative tasks using Bash.
10. Develop a foundation for Linux security and cybersecurity investigations.

---

# 4. Complete Curriculum — 15 Lessons

## Module 1: Linux Administration Fundamentals

### Lesson 1 — Users, Groups & Permissions

Learned how Linux manages users and groups and how file permissions control access to system resources.

**Key topics:**

- User and group management.
- User creation and modification.
- Linux permission structure.
- Numeric permissions and `chmod`.
- Ownership management using `chown`.
- Understanding read, write, and execute permissions.

**Practical outcome:** Created users and groups and configured appropriate file and directory permissions.

### Lesson 2 — Process Management & Monitoring

Learned how Linux executes, manages, and monitors running processes.

**Key topics:**

- Process identification using PID.
- `ps`, `top`, and `htop`.
- Process monitoring using `pgrep` and `pidof`.
- Process termination using signals.
- Foreground and background processes.
- Process priority using `nice` and `renice`.

**Practical outcome:** Practised process monitoring, identification, and management.

### Lesson 3 — Service Management & systemd

Learned how Linux services operate and how system administrators manage them.

**Key topics:**

- Introduction to systemd.
- Service status monitoring.
- Starting and stopping services.
- Enabling and disabling services.
- Understanding service units.
- Using `systemctl`.

**Practical outcome:** Managed Linux services and examined their operational status.

### Lesson 4 — Package Management

Learned how software packages are installed, updated, and removed in Ubuntu.

**Key topics:**

- APT package manager.
- Installing software packages.
- Updating package repositories.
- Upgrading installed packages.
- Removing unnecessary packages.
- Inspecting installed packages.

**Practical outcome:** Practised software installation, updates, and package management.

### Lesson 5 — User, Group & Access Management

Developed a deeper understanding of Linux account administration and access control.

**Key topics:**

- User account management.
- Group membership.
- Primary and supplementary groups.
- Administrative privileges.
- Sudo configuration and usage.
- Account access management.

**Practical outcome:** Managed user accounts and group memberships while understanding administrative access.

### Lesson 6 — Advanced Permissions & ACLs

Learned how Linux provides more flexible access control beyond traditional file permissions.

**Key topics:**

- Access Control Lists (ACLs).
- `getfacl` and `setfacl`.
- User-specific permissions.
- Group-specific permissions.
- Default ACLs.
- Permission inheritance.

**Practical outcome:** Configured and inspected advanced file access permissions.

---

## Module 2: Linux Networking & System Operations

### Lesson 7 — Linux Networking Fundamentals

Learned how Linux systems interact with networks and how network interfaces are configured and inspected.

**Key topics:**

- Network interface identification.
- IP address configuration.
- Interface states: UP and DOWN.
- Network routes.
- DNS fundamentals.
- Network configuration inspection.

**Practical outcome:** Inspected network interfaces and understood Linux network configuration.

### Lesson 8 — Network Troubleshooting & Diagnostics

Learned how to identify and troubleshoot common Linux network connectivity problems.

**Key topics:**

- `ping`.
- `ip`.
- `ss`.
- `traceroute` / `tracepath`.
- `dig` and DNS diagnostics.
- Connectivity troubleshooting.

**Practical outcome:** Practised diagnosing network connectivity and DNS-related issues.

### Lesson 9 — Linux Firewall Management

Learned how Linux firewall rules control incoming and outgoing network traffic.

**Key topics:**

- Firewall fundamentals.
- UFW (Uncomplicated Firewall).
- Allowing and denying traffic.
- Port-based filtering.
- Firewall status inspection.
- Firewall rule management.

**Practical outcome:** Configured and inspected firewall rules using UFW.

### Lesson 10 — Storage & Disk Management

Learned how Linux manages storage devices, partitions, filesystems, and mounted volumes.

**Key topics:**

- Disk and partition inspection.
- `lsblk`.
- `df` and `du`.
- Mounting and unmounting.
- Filesystem fundamentals.
- Storage usage monitoring.

**Practical outcome:** Inspected storage devices and practised disk-space management.

---

## Module 3: Linux Security & Administration

### Lesson 11 — Logs & Security Monitoring

Learned how Linux records system activities and how administrators use logs to monitor system behaviour.

**Key topics:**

- Linux logging fundamentals.
- `/var/log` directory.
- Authentication logs.
- `journalctl`.
- Log filtering and inspection.
- Identifying suspicious activities.

**Practical outcome:** Inspected system logs and practised basic security monitoring.

### Lesson 12 — Backup & Recovery

Learned why backups are important and how Linux administrators preserve and recover important data.

**Key topics:**

- Backup fundamentals.
- File and directory backups.
- `tar` and compression.
- Backup verification.
- Restoration procedures.
- Recovery considerations.

**Practical outcome:** Practised creating and restoring backups.

### Lesson 13 — Task Scheduling & Automation

Learned how Linux can execute administrative tasks automatically at scheduled times.

**Key topics:**

- Cron scheduling.
- Crontab configuration.
- Cron expressions.
- Scheduled scripts.
- Automated maintenance tasks.
- Task scheduling inspection.

**Practical outcome:** Practised scheduling recurring administrative tasks.

### Lesson 14 — Environment & Shell Fundamentals

Learned how the Linux shell manages environment variables, command execution paths, and shell configuration.

**Key topics:**

- Shell fundamentals.
- Shell variables.
- Environment variables.
- PATH configuration.
- Aliases.
- `.bashrc` and `.profile`.
- Login and non-login shells.
- Persistent shell configuration.

**Practical outcome:** Configured shell variables, aliases, and environment settings.

### Lesson 15 — Bash Scripting Fundamentals

Learned how to automate Linux administrative tasks by writing Bash scripts.

**Key topics:**

- Bash scripting fundamentals.
- Shebang and script structure.
- Variables and user input.
- Conditional statements.
- Comparison and logical operators.
- For and while loops.
- Functions.
- Command substitution.
- System information collection.

**Final Practical Project:** Linux System Information and Monitoring Script.

The script collected:

- Current username.
- Hostname.
- Current date.
- IP address.
- System uptime.
- Memory usage.
- Disk usage.

**Practical outcome:** Successfully developed and executed a Bash-based system monitoring script.

---

# 5. Technical Skills Developed

The programme covered the following practical skill areas:

| Skill Area           | Competencies                                  |
| -------------------- | --------------------------------------------- |
| Linux Administration | User, group, and system management            |
| Access Control       | Permissions, ownership, and ACLs              |
| Process Management   | Process monitoring and control                |
| Service Management   | systemd and service operations                |
| Networking           | Interfaces, IP configuration, and diagnostics |
| Firewall             | UFW and traffic filtering                     |
| Storage              | Disk inspection and management                |
| Security Monitoring  | Log inspection and basic investigation        |
| Backup & Recovery    | Data preservation and restoration             |
| Automation           | Cron and scheduled tasks                      |
| Scripting            | Bash programming and system monitoring        |

---

# 6. Cybersecurity Relevance

Linux is widely used in enterprise infrastructure, cloud environments, security operations, and penetration-testing environments.

The skills developed through this programme provide a foundation for several cybersecurity domains:

**SOC & Blue Team**

- Monitoring system activities.
- Reviewing authentication logs.
- Identifying suspicious processes.
- Understanding network connections.

**Penetration Testing**

- Understanding Linux environments.
- Working with the command line.
- Managing files and permissions.
- Understanding services and network ports.

**Incident Response**

- Inspecting system logs.
- Investigating processes.
- Examining system configurations.
- Understanding backups and recovery.

**System & Security Administration**

- Managing accounts and privileges.
- Reviewing firewall configurations.
- Monitoring services.
- Automating administrative operations.

---

# 7. Learning Outcomes

After completing all 15 lessons, the learner developed the ability to:

- Navigate and administer an Ubuntu Linux environment.
- Create and manage Linux users and groups.
- Configure file and directory permissions.
- Monitor and manage running processes.
- Control Linux services using systemd.
- Install and manage software packages.
- Inspect network interfaces and troubleshoot connectivity.
- Configure basic firewall rules.
- Monitor storage and disk usage.
- Inspect Linux logs and authentication activities.
- Perform basic backup and recovery operations.
- Automate recurring tasks using cron.
- Configure shell environments.
- Write and execute Bash scripts.
- Collect essential Linux system information.

---

# 8. Programme Completion & Next Learning Phase

This 15-lesson programme establishes the Linux administration foundation required before progressing towards practical cybersecurity training.

The next learning phase will focus on applying these concepts in realistic, controlled cybersecurity environments.

---

# 9. Final Result

Successfully completed a structured 15-lesson Linux Administration & Security Fundamentals programme using Ubuntu Linux 26.04 LTS in Oracle VirtualBox.

The programme provided hands-on exposure to Linux administration, system monitoring, networking, access control, firewall management, logging, automation, and Bash scripting.

This learning experience establishes a practical foundation for progressing towards TryHackMe, Hack The Box, and further professional cybersecurity training.

---

**Author:** Dhruv Sudani
**Course:** B.Sc. Cyber Security
**Platform:** Ubuntu Linux 24.04 LTS
**Virtualization:** Oracle VirtualBox
**Programme:** Linux Administration & Security Fundamentals
**Total Lessons:** 15
**Status:** Completed

