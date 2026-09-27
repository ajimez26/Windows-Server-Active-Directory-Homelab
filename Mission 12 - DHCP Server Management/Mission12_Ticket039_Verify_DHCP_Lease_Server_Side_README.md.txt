# Mission 12 - Ticket 039: Verify DHCP Lease from the Server Side

## Ticket Summary
Verified from DC01 that the Windows DHCP Server successfully recorded the DHCP lease assigned to CLIENT01.

The lease was first reviewed through the DHCP Management console and then verified using PowerShell. The client IP address, hostname, client identifier, scope, lease state, and expiration information were reviewed to confirm that the server-side DHCP record corresponded with CLIENT01.

## Environment
- Server: DC01
- Client: CLIENT01
- Client Host Name: DESKTOP-9NPE7VN
- Domain: jimeneztech.local
- DHCP Server: 192.168.56.101
- DHCP Scope: 192.168.56.0/24
- Scope Name: JimenezTech Workstations
- CLIENT01 DHCP Address: 192.168.56.130
- CLIENT01 Client ID / MAC Address: 08-00-27-18-12-69
- Server Operating System: Windows Server 2025
- Client Operating System: Windows 11

## Tasks Completed
- Opened the DHCP Management console on DC01
- Reviewed active DHCP address leases
- Located CLIENT01's DHCP lease
- Verified the assigned IPv4 address
- Verified the CLIENT01 hostname
- Verified the DHCP lease expiration
- Verified the lease type
- Queried DHCP leases using PowerShell
- Queried a specific DHCP lease by IP address
- Verified the DHCP Client ID
- Confirmed the DHCP lease was in an Active state
- Correlated the server-side lease with CLIENT01's network configuration

## DHCP Manager Verification
The DHCP Management console was opened on DC01 and the following location was reviewed:

DHCP
DC01
IPv4
Scope [192.168.56.0] JimenezTech Workstations
Address Leases

The Address Leases console displayed an active lease for:

Client IP Address: 192.168.56.130
Client Name: DESKTOP-9NPE7VN
Type: DHCP
Lease Expiration: 9/10/2026

This confirmed that the Windows DHCP Server had recorded the lease previously assigned to CLIENT01.

## PowerShell Scope Lease Verification
PowerShell was used to retrieve the leases associated with the workstation DHCP scope.

Command used:

Get-DhcpServerv4Lease -ScopeId 192.168.56.0

The results displayed CLIENT01 with the following information:

IP Address: 192.168.56.130
Scope ID: 192.168.56.0
Client ID: 08-00-27-18-12-69
Host Name: DESKTOP-9NPE7VN
Address State: Active
Lease Expiration: 9/10/2026

This confirmed that CLIENT01 had an active lease within the JimenezTech Workstations scope.

## Specific IP Lease Lookup
A second PowerShell query was performed using CLIENT01's assigned IPv4 address.

Command used:

Get-DhcpServerv4Lease -IPAddress 192.168.56.130

The command returned the lease associated specifically with 192.168.56.130.

The returned information confirmed:

IP Address: 192.168.56.130
Scope ID: 192.168.56.0
Client ID: 08-00-27-18-12-69
Host Name: DESKTOP-9NPE7VN
Address State: Active

This demonstrated how an administrator can identify the DHCP client associated with a specific IP address during troubleshooting.

## Client and Server Correlation
The server-side DHCP information was compared with the network information previously obtained directly from CLIENT01.

CLIENT01 had reported:

IPv4 Address: 192.168.56.130
DHCP Server: 192.168.56.101

The physical address previously displayed on CLIENT01 was:

08-00-27-18-12-69

The DHCP server lease record showed:

IP Address: 192.168.56.130
Client ID: 08-00-27-18-12-69
Host Name: DESKTOP-9NPE7VN
Address State: Active

The matching IP address and Client ID confirmed that the DHCP lease recorded on DC01 belonged to CLIENT01.

## Verification
Final verification confirmed:

- CLIENT01 has an active server-side DHCP lease
- CLIENT01 IP Address: 192.168.56.130
- DHCP Scope: 192.168.56.0/24
- Client ID: 08-00-27-18-12-69
- Host Name: DESKTOP-9NPE7VN
- Address State: Active
- Lease record is visible through DHCP Manager
- Lease record is retrievable through PowerShell
- A specific lease can be located using its IP address
- Server-side DHCP information matches CLIENT01's client-side configuration

## Evidence
01_DHCP_Server_Address_Lease.png
- Shows CLIENT01's 192.168.56.130 lease in DHCP Manager
- Shows the client hostname
- Shows the DHCP lease expiration
- Shows the lease type as DHCP

02_DHCP_Lease_PowerShell_Verification.png
- Shows all leases within the 192.168.56.0 scope
- Shows CLIENT01's active lease
- Shows Client ID 08-00-27-18-12-69
- Shows the CLIENT01 hostname
- Shows the specific IP address lookup for 192.168.56.130

## Commands Used
Get-DhcpServerv4Lease -ScopeId 192.168.56.0

Get-DhcpServerv4Lease -IPAddress 192.168.56.130

## Skills Demonstrated
- Windows Server Administration
- DHCP Administration
- DHCP Lease Management
- DHCP Management Console
- PowerShell
- IPv4 Address Management
- DHCP Client Identification
- MAC Address / Client ID Correlation
- Client-to-Server Troubleshooting
- Network Infrastructure Validation
- Windows 11 Client Support
- Command-Line Administration
- Technical Documentation

## Outcome
The DHCP lease assigned to CLIENT01 was successfully verified from the server side.

DHCP Manager confirmed that 192.168.56.130 had been leased to CLIENT01, while PowerShell provided additional verification of the scope, Client ID, hostname, lease state, and expiration information.

The Client ID matched CLIENT01's physical network adapter address, confirming that the workstation, assigned IP address, and server-side DHCP lease record all corresponded to the same device.

This ticket demonstrated how DHCP issues can be investigated from the infrastructure side after performing client-side DHCP troubleshooting and verification.