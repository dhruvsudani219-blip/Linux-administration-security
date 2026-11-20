# Windows Networking Fundamentals — Part B

## Objective
Learn essential Windows networking commands for inspecting network configuration, troubleshooting connectivity, diagnosing DNS issues, and investigating active connections.

## Lab Environment
- **Host OS:** Windows 11
- **Virtualization:** Oracle VirtualBox
- **Virtual Network:** Host-Only Adapter
- **Tools:** Command Prompt (CMD) and PowerShell

## Commands Practiced

### 1. Network Configuration

```cmd
ipconfig
ipconfig /all
hostname
getmac
```

- `ipconfig` — displays IP configuration.
- `ipconfig /all` — displays detailed adapter information, DHCP, gateway, DNS, and MAC address.
- `hostname` — displays the computer name.
- `getmac` — displays MAC addresses of network adapters.

### 2. DNS Management and Troubleshooting

```cmd
ipconfig /displaydns
ipconfig /flushdns
nslookup google.com
nslookup google.com 8.8.8.8
```

- `/displaydns` — displays cached DNS records.
- `/flushdns` — clears the local DNS resolver cache.
- `nslookup` — queries DNS to resolve hostnames.
- Specifying `8.8.8.8` queries Google's public DNS resolver.

### 3. Connectivity and Route Diagnostics

```cmd
ping 127.0.0.1
ping 8.8.8.8
ping google.com

tracert 8.8.8.8
tracert google.com

pathping 8.8.8.8

arp -a

route print
```

- `ping` — tests ICMP reachability.
- `tracert` — traces the network path to a destination.
- `pathping` — combines route tracing with packet-loss and latency analysis.
- `arp -a` — displays the IPv4-to-MAC address mappings in the ARP cache.
- `route print` — displays the Windows routing table.

### 4. Connections and Processes

```cmd
netstat -ano
netstat -abno
tasklist /FI "PID eq 1234"
```

- `netstat -ano` — displays connections, listening ports, numerical addresses, and process IDs.
- `netstat -abno` — also attempts to show the associated executable; Administrator privileges may be required.
- `tasklist` — identifies the process associated with a PID. Replace `1234` with the actual PID.

### 5. Network Configuration with Netsh

```cmd
netsh interface show interface
netsh interface ipv4 show config
```

- Displays network interfaces and their states.
- Inspects IPv4 configuration for network interfaces.

### 6. PowerShell Networking

```powershell
Get-NetIPConfiguration
Get-NetAdapter
Get-NetRoute
Get-NetAdapterStatistics
Get-DnsClientServerAddress
Get-NetAdapterStatistics
Resolve-DnsName google.com
Resolve-DnsName google.com -Server 8.8.8.8
Test-NetConnection google.com -Port 443
```

- `Get-NetIPConfiguration` — displays IP configuration.
- `Get-NetAdapter` — displays network adapter details and status.
- `Get-NetRoute` — displays routing information.
- `Get-NetAdapterStatistics` — displays adapter traffic counters.
- `Get-DnsClientServerAddress` — displays configured DNS servers.
- `Resolve-DnsName` — queries DNS records.
- `Test-NetConnection` — tests connectivity, including TCP port accessibility.

## Practical Troubleshooting Workflow

1. Check adapter status using `Get-NetAdapter`.
2. Inspect IP configuration using `ipconfig /all`.
3. Check routes using `Get-NetRoute` or `route print`.
4. Test IP reachability using `ping`.
5. Investigate DNS using `Resolve-DnsName` or `nslookup`.
6. Test application connectivity using `Test-NetConnection`.
7. Investigate unfamiliar connections using `netstat -ano` and identify their PIDs.

## Cybersecurity Relevance

- Identify listening ports and active network connections.
- Investigate processes associated with network connections.
- Diagnose DNS and routing problems.
- Inspect adapter configurations during incident investigations.
- Distinguish suspicious indicators from normal network activity.

**Important:** An unfamiliar port, process, or connection does not automatically indicate malware. Investigate the evidence and context before taking action.

## Key Learning Outcomes

- Practised Windows CMD and PowerShell networking commands.
- Inspected IP addresses, gateways, DNS servers, and MAC addresses.
- Tested connectivity and diagnosed DNS and routing issues.
- Examined listening ports and associated process IDs.
- Applied a structured troubleshooting approach to VirtualBox Host-Only networking.

## Result

Successfully practised essential Windows networking commands for configuration inspection, connectivity troubleshooting, and basic cybersecurity investigation.

**Author:** Dhruv Sudani

**Course:** Self-Learning Linux & Cybersecurity Homelab

**Module:** Networking Fundamentals

**Lesson:** 7 — Windows Networking Commands (Part B)
