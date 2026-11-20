# Linux User, Group & Access Management – Lesson 5

## Project Title

**Linux User, Group & Access Management**

---

# Objective

The objective of this lab was to understand how Linux manages users, groups, authentication information, and administrative privileges.

The lab focused on creating and modifying users, managing groups, understanding primary and supplementary groups, using `sudo` and `su`, and troubleshooting Linux access permissions.

---

# Lab Environment

| Component        | Details                    |
|------------------|----------------------------|
| Operating System | Ubuntu Linux               |
| Virtualization   | Oracle VirtualBox          |
| User Management  | Linux user/group utilities |
| Shell            | Bash                       |
| Environment      | Cybersecurity Homelab      |

---

# Prerequisites

Before starting this lesson, the following concepts were completed:

- Linux file permissions
- Numerical permissions
- Linux service management
- Linux package management
- Basic Linux commands
- Process management

---

# 1. Introduction to Linux User Management

Linux is a multi-user operating system.

Multiple users can have separate:

- User accounts
- User IDs
- Home directories
- Groups
- Permissions
- Authentication information
- Administrative privileges

The basic Linux access-control relationship is:

```text
User
  ↓
Group Membership
  ↓
Permissions
  ↓
Access
````

---

# 2. `/etc/passwd`

The `/etc/passwd` file contains important information about Linux user accounts.

A typical entry follows this structure:

```text
username:x:UID:GID:comment:home_directory:shell
```

Example:

```text
dhruv:x:1000:1000:Dhruv:/home/dhruv:/bin/bash
```

## Seven Fields

| Field | Meaning                  |
| ----- | ------------------------ |
| 1     | Username                 |
| 2     | Password placeholder     |
| 3     | UID                      |
| 4     | Primary GID              |
| 5     | User information/comment |
| 6     | Home directory           |
| 7     | Login shell              |

The `x` does not contain the password.

It indicates that password information is stored separately in:

```text
/etc/shadow
```

---

# 3. `/etc/shadow`

The `/etc/shadow` file stores sensitive authentication information.

Its structure is approximately:

```text
username:password:last_change:min:max:warn:inactive:expire:reserved
```

Important information includes:

* Password hash
* Last password change
* Minimum password age
* Maximum password age
* Password-expiry warning period
* Account expiration information

Example structure:

```text
username:$6$example_hash:20400:0:99999:7:::
```

## Security Consideration

Password hashes are sensitive information.

They should never be unnecessarily exposed, copied, or shared.

Modern Linux systems separate password information from `/etc/passwd` so that sensitive authentication data can have more restrictive access.

---

# 4. `/etc/group`

The `/etc/group` file stores Linux group information.

Typical structure:

```text
group_name:x:GID:members
```

Example:

```text
cybersecurity:x:1002:
```

Fields:

| Field | Meaning                     |
| ----- | --------------------------- |
| 1     | Group name                  |
| 2     | Group password placeholder  |
| 3     | GID                         |
| 4     | Supplementary group members |

---

# 5. Checking User Information

The following commands were used to inspect user accounts:

```bash
whoami
id
groups
```

## `whoami`

Displays the current username.

Example:

```bash
whoami
```

Output:

```text
dhruv
```

## `id`

Displays UID, GID, and group memberships.

Example:

```bash
id
```

Conceptually:

```text
uid=1000(dhruv)
gid=1000(dhruv)
groups=1000(dhruv),27(sudo)
```

## `groups`

Displays the groups associated with a user.

Example:

```bash
groups
```

---

# 6. Filtering User Information

A useful command practiced during the lab was:

```bash
grep "^$(whoami):" /etc/passwd
```

This combines:

* `grep`
* Regular-expression matching
* Command substitution
* `/etc/passwd` filtering

The expression:

```text
^$(whoami):
```

targets the exact line belonging to the current user.

This is a useful Linux administration technique for quickly locating account information.

---

# 7. Creating Users with `useradd`

`useradd` is a low-level Linux user-management command.

Basic example:

```bash
sudo useradd username
```

To explicitly create a home directory:

```bash
sudo useradd -m username
```

The `-m` option means:

```text
-m → Create home directory
```

---

# 8. Creating Users with `adduser`

Ubuntu also provides:

```bash
sudo adduser username
```

`adduser` is a more user-friendly interactive utility.

It can:

* Create the account
* Create the home directory
* Set a password
* Create/configure the user's group
* Ask for optional user information

Example:

```bash
sudo adduser labuser
```

---

# 9. Setting User Passwords

A user's password can be created or changed with:

```bash
sudo passwd username
```

Example:

```bash
sudo passwd labuser
```

The command prompts for:

```text
New password:
Retype new password:
```

Passwords should never be shared with others.

---

# 10. Investigating a Newly Created User

After creating `labuser`, the following commands were used:

```bash
grep "^labuser:" /etc/passwd
```

Check the home directory:

```bash
ls -ld /home/labuser
```

Check the group:

```bash
grep "^labuser:" /etc/group
```

This demonstrated how creating one Linux user affects multiple system components:

```text
                 adduser labuser
                       |
          +------------+------------+
          |            |            |
          ↓            ↓            ↓
   /etc/passwd   /etc/shadow   /etc/group
          |
          ↓
   /home/labuser
```

---

# 11. Modifying Users with `usermod`

The `usermod` command modifies existing user accounts.

General syntax:

```bash
sudo usermod [options] username
```

## Changing the Login Shell

Example:

```bash
sudo usermod -s /bin/sh labuser
```

The `-s` option specifies the login shell.

The shell was then restored:

```bash
sudo usermod -s /bin/bash labuser
```

Verification:

```bash
getent passwd labuser
```

---

# 12. Creating Groups

Groups allow multiple users to share access to resources.

A group was created using:

```bash
sudo groupadd cybersecurity
```

The group was verified with:

```bash
getent group cybersecurity
```

Example:

```text
cybersecurity:x:1002:
```

---

# 13. Adding Users to Groups

The following command was used to add `labuser` to the `cybersecurity` group:

```bash
sudo usermod -aG cybersecurity labuser
```

Important options:

```text
-a → Append
-G → Supplementary groups
```

Therefore:

```text
-aG → Add user to supplementary group
```

---

# 14. Removing Users from Groups

A user can be removed from a supplementary group using:

```bash
sudo gpasswd -d labuser cybersecurity
```

The membership was then restored using:

```bash
sudo usermod -aG cybersecurity labuser
```

Membership was verified using:

```bash
id labuser
```

and:

```bash
groups labuser
```

---

# 15. Primary vs Supplementary Groups

Linux users can belong to multiple groups.

### Primary Group

The user's main group.

Example:

```text
labuser
   ↓
Primary Group → labuser
```

### Supplementary Group

Additional groups that provide extra access.

Example:

```text
labuser
   ↓
Supplementary Group → cybersecurity
```

Conceptually:

```text
             labuser
                |
        +-------+-------+
        ↓               ↓
 Primary Group    Supplementary Group
    labuser          cybersecurity
```

A user normally has one primary group but can belong to multiple supplementary groups.

---

# 16. Primary Group and File Ownership

A practical experiment was performed by switching to `labuser`:

```bash
su - labuser
```

The user was verified with:

```bash
whoami
```

A test file was created:

```bash
touch ~/group_test.txt
```

Ownership was inspected:

```bash
ls -l ~/group_test.txt
```

The file's group ownership demonstrated the relationship between a user's **primary group** and newly created files.

The test file was removed afterward:

```bash
rm ~/group_test.txt
```

---

# 17. `su` vs `sudo`

Two important privilege-management commands were practiced.

## `su`

```bash
su - labuser
```

`su` means **switch user**.

It changes the current shell/session to another user's environment.

Example:

```text
Current user
     ↓
su - labuser
     ↓
labuser session
```

---

## `sudo`

```bash
sudo command
```

`sudo` allows an authorized user to execute a command with elevated privileges.

Example:

```bash
sudo whoami
```

Output:

```text
root
```

However, the user's original session does not permanently become root.

Example:

```text
whoami
   ↓
normal user

sudo whoami
   ↓
root

whoami
   ↓
normal user
```

---

# 18. Checking Sudo Privileges

The user's group memberships were checked using:

```bash
groups
```

The `sudo` group indicates administrative privileges on standard Ubuntu configurations.

The following command was also practiced:

```bash
sudo -l
```

`sudo -l` lists the commands the current user is permitted to run through `sudo`.

This is useful for auditing administrative privileges.

---

# 19. Principle of Least Privilege

A major cybersecurity concept connected to sudo is:

> **Users should receive only the permissions required to perform their tasks.**

Example:

```text
Normal User
    ↓
Limited permissions
    ↓
Reduced attack impact
```

versus:

```text
User
    ↓
Unnecessary administrative privileges
    ↓
Higher security risk
```

This principle is called:

```text
Principle of Least Privilege
```

---

# 20. Deleting Users

Users can be deleted using:

```bash
sudo userdel username
```

This removes the user account.

However, the user's home directory may remain.

---

## Deleting User + Home Directory

The following command was practiced for the temporary lab account:

```bash
sudo userdel -r labuser
```

The `-r` option removes the user's home directory and associated mail spool along with the account.

Conceptually:

```text
userdel
   ↓
Account removed

userdel -r
   ↓
Account + Home directory removed
```

Because `-r` can delete user files, it should be used carefully.

The account was verified afterward using:

```bash
id labuser
```

---

# 21. User & Group Troubleshooting

A practical access-control scenario was analyzed.

Example:

```text
Directory:
/cybersecurity_lab

Owner:
root

Group:
cybersecurity
```

If:

```text
intern → cybersecurity
```

then the group permissions become relevant.

For a directory:

```text
r → List contents
w → Create/delete/rename entries
x → Enter/traverse directory
```

The `x` permission is especially important because a user needs directory traversal permission to access objects inside the directory.

Example:

```text
drwxrwx---
   ↑
   Group has rwx
```

A member of the directory's group can therefore:

```text
Read       → List contents
Write      → Create/delete/rename
Execute    → Enter/traverse
```

---

# Most Important Commands from Lesson 5

```bash
whoami
id
groups
getent passwd USER
getent group GROUP
grep "^$(whoami):" /etc/passwd
sudo passwd USER
sudo useradd -m USER
sudo adduser USER
sudo usermod -s SHELL USER
sudo usermod -aG GROUP USER
sudo groupadd GROUP
sudo gpasswd -d USER GROUP
su - USER
sudo -l
sudo userdel USER
sudo userdel -r USER
```

---

# Key Concepts You Should Now Understand

### User & Identity

* **UID** → Unique User ID
* **GID** → Group ID
* `whoami` → Shows current user
* `id` → Shows UID, GID, and groups
* `groups` → Shows group membership

### Linux Account Files

* `/etc/passwd` → User account information
* `/etc/shadow` → Password hashes and password-aging information
* `/etc/group` → Group information

### User Management

* `useradd` → Basic/low-level user creation
* `adduser` → Interactive user creation
* `usermod` → Modify existing users
* `passwd` → Set/change passwords
* `userdel` → Delete users
* `userdel -r` → Delete users and their home directories

### Group Management

* `groupadd` → Create groups
* `usermod -aG` → Add users to supplementary groups
* `gpasswd -d` → Remove users from groups

### Access & Privileges

* **Primary group** → User's main group
* **Supplementary group** → Additional group membership
* `su` → Switch user
* `sudo` → Execute commands with elevated privileges
* `sudo -l` → Check sudo permissions
* **Least privilege** → Give users only the access they need

### Directory Permissions

```text
r → Read/list
w → Write/modify entries
x → Enter/traverse
```

---

# Cybersecurity Relevance

Linux user and group management is fundamental to:

* SOC operations
* Linux system administration
* Incident response
* Privilege management
* Access control
* Server security
* Cloud security
* Digital forensics
* Cybersecurity investigations

Understanding users, groups, permissions, and sudo privileges helps security professionals identify:

* Unauthorized accounts
* Excessive privileges
* Suspicious group membership
* Improper access
* Privilege-escalation opportunities
* Misconfigured permissions

---

# Practical Skills Demonstrated

During this lesson, the following practical skills were demonstrated:

* Inspecting Linux user accounts
* Reading `/etc/passwd`
* Understanding `/etc/shadow`
* Understanding `/etc/group`
* Creating Linux users
* Setting user passwords
* Creating user home directories
* Modifying user shells
* Creating Linux groups
* Adding users to groups
* Removing users from groups
* Understanding primary and supplementary groups
* Switching users with `su`
* Executing privileged commands with `sudo`
* Auditing sudo privileges
* Deleting user accounts
* Removing user home directories
* Troubleshooting group-based directory access

---

# Final Result

Successfully completed **Linux User, Group & Access Management – Lesson 5**.

The lab provided practical experience with Linux identity management, authentication information, group-based access control, privilege management, and user/group troubleshooting.

The concepts learned in this lesson provide the foundation for more advanced Linux security topics such as:

* Advanced file permissions
* ACLs
* SetUID
* SetGID
* Sticky bit
* Privilege escalation
* Linux security hardening
* Access-control auditing

---

# 📸 Screenshot Structure

The screenshots for this lab are available in the `Screenshots/` directory and are numbered in the order they were performed :

- 1. Who, whoami & id 
- 2.1 Show user information 
- 2.2 User spotted
- 3. Grep the user account from list (Advanced)
- 4. View the permissions of hashed passwords stored in shadow dir
- 5. Safely investigate your encrypted password
- 6.1 Create a user in interactive and convenient way
- 6.2 Check its id 
- 6.3 Check its basic info
- 7.1 Change the default shell of labuser
- 7.2 Undo the changes
- 8. Verify the group exists or not
- 9. Add labuser into cybersecurity group
- 10.1 Remove the user from group
- 10.2 Add user into group again
- 11. Set a temporary password for user
- 12.1 Switch to that user and create a file
- 12.2 Remove that file and go back to normal user 
- 13. Whoami
- 14. Check the sudo group and the commands that are allows to perform by sudo
- 15. Remove the user from scratch

---

# Lesson Summary

```text
Users
  ↓
UID
  ↓
Groups
  ↓
GID
  ↓
Permissions
  ↓
Access Control
  ↓
sudo
  ↓
Administrative Privileges
```

**Lesson 5 Status: COMPLETED ✅**

---

**Author:** Dhruv Sudani

**Course:** Self-Learning Linux & Cybersecurity Homelab

**Module:** Linux Administration

**Lesson:** 5 – User, Group & Access Management