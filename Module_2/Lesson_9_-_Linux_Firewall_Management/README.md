# Linux Firewall Management – Lesson 9

## Project Title

**Linux Firewall Management and Rule Inspection Using UFW and nftables**

---

## Objective

The objective of this lab was to understand Linux firewall fundamentals, inspect firewall configurations, practise managing firewall rules, and learn how firewall policies affect network access.

The lab focused on Ubuntu's Uncomplicated Firewall (UFW), nftables, TCP ports, SSH access protection, and safe firewall troubleshooting.

**Important:** UFW was deliberately left inactive throughout this lab to avoid disrupting SSH access. The exercises focused on inspecting the configuration and managing stored rules rather than enforcing firewall restrictions.

---

## Lab Environment

| Component                  | Details                        |
|----------------------------|--------------------------------|
| Host Operating System      | Windows 11                     |
| Server Operating System    | Ubuntu Linux                   |
| Virtualization Platform    | Oracle VirtualBox              |
| Network Configuration      | NAT and Host-Only Adapter      |
| Network Interface          | `enp0s8` (Host-Only interface) |
| Remote Access              | OpenSSH over TCP port 22       |
| Firewall Tools             | UFW, nftables, iptables        |
| Firewall Status During Lab | UFW inactive                   |

---

## Prerequisites

Before starting this lab, the following concepts and activities had been covered:

- Linux users, groups, and permissions
- SSH remote access between Windows and Ubuntu
- Linux networking fundamentals
- IP addresses, routing, and network interfaces
- Network troubleshooting commands
- Basic TCP and UDP concepts

---

## 1. Introduction to Linux Firewalls

A firewall is a security mechanism that controls network traffic according to configured rules.

A firewall can help control:

- Incoming connections to a Linux server
- Outgoing connections initiated by applications
- Access to specific TCP or UDP ports
- Traffic from particular source addresses or networks
- Network exposure of services such as SSH and web servers

Firewalls are commonly used in Linux administration, cybersecurity, server hardening, and network security monitoring.

### Incoming and Outgoing Traffic

**Incoming traffic:** Connections or packets arriving at the system.

Example: A Windows host connecting to an Ubuntu SSH server.

**Outgoing traffic:** Connections or packets leaving the system.

Example: Ubuntu initiating an HTTPS connection to a remote website.

A firewall's behaviour depends on its rules, default policies, connection state, and the traffic being evaluated.

---

## 2. Understanding Common Firewall Tools

### UFW — Uncomplicated Firewall

UFW provides a simpler command-line interface for managing firewall rules on Ubuntu and other Linux distributions.

It allows administrators to configure rules for ports, protocols, and source addresses.

### nftables

nftables is a Linux framework used to implement packet filtering and other network traffic processing.

It provides a rule-based system for controlling network traffic.

### iptables

iptables is a traditional Linux command-line interface for managing packet-filtering rules. Modern Linux systems may use nftables underneath, depending on their configuration.

These tools are related, but they are not interchangeable in every situation. Inspecting one tool alone does not establish the complete firewall state of a Linux system.

---

## 3. Checking Installed Firewall Tools

The following command was used to check whether the firewall utilities were available:

```bash
command -v ufw; command -v nft; command -v iptables
```

The tools were found to be installed.

**Key learning:** An installed firewall utility does not necessarily mean that its firewall rules are currently active or enforced.

---

## 4. Checking UFW Status

Command:

```bash
sudo ufw status verbose
```

Observed result:

```text
Status: inactive
```

This confirmed that UFW was inactive during the lab.

The distinction between installation and activation is important:

- Installed: The firewall utility is available.
- Configured: Rules or policies may be stored.
- Active: UFW is enabled and enforcing its active firewall configuration.

UFW being inactive does not prove that every other firewall mechanism on the system is inactive.

---

## 5. Inspecting nftables

Command:

```bash
sudo nft list ruleset
```

Observed result: No output was displayed.

This means no rules were displayed by this command at the time of inspection. It does not, by itself, prove that all possible network filtering mechanisms are inactive.

---

## 6. Understanding Default Firewall Policies

The configured default policies were inspected using:

```bash
grep '^DEFAULT_' /etc/default/ufw
```

The relevant values were:

```text
DEFAULT_OUTPUT_POLICY="ACCEPT"
DEFAULT_INPUT_POLICY="DROP"
```

### Meaning of the Policies

| Policy   | Meaning                                                        |
|----------|----------------------------------------------------------------|
| `ACCEPT` | Permits matching traffic by default                            |
| `DROP`   | Discards matching traffic without sending a rejection response |
| `REJECT` | Rejects matching traffic, generally with a response            |

The inspected values were configuration defaults. Because UFW was inactive, they should not be interpreted as proof that those policies were actively filtering traffic during the lab.

### Stateful Firewall Behaviour

A stateful firewall can track connection states. For example, when an outbound HTTPS connection is permitted, response traffic belonging to that established connection is normally allowed by the relevant stateful rules.

This differs from permitting arbitrary new incoming connections.

---

## 7. Understanding SSH Firewall Requirements

SSH is commonly used for secure remote administration of Linux servers.

The default SSH service port is:

```text
TCP 22
```

The SSH service was checked using:

```bash
sudo systemctl is-active ssh
```

Observed result:

```text
active
```

The listening socket was inspected using:

```bash
sudo ss -tlnp | grep ssh
```

The SSH service was found listening on TCP port 22.

The Host-Only network interface was identified using:

```bash
ip -br addr
```

The interface used for Host-Only networking was:

```text
enp0s8
```

The route toward the Windows Host-Only address was inspected using:

```bash
ip route get <WINDOWS_HOST_ONLY_IP>
```

The route used `enp0s8`.

**Security lesson:** An SSH service can be running and listening while a firewall or another network component still prevents a client from connecting.

The route and listening socket are useful diagnostic evidence, but neither alone proves end-to-end connectivity.

---

## 8. Creating an SSH Allow Rule

The following command was used initially to practise adding an SSH rule:

```bash
sudo ufw allow 22/tcp
```

The broad rule was subsequently removed:

```bash
sudo ufw delete allow 22/tcp
```

A more restricted rule was then added using the actual Host-Only subnet:

```bash
sudo ufw allow from <HOST_ONLY_SUBNET> to any port 22 proto tcp
```

The placeholder `<HOST_ONLY_SUBNET>` represents the Host-Only network in CIDR notation. The actual subnet was entered locally and is not recorded in this README.

The stored rule was verified using:

```bash
sudo ufw show added
```

The restricted SSH rule remained present in the configuration.

### Security Consideration

Restricting SSH to a trusted source subnet can reduce exposure. However, this rule allows matching sources across that subnet; it does not restrict access to one individual device unless the source restriction is narrowed accordingly.

Because UFW remained inactive, the stored SSH rule was **not being enforced by UFW during this lab**.

---

## 9. Practising a Temporary Deny Rule

A temporary deny rule was added for TCP port 8080:

```bash
sudo ufw deny 8080/tcp
```

The configured rules were inspected:

```bash
sudo ufw show added
```

The temporary deny rule appeared in the output.

It was then removed:

```bash
sudo ufw delete deny 8080/tcp
```

The rules were checked again:

```bash
sudo ufw show added
```

The temporary deny rule was no longer present.

This exercise demonstrated how to add, inspect, and remove a firewall rule.

**Important:** Since UFW was inactive, the exercise demonstrated rule management, not actual traffic blocking.

---

## 10. Allowing Common Service Ports

The following commands illustrate common UFW rule syntax. They are examples for future use, not a record of rules enabled during this lab.

### HTTP

```bash
sudo ufw allow 80/tcp
```

TCP port 80 is the standard port for HTTP.

### HTTPS

```bash
sudo ufw allow 443/tcp
```

TCP port 443 is the standard port for HTTPS.

### SSH

```bash
sudo ufw allow 22/tcp
```

TCP port 22 is the default SSH port.

Before allowing a service, an administrator should confirm that the service is required and consider restricting access to trusted sources.

---

## 11. Firewall Rule Inspection Commands

| Command                        | Purpose                                                              |
|--------------------------------|----------------------------------------------------------------------|
| `sudo ufw status`              | Checks whether UFW is active                                         |
| `sudo ufw status verbose`      | Displays status and additional policy information                    |
| `sudo ufw status numbered`     | Displays active UFW rules with numbers when available                |
| `sudo ufw show added`          | Displays rules added through UFW's command interface                 |
| `sudo ufw show raw`            | Inspects the underlying firewall configuration exposed by UFW        |
| `sudo nft list ruleset`        | Displays the nftables ruleset                                        | 
| `sudo systemctl is-active ssh` | Checks whether the SSH service is active                             |
| `sudo ss -tlnp`                | Lists listening TCP sockets with process information where available |

**Note:** `ufw status` and `ufw status numbered` report active UFW status and rules. They are not substitutes for `ufw show added` when checking stored rules while UFW is inactive.

---

## 12. Firewall Troubleshooting Methodology

When a network connection fails, do not immediately assume that the firewall is responsible.

A structured troubleshooting approach is more effective:

1. Confirm that the destination service is running.
2. Confirm that the service is listening on the expected address and port.
3. Check the route and network interface.
4. Inspect the relevant firewall rules and policies.
5. Consider other filtering mechanisms and network components.
6. Test connectivity after making an appropriate change.
7. Verify that the change solved the issue without disrupting other access.

For SSH troubleshooting, avoid disabling the SSH service or enabling restrictive firewall policies without a recovery plan.

---

## 13. Important Security Lessons

### Avoid Accidental SSH Lockout

Before enabling UFW on a remotely administered machine:

- Review the complete intended firewall policy.
- Ensure the SSH access rule matches the correct source.
- Consider the order and interaction of allow and deny rules.
- Confirm that the management connection can be recovered.
- Keep access to the VM's local console available when possible.

### Avoid Unnecessary Rules

Every firewall rule should have a clear purpose. Unnecessary rules can make troubleshooting more difficult and increase the risk of unintended access.

### Verify Instead of Assuming

A rule appearing in configuration does not prove that it is active. Similarly, an inactive UFW service does not prove that no other filtering mechanism exists.

---

## 14. Lab Result

The following activities were completed:

- Identified the installed firewall utilities.
- Checked UFW status and default policy configuration.
- Inspected the nftables ruleset.
- Confirmed that SSH was active and listening on TCP port 22.
- Identified the Host-Only network interface and inspected routing.
- Added and removed an unrestricted SSH allow rule.
- Added a source-restricted SSH allow rule.
- Added, inspected, and removed a temporary TCP 8080 deny rule.
- Learned the difference between stored firewall rules and enforced rules.

**Final firewall state:** UFW remained inactive. The restricted SSH allow rule remained stored, and the temporary TCP 8080 deny rule had been removed.

No firewall activation test or actual packet-blocking test was performed.

---

## 15. Key Learning Points

- A firewall controls network traffic according to rules and policies.
- UFW simplifies firewall management on Ubuntu.
- nftables provides a framework for Linux packet filtering.
- An installed firewall tool is not necessarily active.
- Stored rules are not necessarily being enforced.
- TCP port 22 is the default SSH port.
- TCP ports 80 and 443 are standard HTTP and HTTPS ports.
- Source restrictions can reduce unnecessary exposure.
- Firewall rule interactions must be reviewed carefully.
- Other filtering mechanisms may operate even when UFW is inactive.
- A recovery plan is essential before enabling restrictive firewall rules.

---

## 16. Future Improvements

The following topics can be practised in a controlled lab with console recovery available:

- Enabling UFW after reviewing the complete rule set.
- Testing actual allowed and blocked traffic.
- Learning more about UFW rule ordering and precedence.
- Inspecting active nftables rules and chains.
- Comparing firewall behaviour with packet captures.
- Developing a basic Linux server hardening checklist.

# Screenshot Structure

The screenshots for this lab are available in the `Screenshots/` directory and are numbered in the order they were performed

---

**Author:** Dhruv Sudani

**Course:** Self-Learning Linux & Cybersecurity Homelab

**Module:** Linux Administration

**Lesson:** 9 – Linux Firewall Management
