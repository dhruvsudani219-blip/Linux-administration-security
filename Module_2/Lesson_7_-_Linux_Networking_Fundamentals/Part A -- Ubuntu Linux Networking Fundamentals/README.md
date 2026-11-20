# Lesson 7 – Part A: Ubuntu Linux Networking Fundamentals

## Project / Lesson Title

**Ubuntu Linux Networking Fundamentals**

---

## Objective

The objective of this part of the Linux Networking lesson was to understand how Ubuntu handles network interfaces, IP addresses, routing, connectivity, DNS, ports, network neighbors, hostnames, and network management.

The lesson focused primarily on practical Linux networking commands used for:

- Network administration
- Troubleshooting
- Network+ preparation
- Cybersecurity investigation
- Linux server administration

---

# Lab Environment

| Component               | Details                                         |
|-------------------------|-------------------------------------------------|
| Host Operating System   | Windows 11                                      |
| Client / Server         | Ubuntu Linux VM                                 |
| Virtualization Platform | Oracle VirtualBox                               |
| Network Adapter         | Host-Only + NAT                                 |
| Linux Networking Tools  | `ip`, `ping`, `ss`, `resolvectl`, `nmcli`, etc. |

> **Privacy Note:** Private IP addresses used during the lab are intentionally represented using placeholders instead of publishing the exact addresses.

---

# Part A Roadmap

The following topics were covered:

1. Network interfaces
2. `ip link`
3. `ip addr`
4. `ip -br addr`
5. MAC addresses
6. Interface states
7. Loopback interface
8. RX/TX statistics
9. IPv4
10. IPv6
11. Prefix and subnet concepts
12. Network, broadcast and host addresses
13. Private, public and link-local addresses
14. Routing
15. Connectivity testing
16. DNS and hostname resolution
17. Sockets and ports
18. Network interface statistics
19. Neighbor / ARP investigation
20. Hostname investigation
21. Network configuration concepts
22. NetworkManager
23. Device vs connection profiles

---

# 1. Network Interfaces

A network interface is the component through which a computer communicates over a network.

Ubuntu can contain several interfaces.

Common examples:

```text
lo
enp0s3
enp0s8
```

During this lab:

```text
lo      → Loopback interface
enp0s3  → Virtual Ethernet interface
enp0s8  → Virtual Ethernet interface
```

---

# 2. `ip link`

Command:

```bash
ip link
```

Purpose:

Displays network interfaces and their Layer 2/link information.

Important information includes:

- Interface name
- Interface state
- MAC address
- MTU
- Link status

Example structure:

```text
interface
   ↓
state
   ↓
MAC address
   ↓
MTU
```

---

# 3. `ip addr`

Command:

```bash
ip addr
```

Purpose:

Displays IP addresses assigned to network interfaces.

It can show:

- IPv4 addresses
- IPv6 addresses
- Prefix lengths
- Loopback address
- Interface states

Useful relationship:

```text
MAC address → Layer 2
IP address  → Layer 3
```

---

# 4. Brief IP Address View

Command:

```bash
ip -br addr
```

Purpose:

Provides a compact summary of interfaces and their addresses.

This is useful when the full output of:

```bash
ip addr
```

is too large.

---

# 5. MAC Addresses

A MAC address is a Layer 2 hardware/link-layer identifier associated with a network interface.

Example format:

```text
xx:xx:xx:xx:xx:xx
```

MAC addresses are used for communication within the local network.

Important distinction:

```text
MAC address → Layer 2
IP address  → Layer 3
Port        → Layer 4
```

---

# 6. Interface States

Command:

```bash
ip link show enp0s8
```

Important states observed during the lab:

```text
UP
LOWER_UP
```

### UP

The interface is administratively enabled.

### LOWER_UP

The underlying link is active.

A useful mental model:

```text
UP
↓
Interface enabled

LOWER_UP
↓
Link is actually available
```

---

# 7. MTU

MTU means:

**Maximum Transmission Unit**

Typical Ethernet MTU:

```text
1500 bytes
```

It represents the maximum size of an IP packet that can normally be carried in a single Layer 2 frame without fragmentation at that link.

---

# 8. Loopback Interface

Interface:

```text
lo
```

The loopback interface allows the computer to communicate with itself.

Common loopback address:

```text
127.0.0.1
```

IPv6 loopback:

```text
::1
```

Test command:

```bash
ping -c 4 127.0.0.1
```

Purpose:

Tests the local TCP/IP networking stack without using the physical or virtual network.

---

# 9. RX/TX Statistics

Command:

```bash
ip -s link
```

This displays interface statistics.

### RX

Receive statistics.

### TX

Transmit statistics.

Important fields include:

```text
bytes
packets
errors
dropped
```

These statistics can help identify network problems.

For example:

```text
High RX errors
       ↓
Possible receive-side problem
```

or:

```text
Increasing dropped packets
       ↓
Possible congestion/configuration/interface issue
```

---

# 10. IPv4

IPv4 uses 32-bit addresses.

Example:

```text
192.168.1.10
```

IPv4 addresses are commonly written as four decimal octets.

Each octet ranges from:

```text
0 – 255
```

Ubuntu command:

```bash
ip -4 addr
```

Displays IPv4 addresses.

---

# 11. IPv6

IPv6 uses 128-bit addresses.

Example:

```text
2001:db8::1
```

IPv6 addresses are written using hexadecimal notation.

Command:

```bash
ip -6 addr
```

Displays IPv6 addresses.

An interface can have multiple IPv6 addresses simultaneously.

For example, an interface may have:

```text
Global IPv6 address
Link-local IPv6 address
Other IPv6 addresses
```

This is normal.

---

# 12. IPv6 Link-Local Addresses

IPv6 link-local addresses normally begin with:

```text
fe80::
```

They are used for communication on the local network segment.

They are not intended to function as normal globally routable Internet addresses.

---

# 13. Subnetting

> **Live lesson note:** Basic subnetting calculations were intentionally skipped during the live lesson because the learner already understood subnetting. The complete explanation is included here so that this README can be used independently by another learner.

A subnet divides an IP network into smaller logical networks.

Example:

```text
192.168.1.0/24
```

The:

```text
/24
```

is the prefix length.

For IPv4:

```text
/24 = 255.255.255.0
```

A `/24` network contains:

```text
256 total addresses
```

Traditionally:

```text
Network address → 1
Usable host addresses → 254
Broadcast address → 1
```

For:

```text
192.168.1.0/24
```

the traditional ranges are:

```text
Network address:
192.168.1.0

Usable hosts:
192.168.1.1 – 192.168.1.254

Broadcast:
192.168.1.255
```

The prefix tells the system which portion identifies the network and which portion identifies hosts.

---

# 14. Network, Host and Broadcast Addresses

For a traditional IPv4 subnet:

### Network Address

Identifies the network itself.

Example:

```text
192.168.1.0
```

### Host Address

Identifies a device inside the network.

Example:

```text
192.168.1.25
```

### Broadcast Address

Used to send traffic to all hosts on the local IPv4 subnet.

Example:

```text
192.168.1.255
```

> Modern networking can also use subnet sizes where the traditional `/24` example does not directly apply, so always calculate these values from the actual prefix.

---

# 15. Private, Public and Link-Local Addresses

## Private IPv4 ranges

Private IPv4 address space includes:

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

Private addresses are commonly used inside:

- Home networks
- Enterprise networks
- Virtual machines
- Labs

They are not directly routable across the public Internet.

---

## IPv4 Link-Local

IPv4 link-local addresses use:

```text
169.254.0.0/16
```

They are commonly assigned automatically when a device cannot obtain an IPv4 address through normal configuration such as DHCP.

---

## Public IP

A public IP address is globally routable on the Internet.

---

# 16. Routing

Command:

```bash
ip route
```

Purpose:

Displays the IPv4 routing table.

A routing table helps Linux decide:

> Where should this packet go?

Important concepts:

```text
Destination
Gateway
Interface
Metric
Default route
```

---

# 17. Default Route

A default route is used when no more specific route matches the destination.

Conceptually:

```text
Specific route available?
        │
       YES
        ↓
Use specific route

       NO
        ↓
Use default route
```

Example structure:

```text
default via <gateway> dev enp0s3
```

Meaning:

```text
default
   ↓
Use this route when no more specific route exists

via <gateway>
   ↓
Send traffic to this next-hop router

dev enp0s3
   ↓
Use enp0s3
```

---

# 18. Route Decision Investigation

Command:

```bash
ip route get 8.8.8.8
```

Purpose:

Asks the Linux kernel:

> Which route would you use to reach 8.8.8.8?

During the lab, the Internet destination was routed through the VM's Internet-facing interface.

This command is extremely useful during troubleshooting.

---

# 19. Connectivity Testing with `ping`

Linux command:

```bash
ping
```

`ping` uses ICMP Echo Request and Echo Reply messages to test reachability.

---

## Loopback Test

```bash
ping -c 4 127.0.0.1
```

Tests the local networking stack.

---

## Host-Only Interface Test

The Ubuntu Host-Only address was tested.

Purpose:

```text
Ubuntu
   ↓
Its own Host-Only interface
```

---

## Windows Host Test

Ubuntu successfully communicated with the Windows Host-Only adapter.

Purpose:

Tests:

```text
Ubuntu VM
   ↓
VirtualBox Host-Only network
   ↓
Windows Host
```

---

## Internet IP Test

```bash
ping -c 4 8.8.8.8
```

Purpose:

Tests Internet-layer connectivity without relying on DNS.

---

## Domain Test

```bash
ping -c 4 google.com
```

Purpose:

Tests:

```text
DNS resolution
+
Network connectivity
```

---

# 20. DNS

DNS means:

**Domain Name System**

DNS translates names such as:

```text
google.com
```

into IP addresses.

Instead of remembering an IP address, users can use a domain name.

---

# 21. `resolvectl`

Check DNS configuration:

```bash
resolvectl status
```

Query a domain:

```bash
resolvectl query google.com
```

View DNS statistics:

```bash
resolvectl statistics
```

---

# 22. `/etc/resolv.conf`

Command:

```bash
cat /etc/resolv.conf
```

This file provides resolver configuration information.

Check whether it is a symbolic link:

```bash
ls -l /etc/resolv.conf
```

A symbolic link can point to another file managed by the system.

To resolve the complete symbolic-link path:

```bash
readlink -f /etc/resolv.conf
```

The `-f` option follows symbolic links and produces the final canonical path.

---

# 23. `/etc/hosts`

Command:

```bash
cat /etc/hosts
```

`/etc/hosts` provides local hostname-to-IP mappings.

Example:

```text
192.168.1.20    server1
```

This means:

```text
server1 → 192.168.1.20
```

The mapping can work without querying an external DNS server.

---

# 24. NSS — Name Service Switch

Configuration file:

```bash
cat /etc/nsswitch.conf
```

NSS controls which sources Linux uses for certain types of name and identity lookups.

For hostname resolution, the relevant section is:

```text
hosts:
```

Conceptually:

```text
Application
    ↓
NSS
    ↓
Configured lookup sources
    ↓
Result
```

This explains why commands such as:

```bash
getent hosts google.com
```

are useful.

---

# 25. `getent`

Command:

```bash
getent hosts google.com
```

Purpose:

Queries the system's configured name-resolution mechanism.

To resolve the local hostname:

```bash
getent hosts $(hostname)
```

`$(hostname)` is command substitution.

It means:

```text
Run hostname
     ↓
Take its output
     ↓
Use that output as an argument to getent
```

---

# 26. Sockets and Ports

A socket represents a network communication endpoint.

An IP address identifies the host/interface.

A port identifies the service/application endpoint.

Conceptually:

```text
IP address + Port
        ↓
Network service endpoint
```

Example:

```text
192.168.x.x:22
```

Port:

```text
22
```

is commonly associated with SSH.

---

# 27. Port Ranges

TCP and UDP ports use 16-bit numbers:

```text
0 – 65535
```

Common classifications:

```text
0 – 1023
Well-known ports

1024 – 49151
Registered ports

49152 – 65535
Dynamic/private range
```

> Actual ephemeral-port ranges can vary by operating system.

---

# 28. TCP vs UDP

## TCP

TCP is connection-oriented.

A typical TCP connection uses a three-way handshake:

```text
Client → SYN
Server → SYN-ACK
Client → ACK
```

TCP provides reliable, ordered delivery.

---

## UDP

UDP is connectionless.

It does not establish a TCP-style connection before sending data.

UDP is commonly used where low overhead or fast transmission is important.

---

# 29. `ss`

Basic command:

```bash
ss
```

Displays socket information.

---

## Listening TCP and UDP

```bash
ss -tuln
```

Options:

```text
-t → TCP
-u → UDP
-l → Listening
-n → Numeric output
```

---

## Show Processes

```bash
ss -tulpn
```

Additional option:

```text
-p → Process information
```

This can help identify which process owns a listening socket.

---

## Show All TCP Connections

```bash
ss -tan
```

Additional option:

```text
-a → All
```

---

# 30. Important Socket States

### LISTEN

A service is waiting for incoming connections.

### ESTABLISHED

A TCP connection is currently active.

### TIME-WAIT

A TCP connection has recently closed and the system is waiting before fully releasing the connection state.

---

# 31. `Recv-Q` and `Send-Q`

`ss` can display:

```text
Recv-Q
Send-Q
```

### Recv-Q

Data waiting to be received by the application.

### Send-Q

Data waiting to be sent/acknowledged.

These fields can provide clues during network troubleshooting.

---

# 32. Network Interface Statistics

Command:

```bash
ip -s link
```

Important statistics:

```text
RX bytes
RX packets
RX errors
RX dropped

TX bytes
TX packets
TX errors
TX dropped
```

These statistics help identify:

- Packet drops
- Interface errors
- Traffic volume
- Possible network problems

---

# 33. Neighbor Table and ARP

Command:

```bash
ip neigh
```

Linux uses the neighbor table to track Layer 2 neighbors.

For IPv4, this is closely related to **ARP**.

ARP means:

**Address Resolution Protocol**

Its purpose is to discover:

```text
IPv4 address
      ↓
MAC address
```

Example concept:

```text
Who has 192.168.x.x?
        ↓
That device replies with its MAC address.
```

---

# 34. Local vs Remote Destinations

This is an important networking concept.

## Local destination

If Ubuntu communicates with another device on the same local network:

```text
Destination IP
      ↓
ARP
      ↓
Destination MAC
```

The Ethernet frame can be sent directly to that device.

---

## Remote destination

For an Internet destination such as:

```text
8.8.8.8
```

Ubuntu normally does **not** ARP for Google's server.

Instead:

```text
Destination IP
      ↓
Routing table
      ↓
Default gateway / next hop
      ↓
ARP for gateway
      ↓
Gateway MAC address
```

The IP packet still contains:

```text
Destination IP = 8.8.8.8
```

but the Ethernet frame is initially addressed to the:

```text
Gateway MAC
```

Routers then forward the packet toward the final destination.

---

# 35. Hostname Investigation

Command:

```bash
hostname
```

Purpose:

Displays the system hostname.

---

## Detailed Hostname Information

```bash
hostnamectl
```

Provides additional hostname and system information.

---

## Resolve Local Hostname

```bash
getent hosts $(hostname)
```

This tests how the system resolves its own hostname.

---

## Local Hostname Mapping

```bash
cat /etc/hosts
```

Can reveal local mappings between hostnames and IP addresses.

---

# 36. Network Configuration and Netplan

Ubuntu can use **Netplan** as a network configuration layer.

Typical configuration location:

```text
/etc/netplan/
```

Configuration files normally use:

```text
.yaml
```

Example:

```text
/etc/netplan/example.yaml
```

During this lab:

```bash
ls -l /etc/netplan/
```

returned:

```text
total 0
```

This means the directory existed but contained no files.

This was **not treated as a networking failure** because NetworkManager was actively managing the network and connectivity was working.

---

# 37. DHCP vs Static Configuration

## DHCP

DHCP automatically provides network configuration such as:

```text
IP address
Subnet information
Gateway
DNS
```

---

## Static Configuration

A static configuration manually specifies network parameters.

Conceptually:

```text
IP address
Prefix
Gateway
DNS
```

Static addressing is common for systems where a predictable address is required.

Examples:

- Servers
- Network appliances
- Infrastructure systems

---

# 38. Safe Netplan Changes

Important commands include:

```bash
netplan try
```

and:

```bash
netplan apply
```

### `netplan try`

Temporarily applies configuration and provides a mechanism to confirm it.

This is safer when remotely administering a system because an incorrect configuration can otherwise disconnect the administrator.

### `netplan apply`

Applies the configured Netplan configuration.

> **Important:** Network configuration should not be changed casually while connected through SSH. A bad configuration can terminate the SSH session.

---

# 39. NetworkManager

NetworkManager manages network connections and devices on many Linux desktop/server installations.

Command-line management is commonly performed with:

```bash
nmcli
```

---

# 40. NetworkManager General Status

Command:

```bash
nmcli general status
```

During the lab, NetworkManager reported:

```text
STATE          connected
CONNECTIVITY   full
```

Meaning:

```text
connected
    ↓
NetworkManager considers networking active

full
    ↓
NetworkManager considers connectivity available
```

---

# 41. NetworkManager Device Status

Command:

```bash
nmcli device status
```

Purpose:

Displays NetworkManager's view of network devices.

Conceptually:

```text
DEVICE
TYPE
STATE
CONNECTION
```

---

# 42. NetworkManager Connection Profiles

Command:

```bash
nmcli connection show
```

Displays known connection profiles.

A connection profile can contain:

```text
IP configuration
Gateway
DNS
IPv4 settings
IPv6 settings
```

---

# 43. Active Connections

Command:

```bash
nmcli connection show --active
```

Displays only currently active connection profiles.

---

# 44. Device vs Connection

This is one of the most important NetworkManager concepts.

### Device

The actual network interface.

Examples:

```text
enp0s3
enp0s8
```

### Connection

A NetworkManager configuration profile applied to a device.

Think:

```text
DEVICE
enp0s8
   │
   ↓
CONNECTION PROFILE
   │
   ├── IP configuration
   ├── Gateway
   ├── DNS
   └── IPv6 configuration
```

Therefore:

```text
nmcli device status
```

asks:

> What network devices exist and what is their current state?

While:

```text
nmcli connection show
```

asks:

> What connection profiles are available?

And:

```text
nmcli connection show --active
```

asks:

> Which connection profiles are currently active?

---

# 45. Linux Networking Command Summary

| Task                          | Command                          |
|-------------------------------|----------------------------------|
| Show interfaces               | `ip link`                        |
| Show IP addresses             | `ip addr`                        |
| Brief IP view                 | `ip -br addr`                    |
| IPv4 addresses                | `ip -4 addr`                     |
| IPv6 addresses                | `ip -6 addr`                     |
| Interface details             | `ip link show enp0s8`            |
| Interface statistics          | `ip -s link`                     |
| Routing table                 | `ip route`                       |
| Route decision                | `ip route get <destination>`     |
| Test connectivity             | `ping <destination>`             |
| Send 4 pings                  | `ping -c 4 <destination>`        |
| DNS status                    | `resolvectl status`              |
| DNS query                     | `resolvectl query <domain>`      |
| DNS statistics                | `resolvectl statistics`          |
| Resolver configuration        | `cat /etc/resolv.conf`           |
| Resolve symlink               | `readlink -f /etc/resolv.conf`   |
| Local hostname mappings       | `cat /etc/hosts`                 |
| NSS configuration             | `cat /etc/nsswitch.conf`         |
| System name resolution        | `getent hosts <name>`            |
| Hostname                      | `hostname`                       |
| Hostname details              | `hostnamectl`                    |
| All sockets                   | `ss`                             |
| Listening TCP/UDP             | `ss -tuln`                       |
| Listening sockets + processes | `ss -tulpn`                      |
| All TCP connections           | `ss -tan`                        |
| Neighbor table                | `ip neigh`                       |
| Netplan directory             | `ls -l /etc/netplan/`            |
| NetworkManager status         | `nmcli general status`           |
| Network devices               | `nmcli device status`            |
| Connection profiles           | `nmcli connection show`          |
| Active connections            | `nmcli connection show --active` |

---

# 46. Linux Networking Investigation Workflow

A useful troubleshooting sequence is:

```text
1. Check interface
       ↓
   ip link

2. Check IP address
       ↓
   ip addr

3. Check route
       ↓
   ip route

4. Test connectivity
       ↓
   ping

5. Check DNS
       ↓
   resolvectl / getent

6. Check listening ports
       ↓
   ss -tulpn

7. Check neighbors
       ↓
   ip neigh

8. Check hostname
       ↓
   hostname / hostnamectl

9. Check network manager
       ↓
   nmcli
```

This workflow can be used during real Linux troubleshooting.

---

# 47. Cybersecurity Relevance

Linux networking commands are extremely useful during security investigations.

For example:

### Unexpected Interface

```bash
ip link
```

Can reveal unfamiliar interfaces.

---

### Unexpected IP Address

```bash
ip addr
```

Can reveal unexpected addresses.

---

### Suspicious Route

```bash
ip route
```

Can reveal unusual routing configuration.

---

### Unexpected Listening Port

```bash
ss -tulpn
```

Can reveal services listening for incoming connections.

---

### Unexpected Network Neighbor

```bash
ip neigh
```

Can reveal devices recently observed on the local network.

---

### Suspicious Connection

```bash
ss -tan
```

Can help investigate active TCP connections.

---

## Important Security Principle

A listening port is **not automatically a vulnerability**.

Investigation should continue:

```text
Listening port
      ↓
Which process?
      ↓
Which service?
      ↓
Is it expected?
      ↓
What configuration does it use?
      ↓
Who can reach it?
      ↓
Is the exposure necessary?
```

---

# 48. Practical Learning Outcome

After completing Part A, the learner can:

- Identify Linux network interfaces
- Identify MAC addresses
- Identify IPv4 and IPv6 addresses
- Understand interface states
- Understand MTU
- Understand loopback
- Read network statistics
- Understand subnetting concepts
- Identify private/public/link-local addressing
- Read Linux routing tables
- Investigate route decisions
- Test connectivity with `ping`
- Investigate DNS
- Understand `/etc/hosts`
- Understand NSS
- Resolve names with `getent`
- Investigate listening ports with `ss`
- Understand TCP and UDP
- Investigate neighbor/ARP information
- Understand local vs remote Layer 2 delivery
- Investigate hostnames
- Understand Netplan
- Understand NetworkManager
- Use `nmcli`
- Distinguish devices from connection profiles
- Apply a basic Linux network troubleshooting workflow
- Use Linux networking commands from a cybersecurity perspective

---

# 📸 Screenshot Structure

The screenshots for this lab are available in the `Screenshots/` directory and are numbered in the order they were performed :

-

---

# Author

**Author:** Dhruv Sudani

**Course:** Self-Learning Linux & Cybersecurity Homelab

**Module:** Linux Networking Fundamentals

**Lesson:** 7 – Part A

**Lesson Name:** Ubuntu Linux Networking Fundamentals

**Environment:** Windows 11 + Ubuntu Linux VM + Oracle VirtualBox

---

# Next Part

## Part B — Windows Networking

The next section will cover Windows networking commands and their practical relationship to the Linux commands learned in Part A.

Major topics include:

```text
ipconfig
ipconfig /all
ipconfig /flushdns
ping
tracert
pathping
nslookup
arp -a
route print
netstat -ano
hostname
getmac
netsh
Test-NetConnection
Resolve-DnsName
Get-NetIPConfiguration
Get-NetAdapter
Get-NetRoute
Get-NetAdapterStatistics
```

Part B will connect Windows networking with the Linux networking concepts learned in Part A.