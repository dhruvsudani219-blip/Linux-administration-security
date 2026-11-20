# Linux Numeric Permissions (chmod) – Lesson 2

## Project Title

**Understanding Linux Numeric Permissions and Access Control using chmod**

---

## Objective

The objective of this lab was to understand how Linux represents file permissions numerically and how the `chmod` command is used to modify file and directory permissions.

The lab also explored the relationship between symbolic (`rwx`) and numeric (octal) permission formats, enabling permission conversion in both directions.

---

## Lab Environment

| Component                     | Details           |
| ----------------------------- | ----------------- |
| Host Operating System         | Windows 11        |
| Virtualization Software       | Oracle VirtualBox |
| Guest Operating System        | Ubuntu Linux      |
| Linux Shell                   | Bash              |
| Permission Management Utility | chmod             |

---

## Prerequisites

Before performing this lab, the following concepts had already been covered:

* Linux File Permissions
* Owner, Group, and Others
* Read (`r`), Write (`w`), and Execute (`x`) permissions
* Symbolic permission notation (`rwx`)

---

# Lab Objectives

* Understand Linux numeric (octal) permissions.
* Learn how Linux calculates permission values.
* Convert symbolic permissions to numeric format.
* Convert numeric permissions back to symbolic format.
* Modify permissions using the `chmod` command.
* Understand common permission values used in Linux administration.

---

# Understanding Permission Values

Linux assigns a numeric value to each permission.

| Permission    | Symbol | Numeric Value |
| ------------- | ------ | ------------: |
| Read          | `r`    |             4 |
| Write         | `w`    |             2 |
| Execute       | `x`    |             1 |
| No Permission | `-`    |             0 |

Numeric permissions are calculated by adding the values of the permissions assigned to a user category.

---

# Permission Calculation Method

Linux evaluates permissions separately for:

* Owner
* Group
* Others

Each category is assigned a value between **0 and 7**.

### Example 1

Symbolic permission:

```text
rwx
```

Calculation:

```text
Read    = 4
Write   = 2
Execute = 1

Total = 4 + 2 + 1 = 7
```

Result:

```text
rwx = 7
```

---

### Example 2

```text
r-x
```

Calculation:

```text
Read    = 4
Write   = 0
Execute = 1

Total = 4 + 0 + 1 = 5
```

Result:

```text
r-x = 5
```

---

### Example 3

```text
rw-
```

Calculation:

```text
Read    = 4
Write   = 2
Execute = 0

Total = 4 + 2 + 0 = 6
```

Result:

```text
rw- = 6
```

---

### Example 4

```text
---
```

Calculation:

```text
Read    = 0
Write   = 0
Execute = 0

Total = 0
```

Result:

```text
--- = 0
```

---

# Converting Symbolic Permissions to Numeric

Example:

```text
-rwxr-xr--
```

Split into three permission groups:

| Category | Symbolic | Calculation | Numeric |
| -------- | -------- | ----------- | ------: |
| Owner    | `rwx`    | 4 + 2 + 1   |       7 |
| Group    | `r-x`    | 4 + 0 + 1   |       5 |
| Others   | `r--`    | 4 + 0 + 0   |       4 |

Equivalent numeric permission:

```text
754
```

---

# Converting Numeric Permissions to Symbolic

Example:

```text
640
```

Breakdown:

| Category | Numeric | Symbolic |
| -------- | ------: | -------- |
| Owner    |       6 | `rw-`    |
| Group    |       4 | `r--`    |
| Others   |       0 | `---`    |

Equivalent permission:

```text
-rw-r-----
```

---

# Common Permission Values

| Numeric | Symbolic    | Common Usage                              |
| ------- | ----------- | ----------------------------------------- |
| 755     | `rwxr-xr-x` | Scripts, executable programs, directories |
| 750     | `rwxr-x---` | Private shared directories                |
| 700     | `rwx------` | Private directories (e.g., `.ssh`)        |
| 644     | `rw-r--r--` | Text files, configuration files           |
| 640     | `rw-r-----` | Restricted configuration files            |
| 600     | `rw-------` | Private SSH keys, sensitive files         |

---

# Practical Commands Executed

Create a practice file:

```bash
touch permission_test.txt
```

View current permissions:

```bash
ls -l permission_test.txt
```

Assign numeric permissions:

```bash
chmod 640 permission_test.txt
```

Verify changes:

```bash
ls -l permission_test.txt
```

Modify permissions using symbolic notation:

```bash
chmod o+w permission_test.txt
```

Remove write permission:

```bash
chmod o-w permission_test.txt
```

Display file contents:

```bash
cat permission_test.txt
```

---

# Real-World Cybersecurity Relevance

Linux permissions play a critical role in securing systems and preventing unauthorized access.

Examples include:

* Private SSH keys should normally have permissions of **600** to ensure that only the owner can read and modify the key.
* The `.ssh` directory commonly uses **700** to restrict access exclusively to its owner.
* Executable scripts frequently use **755**, allowing execution while preventing unauthorized modification by other users.
* Incorrect permissions such as **777** (`rwxrwxrwx`) may expose sensitive files or allow unauthorized modification and execution, making them a potential security risk.

Understanding these permission models is essential for Linux administration, system hardening, incident response, and cybersecurity operations.

---

# Concepts Learned

* Numeric (Octal) Permissions
* Symbolic Permission Notation
* Permission Calculation
* Owner, Group, and Others
* chmod
* Linux Access Control
* Permission Conversion
* Secure File Permissions

---

# Skills Practiced

* Linux Permission Management
* chmod Administration
* Symbolic-to-Numeric Conversion
* Numeric-to-Symbolic Conversion
* Linux Security Fundamentals
* File Access Control
* Cybersecurity Best Practices

---

# Result

Successfully learned how Linux represents permissions using octal values, converted permissions between symbolic and numeric formats, modified file permissions using the `chmod` command, and understood common permission values used in Linux system administration and cybersecurity.

---

# Future Improvements

* Learn file ownership using `chown`
* Understand Linux groups and group permissions
* Explore Special Permissions (SUID, SGID, Sticky Bit)
* Apply permission management to SSH authentication
* Configure secure permissions for SSH keys and directories

---

## 📸 Screenshots

The screenshots for this lab are available in the `Screenshots/` directory and are numbered in the order they were performed:

- 1. Create a file and View its permissions
- 2. Change permission by using chmod with numerical (Octal) way
- 3. Add write permission to Others
- 4. Revoke write permission from Others
- 5. Set default permissions back
 
--- 

**Author:** Dhruv Sudani

**Course:** Self-Learning Linux & Cybersecurity Homelab

**Module:** Linux Administration

**Lesson:** 2 – Linux Numeric Permissions (chmod)
