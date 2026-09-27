# Mission 12 - Ticket 041: Simulate and Troubleshoot a DHCP Client Failure

## Ticket Summary
Simulated a DHCP service outage to troubleshoot a realistic client network connectivity failure.

The DHCP Server service on DC01 was intentionally stopped, causing CLIENT01 to lose its valid DHCP configuration after attempting to renew its lease.

CLIENT01 was unable to contact the DHCP server and automatically assigned itself an APIPA address in the 169.254.0.0/16 range.

The DHCP Server service was identified as the root cause, restarted on DC01, and CLIENT01 successfully renewed its lease and recovered its reserved IPv4 address.

## Environment
- Server: DC01
- Client: CLIENT01
- Client Host Name: DESKTOP-9NPE7VN
- Domain: jimeneztech.local
- DHCP Server: 192.168.56.101
- DHCP Scope: 192.168.56.0/24
- CLIENT01 Reserved Address: 192.168.56.150
- CLIENT01 MAC Address: 08-00-27-18-12-69
- DNS Server: 192.168.56.101
- Server Operating System: Windows Server 2025
- Client Operating System: Windows 11

## Tasks Completed
- Simulated a DHCP Server service outage
- Verified the DHCP Server service was stopped
- Released CLIENT01's existing DHCP lease
- Attempted to renew CLIENT01's DHCP lease
- Observed a DHCP request timeout
- Identified an APIPA address on CLIENT01
- Recognized APIPA as an indicator of DHCP failure
- Checked the DHCP Server service on DC01
- Identified the stopped DHCP service as the root cause
- Restarted the DHCP Server service
- Verified the DHCP Server service returned to a Running state
- Renewed CLIENT01's DHCP lease
- Verified CLIENT01 recovered its reserved IPv4 address
- Confirmed DHCP and DNS configuration was restored

## Simulated Incident
CLIENT01 was initially operating normally with the DHCP reservation configured during the previous ticket.

Its expected network configuration included:

IPv4 Address: 192.168.56.150
DHCP Server: 192.168.56.101
DNS Server: 192.168.56.101

To simulate a network infrastructure failure, the DHCP Server service on DC01 was stopped using PowerShell.

Command used:

Stop-Service DHCPServer

The service status was then checked using:

Get-Service DHCPServer

The result confirmed:

Status: Stopped
Name: DHCPServer

This created a controlled DHCP outage for troubleshooting purposes.

## Client-Side Failure
On CLIENT01, the existing DHCP lease was released using:

ipconfig /release

A new lease was then requested using:

ipconfig /renew

CLIENT01 returned the following error:

An error occurred while renewing interface Ethernet 2:
unable to contact your DHCP server.
Request has timed out.

The network configuration was then reviewed using:

ipconfig /all

CLIENT01 showed:

DHCP Enabled: Yes
Autoconfiguration IPv4 Address: 169.254.108.219
Subnet Mask: 255.255.0.0

The workstation was no longer using its normal 192.168.56.0/24 network configuration.

## APIPA Identification
CLIENT01 automatically assigned itself:

169.254.108.219

This address belongs to the APIPA range:

169.254.0.0/16

APIPA stands for Automatic Private IP Addressing.

Windows can automatically assign an APIPA address when a network adapter is configured for DHCP but is unable to obtain a valid lease from a DHCP server.

The combination of:

DHCP Enabled: Yes

and:

IPv4 Address: 169.254.108.219

provided an important troubleshooting indicator that CLIENT01 was configured correctly to request DHCP but could not successfully communicate with a functioning DHCP service.

## Root Cause Analysis
After identifying the DHCP timeout and APIPA address on CLIENT01, the DHCP infrastructure was investigated from DC01.

The DHCP Server service was checked using:

Get-Service DHCPServer

The service was confirmed to be:

Stopped

This identified the root cause of the incident.

CLIENT01 could send DHCP requests, but DC01's DHCP Server service was unavailable to process those requests and provide a valid lease.

## Resolution
The DHCP Server service was restored on DC01 using:

Start-Service DHCPServer

The service status was verified using:

Get-Service DHCPServer

The DHCP Server service returned to:

Running

CLIENT01 was then instructed to request another DHCP lease using:

ipconfig /renew

The resulting configuration was reviewed using:

ipconfig /all

## Recovery Verification
After the DHCP Server service was restored, CLIENT01 successfully received:

DHCP Enabled: Yes
IPv4 Address: 192.168.56.150
Subnet Mask: 255.255.255.0
DHCP Server: 192.168.56.101
DNS Server: 192.168.56.101
DNS Suffix: jimeneztech.local

CLIENT01 therefore transitioned from:

169.254.108.219

back to:

192.168.56.150

The restored address also matched the DHCP reservation previously configured for CLIENT01.

This confirmed that the DHCP service was functioning correctly again and that the client's network configuration had been successfully restored.

## Troubleshooting Process
The incident was resolved using the following troubleshooting workflow:

1. Reproduced the user's network connectivity issue
2. Attempted to renew the DHCP lease
3. Observed a DHCP request timeout
4. Reviewed the client's IPv4 configuration
5. Identified a 169.254.x.x APIPA address
6. Investigated the DHCP infrastructure
7. Found the DHCP Server service stopped
8. Restarted the DHCP Server service
9. Renewed the client DHCP lease
10. Verified restoration of the reserved IPv4 address

This demonstrated a structured approach to diagnosing a DHCP-related network connectivity problem.

## Verification
Final verification confirmed:

- DHCP Server service was successfully restored
- CLIENT01 successfully contacted the DHCP server
- CLIENT01 remained DHCP-enabled
- APIPA address was removed
- CLIENT01 recovered 192.168.56.150
- DHCP Server was identified as 192.168.56.101
- DNS Server was identified as 192.168.56.101
- DNS suffix was restored as jimeneztech.local
- CLIENT01's DHCP reservation continued functioning after recovery
- Normal DHCP operation was restored

## Evidence
01_CLIENT01_DHCP_Failure_APIPA.png
- Shows DHCP renewal timeout
- Shows CLIENT01 unable to contact the DHCP server
- Shows APIPA address 169.254.108.219
- Shows DHCP Enabled as Yes
- Demonstrates the client-side symptoms of DHCP failure

02_DHCP_Service_Stopped.png
- Shows the DHCPServer service in a Stopped state
- Demonstrates identification of the root cause on DC01

03_CLIENT01_DHCP_Service_Recovery.png
- Shows CLIENT01 after DHCP service restoration
- Shows DHCP Enabled as Yes
- Shows restored reserved address 192.168.56.150
- Shows DHCP Server 192.168.56.101
- Shows DNS Server 192.168.56.101
- Demonstrates successful client recovery

## Commands Used
DC01 PowerShell:

Stop-Service DHCPServer

Get-Service DHCPServer

Start-Service DHCPServer

CLIENT01 Command Prompt:

ipconfig /release

ipconfig /renew

ipconfig /all

ping 192.168.56.101

## Skills Demonstrated
- Help Desk Troubleshooting
- Windows Server Administration
- Windows 11 Client Support
- DHCP Troubleshooting
- DHCP Service Management
- PowerShell
- IPv4 Troubleshooting
- APIPA Identification
- DHCP Lease Troubleshooting
- Network Connectivity Troubleshooting
- Root Cause Analysis
- Client/Server Troubleshooting
- Incident Resolution
- Service Restoration
- Infrastructure Validation
- Technical Documentation

## Outcome
A simulated DHCP outage was successfully diagnosed and resolved.

After the DHCP Server service on DC01 was stopped, CLIENT01 was unable to renew its lease and automatically assigned itself the APIPA address 169.254.108.219.

The APIPA address and DHCP timeout were used as troubleshooting indicators that the workstation could not obtain a valid DHCP lease.

Investigation of DC01 revealed that the DHCP Server service was stopped.

The service was restarted, CLIENT01 renewed its DHCP configuration, and the workstation successfully recovered its reserved address of 192.168.56.150.

This ticket demonstrated a complete help desk troubleshooting workflow involving problem identification, client-side diagnostics, infrastructure investigation, root cause identification, service restoration, and verification of the final resolution.