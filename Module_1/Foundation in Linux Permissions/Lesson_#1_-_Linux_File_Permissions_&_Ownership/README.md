# Linux File Permissions & Ownership – Lesson 1

## Project Title

**Understanding Linux File Permissions, Ownership, and Access Control**

---

## Objective

The objective of this lab was to understand how Linux protects files and directories using its permission model. The lab focused on interpreting symbolic permissions (`rwx`), identifying file ownership, modifying permissions with `chmod`, and understanding why "Permission denied" occurs.

A real-world troubleshooting scenario involving directory permissions was also encountered and analyzed.

---

## Lab Environment

| Component                  | Details                 |
| -------------------------- | ----------------------- |
| Host Operating System      | Windows 11              |
| Virtualization Software    | Oracle VirtualBox       |
| Guest Operating System     | Ubuntu Linux            |
| Network Configuration      | NAT + Host-Only Adapter |
| Linux Users                | dhruv, intern           |
| Permission Management Tool | chmod                   |

---

## Prerequisites

Before starting this lab, the following tasks had already been completed:

* OpenSSH Server installed and configured
* Secure SSH connection established
* SCP file transfer successfully tested
* Multiple Linux users created
* Remote login using different user accounts verified

---

# Lab Objectives

* Understand Linux file permissions.
* Interpret symbolic permission strings (`rwx`).
* Understand the difference between Owner, Group, and Others.
* Learn how Linux evaluates access requests.
* Modify permissions using `chmod`.
* Observe Linux security in a multi-user environment.

---

# Initial Observation

The original plan was to access a file located inside:

```text
/home/dhruv
```

while logged in as the `intern` user.

However, the following command failed:

```bash
cd /home/dhruv
```

Result:

```text
Permission denied
```

---

# Troubleshooting Process

## Step 1 – Verify Directory Permissions

Checked the permissions of the home directory.

Command used:

```bash
ls -ld /home/dhruv
```

Output:

```text
drwxr-x---  dhruv dhruv ...
```

Analysis:

* Owner (`dhruv`) → Full access (`rwx`)
* Group (`dhruv`) → Read and Execute (`r-x`)
* Others → No permissions (`---`)

Since the `intern` user was neither the owner nor a member of the `dhruv` group, Linux denied access before even checking the file permissions.

---

## Root Cause

The issue was **not** related to the file itself.

Linux first checks permissions on every directory in the file path.

Because the `intern` user could not traverse `/home/dhruv`, access to files inside that directory was also denied.

This demonstrated that directory permissions are evaluated before file permissions.

---

# Revised Lab Setup

To focus on file permissions without interference from home directory restrictions, a dedicated practice directory was created.

Create the lab directory:

```bash
mkdir /tmp/lesson4_lab
```

Create a test file:

```bash
touch /tmp/lesson4_lab/lesson4.txt
```

Verify:

```bash
ls /tmp/lesson4_lab
```

---

# View File Permissions

Display detailed information:

```bash
ls -l /tmp/lesson4_lab
```

Example:

```text
-rw-r--r-- 1 dhruv dhruv 0 Aug 4 lesson4.txt
```

---

# Understanding Permission Strings

Example:

```text
-rw-r--r--
```

Meaning:

| Section | Description        |
| ------- | ------------------ |
| `-`     | Regular file       |
| `rw-`   | Owner permissions  |
| `r--`   | Group permissions  |
| `r--`   | Others permissions |

Permission symbols:

| Symbol | Meaning                |
| ------ | ---------------------- |
| `r`    | Read                   |
| `w`    | Write                  |
| `x`    | Execute                |
| `-`    | Permission not granted |

---

# Verify Ownership

Command:

```bash
ls -l /tmp/lesson4_lab/lesson4.txt
```

Example:

```text
-rw-r--r-- 1 dhruv dhruv ...
```

Owner:

```text
dhruv
```

Group:

```text
dhruv
```

---

# Test Access as Another User

Switch to the `intern` account:

```bash
su - intern
```

Navigate to the practice directory:

```bash
cd /tmp/lesson4_lab
```

Attempt to modify the file:

```bash
echo "Hello from intern" >> lesson4.txt
```

Linux denied write access because the file owner had not granted write permission to Others.

---

# Modify File Permissions

Return to the `dhruv` account.

Grant write permission to Others:

```bash
chmod o+w /tmp/lesson4_lab/lesson4.txt
```

Verify:

```bash
ls -l /tmp/lesson4_lab/lesson4.txt
```

The permission string changed to include write permission for Others.

---

# Verify Again

Switch back to `intern`.

Append text:

```bash
echo "Hello from intern" >> lesson4.txt
```

The operation completed successfully.

Verify the file contents:

```bash
cat lesson4.txt
```

---

# Restore Original Permissions

Return to `dhruv`.

Remove write permission:

```bash
chmod o-w /tmp/lesson4_lab/lesson4.txt
```

Verify:

```bash
ls -l /tmp/lesson4_lab/lesson4.txt
```

---

# Commands Practiced

```bash
touch lesson4.txt
```

```bash
ls -l
```

```bash
ls -ld /home/dhruv
```

```bash
mkdir /tmp/lesson4_lab
```

```bash
touch /tmp/lesson4_lab/lesson4.txt
```

```bash
chmod o+w /tmp/lesson4_lab/lesson4.txt
```

```bash
chmod o-w /tmp/lesson4_lab/lesson4.txt
```

```bash
su - intern
```

```bash
whoami
```

```bash
pwd
```

```bash
cat lesson4.txt
```

```bash
exit
```

---

# Concepts Learned

* Linux file permissions
* Owner, Group, and Others
* Read (`r`)
* Write (`w`)
* Execute (`x`)
* Directory permissions
* File permissions
* Permission inheritance through directory traversal
* File ownership
* Access control using `chmod`

---

# Key Learning

One of the most valuable lessons from this lab was discovering that Linux evaluates directory permissions before file permissions.

Initially, the objective was to modify a file inside another user's home directory. However, the `intern` user was unable to enter `/home/dhruv` because of the directory's permissions.

This demonstrated an important Linux security principle:

> To access a file, a user must first have permission to traverse every directory in its path.

Only after understanding this behavior was the lab redesigned using a dedicated practice directory in `/tmp`, allowing the focus to shift to file permissions and `chmod`.

---

# Skills Practiced

* Linux File Permissions
* Linux Directory Permissions
* File Ownership
* chmod
* Multi-user Access Control
* Linux Security
* Troubleshooting
* System Administration Fundamentals

---

# Result

Successfully interpreted Linux permission strings, identified file ownership, modified permissions using `chmod`, and observed how Linux enforces access control for multiple users.

Additionally, the lab demonstrated that directory permissions are checked before file permissions, providing a deeper understanding of Linux's security model.

---

# Future Improvements

* Learn numeric (octal) permissions
* Understand `chmod 755`, `644`, `700`, and `600`
* Learn file ownership using `chown`
* Explore Linux groups
* Practice permission management in real-world administration scenarios

---

## 📸 Screenshots

The screenshots for this lab are available in the `Screenshots_Revised/` directory and are numbered in the order they were performed:

- 1. Login as normal user
- 2. Create temporary practice directory 
- 3. View directory permissions
- 4. View file ownership
- 5. Switch and Verify User
- 6. Try to navigate into new created dir
- 7. Try to write to created file
- 8. Logout & get back to normal user
- 9. Give write permission to that file
- 10. Test again
- 11. Remove the permission 

---

**Author:** Dhruv Sudani

**Course:** Self-Learning Linux & Cybersecurity Homelab

**Lesson:** 4 – Linux File Permissions & Ownership
