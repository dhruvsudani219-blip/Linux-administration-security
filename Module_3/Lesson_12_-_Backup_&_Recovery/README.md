# Linux Administration – Lesson 12: Backup & Recovery

## Project Title

**Linux Backup and Recovery Management**

---

## Objective

The objective of this lab was to understand Linux backup methods, create backup archives, verify backup integrity, and practise recovering files from previously created backups.

The lab focused on manual backups, TAR archives, GZIP compression, SHA-256 checksum verification, recovery procedures, and backup security practices.

---

## Lab Environment

| Component               | Details                |
|-------------------------|------------------------|
| Host Operating System   | Windows 11             |
| Virtualization Platform | Oracle VirtualBox      |
| Server Operating System | Ubuntu Linux 24.04 LTS |
| Shell                   | Bash                   |
| Practice Directory      | `~/backup_lab`         |

---

## 1. Introduction to Backup Fundamentals

A backup is a separate copy of important data that can be used to restore information after accidental deletion, corruption, hardware failure, or other incidents.

### Types of Backups

| Backup Type         | Description                                  |
|---------------------|----------------------------------------------|
| Full Backup         | Copies all selected data.                    |
| Incremental Backup  | Copies changes since the most recent backup. |
| Differential Backup | Copies changes since the last full backup.   |

### Important Recovery Concept

To restore an incremental backup, the administrator needs the original full backup and every required incremental backup after it.

To restore a differential backup, the administrator needs the original full backup and the latest differential backup.

---

## 2. Important Linux Backup Locations

| Directory  | Purpose                           |
|------------|-----------------------------------|
| `/home`    | User documents and personal files |
| `/etc`     | System configuration files        |
| `/var/www` | Web server files, when applicable |
| `/var/log` | System and application logs       |
| `/var/lib` | Application and service data      |

Directories such as `/proc`, `/sys`, and `/dev` contain virtual or dynamically generated system information and are not ordinary backup targets.

---

## 3. Practical Implementation

### Step 1 – Create a Practice Directory

```bash
mkdir -p ~/backup_lab/original
```

Create sample files:

```bash
echo "Linux Backup Practice" > ~/backup_lab/original/notes.txt
echo "Important project configuration" > ~/backup_lab/original/config.txt
```

### Step 2 – Create a Manual Backup Using `cp`

Create a backup directory:

```bash
mkdir -p ~/backup_lab/backup
```

Copy the original directory:

```bash
cp -r ~/backup_lab/original ~/backup_lab/backup/
```

Verify the copied files:

```bash
ls -R ~/backup_lab
```

### Step 3 – Compare Original and Backup Files

```bash
diff ~/backup_lab/original/notes.txt ~/backup_lab/backup/original/notes.txt
```

```bash
diff ~/backup_lab/original/config.txt ~/backup_lab/backup/original/config.txt
```

No output from `diff` indicates that the compared files have identical content.

### Step 4 – Practise File Recovery

After simulating accidental deletion of the original practice file, restore it from the backup:

```bash
cp ~/backup_lab/backup/original/notes.txt ~/backup_lab/original/
```

Verify the restored file:

```bash
cat ~/backup_lab/original/notes.txt
```

### Step 5 – Create a TAR Archive

```bash
tar -cvf ~/backup_lab/backup.tar -C ~/backup_lab original
```

List archive contents:

```bash
tar -tvf ~/backup_lab/backup.tar
```

Extract the archive:

```bash
mkdir -p ~/backup_lab/recovery
tar -xvf ~/backup_lab/backup.tar -C ~/backup_lab/recovery
```

### Step 6 – Create a Compressed TAR Archive

```bash
tar -czvf ~/backup_lab/backup.tar.gz -C ~/backup_lab original
```

Inspect the compressed archive:

```bash
tar -tzvf ~/backup_lab/backup.tar.gz
```

Extract it:

```bash
mkdir -p ~/backup_lab/gzip_recovery
tar -xzvf ~/backup_lab/backup.tar.gz -C ~/backup_lab/gzip_recovery
```

### Step 7 – Verify Backup Integrity Using SHA-256

Generate the original file's hash:

```bash
sha256sum ~/backup_lab/original/notes.txt
```

Generate the backup file's hash:

```bash
sha256sum ~/backup_lab/backup/original/notes.txt
```

The two SHA-256 hashes matched, confirming that both files had identical content.

Generate a checksum for the compressed archive:

```bash
sha256sum ~/backup_lab/backup.tar.gz
```

Save the checksum:

```bash
sha256sum ~/backup_lab/backup.tar.gz > ~/backup_lab/backup.tar.gz.sha256
```

Verify the archive:

```bash
sha256sum -c ~/backup_lab/backup.tar.gz.sha256
```

Expected verification result:

```text
backup.tar.gz: OK
```

### Step 8 – Practise Archive Recovery

Extract the compressed backup into a separate recovery directory:

```bash
mkdir -p ~/backup_lab/final_recovery
tar -xzvf ~/backup_lab/backup.tar.gz -C ~/backup_lab/final_recovery
```

Verify the restored file:

```bash
cat ~/backup_lab/final_recovery/original/notes.txt
```

Compare restored files with the originals:

```bash
diff ~/backup_lab/original/notes.txt ~/backup_lab/final_recovery/original/notes.txt
```

```bash
diff ~/backup_lab/original/config.txt ~/backup_lab/final_recovery/original/config.txt
```

No output from both comparison commands indicates matching file contents.

---

## 4. Backup Strategies

### 3-2-1 Backup Rule

A widely used backup strategy:

- **3 copies:** Maintain three copies of important data.
- **2 storage media:** Use two different types of storage media.
- **1 offsite copy:** Keep at least one copy at another physical location.

### Local Backup

Stored near the original system.

Examples:
- External HDD
- NAS
- Separate backup server

### Offsite Backup

Stored at a geographically separate location.

Examples:
- Secure cloud storage
- Remote data centre
- Backup server at another office

### Backup Frequency and Retention

- Frequency defines how often backups are created.
- Retention defines how long backups are preserved.

A suitable retention policy helps maintain older recovery points.

---

## 5. Backup Security

### Encryption

Encryption protects backup confidentiality.

- Encryption at rest protects stored data.
- Encryption in transit protects data while travelling across a network.

### File Permissions

Example:

```bash
chmod 600 backup.tar.gz
```

This grants read and write permissions only to the file owner.

Avoid unnecessarily permissive settings such as:

```bash
chmod 777 backup.tar.gz
```

### Credential Protection

Protect:
- SSH private keys
- Cloud access tokens
- Encryption passwords
- Database credentials

Never upload secrets to public GitHub repositories.

### Immutable Backups

Immutable backups cannot be modified or deleted during a configured protection period.

They provide additional protection against ransomware and compromised accounts.

---

## 6. Commands Practised

| Command        | Purpose                                |
|----------------|----------------------------------------|
| `mkdir -p`     | Create directories                     |
| `echo`         | Create sample file content             |
| `cp -r`        | Copy directories recursively           |
| `ls -R`        | Display directory contents recursively |
| `diff`         | Compare file contents                  |
| `cat`          | Display file contents                  |
| `tar -cvf`     | Create TAR archive                     |
| `tar -tvf`     | List TAR archive contents              |
| `tar -xvf`     | Extract TAR archive                    |
| `tar -czvf`    | Create compressed TAR archive          |
| `tar -tzvf`    | List compressed archive contents       |
| `tar -xzvf`    | Extract compressed TAR archive         |
| `sha256sum`    | Generate SHA-256 checksum              |
| `sha256sum -c` | Verify saved checksum                  |
| `chmod`        | Modify file permissions                |

---

## 7. Cybersecurity Relevance

Backup and recovery management is important in:

- Linux system administration
- Disaster recovery planning
- Ransomware incident response
- Digital forensics preparation
- Business continuity
- Cloud infrastructure security
- Server maintenance

Backups help organisations recover critical data and restore services after security incidents or unexpected failures.

---

## 8. Key Learning Points

- Understood full, incremental, and differential backups.
- Identified important Linux directories for backup.
- Created manual backups using `cp`.
- Created TAR archives and compressed TAR.GZ archives.
- Practised extracting and restoring archived data.
- Compared files using `diff`.
- Generated and verified SHA-256 checksums.
- Understood the 3-2-1 backup rule.
- Learned about backup frequency and retention.
- Studied encryption, permissions, credential protection, and immutable backups.

---

## Result

Successfully completed the Linux Backup and Recovery practical lab.

Created manual backups, generated TAR and compressed TAR.GZ archives, verified file integrity using SHA-256 checksums, and practised restoring files from backup archives.

---

# 📸 Screenshot Structure

The screenshots for this lab are available in the `Screenshots/` directory and are numbered in the order they were performed.

---

**Author:** Dhruv Sudani

**Course:** Self-Learning Linux & Cybersecurity Homelab

**Module:** Linux Administration

**Lesson:** 12 – Backup & Recovery