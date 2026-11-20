# Linux Logs & Security Monitoring – Lesson 11

## Project Title

**Linux Log Analysis and Security Monitoring Using journalctl**

---

## Objective

The objective of this lab was to understand Linux logging mechanisms and practise inspecting, filtering, and analysing system logs for troubleshooting and security investigation.

The lab focused on exploring `/var/log`, using `journalctl`, filtering logs by service, severity, and time, and understanding SSH authentication events.

---

## Lab Environment

| Component               | Details                            |
|-------------------------|------------------------------------|
| Host Operating System   | Windows 11                         |
| Virtual Machine         | Ubuntu Linux 24.04 LTS             |
| Virtualization Platform | Oracle VirtualBox                  |
| Logging System          | systemd journal                    |
| Log Directory           | `/var/log`                         |
| Main Log Tool           | `journalctl`                       |
| Investigation Focus     | SSH and system authentication logs |

---

## Introduction to Linux Logs

Linux logs are records of system events and activities.

They help administrators and cybersecurity analysts understand:

- What happened on a system
- When an event occurred
- Which service or component was involved
- Whether an error or suspicious activity occurred
- How system behaviour changed over time

Logs are particularly important in SOC operations, incident response, and system troubleshooting.

---

## Important Linux Log Locations

### `/var/log`

The `/var/log` directory stores traditional Linux log files.

Some commonly encountered logs include:

| Log File   | Purpose                                             |
|------------|-----------------------------------------------------|
| `auth.log` | Authentication and authorization-related events     |
| `syslog`   | General system messages                             |
| `kern.log` | Kernel-related messages                             |
| `journal/` | Persistent systemd journal storage, when configured |

The availability of individual files depends on the system's logging configuration.

### systemd Journal

Modern Linux distributions using systemd maintain a centralised logging system called the systemd journal.

The primary command used to inspect it is:

```bash
journalctl
```

---

## Practical Implementation

### Step 1 – Explore the Log Directory

Command:

```bash
ls -lh /var/log
```

Purpose:

- Lists files and directories inside `/var/log`.
- Displays detailed file information.
- Shows file sizes in human-readable units.

### Step 2 – Display Recent System Events

Command:

```bash
sudo journalctl -n 20 --no-pager
```

Purpose:

- Displays the latest 20 journal entries.
- Helps inspect recent system activity.
- Avoids opening the interactive pager.

### Step 3 – Filter Logs by Service

Command:

```bash
sudo journalctl -u ssh --no-pager -n 20
```

Purpose:

- Displays SSH service-related journal entries.
- Helps investigate SSH service activity without reviewing the entire journal.

### Step 4 – Search for SSH Authentication Events

Command:

```bash
sudo journalctl -u ssh --no-pager | grep -iE 'accepted|failed|invalid user'
```

Purpose:

Searches SSH journal output for common authentication-related terms:

- `Accepted` – successful authentication.
- `Failed` – failed authentication.
- `Invalid user` – authentication attempts involving an unknown username.

**Observation:** No matching entries were returned during this search.

This does not prove that no SSH authentication attempts occurred. Authentication records may be stored elsewhere or may use a different logging format.

### Step 5 – Inspect the Authentication Log

Command:

```bash
ls -lh /var/log/auth.log
```

Purpose:

Checks whether the traditional authentication log file exists.

Command:

```bash
sudo tail -n 10 /var/log/auth.log
```

Purpose:

Displays the latest 10 lines of the authentication log.

**Observation:** The inspected log contained session-opened and session-closed records, along with activity associated with the journal inspection command.

These records demonstrate logged session activity but do not independently establish a remote SSH login.

### Step 6 – Understand Log Severity Levels

Linux journal priorities range from 0 to 7.

| Priority | Name          | Meaning                        |
|----------|---------------|--------------------------------|
| 0        | Emergency     | System is unusable             |
| 1        | Alert         | Immediate action required      |
| 2        | Critical      | Critical condition             |
| 3        | Error         | Error condition                |
| 4        | Warning       | Warning condition              |
| 5        | Notice        | Normal but significant event   |
| 6        | Informational | General information            |
| 7        | Debug         | Detailed debugging information |

Lower priority numbers represent more severe conditions.

### Step 7 – Filter Logs by Severity

Command:

```bash
sudo journalctl -p err --no-pager -n 20
```

Purpose:

- Filters for Error priority and more severe events.
- Displays Emergency, Alert, Critical, and Error entries.
- Limits output to the latest 20 matching entries.

### Step 8 – Filter Logs by Time

Command:

```bash
sudo journalctl --since today --no-pager -n 20
```

Purpose:

Displays recent journal entries recorded since the beginning of the current day.

Time-based filtering is useful when investigating a particular incident window.

---

## Understanding Security Event Timelines

A security analyst uses timestamps to arrange events chronologically.

Example hypothetical SSH investigation:

| Time  | Event                           |
|-------|---------------------------------|
| 14:10 | Failed SSH authentication       |
| 14:11 | Failed SSH authentication       |
| 14:12 | Successful SSH authentication   |
| 14:15 | Sudo session opened             |

This sequence may indicate suspicious activity and requires further investigation.

However, it is not sufficient by itself to confirm a security breach.

An analyst should correlate:

- Source IP address
- Username and authentication method
- SSH connection details
- Sudo activity
- Process information
- System and configuration modifications

---

## Log Rotation and Protection

Linux systems use log rotation to prevent log files from growing indefinitely.

A common utility is:

```bash
logrotate
```

Example rotated log filenames:

```text
auth.log
auth.log.1
auth.log.2.gz
auth.log.3.gz
```

Log rotation helps manage disk space while retaining older records according to configured retention policies.

Logs should be protected against unauthorised modification or deletion, especially during incident investigations.

---

## Commands Practised

| Command                                          | Purpose                               |
|--------------------------------------------------|---------------------------------------|
| `ls -lh /var/log`                                | Explore the log directory             |
| `journalctl`                                     | Read systemd journal entries          |
| `sudo journalctl -n 20 --no-pager`               | Display recent system events          |
| `sudo journalctl -u ssh --no-pager -n 20`        | Filter SSH service logs               |
| `grep -iE`                                       | Search for multiple text patterns     |
| `ls -lh /var/log/auth.log`                       | Check authentication log file         |
| `sudo tail -n 10 /var/log/auth.log`              | Display recent authentication records |
| `sudo journalctl -p err --no-pager -n 20`        | Filter serious log events             |
| `sudo journalctl --since today --no-pager -n 20` | Filter logs by time                   |
| `logrotate`                                      | Log rotation utility                  |

---

## Cybersecurity Relevance

Linux log analysis is important in:

- Security Operations Centre (SOC) monitoring
- Incident response
- Authentication monitoring
- Threat detection
- System troubleshooting
- Digital investigations
- Security auditing

### Key Learning Points

- Linux logs record system events and activities.
- `/var/log` contains traditional log files.
- `journalctl` is used to inspect systemd journal records.
- Logs can be filtered by service, severity, and time.
- SSH authentication records help investigate remote access.
- Event timelines help establish the sequence of activities.
- Log rotation helps manage disk space.
- Suspicious activity must be investigated before declaring an incident confirmed.

---

## Result

Successfully explored Linux logging locations and practised inspecting and filtering system journal and authentication logs.

Understood how Linux logs support system monitoring, troubleshooting, and cybersecurity investigations.

---

## Future Improvements

- Practise advanced `journalctl` filtering.
- Investigate SSH authentication events using controlled test activity.
- Learn `logrotate` configuration and retention policies.
- Explore auditd for system auditing.
- Correlate authentication logs with process and system activity.
- Build a small Linux security monitoring lab.

---

# Screenshot Structure

The screenshots for this lab are available in the `Screenshots/` directory and are numbered in the order they were performed

---

**Author:** Dhruv Sudani

**Course:** Self-Learning Linux & Cybersecurity Homelab

**Module:** Linux Administration

**Lesson:** 11 – Logs & Security Monitoring