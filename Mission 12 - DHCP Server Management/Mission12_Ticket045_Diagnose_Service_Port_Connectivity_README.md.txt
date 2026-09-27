# Mission 12 - Ticket 045: Diagnose Service Port Connectivity

## Ticket Summary
Investigated network and service-level connectivity between CLIENT01 and DC01 using Windows diagnostic tools.

A healthy network baseline was established using Test-NetConnection and tracert. CLIENT01 successfully reached DC01 directly across the local network.

TCP port 53, used by DNS, was then tested to verify that the DNS service was reachable at the TCP transport layer.

A controlled Windows Defender Firewall rule was created on DC01 to block inbound TCP port 53 traffic specifically from CLIENT01.

After the rule was enabled, CLIENT01 remained capable of reaching DC01 through ICMP, but TCP connectivity to port 53 failed.

This demonstrated an important troubleshooting distinction: a server can be reachable at the network level while a specific application or service port remains inaccessible.

The firewall rule was identified as the root cause, disabled, and TCP port 53 connectivity was restored.

## Environment
- Server: DC01
- Client: CLIENT01
- Domain: jimeneztech.local
- DC01 IPv4 Address: 192.168.56.101
- CLIENT01 IPv4 Address: 192.168.56.150
- CLIENT01 Address Assignment: DHCP Reservation
- DNS Server: 192.168.56.101
- Network: 192.168.56.0/24
- Server Operating System: Windows Server 2025
- Client Operating System: Windows 11 Enterprise
- Administration Tools: PowerShell and Windows Defender Firewall with Advanced Security

## Tasks Completed
- Verified network connectivity between CLIENT01 and DC01
- Used Test-NetConnection to establish a connectivity baseline
- Used tracert to examine the network path to DC01
- Confirmed CLIENT01 reached DC01 directly on the local subnet
- Tested TCP port 53 connectivity
- Established a known-good service-port baseline
- Created a controlled Windows Firewall rule
- Configured the rule to block inbound TCP port 53
- Limited the firewall rule specifically to CLIENT01
- Reproduced a service-level connectivity failure
- Confirmed DC01 remained reachable through ICMP
- Confirmed TCP port 53 became inaccessible
- Distinguished host reachability from service-port reachability
- Identified the Windows Firewall rule as the root cause
- Disabled the blocking firewall rule
- Retested TCP port 53
- Confirmed service-port connectivity was restored
- Documented the troubleshooting process

## Network Connectivity Baseline
CLIENT01 first tested connectivity to DC01 using PowerShell.

Command:

Test-NetConnection 192.168.56.101

The output showed:

ComputerName:
192.168.56.101

RemoteAddress:
192.168.56.101

InterfaceAlias:
Ethernet 2

SourceAddress:
192.168.56.150

PingSucceeded:
True

PingReplyDetails (RTT):
0 ms

This confirmed that CLIENT01 could successfully communicate with DC01.

## Network Path Analysis
The network path between CLIENT01 and DC01 was examined using:

tracert 192.168.56.101

The trace reached:

WIN-J9AV7RB7VVJ [192.168.56.101]

in one hop.

Because CLIENT01 and DC01 are located on the same 192.168.56.0/24 network, traffic can reach the destination directly without traversing an intermediate router.

This provided additional confirmation that the basic network path between the systems was functioning correctly.

## Service Port Baseline
TCP connectivity to the DNS service was tested using:

Test-NetConnection 192.168.56.101 -Port 53

Before introducing the simulated incident, TCP port 53 was reachable.

The important result was:

TcpTestSucceeded:
True

This established a known-good service-port baseline before making any firewall configuration changes.

## Simulated Incident
A controlled inbound firewall rule was created on DC01 using Windows Defender Firewall with Advanced Security.

Rule Name:

LAB - Block CLIENT01 TCP DNS

The rule was configured with:

Direction:
Inbound

Protocol:
TCP

Protocol Number:
6

Local Port:
53

Remote Port:
All Ports

Remote IP Address:
192.168.56.150

Action:
Block the connection

Profiles:
Domain
Private
Public

The rule was restricted to CLIENT01 so that the troubleshooting simulation would not unnecessarily block TCP DNS traffic from other systems.

## Failure Reproduction
After enabling the firewall rule, CLIENT01 tested basic connectivity again using:

Test-NetConnection 192.168.56.101

The result showed:

PingSucceeded:
True

This confirmed that DC01 remained reachable.

CLIENT01 then tested TCP port 53 using:

Test-NetConnection 192.168.56.101 -Port 53

The output produced a warning indicating that the TCP connection to:

192.168.56.101:53

failed.

The results showed:

RemotePort:
53

SourceAddress:
192.168.56.150

PingSucceeded:
True

TcpTestSucceeded:
False

This was the key diagnostic result of the ticket.

## Troubleshooting Analysis
The results demonstrated two different levels of connectivity.

Basic host connectivity:

PingSucceeded:
True

Service-port connectivity:

TcpTestSucceeded:
False

Because ICMP communication continued to succeed, CLIENT01 still had a functioning network path to DC01.

However, the TCP connection to port 53 failed.

This indicated that the problem was not:

- Loss of the CLIENT01 network connection
- Incorrect IPv4 addressing
- An unreachable server
- A broken local network path

Instead, the failure was specific to TCP port 53.

This narrowed the investigation toward service availability, port filtering, or firewall configuration.

## Host Reachability vs Service Reachability
One of the primary lessons demonstrated in this ticket was that successful ping communication does not guarantee that an application or network service is accessible.

A server may respond successfully to ICMP while a specific TCP port is:

- Blocked by a firewall
- Closed
- Filtered
- Not listening
- Unavailable because the associated service is stopped

Therefore, troubleshooting should test the actual service or TCP/UDP port required by the application rather than relying exclusively on ping.

Test-NetConnection provides a useful Windows troubleshooting method for making this distinction.

## Root Cause Identification
Windows Defender Firewall with Advanced Security was reviewed on DC01.

The following rule was identified:

LAB - Block CLIENT01 TCP DNS

The Protocols and Ports configuration showed:

Protocol type:
TCP

Protocol number:
6

Local port:
Specific Ports

Port:
53

This directly matched the TCP port that failed during Test-NetConnection testing.

The firewall configuration therefore explained why CLIENT01 could successfully reach DC01 while TCP port 53 remained inaccessible.

## Resolution
The firewall rule:

LAB - Block CLIENT01 TCP DNS

was disabled on DC01.

The rule was disabled rather than permanently deleted so that the configuration could remain available for documentation or future lab exercises without continuing to affect CLIENT01.

## Post-Resolution Verification
After disabling the blocking firewall rule, CLIENT01 tested TCP port 53 again using:

Test-NetConnection 192.168.56.101 -Port 53

The final test returned:

TcpTestSucceeded:
True

This confirmed that TCP port 53 connectivity had been restored.

Because connectivity returned immediately after the firewall rule was disabled, the firewall rule was confirmed as the root cause of the service-port failure.

## Troubleshooting Methodology
The incident was resolved using the following troubleshooting process:

1. Verify CLIENT01's basic network connectivity
2. Examine the network path using tracert
3. Establish a known-good TCP port baseline
4. Reproduce the service-level failure
5. Verify that the destination server remains reachable
6. Test the affected TCP port independently
7. Compare ICMP and TCP connectivity results
8. Investigate Windows Firewall configuration
9. Identify the rule affecting TCP port 53
10. Disable the problematic rule
11. Retest the affected service port
12. Confirm successful recovery

This approach allowed the problem to be isolated without unnecessarily modifying DHCP, DNS, IP addressing, or other network infrastructure.

## Verification
Final verification confirmed:

- CLIENT01 used IPv4 address 192.168.56.150
- DC01 used IPv4 address 192.168.56.101
- CLIENT01 could reach DC01 before the incident
- Test-NetConnection reported PingSucceeded as True
- tracert reached DC01 in one hop
- TCP port 53 was accessible during baseline testing
- DC01 remained reachable while TCP port 53 was blocked
- Test-NetConnection reported PingSucceeded as True during the incident
- Test-NetConnection reported TcpTestSucceeded as False during the incident
- The firewall rule targeted TCP port 53
- The firewall rule was identified as the root cause
- The blocking rule was disabled
- TCP port 53 connectivity was successfully restored
- Test-NetConnection reported TcpTestSucceeded as True after remediation

## Evidence
01_CLIENT01_Network_Path_Baseline.png
- Shows Test-NetConnection to 192.168.56.101
- Shows PingSucceeded as True
- Shows CLIENT01 source address 192.168.56.150
- Shows tracert reaching DC01 in one hop
- Establishes the healthy network baseline

02_CLIENT01_DNS_Port53_Test.png
- Shows the initial TCP port 53 connectivity test
- Establishes that TCP port 53 was accessible before the simulated incident
- Shows TcpTestSucceeded as True

03_CLIENT01_TCP53_Service_Failure.png
- Shows DC01 remained reachable
- Shows PingSucceeded as True
- Shows TCP connection to port 53 failed
- Shows TcpTestSucceeded as False
- Demonstrates host reachability despite service-port failure

04_DC01_TCP53_Blocking_Rule_Identified.png
- Shows the LAB - Block CLIENT01 TCP DNS firewall rule
- Shows TCP as the configured protocol
- Shows protocol number 6
- Shows local port 53
- Documents the configuration responsible for the incident

05_CLIENT01_TCP53_Service_Restored.png
- Shows successful TCP port 53 connectivity after remediation
- Shows TcpTestSucceeded as True
- Confirms the firewall rule was responsible for the failure
- Demonstrates successful restoration of service-port connectivity

## Commands Used
ipconfig /all

Test-NetConnection 192.168.56.101

tracert 192.168.56.101

Test-NetConnection 192.168.56.101 -Port 53

wf.msc

## Skills Demonstrated
- Windows Network Troubleshooting
- PowerShell Network Diagnostics
- Test-NetConnection
- Traceroute Analysis
- TCP/IP Troubleshooting
- TCP Port Testing
- Service-Level Connectivity Testing
- DNS Port Troubleshooting
- Windows Defender Firewall Administration
- Firewall Rule Analysis
- Network Path Analysis
- Host vs Service Reachability Analysis
- Fault Isolation
- Root Cause Analysis
- Incident Reproduction
- Post-Resolution Verification
- Help Desk Troubleshooting Methodology
- Technical Documentation

## Outcome
A simulated service-level connectivity incident was successfully reproduced, diagnosed, and resolved.

CLIENT01 could reach DC01 successfully at the network level, as demonstrated by Test-NetConnection and tracert. However, after TCP port 53 was blocked through Windows Defender Firewall, TCP service connectivity failed while ICMP connectivity continued functioning.

Test-NetConnection demonstrated this distinction by reporting:

PingSucceeded:
True

while simultaneously reporting:

TcpTestSucceeded:
False

Inspection of DC01's firewall configuration identified an inbound rule blocking TCP port 53 from CLIENT01.

After the rule was disabled, TCP port 53 testing returned TcpTestSucceeded as True, confirming successful remediation.

This ticket demonstrated the ability to troubleshoot beyond basic ping tests by analyzing network paths and testing the actual TCP port required by a network service.