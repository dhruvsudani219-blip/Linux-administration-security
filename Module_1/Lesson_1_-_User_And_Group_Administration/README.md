# Linux Administration — Lesson 1

---

## User & Group Administration

**Module:** Linux Administration  
**Lesson:** 1  
**Topic:** User & Group Administration  
**Operating System:** Ubuntu Linux  
**Environment:** Ubuntu Virtual Machine 

---

## 🎯 Objective

The objective of this lesson was to understand how Linux manages users, groups, UIDs, GIDs, file ownership, and group-based permissions.

The practical session focused on:

- Understanding Linux users
- Understanding UID and GID
- Understanding primary and supplementary groups
- Creating and managing groups
- Adding users to supplementary groups
- Understanding file ownership
- Understanding group-based permissions
- Understanding directory traversal permissions
- Testing user access to files
- Troubleshooting `Permission denied`
- Temporarily modifying and restoring permissions

---

# 1. Linux Users

A Linux user represents an account that can interact with the operating system.

Examples:

```text
root
dhruv
intern
```

Each user has information such as:

- Username
- UID
- Primary GID
- Home directory
- Login shell
- Authentication information

Linux internally uses numerical identifiers such as UID and GID to identify users and groups.

---

# 2. UID — User ID

**UID** stands for **User ID**.

It is a numerical identifier assigned to a Linux user.

The following command was used:

```bash
id intern
```

Example:

```text
uid=1001(intern)
```

This means:

```text
Username → intern
UID      → 1001
```

The exact UID may differ between systems.

---

# 3. GID — Group ID

**GID** stands for **Group ID**.

It is a numerical identifier assigned to a Linux group.

Example:

```text
gid=1002(intern)
```

This means:

```text
Group Name → intern
GID        → 1002
```

The exact GID may differ between systems.

---

# 4. Primary Group

Every Linux user has one **primary group**.

The primary group is identified by the user's primary GID.

In this practical lab, the `intern` user had:

```text
User: intern
Primary Group: intern
```

A same-named group is common with Ubuntu's user/group configuration, but Linux does not universally require the primary group to have the same name as the user.

---

# 5. Supplementary Groups

A Linux user can also belong to additional groups called **supplementary groups**.

In this lab, the `intern` user was a member of:

```text
Primary Group:
intern

Supplementary Group:
cybersecurity
```

Therefore:

```text
intern
├── Primary Group → intern
└── Supplementary Group → cybersecurity
```

Supplementary groups are useful for managing access to shared resources.

---

# 6. Checking User Information

The following command was used:

```bash
id intern
```

Example:

```text
uid=1001(intern) gid=1002(intern) groups=1002(intern),1003(cybersecurity)
```

This output provides:

- UID
- Primary GID
- Supplementary group memberships

Another command used to check group membership was:

```bash
groups intern
```

Example:

```text
intern : intern cybersecurity
```

This confirmed that `intern` belonged to both the `intern` and `cybersecurity` groups.

---

# 7. Creating the `cybersecurity` Group

First, the group was checked using:

```bash
getent group cybersecurity
```

Initially, the command returned no output, indicating that the group was not present.

The group was then created using:

```bash
sudo groupadd cybersecurity
```

The group was verified using:

```bash
getent group cybersecurity
```

Example:

```text
cybersecurity:x:1003:
```

The GID may differ depending on the system.

---

# 8. Adding `intern` to the `cybersecurity` Group

The `intern` user was added to the supplementary `cybersecurity` group using:

```bash
sudo usermod -aG cybersecurity intern
```

### Command Breakdown

```text
sudo
→ Execute with administrative privileges

usermod
→ Modify an existing user

-a
→ Append the new group without removing existing supplementary groups

-G
→ Specify supplementary group membership

cybersecurity
→ Group being added

intern
→ User being modified
```

The `-aG` combination is important because using `-G` without `-a` can replace the user's existing supplementary group memberships.

---

# 9. Linux File Permissions

Linux permissions are divided into three categories:

```text
Owner
Group
Others
```

The basic permission types are:

```text
r = Read
w = Write
x = Execute
```

Example:

```text
-rwxr-x---
```

Breakdown:

```text
Owner  → rwx
Group  → r-x
Others → ---
```

---

# 10. Numerical Permissions

Linux permissions can also be represented numerically.

The values are:

| Permission    | Value |
|---------------|------:|
| Read (`r`)    |   4   |
| Write (`w`)   |   2   |
| Execute (`x`) |   1   |

Therefore:

```text
rwx = 4 + 2 + 1 = 7
rw- = 4 + 2     = 6
r-x = 4 + 1     = 5
r-- = 4         = 4
-w- = 2         = 2
--x = 1         = 1
--- = 0         = 0
```

For example:

```text
640
```

means:

```text
Owner  → 6 → rw-
Group  → 4 → r--
Others → 0 → ---
```

Therefore:

```text
640 = -rw-r-----
```

---

# 11. Practical Lab — Group-Based Access Control

A controlled directory was created for the experiment:

```bash
mkdir ~/cybersecurity_lab
```

The directory's group ownership was changed to `cybersecurity`:

```bash
sudo chown dhruv:cybersecurity ~/cybersecurity_lab
```

The directory permissions were configured as:

```bash
chmod 770 ~/cybersecurity_lab
```

This produced:

```text
Owner  → rwx
Group  → rwx
Others → ---
```

Therefore, members of the `cybersecurity` group could access the directory.

---

# 12. Creating the Test File

A test file was created inside the directory:

```bash
touch ~/cybersecurity_lab/report.txt
```

The group ownership was changed:

```bash
sudo chown dhruv:cybersecurity ~/cybersecurity_lab/report.txt
```

The file permissions were configured as:

```bash
chmod 640 ~/cybersecurity_lab/report.txt
```

The resulting permission was:

```text
-rw-r-----
```

The ownership and permissions were therefore:

```text
Owner  → dhruv
Group  → cybersecurity
Others → No permissions

Permission → 640
```

---

# 13. Access Prediction

The `intern` user belonged to:

```text
intern
cybersecurity
```

The file was owned by:

```text
dhruv:cybersecurity
```

and had:

```text
640
```

Therefore, Linux evaluated `intern` using the **group permission**.

The group permission was:

```text
r--
```

Therefore:

| Operation | Result      |
|-----------|-------------|
| Read      | Allowed     |
| Write     | Denied      |
| Execute   | Denied      |

---

# 14. Testing Read Access

After switching to the `intern` user, the following command was executed:

```bash
cat /home/dhruv/cybersecurity_lab/report.txt
```

The command executed successfully.

Nothing appeared on the terminal because the file was empty.

This confirmed that:

```text
Read → ✅ Allowed
```

---

# 15. Testing Write Access

The following command was executed as `intern`:

```bash
echo "Intern was here" >> /home/dhruv/cybersecurity_lab/report.txt
```

The result was:

```text
Permission denied
```

This was expected because the group permission was:

```text
r--
```

There was no `w` permission.

Therefore:

```text
Write → ❌ Denied
```

---

# 16. Testing Execute Access

The file had:

```text
-rw-r-----
```

The group did not have execute permission.

Therefore:

```text
Execute → ❌ Denied
```

This demonstrated that group membership does not automatically provide complete access.

The actual permissions assigned to the group determine what the user can perform.

---

# 17. Troubleshooting — Permission Denied

During the practical experiment, `intern` initially received:

```text
Permission denied
```

even though:

```text
intern ∈ cybersecurity
```

and:

```text
report.txt
Owner  → dhruv
Group  → cybersecurity
Permission → 640
```

were correctly configured.

The problem was therefore not the file's permission.

---

# 18. Investigating the Complete Path

The complete path was:

```text
/home/dhruv/cybersecurity_lab/report.txt
```

The following command was used to inspect every component of the path:

```bash
namei -l /home/dhruv/cybersecurity_lab/report.txt
```

The investigation showed that the problem was related to:

```text
/home/dhruv
```

The `intern` user could not properly traverse this directory.

---

# 19. Directory Execute Permission

Directory permissions have an important difference from regular file permissions.

| Permission |                      Directory Meaning                      |
|------------|-------------------------------------------------------------|
| `r`        | List directory contents                                     |
| `w`        | Create/delete/rename entries, subject to other restrictions |
| `x`        | Enter/traverse the directory                                |

The important permission in this situation was:

```text
x
```

Without execute permission on a directory, a user cannot traverse through it to reach a file or subdirectory inside it.

Therefore, even though:

```text
report.txt
```

had the correct group permission, the user could not reach the file because the parent directory blocked traversal.

---

# 20. Temporary Permission Modification

Because this was a controlled Ubuntu VM laboratory environment, execute permission was temporarily added for Others on `/home/dhruv`:

```bash
sudo chmod o+x /home/dhruv
```

Meaning:

```text
o
→ Others

+x
→ Add execute permission
```

This did not give Others read or write permission.

It only allowed traversal through the directory.

After this temporary change, the `intern` user was able to successfully execute:

```bash
cat /home/dhruv/cybersecurity_lab/report.txt
```

---

# 21. Restoring the Original Permission

After completing the experiment, the temporary permission was revoked:

```bash
sudo chmod o-x /home/dhruv
```

This restored the previous permission state.

This demonstrated an important Linux administration principle:

> Make the minimum required temporary change, perform the required operation, and restore the original configuration after testing.

---

# 22. What Caused the Permission Denied?

The complete access path can be represented as:

```text
intern
   ↓
Member of cybersecurity
   ↓
/home
   ↓
/home/dhruv
   ↓
cybersecurity_lab
   ↓
report.txt
```

The problem occurred at:

```text
/home/dhruv
```

because the user did not have the required directory traversal permission.

Therefore:

```text
Correct File Permission
        +
Correct Group Membership
        +
Blocked Parent Directory
        =
Permission Denied
```

---

# 23. Core Concept Learned

The practical session demonstrated that Linux access control is not determined only by the final file.

The complete process can be represented as:

```text
USER
  ↓
GROUP MEMBERSHIP
  ↓
DIRECTORY TRAVERSAL
  ↓
FILE OWNERSHIP
  ↓
PERMISSION CLASS
  ↓
ACCESS
```

A user can have the correct permission on a file and still receive:

```text
Permission denied
```

if the user cannot traverse one of the directories in the file's path.

---

# 24. Security Relevance

User and group administration is fundamental to Linux security.

Proper user and group management helps administrators implement:

- Least privilege
- Group-based access control
- Role-based access
- Resource isolation
- Controlled access to sensitive files
- Reduced unauthorized modification
- Easier management of multiple users

These concepts are important for:

- Linux Administration
- Cybersecurity
- SOC Operations
- System Administration
- Digital Forensics
- Incident Response
- Server Security

---

# 25. Commands Practiced

```bash
id
id intern
groups intern

getent group cybersecurity

sudo groupadd cybersecurity

sudo usermod -aG cybersecurity intern

mkdir ~/cybersecurity_lab

sudo chown dhruv:cybersecurity ~/cybersecurity_lab

chmod 770 ~/cybersecurity_lab

touch ~/cybersecurity_lab/report.txt

sudo chown dhruv:cybersecurity ~/cybersecurity_lab/report.txt

chmod 640 ~/cybersecurity_lab/report.txt

ls -l ~/cybersecurity_lab/report.txt

ls -ld ~/cybersecurity_lab

namei -l /home/dhruv/cybersecurity_lab/report.txt

cat /home/dhruv/cybersecurity_lab/report.txt

sudo chmod o+x /home/dhruv

sudo chmod o-x /home/dhruv
```

---

# 26. Final Challenge

### Scenario

The `intern` account is a member of:

```text
intern
cybersecurity
```

The file is:

```text
Owner → dhruv
Group → cybersecurity
Permission → 640
```

### Answers

**1. Can `intern` read the file?**

Yes.

The group has:

```text
r--
```

permission.

---

**2. Can `intern` modify the file?**

No.

The group does not have:

```text
w
```

permission.

---

**3. Can `intern` execute the file?**

No.

The group does not have:

```text
x
```

permission.

---

**4. Why was `intern` initially receiving `Permission denied`?**

Although the file permissions and group membership were correct, `intern` could not traverse the `/home/dhruv` directory in the file's absolute path.

Therefore, Linux prevented the user from reaching the destination file.

---

**5. What temporary permission was given to `/home/dhruv`?**

Execute permission was temporarily given to Others:

```bash
sudo chmod o+x /home/dhruv
```

After the experiment, it was revoked:

```bash
sudo chmod o-x /home/dhruv
```

---

# 27. Skills Demonstrated

- Linux user identification
- UID interpretation
- GID interpretation
- Primary group understanding
- Supplementary group understanding
- Group creation
- User-group management
- File ownership
- Group-based access control
- Numerical permissions
- Directory permissions
- Directory traversal
- Permission troubleshooting
- `namei` path analysis
- Temporary permission modification
- Permission restoration

---

# 📸 Screenshot Structure

The screenshots for this lab are available in the `Screenshots/` directory and are numbered in the order they were performed :

- 1. 1.UID_&_GID
	- 1. Check current user
	- 2. Check user ID
	- 3. See all users
	- 4. Understand etc_passwd ( Find your account )
	-  5. Check for groups
	- 6. Find the ID & Group of  user intern

- 2. Create_&_Manage_Groups_+_Permissions
	- 7. Check the existence of  specific group
	- 8. create a group
	- 9. Verify the group creation 
	-  10. Add intern user into cybersecurity group
	- 11. Vefiry the membership
	- 12. Check the ID of intern after adding it into a group
	- 13. Create a temporary dir named cybersecurity_lab and set the group as cybersecurity
	- 14. Create a file inside that dir and add it to cybersecurity group
	- 15. Change its permission to 640
	- 16. Temporarily Modify the permission of current user directory
	- 17. Execute cat command to check read permission
	- 18. Attempt to write in file 
	-   19. Verify if there is any content in files as the permission to write was denied for intern user
	- 20. Revoke the temporary permission of excution from current user's directory

---

# Final Takeaway

This lesson established the fundamentals of Linux **User and Group Administration**.

The practical experiment demonstrated how:

```text
USER
  ↓
GROUP MEMBERSHIP
  ↓
DIRECTORY TRAVERSAL
  ↓
FILE OWNERSHIP
  ↓
PERMISSION CLASS
  ↓
ACCESS
```

work together to determine whether a user can access a resource.

The most important lesson from the troubleshooting process was:

> **Having the correct permission on a file is not enough. The user must also be able to traverse every required directory in the file's path.**

This lesson provides a foundation for further Linux Administration topics such as:

- Process Management
- Package Management
- Services and `systemctl`
- Linux Logs
- Disk Management
- Cron Jobs
- Bash Scripting
- Linux Networking
- Linux Security

---

# Lesson Status

## LESSON 1 COMPLETED

**Linux Administration — User & Group Administration**

**Learn → Practice → Troubleshoot → Understand → Document**

---

# Lesson Summary

| Field                 | Details                       |
|-----------------------|-------------------------------|
| **Module**            | Linux Administration          |
| **Lesson**            | 1                             |
| **Lesson Title**      | User & Group Administration   |
| **Difficulty**        | Beginner                      |
| **Environment**       | Ubuntu Linux Virtual Machine  |
| **Practical Status**  | Completed                     |

---

### Author

**Author:** Dhruv Sudani

**Course:** Self-Learning Linux & Cybersecurity Homelab

**Module:** Linux Administration

**Lesson:** 1 – Uer & Group Administration

---

> *"In Linux, access is determined not only by the destination file, but by every directory that leads to it."*

```