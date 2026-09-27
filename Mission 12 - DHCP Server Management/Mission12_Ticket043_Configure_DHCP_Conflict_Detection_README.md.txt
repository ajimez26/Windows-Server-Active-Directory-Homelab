# Mission 12 - Ticket 043: Configure DHCP Conflict Detection

## Ticket Summary
Reviewed and configured DHCP conflict detection on DC01 to help prevent the DHCP server from leasing an IPv4 address that may already be in use by another device.

The DHCP Server was initially configured with zero conflict detection attempts. The setting was changed to one attempt, allowing the DHCP server to perform an address conflict check before leasing an address to a client.

The configuration was verified through both the DHCP Management console and PowerShell. DHCP scope statistics were also reviewed to confirm the current utilization and reservation status of the workstation scope.

## Environment
- Server: DC01
- Domain: jimeneztech.local
- DHCP Server: 192.168.56.101
- DHCP Scope: 192.168.56.0/24
- Scope Name: JimenezTech Workstations
- Scope Range: 192.168.56.120 - 192.168.56.199
- Exclusion Range: 192.168.56.120 - 192.168.56.129
- CLIENT01 Reserved Address: 192.168.56.150
- Server Operating System: Windows Server 2025
- Administration Tools: DHCP Manager and Windows PowerShell

## Tasks Completed
- Reviewed the current DHCP conflict detection configuration
- Identified that conflict detection was disabled
- Verified the initial setting was 0 attempts
- Configured DHCP to perform one conflict detection attempt
- Applied the updated DHCP server configuration
- Reopened DHCP properties to verify the change
- Verified the setting using PowerShell
- Reviewed DHCP scope utilization statistics
- Verified available addresses within the scope
- Verified the current number of addresses in use
- Verified the current reservation count

## Initial Configuration
The DHCP IPv4 server properties were reviewed through:

DHCP Manager
IPv4
Properties
Advanced

The initial configuration showed:

Conflict detection attempts: 0

This meant the DHCP server was not configured to perform a conflict detection attempt before leasing an IPv4 address to a client.

## DHCP Conflict Detection
DHCP conflict detection provides an additional mechanism for reducing the possibility of assigning an IPv4 address that is already being used on the network.

Before leasing an address, the DHCP server can perform a conflict detection attempt to determine whether the address appears to already be in use.

If a conflict is detected, the server can avoid assigning that address to another DHCP client.

This can help reduce duplicate IP address conflicts caused by situations such as:

- Manually configured static addresses within a DHCP-managed range
- Incorrect network documentation
- Legacy devices using unexpected addresses
- Addresses being used outside centralized DHCP management

## Configuration Change
The DHCP IPv4 Advanced properties were modified from:

Conflict detection attempts: 0

to:

Conflict detection attempts: 1

The configuration was then applied.

The IPv4 Advanced properties were reopened to verify that the new value remained configured.

The resulting setting showed:

Conflict detection attempts: 1

This confirmed that DHCP conflict detection had been enabled.

## PowerShell Verification
The DHCP Server configuration was independently verified using PowerShell.

Command used:

Get-DhcpServerSetting

The output confirmed:

IsDomainJoined: True
IsAuthorized: True
ConflictDetectionAttempts: 1

This verified that:

- DC01 remained joined to the domain
- The DHCP Server remained authorized
- One DHCP conflict detection attempt was configured

PowerShell provided an independent method of validating the setting rather than relying exclusively on the DHCP graphical management console.

## DHCP Scope Statistics
The current DHCP scope utilization was reviewed using:

Get-DhcpServerv4ScopeStatistics -ScopeId 192.168.56.0

The results showed:

Scope ID: 192.168.56.0
Free Addresses: 69
Addresses In Use: 1
Percentage In Use: Approximately 1.43%
Reserved Addresses: 1
Pending Addresses: 0

These statistics confirmed that the DHCP scope had significant address capacity remaining and contained the reservation previously configured for CLIENT01.

## Conflict Detection Considerations
Conflict detection can provide additional protection against duplicate IPv4 address assignments, but increasing the number of detection attempts may also introduce additional processing before addresses are leased.

For this lab environment, one conflict detection attempt was configured to demonstrate the feature while avoiding unnecessary additional checks.

A live duplicate-IP conflict was not intentionally introduced because the lab currently relies on DC01 for Active Directory, DNS, and DHCP services.

Creating an unnecessary address conflict involving the domain controller could disrupt multiple infrastructure services.

Instead, the conflict detection feature was safely configured and independently verified without destabilizing the existing lab environment.

## Verification
Final verification confirmed:

- DHCP Server remained operational
- DHCP Server remained authorized
- Initial conflict detection setting was 0
- Conflict detection was successfully changed to 1
- DHCP Manager displayed the updated setting
- PowerShell reported ConflictDetectionAttempts as 1
- DHCP Scope 192.168.56.0 remained operational
- 69 DHCP addresses were available
- 1 address was currently in use
- 1 reservation was configured
- 0 addresses were pending
- Existing DHCP infrastructure remained intact

## Evidence
01_DHCP_Conflict_Detection_Before.png
- Shows the DHCP IPv4 Advanced properties
- Shows Conflict detection attempts configured as 0
- Documents the original server configuration

02_DHCP_Conflict_Detection_Enabled.png
- Shows Conflict detection attempts configured as 1
- Demonstrates the configuration change in DHCP Manager

03_DHCP_Conflict_Detection_PowerShell_Verification.png
- Shows Get-DhcpServerSetting output
- Shows ConflictDetectionAttempts configured as 1
- Shows DHCP server authorization and domain status
- Shows DHCP scope statistics
- Shows 69 free addresses
- Shows 1 address in use
- Shows 1 reserved address
- Shows 0 pending addresses

## Commands Used
Get-DhcpServerSetting

Get-DhcpServerv4ScopeStatistics -ScopeId 192.168.56.0

## Skills Demonstrated
- Windows Server Administration
- DHCP Administration
- DHCP Conflict Detection
- IPv4 Address Management
- Duplicate IP Conflict Prevention
- DHCP Scope Management
- DHCP Scope Monitoring
- DHCP Capacity Monitoring
- PowerShell
- DHCP Management Console
- Server Configuration Verification
- Network Infrastructure Administration
- Preventive Network Administration
- Technical Documentation

## Outcome
DHCP conflict detection was successfully enabled on DC01.

The DHCP Server was initially configured with zero conflict detection attempts. The configuration was changed to one attempt through DHCP Manager and independently verified using PowerShell.

PowerShell confirmed that ConflictDetectionAttempts was set to 1 and that the DHCP server remained domain joined and authorized.

DHCP scope statistics were also reviewed, confirming 69 available addresses, one address currently in use, one reservation, and zero pending addresses.

This ticket demonstrated preventive DHCP administration by configuring a mechanism designed to reduce the risk of duplicate IPv4 address assignments while preserving the stability of the existing Active Directory, DNS, and DHCP lab infrastructure.