# Mission 12 - Ticket 037: Validate and Administer DHCP Scope

## Ticket Summary
Validated the Windows Server DHCP configuration using PowerShell to confirm that the DHCP server was authorized in Active Directory and that the workstation scope was configured correctly.

During validation, the DHCP scope was discovered to be inactive. The scope was activated using PowerShell and then rechecked to confirm successful operation.

## Environment
- Server: DC01
- Domain: jimeneztech.local
- DHCP Server IP: 192.168.56.101
- DHCP Scope: 192.168.56.0/24
- Scope Name: JimenezTech Workstations
- Operating System: Windows Server 2025
- Administration Tool: Windows PowerShell

## Tasks Completed
- Verified DHCP server authorization in Active Directory
- Verified the configured IPv4 DHCP scope
- Identified that the DHCP scope was inactive
- Activated the DHCP scope using PowerShell
- Verified the scope was successfully activated
- Verified the DHCP exclusion range
- Verified DHCP DNS scope options
- Confirmed the configured DNS domain
- Confirmed the configured DNS server

## Commands Used

Get-DhcpServerInDC

Get-DhcpServerv4Scope

Set-DhcpServerv4Scope -ScopeId 192.168.56.0 -State Active

Get-DhcpServerv4ExclusionRange -ScopeId 192.168.56.0

Get-DhcpServerv4OptionValue -ScopeId 192.168.56.0

## DHCP Authorization Verification
The following command was used to verify that the DHCP server was authorized in Active Directory:

Get-DhcpServerInDC

The results confirmed:

IP Address: 192.168.56.101
DNS Name: win-j9av7rb7vvj.jimeneztech.local

This confirmed that DC01 was properly authorized to provide DHCP services within the jimeneztech.local domain.

## DHCP Scope Validation
The IPv4 scope configuration was checked using:

Get-DhcpServerv4Scope

The configured scope was successfully detected:

Scope ID: 192.168.56.0
Subnet Mask: 255.255.255.0
Start Range: 192.168.56.120
End Range: 192.168.56.199
Lease Duration: 8 days

However, the scope state was reported as:

Inactive

This meant the DHCP server would not issue addresses from the scope until it was activated.

## Resolution
The DHCP scope was activated using:

Set-DhcpServerv4Scope -ScopeId 192.168.56.0 -State Active

The scope was then checked again using:

Get-DhcpServerv4Scope

The scope state successfully changed to:

Active

This confirmed that the JimenezTech Workstations DHCP scope was ready to provide leases to clients.

## Exclusion Range Verification
The configured exclusion range was verified using:

Get-DhcpServerv4ExclusionRange -ScopeId 192.168.56.0

The results confirmed:

Start Range: 192.168.56.120
End Range: 192.168.56.129

These addresses are excluded from dynamic DHCP distribution.

The effective dynamically assignable client range therefore begins at:

192.168.56.130

## DHCP Scope Option Verification
The DHCP scope options were verified using:

Get-DhcpServerv4OptionValue -ScopeId 192.168.56.0

The results confirmed:

Option 006 - DNS Servers
192.168.56.101

Option 015 - DNS Domain Name
jimeneztech.local

The lease configuration was also displayed in the PowerShell results.

This confirmed that DHCP clients will receive DC01 as their DNS server and jimeneztech.local as their DNS domain.

## Troubleshooting
During the validation process, the DHCP scope was discovered to be inactive even though previous GUI configuration appeared to indicate that it had been activated.

PowerShell was used to identify the actual scope state and correct the configuration.

An initial PowerShell command used to retrieve the DHCP options also contained a spelling error, resulting in a CommandNotFoundException.

The command was corrected and executed successfully.

## Verification
Final verification confirmed:

- DHCP server authorized in Active Directory
- DHCP Server IP: 192.168.56.101
- DHCP Scope: 192.168.56.0/24
- DHCP Scope State: Active
- Address Range: 192.168.56.120 - 192.168.56.199
- Exclusion Range: 192.168.56.120 - 192.168.56.129
- DNS Server: 192.168.56.101
- DNS Domain: jimeneztech.local
- Lease Duration: 8 days

## Evidence
01_DHCP_Scope_Activation_PowerShell.png
- Shows DHCP server authorization
- Shows initial DHCP scope status
- Shows PowerShell-based scope validation and activation

02_DHCP_Exclusions_and_Options_PowerShell.png
- Shows configured DHCP exclusion range
- Shows DNS Server option
- Shows DNS Domain Name option
- Demonstrates PowerShell-based DHCP administration

## Skills Demonstrated
- Windows Server Administration
- DHCP Administration
- PowerShell
- Active Directory DHCP Authorization
- DHCP Scope Management
- DHCP Scope Activation
- DHCP Exclusion Management
- DHCP Scope Option Validation
- DNS and DHCP Integration
- Command-Line Troubleshooting
- Infrastructure Validation
- Technical Documentation

## Outcome
The DHCP infrastructure was successfully validated using PowerShell.

An inactive DHCP scope was identified and corrected before client deployment. The DHCP server authorization, scope status, exclusion range, DNS server, DNS domain, and lease configuration were verified successfully.

The DHCP environment is now ready for client-side lease testing.