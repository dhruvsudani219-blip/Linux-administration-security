# Storage & Disk Management – Lesson 10

## Project Title

**Linux Storage Inspection and Disk-Space Troubleshooting Using Ubuntu**

---

## Objective

The objective of this lab was to understand Linux storage devices, partitions, filesystems, mount points, filesystem capacity, and directory disk usage.

The lab focused on using read-only Linux commands to inspect storage and investigate disk-space problems safely.

---

## Lab Environment

| Component               | Details                 |
|-------------------------|-------------------------|
| Host Operating System   | Windows 11              |
| Server Operating System | Ubuntu Linux 26.10      |
| Virtualization Platform | Oracle VirtualBox       |
| Storage Environment     | Virtual machine storage |
| Root Filesystem Type    | ext4                    |
| Root Mount Point        | `/`                     |
| Administrative Shell    | Bash                    |

---

## 1. Introduction to Linux Storage

Linux uses several related storage concepts.

### Disk

A disk is a storage device presented to the operating system. In a virtual machine, a virtual disk may be backed by a file on the host computer.

### Partition

A partition is a defined region of a disk. A single disk can contain multiple partitions.

### Filesystem

A filesystem organises files and directories on a storage device. Examples include ext4, XFS, NTFS, FAT32, and exFAT.

### Mount Point

A mount point is a directory through which a filesystem becomes accessible.

Examples include:

- `/` — the root of the Linux directory tree.
- `/home` — commonly contains users' home directories.
- `/mnt` — commonly used for temporary mounts.
- `/media` — commonly used for removable media.

---

## 2. Listing Disks and Partitions

Command:

```bash
lsblk
```

Purpose:

- Lists block devices.
- Shows disks and their partitions.
- Displays device sizes and types.
- Shows mount points when available.

Important columns:

| Column        | Meaning                               |
|---------------|---------------------------------------|
| `NAME`        | Device or partition name              |
| `SIZE`        | Reported storage size                 |
| `TYPE`        | Device type, such as `disk` or `part` |
| `MOUNTPOINTS` | Mount points associated with a device |

Device names vary by system. Common examples include `sda`, `sdb`, and `nvme0n1`.

---

## 3. Checking Filesystem Capacity

Command:

```bash
df -h
```

The `df` command reports filesystem space usage. The `-h` option displays values in human-readable units.

Important columns:

| Column       | Meaning                           |
|--------------|-----------------------------------|
| `Filesystem` | Storage source                    |
| `Size`       | Total filesystem capacity         |
| `Used`       | Space currently used              |
| `Avail`      | Space available to ordinary users |
| `Use%`       | Percentage of space used          |
| `Mounted on` | Filesystem mount point            |

The root filesystem mounted at `/` was inspected during the lab.

**Key learning:** `df -h` is useful for determining whether a filesystem is running low on space.

---

## 4. Measuring Directory Disk Usage

### Inspecting `/var/log`

Command:

```bash
sudo du -sh /var/log
```

Observed result: Approximately 400 MB.

The `/var/log` directory commonly stores system and application logs.

### Inspecting `/home`

Command:

```bash
sudo du -sh /home
```

Observed result: Approximately 1.2 GB.

The `/home` directory commonly contains user files and personal directories.

### Understanding the command

| Component             | Meaning                   |
|-----------------------|---------------------------|
| `du`                  | Measures disk usage       |
| `-s`                  | Displays a summary        |
| `-h`                  | Uses human-readable units |
| `/var/log` or `/home` | Directory being measured  |

Directory usage is not the same as total filesystem usage. Other directories, filesystem metadata, and allocation behaviour also contribute to storage consumption.

---

## 5. Identifying Filesystem Types

Command:

```bash
lsblk -f
```

Purpose:

- Displays filesystem-related information.
- Shows filesystem types.
- Displays filesystem labels and UUIDs when available.
- Shows mount points.

Important columns:

| Column        | Meaning                            |
|---------------|------------------------------------|
| `FSTYPE`      | Filesystem type                    |
| `FSVER`       | Filesystem version, when available |
| `LABEL`       | Optional filesystem label          |
| `UUID`        | Filesystem identifier              |
| `MOUNTPOINTS` | Associated mount points            |

### Lab Observation

The root filesystem was identified as **ext4**.

ext4 is a widely used Linux filesystem.

---

## 6. Inspecting Mount Points

Command:

```bash
findmnt
```

Purpose:

- Lists mounted filesystems.
- Displays mount targets.
- Helps identify which filesystems are attached to the Linux directory tree.

The root mount point `/` was observed during the lab.

If `/home` is not a separate mount target, it is generally a directory on another mounted filesystem, commonly the root filesystem.

**Key learning:** A directory is not necessarily a separate filesystem. Its storage capacity depends on the filesystem on which it resides.

---

## 7. Inspecting Persistent Mount Configuration

Command:

```bash
cat /etc/fstab
```

The `/etc/fstab` file defines filesystems and related options intended for mounting automatically, typically during system startup or through system mount operations.

Entries may identify filesystems using:

- UUIDs
- Device paths
- Labels

A UUID can help identify a filesystem independently of device naming.

The file was inspected without modification during this lab.

**Safety note:** Incorrect changes to `/etc/fstab` can cause mounting failures or complicate system startup. Always understand an entry and maintain a recovery plan before editing it.

---

## 8. Understanding the Difference Between `df` and `du`

| Command        | Main purpose                                      |
|----------------|---------------------------------------------------|
| `df -h`        | Shows filesystem capacity and free space          |
| `du -sh /path` | Summarises disk usage for a directory             |
| `lsblk`        | Lists block devices and partitions                |
| `lsblk -f`     | Displays filesystem information for block devices |
| `findmnt`      | Displays mounted filesystems and mount targets    |

For example, `df -h` can reveal that `/` is nearly full, while `du` can help identify which directories contribute to storage consumption.

The reported values may differ because filesystem allocation, metadata, deleted-but-open files, and other factors affect the relationship between directory measurements and filesystem usage.

---

## 9. Storage Troubleshooting Methodology

When a filesystem approaches 100% usage, investigate before deleting files.

A safe approach is:

1. Identify which filesystem is full using `df -h`.
2. Investigate relevant directories using `du`.
3. Identify large files and determine their purpose.
4. Check whether logs, application data, caches, or temporary files are responsible.
5. Consider retention requirements before removing data.
6. Use the appropriate cleanup method.
7. Recheck filesystem usage after the change.

### Security Consideration

System and security logs may be important for incident investigation. Blindly deleting logs can destroy useful evidence and interfere with troubleshooting.

Prefer appropriate log rotation, retention management, or application-specific cleanup methods.

---

## 10. Final Practical Review

The following commands were practised:

```bash
lsblk
df -h
sudo du -sh /var/log
sudo du -sh /home
lsblk -f
findmnt
cat /etc/fstab
```

All commands above were used for storage inspection. No destructive disk operations or persistent storage configuration changes were performed during this lab.

### Lab Observations

- The root filesystem uses ext4.
- The root filesystem was inspected with `df -h`.
- `/var/log` reported approximately 400 MB of disk usage.
- `/home` reported approximately 1.2 GB of disk usage.
- `/` was identified as the relevant mount point.
- `/etc/fstab` was inspected without modification.

---

## 11. Key Learning Points

- Disks, partitions, filesystems, and mount points serve different purposes.
- `lsblk` lists storage devices and partitions.
- `df -h` reports filesystem capacity and usage.
- `du -sh` summarises directory disk usage.
- `lsblk -f` provides filesystem information.
- `findmnt` helps identify mount points.
- `/etc/fstab` contains persistent mount configuration.
- UUIDs help identify filesystems.
- Filesystem usage and directory usage are related but not identical.
- Storage problems should be investigated before deleting data.
- Destructive disk operations should never be performed without verifying the target and maintaining an appropriate recovery plan.

---

## 12. Future Improvements

Potential follow-up topics include:

- Inspecting individual large files and directories.
- Understanding inode exhaustion.
- Learning safe mounting and unmounting procedures.
- Inspecting filesystem and disk health indicators.
- Understanding virtual disk expansion.
- Practising storage troubleshooting in a disposable test environment.
- Learning backup and recovery procedures.

# Screenshot Structure

The screenshots for this lab are available in the `Screenshots/` directory and are numbered in the order they were performed

---

**Author:** Dhruv Sudani

**Course:** Self-Learning Linux & Cybersecurity Homelab

**Module:** Linux Administration

**Lesson:** 10 – Storage & Disk Management
