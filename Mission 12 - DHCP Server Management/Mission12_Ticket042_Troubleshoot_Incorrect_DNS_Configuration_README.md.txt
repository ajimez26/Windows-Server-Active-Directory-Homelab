# Mission 12 - Ticket 042: Troubleshoot Incorrect DNS Configuration on CLIENT01

## Ticket Summary
Simulated and resolved a DNS configuration issue on CLIENT01 in which the workstation maintained valid IP connectivity but was unable to resolve the jimeneztech.local domain.

CLIENT01 was intentionally configured with an incorrect DNS server address of 192.168.56.200.

Troubleshooting confirmed that CLIENT01 could successfully communicate with DC01 by IP address while DNS queries timed out.

The incorrect DNS configuration was identified and changed back to automatic DHCP-provided DNS configuration. CLIENT01 then received the correct DNS server address of 192.168.56.101 and successfully resolved the jimeneztech.local domain.

## Environment
- Server: DC01
- Client: CLIENT01
- Client Host Name: DESKTOP-9NPE7VN
- Domain: jimeneztech.local
- DHCP Server: 192.168.56.101
- Correct DNS Server: 192.168.56.101
- Simulated Incorrect DNS Server: 192.168.56.200
- CLIENT01 Reserved Address: 192.168.56.150
- DHCP Scope: 192.168.56.0/24
- Server Operating System: Windows Server 2025
- Client Operating System: Windows 11

## Tasks Completed
- Simulated an incorrect DNS configuration on CLIENT01
- Maintained DHCP-based IPv4 addressing
- Configured an incorrect DNS server manually
- Verified CLIENT01 retained its valid IPv4 address
- Verified CLIENT01 could communicate with DC01 by IP address
- Tested DNS name resolution
- Observed DNS request timeouts
- Distinguished an IP connectivity problem from a DNS problem
- Identified the incorrect DNS server configuration
- Restored automatic DNS configuration
- Renewed CLIENT01's network configuration
- Flushed the local DNS resolver cache
- Verified CLIENT01 received the correct DNS server
- Verified successful jimeneztech.local name resolution

## Simulated Incident
CLIENT01 was operating normally with the following network configuration:

IPv4 Address: 192.168.56.150
DHCP Server: 192.168.56.101
DNS Server: 192.168.56.101

To simulate a DNS-related support incident, the DNS configuration on Ethernet 2 was manually changed.

The IPv4 address remained configured to:

Obtain an IP address automatically

The DNS configuration was changed to:

Use the following DNS server addresses

Preferred DNS Server:

192.168.56.200

This created a situation where CLIENT01 still had a valid DHCP-assigned IP address but was configured to use an incorrect DNS server.

## Initial Configuration Verification
The client configuration was reviewed using:

ipconfig /all

The results confirmed:

DHCP Enabled: Yes
IPv4 Address: 192.168.56.150
Subnet Mask: 255.255.255.0
DHCP Server: 192.168.56.101
DNS Server: 192.168.56.200

This showed that CLIENT01 still had a valid network address and DHCP lease, but its DNS configuration was incorrect.

## Connectivity Testing
Basic IP connectivity to DC01 was tested using:

ping 192.168.56.101

The results showed:

Packets Sent: 4
Packets Received: 4
Packets Lost: 0
Packet Loss: 0%

This confirmed that CLIENT01 could successfully communicate with DC01 over the network.

Because the test used an IP address directly, DNS was not required for the communication to succeed.

## DNS Failure Verification
DNS name resolution was tested using:

nslookup jimeneztech.local

The query attempted to use:

DNS Server: 192.168.56.200

The request repeatedly timed out and returned:

DNS request timed out.

This demonstrated that basic network connectivity was functioning while DNS name resolution was failing.

The successful IP-based ping combined with the failed hostname lookup helped isolate the problem to DNS rather than general network connectivity.

## Root Cause Analysis
CLIENT01 had:

- A valid DHCP-assigned IPv4 address
- A valid subnet mask
- Connectivity to DC01 by IP address
- An active DHCP configuration

However, the configured DNS server was:

192.168.56.200

The correct internal DNS server for the jimeneztech.local environment was:

192.168.56.101

The incorrect DNS server configuration was therefore identified as the root cause of the name resolution failure.

## Resolution
CLIENT01's Ethernet 2 IPv4 properties were opened.

The IP configuration remained set to:

Obtain an IP address automatically

The DNS configuration was changed from the manually configured 192.168.56.200 address back to:

Obtain DNS server address automatically

CLIENT01 was then allowed to receive the DNS configuration provided by the Windows DHCP Server.

The following commands were used during recovery:

ipconfig /renew

ipconfig /flushdns

The network configuration was then verified using:

ipconfig /all

## Recovery Verification
After restoring automatic DNS configuration, CLIENT01 showed:

DHCP Enabled: Yes
IPv4 Address: 192.168.56.150
Subnet Mask: 255.255.255.0
DHCP Server: 192.168.56.101
DNS Server: 192.168.56.101
DNS Suffix: jimeneztech.local

The incorrect 192.168.56.200 DNS server was no longer present.

DNS resolution was tested again using:

nslookup jimeneztech.local

CLIENT01 successfully queried:

192.168.56.101

The jimeneztech.local domain successfully resolved to addresses associated with DC01, including:

192.168.56.101
10.0.2.15

This confirmed that DNS functionality had been restored.

## Troubleshooting Process
The incident was resolved using the following troubleshooting workflow:

1. Confirmed CLIENT01 had a valid IPv4 address
2. Reviewed the configured DNS server
3. Tested connectivity to DC01 using its IP address
4. Confirmed successful IP connectivity
5. Tested jimeneztech.local using nslookup
6. Observed DNS request timeouts
7. Compared the configured DNS server with the expected internal DNS server
8. Identified 192.168.56.200 as the incorrect DNS server
9. Restored automatic DHCP-provided DNS configuration
10. Renewed the network configuration
11. Flushed the DNS resolver cache
12. Verified DNS Server 192.168.56.101 was restored
13. Repeated the DNS lookup
14. Confirmed successful name resolution

## Verification
Final verification confirmed:

- CLIENT01 remained DHCP-enabled
- CLIENT01 retained reserved address 192.168.56.150
- CLIENT01 could communicate with DC01 by IP address
- Incorrect DNS Server 192.168.56.200 was identified
- DNS configuration was returned to automatic
- DHCP provided DNS Server 192.168.56.101
- DNS suffix remained jimeneztech.local
- nslookup successfully resolved jimeneztech.local
- Normal DNS functionality was restored

## Evidence
01_CLIENT01_Incorrect_DNS_Configuration.png
- Shows CLIENT01 with valid IPv4 address 192.168.56.150
- Shows DHCP Enabled as Yes
- Shows DHCP Server 192.168.56.101
- Shows incorrect DNS Server 192.168.56.200

02_CLIENT01_DNS_Failure_Diagnosis.png
- Shows successful ping to 192.168.56.101
- Shows 0% packet loss
- Shows failed nslookup for jimeneztech.local
- Shows DNS requests being sent to incorrect DNS Server 192.168.56.200
- Demonstrates working IP connectivity while DNS resolution fails

03_CLIENT01_DNS_Resolution_Restored.png
- Shows CLIENT01 using DNS Server 192.168.56.101
- Shows CLIENT01 retaining reserved address 192.168.56.150
- Shows DHCP Enabled as Yes
- Shows successful nslookup of jimeneztech.local
- Demonstrates successful restoration of DNS functionality

## Commands Used
ipconfig /all

ping 192.168.56.101

nslookup jimeneztech.local

ipconfig /renew

ipconfig /flushdns

## Skills Demonstrated
- Help Desk Troubleshooting
- Windows 11 Client Support
- DNS Troubleshooting
- DHCP Troubleshooting
- IPv4 Networking
- TCP/IP Troubleshooting
- DNS Client Configuration
- DNS Resolution Testing
- Ping Connectivity Testing
- nslookup
- Network Configuration Analysis
- Fault Isolation
- Root Cause Analysis
- Client/Server Troubleshooting
- Incident Resolution
- Technical Documentation

## Outcome
A simulated DNS configuration issue on CLIENT01 was successfully diagnosed and resolved.

CLIENT01 retained a valid DHCP-assigned IPv4 address and could communicate with DC01 using its IP address, but DNS requests failed because the workstation had been manually configured to use the incorrect DNS server 192.168.56.200.

Testing with ping and nslookup isolated the problem to DNS rather than general network connectivity.

The DNS configuration was returned to automatic, allowing the Windows DHCP Server to provide the correct DNS server address of 192.168.56.101.

After renewing the network configuration and clearing the DNS resolver cache, CLIENT01 successfully resolved jimeneztech.local.

This ticket demonstrated the ability to distinguish between IP connectivity and DNS name resolution problems, identify an incorrect client configuration, determine the root cause, implement a resolution, and verify restored service.