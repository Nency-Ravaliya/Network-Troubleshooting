# Network-Troubleshooting

# Troubleshooting Google.com Connectivity Issues

This guide outlines a series of commands to diagnose issues with accessing google.com. Follow these steps to determine where the problem lies.

## 1. `ping`
**Purpose:** Check if google.com is reachable and measure the latency.  
**Command:**
```bash
ping google.com
```

## Expected Output:

```
PING google.com (142.250.190.14): 56 data bytes
64 bytes from 142.250.190.14: icmp_seq=0 ttl=113 time=12.3 ms
64 bytes from 142.250.190.14: icmp_seq=1 ttl=113 time=11.8 ms
```

### Explanation: If you get replies, it means the network path to google.com is operational. If you see "Request timed out" or no response, there might be a network issue or google.com might be down.

## 2. `traceroute`
**Purpose:** Identify the route packets take to reach google.com and see where delays or failures occur.
**Command:**

`traceroute google.com`

## Expected Output:
```
traceroute to google.com (142.250.190.14), 30 hops max, 60 byte packets
 1  router.local (192.168.1.1)  1.234 ms  1.987 ms  1.543 ms
 2  isp-gateway (203.0.113.1)  10.234 ms  10.987 ms  10.654 ms
 3  google.com (142.250.190.14)  15.123 ms  15.456 ms  15.789 ms
```

### Explanation: This shows each hop between your computer and google.com. If you see * * *, it means a hop didn’t respond. Persistent * * * or high latencies may indicate where the problem is.

## 3. `netstat`
**Purpose:** Check if any local services are using network ports that might affect connectivity.
**Command:**

`netstat -tuln`  

## Expected Output:

```
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN
tcp6       0      0 :::80                   :::*                    LISTEN
```

### Explanation: This shows the open ports and services running on your local machine. Ensure there are no unexpected services blocking necessary ports.

## 4. `telnet`
**Purpose:** Check connectivity to specific ports on google.com.
**Command:**

`telnet google.com 80`

## Expected Output:
```
Trying 142.250.190.14...
Connected to google.com.
Escape character is '^]'.
```

### Explanation: If the connection is successful, you’ll get a blank screen or a response from the server. If it fails, there may be issues with connectivity or the port might be blocked.

## 5. `tcpdump`
**Purpose:** Capture and analyze network packets to see if requests to google.com are being sent and received correctly.
**Command:**

`sudo tcpdump -i eth0 host google.com`

## Expected Output:
```
15:30:01.123456 IP your-computer.12345 > google.com.http: Flags [P.], seq 123456:123489, ack 654321, win 12345, length 33
15:30:01.234567 IP google.com.http > your-computer.12345: Flags [.], ack 123489, win 12345, length 0
```

## 6. `nslookup`

**Purpose:** Query the Domain Name System (DNS) to get the IP address of google.com.

**Command:**

```bash
nslookup google.com
```

```
Server:         192.168.1.1
Address:        192.168.1.1#53

Non-authoritative answer:
Name:   google.com
Address: 142.250.190.14
```

### Explanation: This checks if your DNS server can resolve google.com to its IP address. If it fails, there might be an issue with your DNS configuration or the server you're querying.

## 7. `dig`
**Purpose:** Provides detailed DNS query information about google.com, including its IP address and authoritative DNS servers.

**Command:**
`dig google.com`

```
;; ANSWER SECTION:
google.com.     299 IN  A   142.250.190.14
```

### Explanation: This command gives a detailed view of DNS queries. It’s useful for seeing how DNS queries are resolved and finding out additional details about the domain's DNS settings.

## 8. `curl`
Purpose: Test HTTP/HTTPS connectivity to google.com.

**Command:**

`curl -I https://www.google.com`

```
HTTP/2 200 
content-type: text/html; charset=ISO-8859-1
date: Tue, 10 Sep 2024 12:00:00 GMT
```

### Explanation: This shows if an HTTP connection to google.com is possible and provides some details about the response. If it fails, the issue might be with web access or firewall rules.

## 9. `arp`
**Purpose:** View and manage the ARP (Address Resolution Protocol) table, which maps IP addresses to MAC addresses.

**Command:**
`arp -a`

`google.com (142.250.190.14) at 00:aa:bb:cc:dd:ee [ether] on eth0`

### Explanation: This shows the current ARP table entries and maps known IP addresses to MAC addresses. If your system cannot resolve IP-to-MAC, it may point to a local network issue.

## 10. `systemctl`
**Purpose:** Check if network services are running properly on your machine.

**Command:**
`systemctl status NetworkManager`

```
● NetworkManager.service - Network Manager
   Loaded: loaded (/lib/systemd/system/NetworkManager.service; enabled; vendor preset: enabled)
   Active: active (running)
```
### Explanation: Ensures that the network manager service is running correctly. If it's inactive or failed, network configuration might not work properly.

# Network Troubleshooting Summary

## Summary

- **ping:** Verify basic connectivity.
- **traceroute:** Identify routing path and potential delays.
- **netstat:** Check local network connections.
- **telnet:** Test connectivity to specific ports.
- **tcpdump:** Capture and analyze network traffic.
- **nslookup:** Query DNS and get the IP address of a domain.
- **dig:** Provides detailed DNS query information.
- **curl:** Test HTTP/HTTPS connectivity.
- **arp:** Manage ARP table entries (IP to MAC address mappings).
- **systemctl:** Ensure network services are running properly.

By using these commands in sequence, you can pinpoint where the issue might be occurring—whether it’s a connectivity problem, a DNS resolution issue, or something on the local machine.
