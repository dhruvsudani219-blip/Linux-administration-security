# Linux Service Management & Troubleshooting – Lesson 3

## Project Title

**Linux Service Management and Troubleshooting Using systemd**

---

# Objective

The objective of this lab was to understand how Linux services are managed using **systemd** and the `systemctl` command.

The lab focused on understanding the difference between services and processes, managing service states, configuring services to start automatically during boot, inspecting systemd unit files and properties, investigating failed services, analyzing service logs using `journalctl`, and troubleshooting a service using process and network information.

The practical investigation was performed primarily on the **OpenSSH (`ssh.service`)** service.

---

# Lab Environment

| Component               | Details                 |
|-------------------------|-------------------------|
| Operating System        | Ubuntu Linux 24.04 LTS  |
| Virtualization Platform | Oracle VirtualBox       |
| Host Operating System   | Windows 11              |
| Service Manager         | systemd                 |
| Service Investigated    | OpenSSH (`ssh.service`) |
| SSH Daemon              | `sshd`                  |
| Network Environment     | Host-Only Adapter       |

---

# Prerequisites

Before starting this lesson, the following concepts were already completed:

- Basic Linux commands
- Linux processes
- PID and PPID
- Process states
- Process hierarchy
- `ps`
- `pgrep`
- `pidof`
- `pstree`
- `top`
- `htop`
- Linux file permissions
- Numerical file permissions
- Basic SSH configuration
- SSH key-based authentication

---

# Introduction to Linux Services

A **service** is a background operation or software component that provides functionality to the operating system or other applications.

Examples include:

SSH
NetworkManager
cron
system logging
web servers
database servers

A service is different from a process.

A **process** is a running instance of a program.

For example:

systemd
   |
   └── ssh.service
          |
          └── sshd
               |
               └── PID

Here:

* `ssh.service` is a **systemd service unit**
* `sshd` is the actual SSH daemon/process
* The process receives a PID

---

# systemd

Modern Ubuntu systems use **systemd** as the primary system and service manager.

One of the most important characteristics of systemd is:

systemd = PID 1

PID 1 is responsible for starting and managing many system services and processes during system initialization and normal operation.

Conceptually:

systemd (PID 1)
      |
      +---- NetworkManager.service
      |
      +---- ssh.service
      |
      +---- cron.service
      |
      +---- other services

---

# systemctl

`systemctl` is the primary command-line utility used to communicate with and manage systemd.

General syntax:

systemctl COMMAND UNIT

Example:

systemctl status ssh

---

# Service States vs Boot Configuration

One of the most important concepts learned in this lesson was that:

**Current service state and boot configuration are independent.**

A service can be:

active + enabled
active + disabled
inactive + enabled
inactive + disabled

---

# Active vs Inactive

### Active

active

Means the service is currently active.

For a normal running service:

active (running)

### Inactive

inactive

Means the service is not currently running.

---

# Enabled vs Disabled

### Enabled

enabled

Means the service is configured to start automatically during the appropriate system boot process.

### Disabled

disabled

Means the service is not configured for automatic startup during boot.

Important:

enabled ≠ running

A service can be:

inactive + enabled

This means:

Not running now
+
Will automatically start during boot

Similarly:

active + disabled

means:

Running now
+
Will not automatically start during boot

---

# Checking Service Status

Command:

systemctl status ssh

This provides a human-readable summary of the SSH service.

Typical information includes:

Loaded
Active
Main PID
Recent log messages
Description

Example:

ssh.service - OpenBSD Secure Shell server
Loaded: loaded (...)
Active: active (running)
Main PID: 1402

---

# Understanding systemctl status Output

## Loaded

Example:

Loaded: loaded

Means systemd successfully loaded the unit configuration.


It may also show:

enabled

which indicates boot enablement.


These are different concepts.


loaded
   ↓
Unit configuration successfully loaded

enabled
   ↓
Configured for automatic startup


---

# Active State

Example:


Active: active (running)


This indicates that the service is currently running.

Possible high-level states include:


active
inactive
failed

---

# Main PID

Example:


Main PID: 1402


The Main PID identifies the main process associated with the service.

The PID may change after restarting the service or rebooting the system.

For example:


Before reboot:
sshd → PID 1402

After reboot:
sshd → PID 1336


The service remains:


ssh.service


but its newly created process can receive a different PID.

---

# Starting a Service

Command:


sudo systemctl start ssh


This starts the SSH service immediately.

Important:


start


does not automatically enable the service for future boots.

For example:


inactive + enabled
        |
        | systemctl start ssh
        ↓
active + enabled

---

# Stopping a Service

Command:


sudo systemctl stop ssh


This stops the service immediately.

It does not necessarily change its boot configuration.

For example:


active + enabled
        |
        | systemctl stop ssh
        ↓
inactive + enabled


The service can therefore remain enabled even though it is currently stopped.

---

# Restarting a Service

Command:


sudo systemctl restart ssh


Restarting a service generally involves stopping and starting the service again.

It is useful when:

* Service configuration has changed
* The service needs to reload its normal startup process
* The service is behaving unexpectedly

A restart may result in the service receiving a **new PID**.

Example:


Before restart:
sshd → PID 1354

After restart:
sshd → PID 3627

---

# Enabling a Service

Command:


sudo systemctl enable ssh


This configures the service to start automatically during future system boots.

Important:


enable ≠ start


For example:


inactive + disabled
        |
        | systemctl enable ssh
        ↓
inactive + enabled


The service is still stopped right now.

---

# Disabling a Service

Command:


sudo systemctl disable ssh


This removes the service's automatic startup configuration.

For example:


active + enabled
        |
        | systemctl disable ssh
        ↓
active + disabled


The service can continue running even though it is disabled for future boots.

---

# Enable and Start Immediately

Command:


sudo systemctl enable --now ssh


This performs both actions:


enable
+
start immediately


Therefore:

inactive + disabled
        |
        | systemctl enable --now ssh
        ↓
active + enabled

---

# Disable and Stop Immediately

Command:


sudo systemctl disable --now ssh


This performs:


disable
+
stop immediately


Therefore:


active + enabled
        |
        | systemctl disable --now ssh
        ↓
inactive + disabled

---

# Checking Whether a Service Is Active

Command:


systemctl is-active ssh


Example output:


active


This is useful when only the current active/inactive state is required.

---

# Checking Whether a Service Is Enabled

Command:

```bash
systemctl is-enabled ssh
```

Example:

```text
enabled
```

This checks whether the service is configured for automatic startup.

It does not mean that the service is currently running.

---

# Inspecting systemd Unit Configuration

Command:

```bash
systemctl cat ssh
```

This displays the systemd unit configuration associated with the SSH service.

The output can contain sections such as:

```text
[Unit]

[Service]

[Install]
```

---

# [Unit] Section

The `[Unit]` section contains information about the unit itself and its relationships with other units.

For example:

```text
Description=OpenBSD Secure Shell server
```

The description provides human-readable information about the service.

---

# [Service] Section

The `[Service]` section contains information about how the service process is managed.

One important directive is:

```text
ExecStart
```

`ExecStart` specifies the command that systemd should execute to start the service.

Conceptually:

```text
systemctl start ssh
        |
        ↓
systemd reads service configuration
        |
        ↓
ExecStart
        |
        ↓
sshd starts
```

---

# [Install] Section

The `[Install]` section contains information used when enabling a unit and associating it with systemd targets.

This is related to the service's boot enablement configuration.

---

# systemctl show

Command:

```bash
systemctl show ssh
```

This displays detailed systemd properties of the SSH unit, usually in:

```text
key=value
```

format.

Example:

```text
Id=ssh.service
LoadState=loaded
ActiveState=active
SubState=running
MainPID=1402
UnitFileState=enabled
```

---

# Important systemctl show Properties

| Property        | Meaning                                      |
| --------------- | -------------------------------------------- |
| `Id`            | Unit name                                    |
| `LoadState`     | Whether systemd successfully loaded the unit |
| `ActiveState`   | High-level current state                     |
| `SubState`      | More specific current state                  |
| `MainPID`       | Main process PID associated with the service |
| `UnitFileState` | Unit file enablement state                   |

---

# Filtering systemctl show

Instead of displaying all properties:

```bash
systemctl show ssh
```

a specific property can be selected.

Example:

```bash
systemctl show -p MainPID ssh
```

Output:

```text
MainPID=1402
```

Another example:

```bash
systemctl show -p ActiveState ssh
```

Output:

```text
ActiveState=active
```

This is useful when only one specific property is required.

---

# systemctl list-units

Command:

```bash
systemctl list-units
```

This provides an overview of currently loaded systemd units.

Typical columns include:

```text
UNIT
LOAD
ACTIVE
SUB
DESCRIPTION
```

Example:

```text
UNIT              LOAD   ACTIVE   SUB      DESCRIPTION
ssh.service       loaded active   running  OpenBSD Secure Shell server
```

---

# Understanding list-units Columns

## UNIT

The name of the systemd unit.

Example:

```text
ssh.service
```

A unit can represent different systemd object types, such as:

```text
Service
Socket
Mount
Target
Device
Timer
```

---

## LOAD

Indicates whether systemd successfully loaded the unit configuration.

Example:

```text
loaded
```

Important:

```text
LOAD ≠ ENABLED
```

---

## ACTIVE

Represents the high-level current state.

Examples:

```text
active
inactive
failed
```

---

## SUB

Represents a more specific state of the unit.

For a service:

```text
running
```

Other unit types can have different sub-states.

---

## DESCRIPTION

Provides a human-readable description of the unit.

---

# systemctl list-unit-files

Command:

```bash
systemctl list-unit-files
```

This lists installed systemd unit files and their enablement states.

Example:

```text
UNIT FILE             STATE
ssh.service           enabled
cron.service          enabled
example.service       disabled
```

This command is particularly useful when investigating:

> "Which services are configured to start automatically?"

---

# Difference Between list-units and list-unit-files

```text
systemctl list-units
        ↓
Currently loaded units
```

```text
systemctl list-unit-files
        ↓
Installed unit files + enablement state
```

Therefore:

```text
list-units
```

is useful for investigating the current systemd unit environment.

Whereas:

```text
list-unit-files
```

is useful for investigating unit files and their boot configuration.

---

# Finding Failed Services

Command:

```bash
systemctl --failed
```

This displays failed systemd units.

Example:

```text
UNIT              LOAD   ACTIVE   SUB      DESCRIPTION
example.service   loaded failed   failed   Example Service
```

If a service appears as failed, the next investigation steps can include:

```bash
systemctl status example.service
```

and:

```bash
journalctl -u example.service
```

The purpose is to determine what happened and identify the underlying cause.

---

# Introduction to journalctl

`journalctl` is used to view logs collected by the systemd journal.

Running:

```bash
journalctl
```

can produce a large amount of historical system information.

For service-specific investigation, it is more efficient to filter the logs.

---

# Viewing SSH Logs

Command:

```bash
journalctl -u ssh
```

The `-u` option specifies a systemd unit.

Therefore:

```bash
journalctl -u ssh
```

means:

> Display journal entries associated with the SSH service.

---

# Filtering Logs by Date

Command:

```bash
journalctl -u ssh --since today
```

This displays SSH-related journal entries from today.

This is more focused than:

```bash
journalctl -u ssh
```

which may contain a much larger historical range.

---

# Viewing Recent Log Entries

Command:

```bash
journalctl -u ssh -n 20
```

The `-n 20` option displays the most recent 20 journal entries.

This is useful when investigating recent service activity without reading a large historical log.

---

# Combining Journal Filters

Command:

```bash
journalctl -u ssh --since today -n 20
```

This combines multiple filters.

It asks for:

```text
SSH logs
+
from today
+
limited to the most recent 20 entries
```

This is useful during troubleshooting because narrowing the search reduces unnecessary information.

---

# Investigating a Specific Time Period

Command:

```bash
journalctl -u ssh --since "18:00" --until "19:00"
```

This investigates SSH journal entries within a specific time window.

For example:

```text
18:00 → 19:00
```

This is particularly useful when an administrator knows approximately when an incident occurred.

---

# Service Troubleshooting Methodology

A general service troubleshooting workflow developed during this lesson was:

```text
Service problem
      ↓
systemctl status
      ↓
Determine current state
      ↓
journalctl
      ↓
Investigate historical events/errors
      ↓
Identify likely cause
      ↓
Investigate related processes/configuration
      ↓
Apply appropriate remediation
      ↓
Start/restart service
      ↓
Verify
```

Important principle:

> Do not immediately apply a fix before identifying the cause.

---

# Practical SSH Incident Investigation

A simulated SSH incident was investigated during this lesson.

The situation was:

```text
SSH service failed
```

The investigation began with:

```bash
systemctl status ssh
```

The service was found to be in a failed state.

---

# Step 1 – Investigate SSH Logs

Command:

```bash
journalctl -u ssh --since "18:00" --until "18:30"
```

The logs revealed:

```text
Bind to port 22 failed: Address already in use
```

This indicated that SSH could not bind to port 22 because another process was already using it.

---

# Step 2 – Investigate Listening Network Ports

Command:

```bash
sudo ss -ltnp
```

The command was used to identify listening TCP sockets and the processes associated with them.

Important options:

```text
-l → listening sockets
-t → TCP
-n → numerical addresses and ports
-p → process information
```

Conceptually:

```text
TCP port 22
      ↓
Process using port 22
      ↓
PID
```

---

# SSH Process and Service Relationship

The investigation demonstrated the relationship:

```text
systemd
   |
   └── ssh.service
          |
          └── sshd
                 |
                 └── PID
                      |
                      └── TCP port 22
```

Important distinction:

```text
ssh.service
```

is the systemd service unit.

```text
sshd
```

is the actual SSH daemon/process.

The process receives a PID.

---

# Step 3 – Investigating a Process

A process occupying a required port should be investigated before terminating it.

Example:

```bash
ps -p 2450 -o pid,ppid,state,user,cmd
```

This can provide:

```text
PID
PPID
STATE
USER
CMD
```

Further investigation may include:

```bash
ps -p 2450 -f
```

and:

```bash
pstree -p 2450
```

The objective is to determine:

* Who owns the process
* What command it is running
* Who its parent process is
* Whether it is legitimate
* Whether it is associated with another service
* Why it is using the required port

---

# Important Security Principle

A process should not be terminated merely because it appears unfamiliar.

The correct approach is:

```text
Unknown process
      ↓
Investigate
      ↓
Identify
      ↓
Determine legitimacy
      ↓
Determine impact
      ↓
Choose remediation
```

Important principle:

> **Suspicious does not automatically mean malicious.**

---

# Graceful Process Termination

If termination is appropriate, the preferred signal is normally:

```bash
kill PID
```

For example:

```bash
kill 2450
```

By default, `kill` sends:

```text
SIGTERM (15)
```

SIGTERM allows a process an opportunity to terminate gracefully and perform cleanup.

---

# Forceful Process Termination

If a process remains unresponsive and forceful termination is justified:

```bash
kill -9 2450
```

The `-9` represents:

```text
SIGKILL (9)
```

SIGKILL cannot be caught, ignored, or handled by the target process.

It should generally not be the first choice because the process does not get an opportunity to perform normal cleanup.

Potential consequences include:

* Unsaved data
* Files not being closed normally
* Resources not being released cleanly
* Other cleanup operations not being performed

---

# Cross-Verification of SSH

After identifying the SSH Main PID:

```bash
systemctl show -p MainPID ssh
```

the process can be correlated with network information:

```bash
sudo ss -ltnp
```

For example:

```text
systemctl show
      ↓
MainPID=1336

ss
      ↓
sshd → PID 1336 → :22
```

This provides strong evidence that the SSH service's main process is the process listening on TCP port 22.

---

# Process Investigation

The process can further be investigated using:

```bash
ps -p 1336 -o pid,ppid,user,state,cmd
```

Example:

```text
PID     PPID   USER   STATE   CMD
1336    1      root   Ss      sshd ...
```

The relationship can be represented as:

```text
systemd (PID 1)
      |
      └── sshd (PID 1336)
              |
              └── LISTEN :22
```

---

# Understanding `Ss` Process State

A process state such as:

```text
Ss
```

contains two pieces of information.

```text
S
```

means:

```text
Interruptible Sleep
```

The process is sleeping/waiting and can be interrupted.

The lowercase:

```text
s
```

indicates that the process is a **session leader**.

Therefore:

```text
Ss
```

can be interpreted as:

```text
Interruptible Sleep + Session Leader
```

---

# Complete SSH Troubleshooting Workflow

A generalized SSH troubleshooting workflow developed during this lesson is:

```bash
systemctl status ssh
```

↓

```bash
journalctl -u ssh --since "18:00" --until "18:30"
```

↓

Identify the error.

If the error is:

```text
Bind to port 22 failed: Address already in use
```

then investigate:

```bash
sudo ss -ltnp
```

↓

Identify the PID using port 22.

↓

Investigate the PID:

```bash
ps -p PID -o pid,ppid,state,user,cmd
```

↓

Investigate its parent:

```bash
ps -p PPID -f
```

↓

Investigate process hierarchy:

```bash
pstree -p PID
```

↓

Determine whether the process is legitimate and why it owns port 22.

↓

Apply the appropriate remediation.

↓

Start SSH:

```bash
sudo systemctl start ssh
```

↓

Verify:

```bash
systemctl status ssh
```

↓

Verify Main PID:

```bash
systemctl show -p MainPID ssh
```

↓

Verify SSH network listener:

```bash
sudo ss -ltnp
```

↓

Verify boot configuration:

```bash
systemctl is-enabled ssh
```

↓

If automatic startup is required:

```bash
sudo systemctl enable ssh
```

---

# Commands Practiced

## Service Management

```bash
systemctl status ssh
systemctl start ssh
systemctl stop ssh
systemctl restart ssh
systemctl enable ssh
systemctl disable ssh
systemctl enable --now ssh
systemctl disable --now ssh
```

## Service State Checking

```bash
systemctl is-active ssh
systemctl is-enabled ssh
```

## Service Inspection

```bash
systemctl cat ssh
systemctl show ssh
systemctl show -p MainPID ssh
systemctl show -p ActiveState ssh
```

## Unit Listing

```bash
systemctl list-units
systemctl list-unit-files
systemctl --failed
```

## Log Investigation

```bash
journalctl -u ssh
journalctl -u ssh --since today
journalctl -u ssh -n 20
journalctl -u ssh --since today -n 20
journalctl -u ssh --since "18:00" --until "19:00"
```

## Process Investigation

```bash
ps -p PID -f
ps -p PID -o pid,ppid,state,user,cmd
pstree -p PID
pgrep process_name
pidof process_name
```

## Network Investigation

```bash
sudo ss -ltnp
```

## Process Termination

```bash
kill PID
kill -9 PID
```

---

# Key Learning Points

* `systemd` is the primary system and service manager on modern Ubuntu systems.
* `systemd` normally runs as PID 1.
* A systemd service unit and its underlying process are different concepts.
* `systemctl` is used to manage systemd units.
* `active` describes the current service state.
* `enabled` describes automatic startup configuration.
* A service can be active and disabled simultaneously.
* A service can be inactive and enabled simultaneously.
* `start` affects the current service state.
* `enable` affects future boot behavior.
* `enable --now` enables a service and starts it immediately.
* `systemctl cat` displays unit-file configuration.
* `systemctl show` displays detailed unit properties.
* `systemctl list-units` provides an overview of loaded units.
* `systemctl list-unit-files` displays installed unit files and their enablement states.
* `systemctl --failed` helps identify failed units.
* `journalctl -u` filters logs for a particular systemd unit.
* `--since`, `--until`, and `-n` can narrow journal investigations.
* `ss -ltnp` can identify processes listening on TCP ports.
* `ssh.service` is the systemd unit while `sshd` is the SSH daemon/process.
* PIDs can change after rebooting or restarting a service.
* `SIGTERM (15)` is normally preferred over `SIGKILL (9)`.
* `SIGKILL` should generally be reserved for situations where graceful termination is unsuccessful and forceful termination is justified.
* Troubleshooting should identify the cause before applying remediation.
* Evidence should be correlated across service state, logs, processes, and network information.

---

# Final Result

Successfully learned and practiced Linux service management and troubleshooting using `systemd` and `systemctl`.

The practical work included managing the SSH service, understanding service states and boot enablement, inspecting systemd unit configuration and properties, investigating journal logs, identifying failed services, correlating systemd services with their underlying processes, and investigating network ports associated with services.

The SSH troubleshooting investigation demonstrated how a Linux administrator can move from:

```text
Service Failure
      ↓
Service Status
      ↓
Journal Logs
      ↓
Root Cause
      ↓
Process Investigation
      ↓
Network Investigation
      ↓
Remediation
      ↓
Service Restart
      ↓
Verification
```

This lesson provided practical experience in Linux system administration and basic service troubleshooting.

---

# 📸 Screenshot Structure

The screenshots for this lab are available in the `Screenshots/` directory and are numbered in the order they were performed :

* Part_1_-_Understanding_Service_VS_Process :

	- 1. systemctl status 
	- 2. systectl status ssh
	- 3. Quick check  if the system active or not 
	- 4. Quick check of enabled or disenabled services
	- 5. Stop the service , not disable it
	- 6. Strat service 
	- 7. Restart the service and observe PID change
	- 8. Disable + stop the SSH 
	- 9. Enable + start SSH
	- 10. Check status
	- 11. Reading logs of the entire sys with journalctl 
	- 12. Read logs of ssh with journalctl
	- 13. Reading logs with limitation 
	- 14. Read today's logs of SSH with journalctl
	- 15. Read journal entries (logs) of today's with the limitation of 5 entries using journalctl
	- 16. Read logs of particular time duration with journalctl

* Part_2_-_Inspecting_systemd_Services :

	- 17. Dispaly the configuration of SSH unit
	- 18. Show detailed properties of unit
	- 19.1  List out all loaded units 
	- 19.2 SSH service spotted
	- 20.1 List out all unit files which are enablement
	- 20.2 SSH service spotted
	- 21. see the network statistics of listening ports to TCP with numerical IP
	
---

# Future Improvements

Future Linux administration lessons can build upon this knowledge by covering:

* Advanced `systemd` unit configuration
* Creating custom systemd services
* Service dependencies
* systemd targets
* Timers and scheduled services
* Service hardening
* SSH server hardening
* Resource limits for services
* Advanced log analysis
* Network service troubleshooting
* Linux boot process investigation

---

**Author:** Dhruv Sudani

**Course:** Self-Learning Linux & Cybersecurity Homelab

**Module:** Linux Administration

**Lesson:** 3 – Linux Service Management & Troubleshooting