# Mission 12 - Ticket 044: Troubleshoot Windows Firewall Blocking Network Connectivity

## Ticket Summary
Investigated a network connectivity issue affecting CLIENT01 where the workstation maintained a valid DHCP configuration and functional DNS resolution but could no longer successfully ping the domain controller.

A controlled Windows Defender Firewall rule was created on DC01 to block inbound ICMPv4 traffic specifically from CLIENT01.

The resulting failure was investigated from the client workstation. DHCP and DNS functionality were verified independently, helping isolate the issue to a specific type of network traffic rather than a complete network outage.

The blocking firewall rule was identified as the root cause, disabled, and connectivity was successfully restored.

## Environment
- Server: DC01
- Client: CLIENT01
- Domain: jimeneztech.local
- DC01 IPv4 Address: 192.168.56.101
- CLIENT01 IPv4 Address: 192.168.56.150
- CLIENT01 Address Assignment: DHCP Reservation
- DHCP Server: 192.168.56.101
- DNS Server: 192.168.56.101
- Server Operating System: Windows Server 2025
- Client Operating System: Windows 11 Enterprise
- Firewall Management Tool: Windows Defender Firewall with Advanced Security

## Tasks Completed
- Verified normal connectivity before introducing the simulated incident
- Verified CLIENT01 could successfully communicate with DC01
- Verified DNS resolution was functioning normally
- Created a controlled inbound Windows Firewall rule on DC01
- Configured the rule to block ICMPv4 traffic
- Limited the rule specifically to CLIENT01
- Reproduced a network connectivity failure
- Verified CLIENT01 retained a valid DHCP configuration
- Verified DNS continued functioning during the incident
- Distinguished ICMP failure from a complete network outage
- Investigated Windows Defender Firewall rules on DC01
- Identified the blocking firewall rule
- Verified the rule's protocol and source IP scope
- Disabled the problematic firewall rule
- Retested connectivity from CLIENT01
- Confirmed successful communication was restored
- Documented the troubleshooting process and root cause

## Initial Connectivity Baseline
Before introducing the simulated incident, CLIENT01 connectivity was tested to establish a known-good baseline.

The following command was used:

ping 192.168.56.101

CLIENT01 successfully received responses from DC01 with no packet loss.

DNS functionality was also tested using:

nslookup jimeneztech.local

The domain successfully resolved using the internal DNS server at:

192.168.56.101

This established that network connectivity and DNS resolution were functioning normally before troubleshooting began.

## Simulated Incident
A controlled firewall rule was created on DC01 using Windows Defender Firewall with Advanced Security.

The rule was configured with the following properties:

Rule Name:
LAB - Block CLIENT01 ICMP

Direction:
Inbound

Rule Type:
Custom

Protocol:
ICMPv4

Remote IP Address:
192.168.56.150

Action:
Block the connection

Profiles:
Domain
Private
Public

Description:
Temporary firewall rule for Ticket 044 troubleshooting simulation.

Limiting the remote IP address to 192.168.56.150 ensured that the simulated incident affected CLIENT01 specifically rather than unnecessarily blocking ICMP traffic from the entire lab network.

## Failure Reproduction
After the firewall rule was enabled, CLIENT01 attempted to communicate with DC01 using:

ping 192.168.56.101

The ping requests failed.

This demonstrated that ICMP communication between CLIENT01 and DC01 was no longer functioning after the firewall configuration change.

## DHCP Verification
CLIENT01's network configuration was reviewed using:

ipconfig /all

The workstation retained its valid DHCP configuration, including:

IPv4 Address:
192.168.56.150

Subnet Mask:
255.255.255.0

DHCP Server:
192.168.56.101

DNS Server:
192.168.56.101

This indicated that the issue was not caused by CLIENT01 losing its DHCP lease or receiving an incorrect IPv4 configuration.

## DNS Troubleshooting
DNS functionality was tested independently using:

nslookup jimeneztech.local

Despite the failed ICMP communication, DNS resolution continued to function successfully through:

192.168.56.101

This was an important troubleshooting observation.

CLIENT01 was still capable of communicating with the server for DNS services even though ICMP echo traffic was failing.

This demonstrated that the problem was not a complete loss of connectivity between CLIENT01 and DC01.

Instead, the symptoms suggested that a specific protocol or traffic type was being filtered.

## Troubleshooting Analysis
The troubleshooting results showed:

- CLIENT01 had a valid IPv4 address
- CLIENT01 had a valid DHCP lease
- CLIENT01 had the correct DNS server
- DNS resolution was successful
- ICMP echo requests to DC01 failed

Because DHCP configuration remained valid and DNS queries were still reaching DC01 successfully, the investigation shifted away from DHCP and DNS.

The selective nature of the failure indicated that traffic filtering or firewall configuration was a likely cause.

## Root Cause Identification
Windows Defender Firewall with Advanced Security was reviewed on DC01.

The following inbound rule was identified:

LAB - Block CLIENT01 ICMP

The rule properties confirmed:

Protocol:
ICMPv4

Remote IP:
192.168.56.150

Action:
Block the connection

This matched CLIENT01's IPv4 address and the exact protocol experiencing the failure.

The firewall rule was therefore identified as the root cause of the connectivity issue.

## Resolution
The firewall rule:

LAB - Block CLIENT01 ICMP

was disabled on DC01.

The rule was disabled rather than deleted so that the lab configuration could remain available for documentation and future troubleshooting exercises without actively affecting CLIENT01.

## Post-Resolution Verification
After disabling the firewall rule, CLIENT01 tested connectivity again using:

ping 192.168.56.101

ICMP communication was successfully restored.

CLIENT01 once again received responses from DC01 with no packet loss.

DNS resolution was also tested using:

nslookup jimeneztech.local

DNS continued to function normally.

The successful ping after disabling the firewall rule confirmed that the firewall configuration was responsible for the original connectivity failure.

## Troubleshooting Methodology
This ticket demonstrated the importance of testing individual network services rather than assuming that a failed ping means a device has completely lost network connectivity.

The troubleshooting process followed:

1. Establish a known-good connectivity baseline
2. Reproduce the reported failure
3. Verify the client's IPv4 configuration
4. Verify DHCP configuration
5. Test DNS independently
6. Compare working and failing protocols
7. Investigate host-based firewall configuration
8. Identify the rule matching the affected traffic
9. Disable the problematic rule
10. Retest the original symptom
11. Confirm successful resolution

This approach helped isolate the problem systematically rather than making unnecessary changes to DHCP, DNS, or the client's network configuration.

## Verification
Final verification confirmed:

- CLIENT01 retained IPv4 address 192.168.56.150
- DHCP configuration remained valid
- DNS server remained 192.168.56.101
- DNS resolution continued functioning during the incident
- ICMP communication failed while the blocking rule was enabled
- The firewall rule specifically targeted CLIENT01
- The firewall rule specifically blocked ICMPv4
- The blocking rule was identified as the root cause
- The rule was successfully disabled
- ICMP connectivity to DC01 was restored
- Existing DHCP and DNS services remained operational

## Evidence
01_CLIENT01_Connectivity_Baseline.png
- Shows successful connectivity before the simulated incident
- Shows successful ping to DC01
- Establishes a known-good baseline

02_CLIENT01_ICMP_Ping_Failure.png
- Shows failed ICMP communication after the firewall rule was enabled
- Documents the primary symptom reported during the incident

03_CLIENT01_DNS_Still_Functional.png
- Shows successful DNS resolution during the ICMP failure
- Demonstrates that CLIENT01 retained communication with DC01 for DNS services
- Helps isolate the issue to specific network traffic

04_DC01_Blocking_Firewall_Rule_Identified.png
- Shows the Windows Defender Firewall rule responsible for the incident
- Documents the ICMPv4 configuration
- Shows CLIENT01's 192.168.56.150 address within the rule scope

05_CLIENT01_Firewall_Connectivity_Restored.png
- Shows successful ping communication after the firewall rule was disabled
- Confirms that the identified firewall rule was the root cause
- Demonstrates successful incident resolution

## Commands Used
ping 192.168.56.101

ipconfig /all

nslookup jimeneztech.local

wf.msc

## Skills Demonstrated
- Windows Server Administration
- Windows 11 Troubleshooting
- Windows Defender Firewall Administration
- Firewall Rule Analysis
- ICMP Troubleshooting
- TCP/IP Troubleshooting
- DHCP Verification
- DNS Verification
- Network Connectivity Troubleshooting
- Protocol-Level Troubleshooting
- Network Fault Isolation
- Root Cause Analysis
- Incident Reproduction
- Connectivity Testing
- Windows Networking
- Help Desk Troubleshooting Methodology
- Technical Documentation

## Outcome
A simulated network connectivity incident was successfully reproduced and resolved.

CLIENT01 maintained a valid DHCP configuration and continued communicating with DC01 for DNS services while ICMP traffic was blocked.

By testing network configuration, DNS resolution, and ICMP communication independently, the problem was isolated to protocol-specific traffic filtering rather than DHCP, DNS, or general network connectivity.

Inspection of Windows Defender Firewall on DC01 identified an inbound rule blocking ICMPv4 traffic specifically from CLIENT01 at 192.168.56.150.

The rule was disabled, and subsequent testing confirmed that ICMP communication was restored.

This ticket demonstrated a structured help desk troubleshooting process involving symptom reproduction, network-layer testing, firewall analysis, root cause identification, corrective action, and post-resolution verification.