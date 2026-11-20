# Advanced Linux Permissions & ACLs – Lesson 6

## Project Title

**Advanced Linux Permissions, Special Permissions & Access Control Lists**

---

# Objective

The objective of this lab was to understand advanced Linux permission mechanisms used for system administration and security.

The lab focused on:

- SUID
- SGID
- Sticky Bit
- Special permission numbers
- Access Control Lists (ACLs)
- ACL masks
- Effective permissions
- Directory traversal
- Permission investigation

---

# Lab Environment

| Component        | Details                                  |
|------------------|------------------------------------------|
| Operating System | Ubuntu Linux                             |
| Virtualization   | Oracle VirtualBox                        |
| Environment      | Linux Cybersecurity Homelab              |
| User             | Linux local user                         |
| Purpose          | Linux Administration & Security Practice |

---

# Introduction

Linux provides the traditional permission model:

```text
Owner → Group → Others
```

with three basic permissions:

```text
r → Read
w → Write
x → Execute
```

Linux also provides advanced permission mechanisms such as:

```text
SUID
SGID
Sticky Bit
ACL
```

These mechanisms allow more detailed access control and are important for Linux administration and security investigations.

---

# Part 1 – Linux Permission Model Review

Linux permissions are represented using:

```text
rwx
```

for:

```text
Owner
Group
Others
```

For example:

```text
-rwxr-xr--
```

means:

```text
Owner  → rwx
Group  → r-x
Others → r--
```

For directories:

```text
r → List directory contents
w → Create/delete/rename directory entries
x → Enter/traverse the directory
```

Directory `x` permission is particularly important when accessing files through a path.

---

# Part 2 – SUID

SUID means:

**Set User ID**

When SUID is applied to an executable, the program runs with the effective privileges of the file owner.

Example:

```text
-rwsr-xr-x
```

The `s` appears in the owner execute position.

---

## SUID Numeric Value

```text
4000
```

Example:

```bash
chmod 4755 program
```

---

## Find SUID Files

Command:

```bash
find / -perm -4000 -type f 2>/dev/null
```

This searches the system for files with the SUID bit enabled.

Finding a SUID file does not automatically mean the file is malicious.

A security investigation should examine:

1. File location
2. File owner
3. File type
4. Package origin
5. Whether SUID is expected
6. Whether the file has been modified or replaced

---

# SUID Investigation

Example:

```bash
ls -l /usr/bin/passwd
```

Used to inspect:

- Permissions
- Owner
- Group

File information:

```bash
file /usr/bin/passwd
```

Package ownership:

```bash
dpkg -S /usr/bin/passwd
```

The `file` command may display information such as architecture and build metadata.

Build metadata is not the same thing as a cryptographic hash.

---

# Part 3 – SGID

SGID means:

**Set Group ID**

SGID has two important behaviors.

## SGID on Executables

An SGID executable can run with the effective group privileges of the file's group.

Example:

```text
-rwxr-sr-x
```

The `s` appears in the group execute position.

---

## SGID on Directories

When SGID is applied to a directory, newly created files and directories normally inherit the directory's group ownership.

Example:

```text
drwxrwsr-x
```

The `s` in the group position indicates SGID.

---

# SGID Lab

Created a test directory:

```bash
mkdir ~/sgid_lab
```

Created a group:

```bash
sudo groupadd sgidlab
```

Changed ownership:

```bash
sudo chown "$(whoami)":sgidlab ~/sgid_lab
```

Enabled SGID:

```bash
chmod g+s ~/sgid_lab
```

Created a test file:

```bash
touch ~/sgid_lab/test.txt
```

The file inherited the directory's group ownership.

---

# Important SGID Concept

SGID on a directory does not give users additional permissions to files.

For example, if a file has:

```text
-r--r--r--
```

SGID does not automatically give a group member write or execute permission.

SGID controls group inheritance.

File permissions are still evaluated separately.

---

# SGID Numeric Value

```text
2000
```

Example:

```bash
chmod 2755 directory
```

Breakdown:

```text
2 → SGID
7 → Owner rwx
5 → Group r-x
5 → Others r-x
```

---

# Part 4 – Sticky Bit

Sticky Bit is commonly used on shared writable directories.

Example:

```text
drwxrwxrwt
```

The `t` indicates Sticky Bit.

A classic example is:

```text
/tmp
```

Sticky Bit restricts users from deleting or renaming entries belonging to other users in the shared directory, subject to the directory owner/root rules.

---

# Sticky Bit Numeric Value

```text
1000
```

Example:

```bash
chmod 1777 directory
```

Breakdown:

```text
1 → Sticky Bit
777 → rwxrwxrwx
```

---

# Sticky Bit Practical Lab

Created:

```bash
mkdir ~/sticky_lab
```

Set normal shared permissions:

```bash
chmod 777 ~/sticky_lab
```

Enabled Sticky Bit:

```bash
chmod 1777 ~/sticky_lab
```

Verified:

```bash
ls -ld ~/sticky_lab
```

Created temporary users:

```bash
sudo useradd -m stickyA
sudo useradd -m stickyB
```

Created a shared directory:

```bash
sudo mkdir /tmp/sticky_lab
sudo chmod 777 /tmp/sticky_lab
```

Created a file as `stickyA`:

```bash
sudo -u stickyA touch /tmp/sticky_lab/A_file.txt
```

Without Sticky Bit, `stickyB` was able to delete the file.

After enabling:

```bash
sudo chmod 1777 /tmp/sticky_lab
```

`stickyB` could no longer delete `stickyA`'s file.

---

# Special Permission Summary

```text
4000 → SUID
2000 → SGID
1000 → Sticky Bit
```

Memory trick:

```text
4 → SUID
2 → SGID
1 → Sticky
```

---

# Part 5 – Access Control Lists (ACL)

Traditional Linux permissions provide:

```text
Owner
Group
Others
```

ACLs provide more granular access control.

For example:

```text
Owner       → rw-
Group       → r--
Specific user → rw-
Others      → ---
```

This allows a specific user to receive additional permissions without changing the file's ownership or main group.

---

# Check ACL Tools

Command:

```bash
getfacl --version
```

The ACL utility used in this lab was:

```text
getfacl 2.3.2
```

---

# getfacl

`getfacl` displays the Access Control List of a file or directory.

Command:

```bash
getfacl ~/acl_test.txt
```

Typical ACL entries:

```text
user::rw-
group::rw-
other::r--
```

Meaning:

```text
user::  → File owner
group:: → Owning group
other:: → Everyone else
```

---

# setfacl

`setfacl` modifies ACL entries.

Example:

```bash
setfacl -m u:stickyA:rw ~/acl_test.txt
```

Breakdown:

```text
setfacl → Modify ACL
-m → Modify/add ACL entry
u → User
stickyA → Specific username
rw → Read + Write
```

This gives `stickyA` read and write access through the ACL.

---

# Remove a Specific ACL Entry

Command:

```bash
setfacl -x u:stickyA ~/acl_test.txt
```

Meaning:

```text
-x → Remove ACL entry
u:stickyA → Specific user entry
```

---

# ACL Mask

The ACL mask controls the maximum effective permissions for:

- Named users
- Owning group entry
- Named groups

The mask does NOT apply to:

- File owner entry
- Others entry

Example:

```text
user::rwx
user:stickyA:rwx
group::rwx
mask::r-x
other::r--
```

The ACL requests:

```text
stickyA → rwx
```

But the mask is:

```text
r-x
```

Therefore the effective permission is:

```text
r-x
```

---

# ACL Mask Practical Test

Set:

```bash
setfacl -m u:stickyA:rwx ~/acl_test.txt
```

Then restrict the mask:

```bash
setfacl -m m:r-- ~/acl_test.txt
```

The ACL could then show:

```text
user:stickyA:rwx
mask::r--
```

The effective permission becomes:

```text
r--
```

Therefore `stickyA` can read the file but cannot write to it.

---

# ACL Troubleshooting

During the lab, `stickyA` initially received:

```text
Permission denied
```

even though the file ACL granted:

```text
user:stickyA:rw-
```

The cause was directory traversal.

The home directory had permissions similar to:

```text
drwxr-x---
```

The user `stickyA` was not a member of the owning group, so it did not have the required directory traversal permission.

An ACL entry was temporarily added:

```bash
setfacl -m u:stickyA:x ~
```

This allowed `stickyA` to traverse the home directory without granting read or write permission to the directory itself.

---

# Important Directory Traversal Concept

Having permission on a file is not always sufficient.

For:

```text
/home/dhruv/acl_test.txt
```

the user must be able to traverse the directories in the path.

Conceptually:

```text
/home
  ↓
/home/dhruv
  ↓
acl_test.txt
```

Directory `x` permission allows traversal.

The `namei` command can help investigate the permissions of every path component:

```bash
namei -l ~/acl_test.txt
```

---

# Shell Context and ~ Expansion

During the ACL test, this command was discussed:

```bash
sudo -u stickyA sh -c 'echo "Written by stickyA" >> ~/acl_test.txt'
```

Inside the shell running as `stickyA`,:

```text
~
```

refers to:

```text
/home/stickyA
```

not:

```text
/home/dhruv
```

Therefore an absolute path was used:

```bash
sudo -u stickyA sh -c 'echo "Written by stickyA" >> /home/dhruv/acl_test.txt'
```

This successfully targeted the intended file.

---

# ACL Permission Investigation

Example:

```text
user::rw-
user:stickyA:rwx
group::rw-
mask::r--
other::r--
```

Analysis:

```text
Owner:
rw-

ACL entry for stickyA:
rwx

ACL mask:
r--

Effective stickyA permission:
r--

Others:
r--
```

Therefore:

```text
stickyA → Read ✓
stickyA → Write ✗
stickyA → Execute ✗
```

The mask reduced the effective permission from `rwx` to `r--`.

---

# Final Practical Investigation

Investigated:

```text
/opt/project
```

with permissions:

```text
drwxrwsr-t
```

The directory contained:

```text
-rwxrwxr--+ report.sh
```

ACL:

```text
user::rwx
user:alice:rwx
group::rwx
group:developers:rwx
mask::r-x
other::r--
```

Alice's ACL entry requested:

```text
rwx
```

The mask restricted the effective permission to:

```text
r-x
```

Therefore Alice could:

```text
Read    ✓
Execute ✓
Write   ✗
```

The directory's:

```text
s
```

represented SGID, while:

```text
t
```

represented Sticky Bit.

New files created inside the SGID directory inherit the directory's group ownership.

---

# Commands Practiced

```bash
find / -perm -4000 -type f 2>/dev/null

ls -l /usr/bin/passwd
file /usr/bin/passwd
dpkg -S /usr/bin/passwd

mkdir ~/sgid_lab
sudo groupadd sgidlab
sudo chown "$(whoami)":sgidlab ~/sgid_lab
chmod g+s ~/sgid_lab
touch ~/sgid_lab/test.txt

mkdir ~/sticky_lab
chmod 777 ~/sticky_lab
chmod 1777 ~/sticky_lab

sudo useradd -m stickyA
sudo useradd -m stickyB

sudo mkdir /tmp/sticky_lab
sudo chmod 777 /tmp/sticky_lab
sudo chmod 1777 /tmp/sticky_lab

getfacl --version
getfacl ~/acl_test.txt

setfacl -m u:stickyA:rw ~/acl_test.txt
setfacl -x u:stickyA ~/acl_test.txt
setfacl -m u:stickyA:rwx ~/acl_test.txt
setfacl -m m:r-- ~/acl_test.txt

setfacl -m u:stickyA:x ~
setfacl -x u:stickyA ~

namei -l ~/acl_test.txt
ls -l ~/acl_test.txt
ls -ld ~
```

---

# Security Relevance

Advanced Linux permissions are important for:

- Linux system administration
- SOC investigations
- Privilege escalation analysis
- Server hardening
- Access-control investigations
- Incident response
- Shared server environments

Important investigation questions include:

```text
Who owns the file?
What group owns it?
Are SUID/SGID/Sticky bits enabled?
Are ACLs present?
What does the ACL mask allow?
What are the effective permissions?
Can the user traverse the directory path?
Is the permission expected?
```

---

# Key Learning Points

- SUID uses the file owner's effective privileges when an executable runs.
- SGID on executables uses the file group's effective privileges.
- SGID on directories causes new objects to inherit the directory's group ownership.
- Sticky Bit restricts deletion/rename of other users' entries in shared writable directories.
- `4000` represents SUID.
- `2000` represents SGID.
- `1000` represents Sticky Bit.
- ACLs provide granular permissions for specific users and groups.
- `getfacl` displays ACLs.
- `setfacl` modifies ACLs.
- `setfacl -x` removes a specific ACL entry.
- The ACL mask limits effective permissions for named users and groups.
- The ACL mask does not limit the owner or `other::` entry.
- File permissions alone are not enough if directory traversal is blocked.
- `~` depends on the user/shell context.
- Effective permissions should always be considered during security investigations.

---

# Result

Successfully completed the Advanced Linux Permissions & ACLs laboratory.

The lab demonstrated:

```text
SUID
SGID
Sticky Bit
ACL
ACL Mask
Effective Permissions
Directory Traversal
Permission Investigation
```

# 📸 Screenshot Structure

The screenshots for this lab are available in the `Screenshots/` directory and are numbered in the order they were performed :

- 1. List directory permissions
- 2.1 Find SUID files
- 2.2 Grab first 10 files
- 2.3 View the permissions of that file
- 2.4 Investigation cmds of suspicious SUID files
- 3.1 Create a directory and a group, also change the ownership of user and group of that directory
- 3.2 Add SGID to group and create f file to see inheritence of group owner for that file
- 4.1 View Sticky Bit t
- 4.2 Create a directory and give full permission
- 4.3 Enable Sticky Bit 
- 4.4 Create users and set password
- 4.5 Create adirectory at safe place 
- 4.6 Create a file and remove it using different users
- 4.7 Enable Sticky Bit and perform the operation again
- 5.1 Check if ACL utilities are presented or not
- 5.2 Use getfacl cmd
- 5.3 Look at the ACL mask
- 5.4 Troubleshooting the traversal issue
- 5.5 Troubleshooting the traversal issue
- 5.6 Command to troubleshoot traversal issue
- 5.7 Read the content of file BUT can not write
- 5.8 Troubleshot the write content issue
- 5.9 Delete an entry from ACL
- 5.10 Add a user with rwx permission while noticing the mask of ACL
- 5.11 Limit the mask permission
- 5.12 Check for the implemention of mask permission
- 5.13 Clean up
- 6.1 Remove  created users
- 6.2 Remove created groups
- 6.3 Remove files and directory created
- 6.4 Remove ACLtest file

---

The practical exercises demonstrated how Linux determines whether a user can actually access, modify, execute, or delete resources.

---

**Author:** Dhruv Sudani

**Course:** Self-Learning Linux & Cybersecurity Homelab

**Module:** Linux Administration

**Lesson:** 6 – Advanced Linux Permissions & ACLs