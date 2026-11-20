# Package Management & Software Installation – Lesson 4

## Project Title

**Linux Package Management and Software Installation Using APT and DPKG**

---

# Objective

The objective of this lab was to understand and practically use Ubuntu's package management system.

The lab focused on:

- Understanding Linux packages
- Using APT for package management
- Updating package repositories
- Upgrading installed packages
- Installing and removing software
- Understanding `remove` vs `purge`
- Understanding `autoremove`
- Searching and inspecting packages
- Understanding Ubuntu repositories
- Working with `.deb` packages
- Using `dpkg`
- Understanding package dependencies
- Troubleshooting broken packages

---

# Lab Environment

| Component               | Details                 |
|-------------------------|-------------------------| 
| Host Operating System   | Windows 11              |
| Server/Client Machine   | Ubuntu Linux VM         |
| Virtualization Platform | Oracle VirtualBox       |
| Ubuntu Version          | Ubuntu 24.04 LTS        |
| Package Manager         | APT                     |
| Low-Level Package Tool  | DPKG                    |
| Repository              | Ubuntu APT Repositories |
| Network Mode            | Host-Only Adapter       |

---

# Prerequisites

Before starting this lesson, the following topics were completed:

- Linux fundamentals
- Linux users and permissions
- SSH fundamentals
- SSH key authentication
- Linux service management
- `systemctl`
- `journalctl`
- Process management
- Basic Linux command-line usage

---

# 1. What is a Linux Package?

A package is a collection of files required to install a software application on Linux.

Ubuntu primarily uses `.deb` packages.

Examples:

```text
openssh-server
nginx
htop
tree
python3
cowsay
````

A package may contain:

* Program files
* Configuration files
* Documentation
* Dependencies
* Metadata

---

# 2. What is a Package Manager?

A package manager is software used to:

* Install packages
* Remove packages
* Upgrade packages
* Search for software
* Resolve dependencies
* Obtain software from repositories

Ubuntu uses:

```text
APT
```

APT provides a high-level interface for package management.

---

# 3. APT Version

The first practical command used was:

```bash
apt --version
```

Observed APT version:

```text
2.8.3
```

This confirmed that APT was installed and available on the Ubuntu system.

---

# 4. Updating Package Information

Command:

```bash
sudo apt update
```

This command was executed successfully.

## Purpose

`apt update` refreshes the local package information from configured repositories.

It does **not** upgrade installed packages.

The command allows APT to learn:

* Which packages are available
* Which versions are available
* Which updates exist
* Which security updates are available

---

# Understanding `sudo`

The `sudo` command provides administrative privileges.

Example:

```bash
sudo apt update
```

APT modifies package information stored in protected system locations, so administrative privileges are normally required.

---

# Understanding APT Update Output

During `apt update`, output included terms such as:

```text
Hit
Get
Fetched
```

## `Hit`

Indicates that repository information was already available/current and did not need to be downloaded again.

## `Get`

Indicates that APT downloaded repository information.

## `Fetched`

Shows the amount of repository data downloaded.

---

# 5. Checking Upgradable Packages

Command:

```bash
apt list --upgradable
```

The system initially reported:

```text
16 packages
```

were available for upgrade.

Example repository label:

```text
noble-updates
```

`noble` is the codename associated with Ubuntu 24.04 LTS.

`noble-updates` contains updates for the Ubuntu release.

At this stage, the packages were only **available for upgrade**.

They had not yet been upgraded.

---

# 6. Upgrading Packages

Command:

```bash
sudo apt upgrade
```

APT displayed the packages that were going to be upgraded.

The practical output included:

```text
58 upgraded
0 newly installed
0 to remove
0 not upgraded
```

It also showed:

```text
42 standard LTS security updates
```

The download requirement was approximately:

```text
131 MB
```

APT also estimated:

```text
2,040 kB of additional disk space
```

After reviewing the changes, the upgrade was confirmed.

---

# Understanding APT Upgrade Output

## `58 upgraded`

58 existing packages would be upgraded to newer versions.

## `0 newly installed`

No additional packages would be installed.

## `0 to remove`

No packages would be removed.

## `131 MB of archives`

Approximately 131 MB of package archives needed to be downloaded.

This is the **download size**, not the permanent disk space used by the upgrade.

## `2,040 kB additional disk space`

APT estimated that the upgraded packages would require approximately 2 MB of additional disk space after installation.

---

# 7. Verifying Available Updates

Command:

```bash
apt list --upgradable
```

After the upgrade completed, the output was:

```text
Listing... Done
```

with no packages listed.

This confirmed:

```text
No packages currently waiting for upgrade
```

---

# 8. Installing Packages

The next practical task was installing software.

The package `htop` was tested.

Command:

```bash
sudo apt install htop
```

APT reported that:

```text
htop is already the newest version
```

Therefore:

```text
0 upgraded
0 newly installed
0 to remove
```

This demonstrated that APT checks the current package state before making changes.

---

# 9. Installing `cowsay`

A second package was used for practical installation testing:

```text
cowsay
```

The package was installed successfully.

The installation was then tested using:

```bash
cowsay "APT works!"
```

The command successfully displayed the cowsay ASCII output.

This confirmed that:

```text
Package downloaded
        ↓
Package installed
        ↓
Program available
        ↓
Program executed successfully
```

---

# 10. Removing a Package

Command:

```bash
sudo apt remove cowsay
```

The package was successfully removed.

The installation was then verified.

`cowsay` was no longer available as an installed program.

This demonstrated that:

```text
apt remove
     ↓
Removes the package/software
```

However, package-managed configuration files may remain.

---

# 11. `apt remove` vs `apt purge`

The difference between the two commands was studied practically.

## `apt remove`

```bash
sudo apt remove <package>
```

Removes the software package.

Package-managed configuration files may remain.

Mental model:

```text
apt remove
     ↓
Software ❌
Configuration ⚠️ may remain
```

---

## `apt purge`

```bash
sudo apt purge <package>
```

Removes the software package and its package-managed configuration files.

Mental model:

```text
apt purge
     ↓
Software ❌
Configuration ❌
```

The `cowsay` package was subsequently purged after it had already been removed.

---

# 12. `apt autoremove`

Command:

```bash
sudo apt autoremove
```

The purpose of this command is to remove packages that were automatically installed as dependencies and are no longer considered necessary.

Example dependency relationship:

```text
Application
     │
 ┌───┴───┐
 ↓       ↓
Dep A   Dep B
         ↓
        Dep C
```

If the original application is removed and those dependencies are no longer required, APT may identify them as candidates for automatic removal.

---

# Important `autoremove` Incident

During the practical lab, `apt autoremove` proposed a very large removal operation.

APT reported approximately:

```text
223 packages will be removed
696 MB of disk space will be freed
```

The proposed removal list included significant packages and components such as:

```text
python3.10
python3.10-dev
python3.10-minimal
g++-11
samba
nginx modules
LibreOffice components
GNOME/GTK libraries
development libraries
```

Because the proposed removal was unusually large, the operation was **not confirmed**.

The command was cancelled using:

```text
n
```

---

# Why the `autoremove` Incident Was Important

`apt autoremove` does not simply mean:

```text
"Remove the package I just uninstalled."
```

Instead, it means:

```text
Find automatically installed packages
that APT currently considers no longer required.
```

Therefore, an administrator should always inspect the proposed removal list before confirming.

Important rule:

> **Never blindly confirm a large `apt autoremove` operation.**

---

# 13. Investigating Automatically Installed Packages

Command:

```bash
apt-mark showauto
```

This displays packages that APT considers automatically installed.

For a shorter output:

```bash
apt-mark showauto | head -20
```

This command was executed successfully.

---

# 14. Searching for Packages

Command:

```bash
apt search tree
```

This was used to search the configured repositories for packages related to `tree`.

APT search is useful when the exact package name is unknown.

Mental model:

```text
apt search <keyword>
        ↓
Find available packages
```

---

# 15. Viewing Package Information

Command:

```bash
apt show tree
```

This command was executed successfully.

`apt show` provides detailed information about a package.

Information may include:

* Package name
* Version
* Architecture
* Repository
* Dependencies
* Package size
* Description

Mental model:

```text
apt search
     ↓
Find the package

apt show
     ↓
Investigate the package
```

---

# 16. Listing Installed Packages

Command:

```bash
apt list --installed
```

This displays packages currently installed on the Ubuntu system.

Because Ubuntu contains many packages, the output can be very large.

A specific package can be searched using:

```bash
apt list --installed | grep openssh
```

This was used to investigate installed OpenSSH-related packages.

---

# 17. Ubuntu Repositories

APT obtains packages and package information from repositories.

Repository concept:

```text
Ubuntu VM
    │
    │ APT
    ↓
Ubuntu Repository
    │
    ├── openssh-server
    ├── nginx
    ├── htop
    ├── tree
    ├── python3
    └── thousands of other packages
```

Repositories provide:

* Packages
* Package versions
* Metadata
* Updates
* Security updates

---

# 18. Repository Configuration

The repository configuration was inspected using:

```bash
cat /etc/apt/sources.list.d/ubuntu.sources
```

This displayed the configured Ubuntu repository information.

The configuration included concepts such as:

```text
Types: deb
URIs: ...
Suites: noble
Components: main restricted universe multiverse
```

Ubuntu 24.04 uses the `.sources` format for repository configuration on the system.

---

# 19. Ubuntu Repository Components

Common Ubuntu repository components include:

```text
main
restricted
universe
multiverse
```

They provide different categories of software maintained or distributed through Ubuntu's repository system.

---

# 20. `.deb` Packages

Ubuntu uses the Debian package format:

```text
.deb
```

A `.deb` file contains the files and metadata required for a Debian/Ubuntu package.

APT commonly obtains `.deb` packages from repositories.

Concept:

```text
APT
 ↓
Repository
 ↓
.deb package
 ↓
Installation
```

---

# 21. APT vs DPKG

APT and DPKG were compared.

| APT                                    | DPKG                                                        |
| -------------------------------------- | ----------------------------------------------------------- |
| High-level package management tool     | Low-level package management tool                           |
| Works with repositories                | Works directly with `.deb` packages                         |
| Can download packages                  | Requires package file when installing directly              |
| Handles dependencies                   | Does not automatically resolve dependencies in the same way |
| Used for normal package administration | Used for direct package operations and inspection           |

Example APT installation:

```bash
sudo apt install <package>
```

Example DPKG installation of a local `.deb` file:

```bash
sudo dpkg -i package.deb
```

---

# 22. Checking Package Status with DPKG

Command:

```bash
dpkg -s openssh-server
```

This displays detailed status information about the installed package.

Important information includes:

```text
Status: install ok installed
```

This indicates that the package is installed correctly.

---

# 23. Listing Package Files with DPKG

Command:

```bash
dpkg -L openssh-server
```

This lists files installed by the package.

This is useful for administrators who need to determine:

> Which files were installed by this package?

---

# 24. Package Dependencies

Packages often depend on other packages.

Example:

```text
Application
     │
     ├── Dependency A
     │
     ├── Dependency B
     │
     └── Dependency C
```

If required dependencies are missing or incorrectly configured, package installation or configuration may fail.

This is called a:

```text
Dependency problem
```

---

# 25. Checking Dependencies

Command:

```bash
apt-cache depends <package>
```

Example:

```bash
apt-cache depends python3.10
```

This displays packages that the selected package depends on.

---

# 26. Checking Reverse Dependencies

Command:

```bash
apt-cache rdepends <package>
```

Example:

```bash
apt-cache rdepends python3.10
```

Reverse dependencies answer:

> Which packages depend on this package?

This can help determine whether removing a package could affect other software.

---

# 27. Troubleshooting Broken Packages

Command:

```bash
sudo apt --fix-broken install
```

Short form:

```bash
sudo apt -f install
```

These commands attempt to repair broken package dependencies and incomplete package configurations.

---

# Practical Result

The command:

```bash
sudo apt --fix-broken install
```

was executed successfully.

No broken dependencies were found.

Therefore:

```text
Nothing was broken
        ↓
Nothing required fixing
```

This confirmed that the Ubuntu package system was healthy.

---

# 28. Package Management Troubleshooting Workflow

A practical Linux administrator workflow can be represented as:

```text
       Package Problem
             │
             ↓
       Search package
             │
             ↓
       Inspect package
             │
             ↓
       Install/Upgrade
             │
             ↓
          Verify
             │
             ↓
      Something wrong?
        ┌────┴────┐
       Yes        No
        │          │
        ↓          ↓
Investigate      Done
dependencies
        │
        ↓
apt --fix-broken install
```

---

# 29. Commands Practiced

## APT Information

```bash
apt --version
```

## Update Repository Information

```bash
sudo apt update
```

## List Upgradable Packages

```bash
apt list --upgradable
```

## Upgrade Packages

```bash
sudo apt upgrade
```

## Install Package

```bash
sudo apt install <package>
```

Example:

```bash
sudo apt install cowsay
```

## Remove Package

```bash
sudo apt remove <package>
```

## Purge Package

```bash
sudo apt purge <package>
```

## Remove Unused Dependencies

```bash
sudo apt autoremove
```

## Search Packages

```bash
apt search <keyword>
```

## Show Package Information

```bash
apt show <package>
```

## List Installed Packages

```bash
apt list --installed
```

## Check Automatically Installed Packages

```bash
apt-mark showauto
```

## Inspect Repository Configuration

```bash
cat /etc/apt/sources.list.d/ubuntu.sources
```

## DPKG Package Status

```bash
dpkg -s <package>
```

## DPKG Package Files

```bash
dpkg -L <package>
```

## Check Dependencies

```bash
apt-cache depends <package>
```

## Check Reverse Dependencies

```bash
apt-cache rdepends <package>
```

## Repair Broken Dependencies

```bash
sudo apt --fix-broken install
```

Alternative:

```bash
sudo apt -f install
```

## Install Local `.deb`

```bash
sudo dpkg -i package.deb
```

---

# 30. Important Concepts Learned

### `apt update`

Refreshes package information.

```text
Does NOT upgrade packages.
```

### `apt upgrade`

Upgrades installed packages to newer available versions.

### `apt install`

Installs a package.

### `apt remove`

Removes a package while package-managed configuration may remain.

### `apt purge`

Removes the package and its package-managed configuration.

### `apt autoremove`

Removes automatically installed dependencies that APT considers no longer required.

### `apt search`

Searches available packages.

### `apt show`

Displays detailed package information.

### `dpkg -s`

Displays package status/details.

### `dpkg -L`

Lists files installed by a package.

### `apt --fix-broken install`

Attempts to repair broken package dependencies/configuration.

---

# 31. Security and Linux Administration Relevance

Package management is an important Linux administration and cybersecurity skill.

Security teams need package management for:

* Security updates
* Vulnerability remediation
* Server maintenance
* Software installation
* Incident response
* Malware investigation
* Dependency analysis
* System hardening

For example, if a vulnerable version of a package is discovered, an administrator can investigate:

```bash
apt show <package>
```

Check available updates:

```bash
apt list --upgradable
```

Then upgrade:

```bash
sudo apt upgrade
```

Package information can also help during incident response when investigating installed software.

---

# 32. Administrator Best Practices

### 1. Update package information regularly

```bash
sudo apt update
```

### 2. Review upgrades before confirming

```bash
sudo apt upgrade
```

### 3. Do not blindly use `autoremove`

Always inspect the proposed removal list.

### 4. Inspect unfamiliar packages

```bash
apt show <package>
```

### 5. Verify installations

```bash
dpkg -s <package>
```

### 6. Understand dependencies

```bash
apt-cache depends <package>
```

### 7. Be careful with `purge`

Purging can remove configuration files that may be useful for future reinstallation.

---

# 33. Practical Package Management Workflow

A recommended workflow learned during this lesson:

```text
Search
  ↓
Inspect
  ↓
Install / Upgrade
  ↓
Verify
  ↓
Troubleshoot if necessary
  ↓
Clean unused dependencies carefully
```

Example:

```bash
apt search <package>
apt show <package>
sudo apt install <package>
dpkg -s <package>
```

If a dependency problem occurs:

```bash
sudo apt --fix-broken install
```

---

# 34. Lesson 4 Final Result

Successfully completed the practical Linux Package Management & Software Installation lesson.

The following were successfully practiced:

* APT version checking
* Repository updates
* Package upgrades
* Package installation
* Package removal
* Package purging
* Automatic dependency cleanup
* Package searching
* Package information inspection
* Installed package verification
* Repository configuration inspection
* `.deb` package concepts
* DPKG package inspection
* Dependency analysis
* Broken package troubleshooting

The lab also demonstrated an important real-world Linux administration principle:

> **Always review what a package-management command plans to change before confirming it.**

---

# Key Learning Points

* APT is Ubuntu's high-level package management system.
* DPKG is the low-level Debian package management tool.
* Ubuntu uses `.deb` packages.
* `apt update` refreshes package information.
* `apt upgrade` upgrades installed packages.
* `apt install` installs software.
* `apt remove` removes software while configuration may remain.
* `apt purge` removes software and package-managed configuration.
* `apt autoremove` removes unused automatically installed dependencies.
* `apt search` finds packages.
* `apt show` provides package information.
* `dpkg -s` checks package status.
* `dpkg -L` lists package files.
* Package dependencies are important for successful software installation.
* `apt --fix-broken install` can repair dependency problems.
* Repository configuration determines where APT obtains package information.
* Package changes should always be reviewed before confirmation.

---

# 📸 Screenshot Structure

The screenshots for this lab are available in the `Screenshots/` directory and are numbered in the order they were performed :

- 1.  Check package manager version 
- 2.1 Update the packages
- 2.2 Update successful
- 3.  List out upgradable packages
- 4.1 Install the packages
- 4.2 Details of packages and permission 
- 4.3 Verify the upgrade 
- 5.1 Detect the cowsay package
- 5.2 Install cowsay package
- 5.3 Verify the installation of cowsay
- 5.4 Verify the package through command execution
- 6.1 Remove the cowsay package
- 6.2 Package removed
- 6.3 Verify it 
- 7.1 Remove configuration files of cowsay using purge
- 7.2 Verify it 
- 8.1 Autoremove
- 8.2 Asking for permission to autoremove
- 8.3 Permission denied due to required resources
- 9.  Check for autoinstalled packages
- 10. Search for packages 
- 11. Get the detailed view of particular package
- 12.1 List out installed packages
- 12.2 Lsit installed opnessh package
- 13.  View Ubuntu repository information
- 14.1 View the status of package by dpkg
- 14.2 View the related downloaded files of package by dpkg
- 15.1 Fix broken or misconfigured dependencies
- 15.2 Nothing was broken or misconfigured

---

# Future Improvements

The next advanced package-management topics can include:

* Installing a downloaded `.deb` package
* Comparing `dpkg -i` with `apt install ./package.deb`
* Package version pinning
* Holding packages with `apt-mark hold`
* Package verification
* Repository management
* PPAs
* Snap vs APT
* Security-focused package management
* Automated package updates

---

**Author:** Dhruv Sudani

**Course:** Self-Learning Linux & Cybersecurity Homelab

**Module:** Linux Administration

**Lesson:** 4 – Package Management & Software Installation