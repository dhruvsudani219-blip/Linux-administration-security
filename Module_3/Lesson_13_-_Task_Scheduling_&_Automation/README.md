# Task Scheduling & Automation – Lesson 13

## Project Title

**Linux Task Scheduling and Automation Using Cron, At and Systemd Timers**

## Objective

The objective of this lesson was to understand Linux task scheduling and automate recurring and one-time administrative tasks.

The practical focused on cron jobs, the `at` scheduler, systemd timers, monitoring scheduled tasks, troubleshooting failures and scheduling security.

## Lab Environment

| Component        | Details                  |
|------------------|--------------------------|
| Operating System | Ubuntu Linux 24.04 LTS   |
| Virtualization   | Oracle VirtualBox        |
| Shell            | Bash                     |
| Scheduling Tools | Cron, at, systemd timers |

## 1. Introduction to Task Scheduling

Linux provides several mechanisms for scheduling automated tasks:

- **Cron:** Executes recurring tasks.
- **At:** Executes a task once at a specified time.
- **systemd timers:** Schedule systemd services using calendar-based or monotonic timers.

## 2. Cron Daemon

Cron is a background service responsible for executing scheduled recurring tasks.

Commands practiced:

```bash
systemctl status cron
sudo systemctl start cron
sudo systemctl enable cron
```

## 3. Crontab Syntax

Cron uses five scheduling fields:

```text
minute hour day-of-month month day-of-week
```

Examples:

```bash
30 2 * * *       # Every day at 2:30 AM
0 9 * * 1        # Every Monday at 9 AM
*/15 * * * *     # Every 15 minutes
```

Special scheduling symbols:

| Symbol | Meaning         |
|--------|-----------------|
| `*`    | Every           |
| `,`    | Multiple values |
| `-`    | Range           |
| `/`    | Interval        |

## 4. Managing Cron Jobs

Commands practiced:

```bash
crontab -l
crontab -e
```

A harmless test job was created to append a message to a log file every minute:

```bash
* * * * * echo "Cron job executed" >> /home/USERNAME/cron_test.log
```

The job was verified and subsequently removed from the crontab.

## 5. Practical Cron Automation

Created a system monitoring script:

```bash
nano ~/cron_lab/system_report.sh
```

Script:

```bash
#!/bin/bash

echo "===== System Monitoring Report ====="
date
echo " "
echo "System Uptime:"
uptime
echo " "
echo "Disk Usage:"
df -h /
echo " "
echo "===== Report Completed ====="
```

Made the script executable:

```bash
chmod +x ~/cron_lab/system_report.sh
```

Executed the script manually and configured a recurring cron job:

```bash
*/5 * * * * /bin/bash /home/USERNAME/cron_lab/system_report.sh >> /home/USERNAME/cron_lab/system_report.log 2>&1
```

The job was tested and then removed from the crontab.

## 6. One-Time Scheduling Using at

The `at` command schedules commands for one-time execution.

Checked the installation:

```bash
at -V
```

Checked and started the scheduling daemon:

```bash
systemctl status atd
sudo systemctl start atd
```

Scheduled a one-time task:

```bash
echo 'echo "One-time task executed" >> ~/at_test.log' | at now + 1 minute
```

Checked pending jobs:

```bash
atq
```

Removed a pending job using:

```bash
atrm JOB_NUMBER
```

The one-time scheduling mechanism was practised successfully. The pending-job queue was empty when inspected after execution.

## 7. systemd Timers

systemd timers provide another mechanism for scheduling system services.

Two important components:

- `.timer` – Defines when a task should execute.
- `.service` – Defines what task should execute.

Commands practiced:

```bash
systemctl list-timers --all
systemctl status apt-daily.timer
systemctl cat apt-daily.timer
```

Important timer directives:

| Directive             | Purpose                                            |
|-----------------------|----------------------------------------------------|
| `OnCalendar=`         | Defines calendar-based scheduling                  |
| `RandomizedDelaySec=` | Adds a random scheduling delay                     |
| `Persistent=true`     | Allows missed calendar activations to be caught up |

## 8. Monitoring and Troubleshooting

Inspected cron and atd logs:

```bash
sudo journalctl -u cron --since today
sudo journalctl -u atd --since today
```

Common scheduling problems:

1. Incorrect cron syntax.
2. Incorrect script permissions.
3. Incorrect file paths.
4. Inactive scheduling services.
5. Different PATH and environment variables.
6. Missing output and error redirection.

## 9. Scheduling Security

Scheduled tasks can also be abused to establish persistence after unauthorised access.

Security practices:

- Apply the principle of least privilege.
- Protect scripts from unauthorised modification.
- Prefer absolute paths.
- Avoid storing credentials directly in crontab entries.
- Audit unfamiliar scheduled tasks.
- Investigate suspicious cron jobs and systemd timers.

Useful security inspection commands:

```bash
crontab -l
sudo ls -la /etc/cron.d/
systemctl list-timers --all
```

## 10. Commands Practiced

| Command                       | Purpose                    |
|-------------------------------|----------------------------|
| `systemctl status cron`       | Check cron service         |
| `systemctl enable cron`       | Enable cron at boot        |
| `crontab -l`                  | List cron jobs             |
| `crontab -e`                  | Edit cron jobs             |
| `at -V`                       | Check at version           |
| `atq`                         | List pending at jobs       |
| `atrm`                        | Remove pending at jobs     |
| `systemctl list-timers --all` | List systemd timers        |
| `systemctl cat`               | Inspect unit configuration |
| `journalctl`                  | Inspect service logs       |

## Key Learning Points

- Cron is used for recurring tasks.
- At is used for one-time task scheduling.
- systemd timers activate corresponding services.
- Cron jobs may run in a limited environment.
- Absolute paths help prevent scheduling failures.
- Unexpected scheduled tasks can indicate malicious persistence.

## Result

Successfully studied and practised Linux task scheduling and automation using cron, at and systemd timers.

Created and tested recurring system monitoring automation, scheduled a one-time task, inspected systemd timer configurations and explored scheduling logs and security practices.

---

# 📸 Screenshot Structure

The screenshots for this lab are available in the `Screenshots/` directory and are numbered in the order they were performed

---

**Author:** Dhruv Sudani  
**Course:** Self-Learning Linux & Cybersecurity Homelab  
**Module:** Linux Administration  
**Lesson:** 13 – Task Scheduling & Automation