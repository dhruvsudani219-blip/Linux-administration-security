# Linux Administration — Lesson 2
## Process Management & Monitoring

---

## Lesson Information

| Field             | Details                         |
|-------------------|---------------------------------|
| **Lesson Number** | 2                               |
| **Module**        | Linux Administration            |
| **Lesson Name**   | Process Management & Monitoring |
| **Platform**      | Ubuntu Linux                    |
| **Author**        | Dhruv                           |
| **Status**        | Completed                       |
| **Difficulty**    | Beginner → Intermediate         |

---

# Learning Objectives

The objective of this lesson was to understand how Linux creates, manages, monitors, prioritizes, and terminates processes.

The following concepts were studied and practiced:

- Linux processes
- PID and PPID
- Parent and child processes
- `ps` command
- Process states
- Foreground and background processes
- Shell job control
- Job IDs
- `jobs`, `bg`, and `fg`
- Linux signals
- `SIGTERM` and `SIGKILL`
- `kill` command
- Process priority
- Nice values
- `nice` and `renice`
- `PR` and `NI`
- Real-time process monitoring
- `top`
- `htop`
- `pgrep`
- `pidof`
- `pstree`
- Process investigation methodology

---

# 1. Introduction to Linux Processes

A **process** is a running instance of a program or command.

Whenever a command is executed in Linux, the operating system creates a process to execute that command.

For example:

```bash
sleep 100
```

creates a process that waits for 100 seconds before terminating.

Every running process is assigned a unique **Process ID (PID)**.

---

# 2. PID — Process ID

PID stands for **Process ID**.

It is a numerical identifier assigned by Linux to a process.

Example:

```text
PID = 4031
```

The PID identifies the process itself.

To view processes:

```bash
ps
```

or:

```bash
ps -ef
```

---

# 3. PPID — Parent Process ID

PPID stands for **Parent Process ID**.

It identifies the process that created or launched another process.

For example:

```text
PID  = 4560
PPID = 3284
```

This represents:

```text
3284 → Parent Process
   │
   └── 4560 → Child Process
```

A shell such as `bash` can create child processes when commands are executed.

---

# 4. Parent-Child Process Relationship

Linux processes form a hierarchical structure.

A simplified example:

```text
systemd
   │
   └── gnome-terminal
          │
          └── bash
                │
                └── sleep
```

The hierarchy can be viewed using:

```bash
pstree
```

To include PIDs:

```bash
pstree -p
```

This helps administrators understand which process created another process.

---

# 5. `ps` — Process Status

`ps` stands for **Process Status**.

It provides a snapshot of currently running processes.

Basic command:

```bash
ps
```

Detailed process information:

```bash
ps -ef
```

The `-e` option displays processes from all users.

The `-f` option displays the processes in full-format listing.

Typical information includes:

```text
UID
PID
PPID
C
STIME
TTY
TIME
CMD
```

---

# 6. Inspecting a Specific Process

When the PID is known, a specific process can be inspected using:

```bash
ps -p PID -f
```

Example:

```bash
ps -p 5200 -f
```

A customized process listing can also be created:

```bash
ps -o pid,ppid,user,state,cmd -p 5200
```

This displays:

- PID
- PPID
- USER
- STATE
- CMD

---

# 7. Process States

Linux processes can exist in different states.

Important states studied in this lesson include:

| State | Meaning               |
|-------|-----------------------|
| `R`   | Running or runnable   |
| `S`   | Interruptible Sleep   | (Sleeping / Waiting)
| `D`   | Uninterruptible Sleep |
| `T`   | Stopped               |
| `Z`   | Zombie                |

### `S` — Interruptible Sleep

A process in `S` state is waiting for an event or resource.

For example:

```bash
sleep 100
```

normally spends its time waiting, so it may appear in:

```text
S
```

state.

`S` does **not** automatically mean that the process is malfunctioning.

---

# 8. `sleep` Command

The `sleep` command pauses execution for a specified amount of time.

Example:

```bash
sleep 100
```

This creates a process that waits for 100 seconds.

Longer test processes were also used:

```bash
sleep 300
```

and:

```bash
sleep 600
```

These commands were useful for practicing process monitoring and management.

---

# 9. Foreground Processes

A foreground process occupies the terminal while it is running.

Example:

```bash
sleep 100
```

While the command is running in the foreground, the terminal waits for it to complete.

The terminal prompt becomes unavailable until the process finishes or is moved to the background.

---

# 10. `Ctrl + Z` — Stopping a Foreground Job

Pressing:

```text
Ctrl + Z
```

stops the foreground job.

Example:

```text
[1]+  Stopped    sleep 100
```

The process is **not terminated**.

It still exists, but it is currently stopped.

A stopped process may show:

```text
T
```

as its process state.

---

# 11. Job ID vs PID

A shell assigns a **Job ID** to jobs that it manages.

For example:

```text
[1]+  Stopped    sleep 100
```

Here:

```text
1
```

is the **Job ID**.

It is not the PID.

The shell refers to the job using:

```bash
%1
```

Therefore:

```text
%1 → Job ID 1
```

while:

```text
PID → Linux Process ID
```

### Important distinction

```text
PID
↓
Identifies the Linux process

Job ID
↓
Identifies a job managed by the current shell
```

---

# 12. `jobs` Command

The `jobs` command displays jobs managed by the current shell.

Example:

```bash
jobs
```

Possible output:

```text
[1]+  Running    sleep 100 &
```

The number inside `[ ]` represents the shell's Job ID.

---

# 13. `bg` — Background Jobs

A stopped job can be resumed in the background using:

```bash
bg %1
```

This resumes Job 1 in the background.

The terminal prompt becomes available again.

The process continues running while other commands can be entered.

A background process still has a PID.

---

# 14. `fg` — Foreground Jobs

A background job can be brought back to the foreground using:

```bash
fg %1
```

The terminal then waits for the foreground process.

The process continues to have the same PID.

`fg` changes the foreground/background status of a job; it does not create a new process.

---

# 15. Linux Signals

Linux uses **signals** to communicate with processes.

Signals can request that a process perform a particular action.

Important signals studied:

| Signal    | Number | Purpose                      |
|-----------|-------:|------------------------------|
| `SIGHUP`  | 1      | Hang-up/ Reload behavior     |
| `SIGTERM` | 15     | Graceful termination request |
| `SIGKILL` | 9      | Forceful termination         |
| `SIGCONT` | 18     | Continue a stopped process   |
| `SIGSTOP` | 19     | Stop a process               |


---

# 16. `kill` Command

The `kill` command sends a signal to a process.

Despite its name, `kill` does not always mean forcefully terminate.

For example:

```bash
kill 5200
```

By default, this sends:

```text
SIGTERM (15)
```

to the specified process.

---

# 17. SIGTERM — Signal Terminate

`SIGTERM` stands for **Signal Terminate**.

It requests that a process terminate gracefully.

Example:

```bash
kill 5200
```

By default:

```text
kill PID
    ↓
SIGTERM (15)
```

SIGTERM gives the application an opportunity to perform normal cleanup, such as:

- Saving data where supported
- Closing files
- Releasing resources
- Performing shutdown operations
- Exiting normally

Therefore, SIGTERM should normally be attempted before SIGKILL.

---

# 18. SIGKILL — Signal Kill

`SIGKILL` is signal number `9`.

It forcefully terminates a process.

Example:

```bash
kill -9 5200
```

This sends:

```text
SIGKILL (9)
```

SIGKILL cannot be caught, blocked, or handled by the target process.

Therefore, it should generally not be the first choice.

A typical administration approach is:

```text
Process needs to terminate
        │
        ▼
   SIGTERM (15)
        │
        ├── Process exits → Done
        │
        └── Still running
                │
                ▼
       Investigate the process
                │
                ▼
          SIGKILL (9)
```

---

# 19. SIGTERM vs SIGKILL

| Feature             | SIGTERM | SIGKILL |
|---------------------|---------|---------|
| Signal Number       | 15      | 9       |
| Graceful            | Yes     | No      |
| Forceful            | No      | Yes     |
| Cleanup opportunity | Yes     | No      |
| Can be handled      | Yes     | No      |
| Normal first choice | Yes     | No      |


---

# 20. Process Priority

Linux schedules multiple processes for CPU time.

Process scheduling can be influenced using the **nice value**.

The normal nice-value range is:

```text
-20 → Highest priority
  0 → Normal priority
+19 → Lowest priority
```

The relationship is:

```text
Lower nice value
      ↓
Higher scheduling priority

Higher nice value
      ↓
Lower scheduling priority
```

A higher nice value does **not** mean the process waits until every other process finishes.

It means the process generally receives lower scheduling preference when competing for CPU resources.

---

# 21. `nice` Command

The `nice` command starts a **new process** with a specified nice value.

Example:

```bash
nice -n 10 sleep 600
```

This starts the `sleep` process with:

```text
NI = 10
```

A positive nice value gives the process lower scheduling priority relative to processes with lower nice values.

---

# 22. `renice` Command

`renice` changes the nice value of an **already-running process**.

Example:

```bash
renice 15 -p 4030
```

This changes the nice value of PID `4030`.

Example:

```text
Before:
NI = 10

After:
NI = 15
```

Because:

```text
10 → 15
```

the process receives lower scheduling priority.

---

# 23. `nice` vs `renice`

| Command  | Purpose                                         |
|----------|-------------------------------------------------|
| `nice`   | Starts a new process with a selected nice value |
| `renice` | Changes the nice value of an existing process   |

Memory concept:

```text
nice
 ↓
NEW process

renice
 ↓
EXISTING process
```

---

# 24. PR and NI

While monitoring processes, `top` displays columns such as:

```text
PR
NI
```

### NI — Nice Value

`NI` represents the **nice value** of a process.

It can be influenced using:

```bash
nice
```

and:

```bash
renice
```

### PR — Priority

`PR` represents scheduling priority information displayed by the process-monitoring tool.

Approximate Formula :- PR = NI + 20

For example:

```text
PR = 30
NI = 10
```

These are different concepts.

A useful conceptual relationship is:

```text
NI
 ↓
Influences scheduling
 ↓
Kernel scheduler
 ↓
PR
```

For normal processes, a higher nice value generally corresponds to lower scheduling priority.

---

# 25. `top` — Real-Time Process Monitoring

`top` provides a continuously updating view of processes and system resource usage.

Run:

```bash
top
```

Important columns include:

```text
PID
USER
PR
NI
%CPU
%MEM
COMMAND
```

`top` can be used for:

- Real-time process monitoring
- CPU usage analysis
- Memory usage analysis
- Process identification
- Priority observation

To exit:

```text
q
```

---

# 26. Practical Observation with `top`

A `sleep` process was monitored using `top`.

Example observation:

```text
PID    NI    %CPU    %MEM
3525   10    0.0     0.1
```

Interpretation:

```text
PID = 3525
```

The process identifier was `3525`.

```text
NI = 10
```

The process had a positive nice value.

```text
%CPU = 0.0
```

The `sleep` process was not actively consuming significant CPU resources because it was mainly waiting.

```text
%MEM = 0.1
```

The process was using a small amount of system memory.

---

# 27. `htop` — Interactive Process Monitoring

`htop` is an interactive, real-time process monitoring tool.

Run:

```bash
htop
```

It provides information similar to `top`, but with a more interactive and user-friendly interface.

Important information includes:

```text
PID
USER
PRI
NI
VIRT
RES
SHR
STATE
CPU%
MEM%
TIME
COMMAND
```

---

# 28. Practical Observation with `htop`

A `sleep` process was monitored using `htop`.

Example observation:

```text
PID    NI    CPU%    MEM%
4840   10    0.0     0.1
```

Interpretation:

- `PID  = 4840` → identifies the process
- `NI   = 10`   → positive nice value
- `CPU% = 0.0`  → negligible CPU usage
- `MEM% = 0.1`  → low memory usage

The interactive interface of `htop` was found to be easier to navigate than `top`.

---

# 29. Useful `htop` Keys

| Key     | Function      |
|---------|---------------|
| `F3`    | Search        |
| `F4`    | Filter        | 
| `F5`    | Tree view     |
| `F6`    | Sort          |
| `F9`    | Send a signal |
| `F10`   | Exit          |
| `↑ / ↓` | Navigate      |

`F5` can be used to display process relationships in a tree-style view.

---

# 30. `pgrep`

`pgrep` searches for processes and returns matching PIDs.

Example:

```bash
pgrep sleep
```

Possible output:

```text
4359
```

This is useful when the primary objective is to quickly identify a process PID.

`pgrep` can also provide more flexible filtering capabilities.

For example:

```bash
pgrep -u dhruv sleep
```

can search for matching `sleep` processes belonging to a particular user.

---

# 31. `pidof`

`pidof` is primarily used to find PIDs associated with a program or executable name.

Example:

```bash
pidof sleep
```

Possible output:

```text
4521
```

It is useful for quickly finding the PID of a program.

---

# 32. `pgrep` vs `pidof`

Both commands can be used for quick PID lookup.

Example:

```bash
pgrep sleep
```

and:

```bash
pidof sleep
```

may return the PID of the same running process.

However, there is a practical difference:

```text
pidof
 ↓
Primarily program/executable-name-oriented PID lookup

pgrep
 ↓
More flexible process searching and filtering
```

Therefore:

```text
Quick program PID lookup → pidof

Flexible process searching → pgrep
```

---

# 33. `pstree` — Process Hierarchy

`pstree` displays processes in a hierarchical tree structure.

Basic:

```bash
pstree
```

With PIDs:

```bash
pstree -p
```

Example:

```text
systemd
 └── gnome-terminal
      └── bash
           └── sleep
```

This helps determine parent-child relationships.

---

# 34. Process Investigation Workflow

A systematic Linux process investigation can follow this workflow:

```text
1. Identify the process
        ↓
2. Find the PID
        ↓
3. Inspect PID / PPID / USER / STATE / CMD
        ↓
4. Investigate the parent process
        ↓
5. View the process hierarchy
        ↓
6. Monitor CPU and memory
        ↓
7. Check PR and NI
        ↓
8. Determine whether termination is necessary
        ↓
9. Attempt SIGTERM
        ↓
10. Use SIGKILL only if necessary
```

---

# 35. Final Process-Management Investigation

A simulated administrator investigation was performed using the following scenario:

```text
Process:
backup.sh

PID:
4820

PPID:
3510

STATE:
S

PR:
30

NI:
10
```

The investigation process was:

### Step 1 — Identify the process

```bash
ps -ef | grep "backup.sh"
```

### Step 2 — Inspect the process

```bash
ps -p 4820 -o pid,ppid,user,state,cmd
```

### Step 3 — Investigate the parent

```bash
ps -p 3510 -f
```

### Step 4 — View process hierarchy

```bash
pstree -p
```

### Step 5 — Monitor resource usage

```bash
top
```

or:

```bash
htop
```

### Step 6 — Gracefully terminate

```bash
kill 4820
```

This sends:

```text
SIGTERM (15)
```

by default.

### Step 7 — Force termination only if necessary

```bash
kill -9 4820
```

This sends:

```text
SIGKILL (9)
```

---

# 36. Final Investigation Explanation

A professional process investigation can be summarized as:

> I first used `ps -ef | grep "backup.sh"` to identify the PID and obtain detailed information about the `backup.sh` process. I then used `ps -p 4820 -o pid,ppid,user,state,cmd` to inspect its PID, PPID, user, process state, and command. The PPID indicates that PID 3510 is the parent process. I used `pstree -p` to visualize the process hierarchy. The process state `S` means Interruptible Sleep, which indicates that the process is waiting and does not necessarily indicate a problem. I used `top` or `htop` to monitor CPU and memory usage. Its NI value of `10` means that the process has a positive nice value and therefore has lower scheduling priority relative to processes with lower nice values. When termination was required, I first used `kill 4820`, which sends SIGTERM (15) by default. Since the process remained unresponsive, I could then consider `kill -9 4820`, which sends SIGKILL (9) and forcefully terminates the process.

---

# 37. Commands Practiced

```bash
ps
ps -ef
ps -p PID -f
ps -p PID -o pid,ppid,user,state,cmd
ps -o pid,ppid,user,state,cmd -p PID

pstree
pstree -p

sleep 100
sleep 300
sleep 600

jobs
bg %1
fg %1

kill PID
kill -9 PID

nice -n 10 sleep 600
renice 15 -p PID

top
htop

pgrep sleep
pidof sleep
```

---

# 38. Command Reference

| Command       |                   Purpose                    |
|---------------|----------------------------------------------|
| `ps`          | Display a process snapshot                   |
| `ps -ef`      | Display detailed information about processes |
| `ps -p PID -f`| Inspect a specific process                   |
| `ps -o ...`   | Display selected process information         |
| `pstree`      | Display process hierarchy                    |
| `pstree -p`   | Display hierarchy with PIDs                  |
| `sleep`       | Create a waiting process for testing         |
| `jobs`        | Display shell jobs                           |
| `bg %1`       | Resume Job 1 in background                   |
| `fg %1`       | Bring Job 1 to foreground                    |
| `kill PID`    | Send SIGTERM by default                      |
| `kill -9 PID` | Send SIGKILL                                 |
| `nice`        | Start a process with a selected nice value   |
| `renice`      | Change nice value of an existing process     |
| `top`         | Real-time process monitoring                 |
| `htop`        | Interactive real-time process monitoring     |
| `pgrep`       | Search processes and return matching PIDs    |
| `pidof`       | Find PIDs associated with a program          |

---

# 39. Important Linux Administration Notes

### 1. Prefer SIGTERM before SIGKILL

Use:

```bash
kill PID
```

before:

```bash
kill -9 PID
```

whenever appropriate.

---

### 2. PID and Job ID are different

```text
PID
→ Linux process identifier

%1
→ Shell Job ID
```

---

### 3. `S` does not automatically mean a problem

```text
S = Interruptible Sleep
```

A process can legitimately remain in this state while waiting.

---

### 4. Higher nice value means lower scheduling priority

```text
-20 → Highest priority
  0 → Normal priority
+19 → Lowest priority
```

---

### 5. `ps`, `top`, and `htop` have different purposes

```text
ps
↓
Process snapshot

top
↓
Real-time process monitoring

htop
↓
Interactive real-time process monitoring
```

---

### 6. `nice` and `renice` are different

```text
nice
↓
Starts a new process with a nice value

renice
↓
Changes the nice value of an existing process
```

---

# 40. Key Takeaways

After completing this lesson, the following concepts were understood and practically demonstrated:

```text
Process
   ↓
PID
   ↓
PPID
   ↓
Parent / Child relationship
   ↓
Process State
   ↓
Foreground / Background
   ↓
Job ID
   ↓
Signals
   ↓
SIGTERM / SIGKILL
   ↓
Process Priority
   ↓
Nice Value
   ↓
PR / NI
   ↓
Resource Monitoring
   ↓
top / htop
   ↓
Process Searching
   ↓
pgrep / pidof
   ↓
Process Hierarchy
   ↓
pstree
```

---

# 📸 Screenshot Structure

The screenshots for this lab are available in the `Screenshots/` directory and are numbered in the order they were performed :

- 1.1 Sleep command
- 1.2 ps -ef command to investigate the execution of sleep cmd
- 1.3 Know the Parent & Child process
- 2.1 pstree 
- 2.2 pstree output
- 2.3 pstree -p
- 2.4 pstree -p output
- 3.1 Check commands state
- 3.2  Stop sleep cmd and check its state again
- 4.  jobs cmd
- 5.1  bg cmd with job ID
- 5.2 bg cmd with job ID (Immediately) 
- 6. fg cmd 
- 7.1  Run sleep 300
- 7.2 kill command with SIGTERM
- 8.1 nice command
- 8.2 Check the PID & NI value 
- 9.1 sleep command with nice 
- 9.2 renice command 
- 10.1 sleep with nice cmd
- 10.2  top cmd to find out sleep statistics 
- 11.1 Sleep with nice command 
- 11.2 psgrep cmd 
- 12. pidof cmd
- 13.1 install htop cmd 
- 13.2 htop cmd output
- 13.3 Sleep cmd with nice
- 13.4 find sleep through htop

---


# Lesson Completion

## ✅ Linux Administration — Lesson 2 Completed

**Lesson:** Process Management & Monitoring

**Status:** `COMPLETED`

The practical work covered process identification, process hierarchy, process states, shell job control, process termination, scheduling priority, and real-time resource monitoring.

---

### Author

**Author:** Dhruv Sudani

**Course:** Self-Learning Linux & Cybersecurity Home lab

**Module:** Linux Administration

**Lesson:** 2 – Process Management & Monitoring

---

> **"Understand the process before you terminate the process."**

---

# Next Step

Proceed to:

**Linux Administration — Lesson 3**

Continue building practical Linux system-administration skills through hands-on exercises and troubleshooting.