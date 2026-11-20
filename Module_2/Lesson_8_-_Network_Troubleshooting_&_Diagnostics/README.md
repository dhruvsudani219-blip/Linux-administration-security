# Linux Administration — Lesson 8: Network Troubleshooting & Diagnostics

## Project Title

**Linux Network Troubleshooting and Diagnostics Lab**

## Objective

The objective of this lab was to learn how to diagnose common network connectivity problems in Linux using command-line networking and diagnostic tools.

The lab covered connectivity testing, DNS resolution, web access, TCP port testing, route tracing, socket inspection, and basic packet capture.

## Lab Environment

| Component               | Details                                   |
|-------------------------|-------------------------------------------|
| Host Operating System   | Windows 11                                |
| Virtual Machine         | Ubuntu Linux                              |
| Virtualization Platform | Oracle VirtualBox                         |
| Networking              | NAT and Host-Only networking              |
| Tools                   | Linux networking and diagnostic utilities |

## 1. Network Troubleshooting Workflow

A structured troubleshooting process helps identify problems without making unnecessary configuration changes.

The general workflow is:

1. Check network interfaces and IP configuration.
2. Verify the routing table.
3. Test local and external IP connectivity.
4. Check DNS resolution.
5. Test web access and TCP ports.
6. Inspect routes, sockets, and packets when necessary.
7. Interpret the evidence before deciding on a solution.

## 2. Establish a Network Baseline

### Commands

```bash
ip -br addr
ip route
```

- `ip -br addr` displays network interfaces and their addresses in a compact format.
- `ip route` displays the routing table, including the default route when configured.

**Learning outcome:** Learned to check interface state, assigned IP addresses, and routing information before investigating connectivity problems.

## 3. Test Connectivity with ping

### Commands

```bash
ping -c 4 127.0.0.1
ping -c 4 8.8.8.8
```

- `127.0.0.1` is the IPv4 loopback address used to test the local networking stack.
- `8.8.8.8` is a public DNS resolver IP address that can also be used as a connectivity test target.
- `-c 4` sends four ping requests and then stops.

**Learning outcome:** Practised checking local networking and external IP connectivity.

## 4. DNS Troubleshooting

DNS translates domain names into IP addresses.

### Commands

```bash
getent hosts google.com
resolvectl status
resolvectl query google.com
dig -v
dig google.com
dig google.com A
```

- `getent hosts` checks name resolution through the system's configured lookup mechanisms.
- `resolvectl status` displays resolver configuration.
- `resolvectl query` performs a DNS lookup using the system resolver.
- `dig` performs DNS queries and displays DNS response details.
- An `A` record maps a hostname to an IPv4 address.

**Learning outcome:** Verified hostname resolution using multiple tools and learned how to inspect DNS configuration.

## 5. Web Requests and Downloads

### Using curl

```bash
curl --version
curl -I https://example.com
```

`curl -I` requests HTTP response headers, allowing a quick check of web-server access and the returned HTTP status.

### Using wget

```bash
wget --version
wget -O /tmp/network-test.txt https://example.com
cat /tmp/network-test.txt
ls -lh /tmp/network-test.txt
rm /tmp/network-test.txt
```

- `wget` downloads content from a URL.
- `-O` specifies the output filename.
- `cat` displays the downloaded file.
- `ls -lh` displays its size in a readable format.
- `rm` removes the temporary test file.

**Learning outcome:** Practised checking HTTPS access, downloading a web resource, inspecting the downloaded file, and cleaning up.

## 6. TCP Port Testing with nc

### Commands

```bash
nc -h
nc -vz -w 5 example.com 443
nc -vz -w 5 example.com 81
```

- `nc` is Netcat, a utility for testing network connections.
- `-v` enables verbose output.
- `-z` checks connectivity without performing a normal interactive data exchange.
- `-w 5` sets a five-second connection timeout.
- TCP port 443 is commonly used for HTTPS.

**Observed results:**

- The connection test to port 443 succeeded.
- The connection test to port 81 timed out.

**Interpretation:** The connection to port 81 was not established within the timeout. This does not, by itself, prove that the port is closed or identify the exact cause.

## 7. Route Tracing with traceroute

### Command

```bash
traceroute --version
traceroute -m 12 -w 2 8.8.8.8
```

- `-m 12` sets the maximum number of hops to 12.
- `-w 2` sets the response wait time to two seconds.

`traceroute` helps investigate the path packets take toward a destination. Some routers may not respond to its probes, so missing responses do not necessarily mean the destination is unreachable.

## 8. Path and Latency Analysis with mtr

### Commands

```bash
mtr --version
mtr -r -c 10 8.8.8.8
```

- `mtr` combines route tracing with repeated measurements.
- `-r` produces a report.
- `-c 10` specifies ten measurement cycles.

**Learning outcome:** Practised examining the route, response times, and reported packet loss. Loss at an intermediate hop must be interpreted carefully because routers may limit diagnostic responses.

## 9. Inspect Routing Decisions

### Command

```bash
ip route get 8.8.8.8
```

This shows the route Linux would select for the destination, including relevant interface and source-address information when available.

**Learning outcome:** Learned how to investigate the routing decision for a specific destination.

## 10. Inspect Listening Sockets

### Command

```bash
ss -tuln
```

| Option | Meaning                     |
|--------|-----------------------------|
| `-t`   | TCP sockets                 |
| `-u`   | UDP sockets                 |
| `-l`   | Listening sockets           |
| `-n`   | Numeric addresses and ports |

**Learning outcome:** Practised inspecting listening TCP and UDP sockets to understand which services may be accepting connections.

## 11. Basic Packet Capture with tcpdump

### Packet capture command

```bash
sudo tcpdump -ni lo -c 4 icmp
```

### Packet-generation command

```bash
ping -c 2 127.0.0.1
```

Explanation:

- `sudo` runs the capture with elevated privileges.
- `-n` prevents name resolution.
- `-i lo` selects the loopback interface.
- `-c 4` stops after four matching packets.
- `icmp` filters for ICMP traffic.

**Observed result:** Four packets were captured: two ICMP Echo Requests and two ICMP Echo Replies.

**Learning outcome:** Successfully captured and identified basic ping traffic on the loopback interface.

Packet capture should be limited to the interfaces and traffic required for the investigation because captures may contain sensitive information.

## 12. Final Troubleshooting Scenario

### Scenario

Suppose a Linux machine can:

- Ping a public IP address.
- Resolve a domain name through DNS.
- Access an HTTPS website.
- Not establish a TCP connection to a particular port within the timeout.

### Correct interpretation

These observations indicate that basic connectivity, DNS resolution, and HTTPS access are working for the tested destinations. The failed connection requires further investigation.

Possible causes include:

- No service listening on the target port.
- A firewall or network filtering rule.
- The target service being unavailable.
- A routing or connectivity issue specific to that destination or port.

**Key lesson:** A failed connection to one port does not prove that the entire internet connection is broken.

## 13. Commands Practised

```bash
ip -br addr
ip route
ping -c 4 127.0.0.1
ping -c 4 8.8.8.8
getent hosts google.com
resolvectl status
resolvectl query google.com
dig google.com
dig google.com A
curl -I https://example.com
wget -O /tmp/network-test.txt https://example.com
nc -vz -w 5 example.com 443
nc -vz -w 5 example.com 81
traceroute -m 12 -w 2 8.8.8.8
mtr -r -c 10 8.8.8.8
ip route get 8.8.8.8
ss -tuln
sudo tcpdump -ni lo -c 4 icmp
```

## 14. Cybersecurity Relevance

These skills support:

- Linux system administration.
- SOC investigations and initial triage.
- Network connectivity troubleshooting.
- DNS and web-access investigations.
- Service and port availability checks.
- Basic packet-level analysis.

## 15. Key Learning Points

- Check interfaces and routes before changing network settings.
- Use `ping` to test reachability, while remembering that ICMP can be filtered.
- Use `getent`, `resolvectl`, and `dig` to investigate DNS.
- Use `curl` and `wget` to test web access and retrieve resources.
- Use `nc` to test TCP connectivity to a specific port.
- Use `traceroute` and `mtr` to investigate network paths and latency.
- Use `ip route get` to inspect route selection.
- Use `ss` to inspect listening sockets.
- Use `tcpdump` to capture and inspect packets.
- Interpret test results together rather than relying on one command.

## Result

Successfully practised a collection of Linux network troubleshooting tools and performed a basic ICMP packet capture using `tcpdump`.

The lab strengthened practical understanding of connectivity testing, DNS resolution, TCP port diagnostics, routing, socket inspection, and packet analysis.

## Future Improvements

- Practise troubleshooting connectivity between Windows and Ubuntu over a Host-Only network.
- Learn basic firewall troubleshooting.
- Explore more focused packet captures and saved capture files.
- Revisit network throughput measurement with `iperf3` when studying performance troubleshooting.

# 📸 Screenshot Structure

The screenshots for this lab are available in the `Screenshots/` directory and are numbered in the order they were performed

**Author:** Dhruv Sudani

**Course:** Self-Learning Linux & Cybersecurity Homelab

**Module:** Linux Administration

**Lesson:** 8 – Network Troubleshooting & Diagnostics