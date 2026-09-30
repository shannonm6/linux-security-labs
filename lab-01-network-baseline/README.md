</> Markdown

# Lab 01: Linux Network & Service Baseline

## Objective 

The onjective of this lab was to establish a baseline of listening network ports and services on a newly installed Ubuntu system. I used native Linux networking tools to identify listening TCP and UDP ports, determine which processes owned these ports, and evaluate whether services were exposed to the network or restricted to the local host.

## Environment

 - Ubuntu Linux virtual machine
 - Oracle VirtualBox
 - Local user: analyst
 - Tools: ss, sudo

## Initial Network Enumeration

I first examined the TCP and UDP ports listening on the system

### Command

```bash
ss -tuln
```

### Findings

Addresses in the 127.x.x.x range are loopback addresses, meaning the traffic is directed back to the local machine. Services listening only on these addresses are accessible from the Ubuntu host itself rather than directly from other systems on the network. 

Port 5353 was listening on 0.0.0.0, indicating that the service was bound to all IPv4 interfaces rather than only the loopback interface. Further process enumeration identified Avahi as the service using this port. This represented a potentially network-accessible service that warrated further investigation. 

## Service and Process Identification

After identifying the listening ports, I used elevated privilages to determine which processes and services were associated with them. 

## Command 

```bash
sudo ss -tulpn

### Results

| Port | Protocol | Service | Purpose |
|------|----------|---------|---------|
| 53 | TCP/UDP | systemd-resolved | DNS resolution |
| 323 | UDP | chronyd | Time synchronization |
| 5353 | UDP | avahi-daemon | Multicast DNS/service discovery |
| 631 | TCP | cupsd | CUPS printing/IPP |

### Analysis

CUPS was listening on port 631 using the loopback addresses 127.0.0.1 and ::1. This indicated that the printing service was restricted to the local host rather than listening on all network interfaces. This reduced the network exposure of the service.

## Security Takeaways

This lab demonstrated how listening ports can be used to establish a network baseline and identify potentially exposed services. I learned that identifying an open port alone is not enough; the binding address and associated process must also be examined to understand the potential exposure.

Key concepts practiced:
- TCP and UDP service enumeration
- Loopback vs. network-facing interfaces
- Port and process identification
- DNS, mDNS, NTP, and IPP services
- Using elevated privileges for process enumeration
- Establishing a baseline for future comparison

## Conclusion

The system baseline identified several expected Ubuntu services. Most identified services were restricted to the local host, while Avahi was listening on all IPv4 interfaces. Establishing this baseline provides a reference that can be compared against future system states to identify unexpected listening services or configuration changes.

