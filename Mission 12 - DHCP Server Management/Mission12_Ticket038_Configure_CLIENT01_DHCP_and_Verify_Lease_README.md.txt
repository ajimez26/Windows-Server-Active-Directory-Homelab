# Mission 12 - Ticket 038: Configure CLIENT01 for DHCP and Verify Dynamic Address Assignment

## Ticket Summary
Migrated CLIENT01 from a manually configured static IPv4 address to dynamic DHCP addressing.

After configuring the workstation to obtain its IP address and DNS configuration automatically, CLIENT01 successfully received an IPv4 lease from the Windows DHCP Server running on DC01.

Network connectivity and Active Directory DNS resolution were then tested to verify that the DHCP-provided configuration was functional.

## Environment
- Server: DC01
- Client: CLIENT01
- Client Host Name: DESKTOP-9NPE7VN
- Domain: jimeneztech.local
- DHCP Server: 192.168.56.101
- DNS Server: 192.168.56.101
- Network: 192.168.56.0/24
- DHCP Scope: JimenezTech Workstations
- Client Operating System: Windows 11

## Initial Client Configuration
Before migrating CLIENT01 to DHCP, the existing network configuration was reviewed using:

ipconfig /all

The Ethernet 2 adapter showed:

DHCP Enabled: No
IPv4 Address: 192.168.56.102
Subnet Mask: 255.255.255.0
Default Gateway: None
DNS Server: 192.168.56.101

This confirmed that CLIENT01 was using a manually assigned static IPv4 address.

## Tasks Completed
- Reviewed CLIENT01's existing IPv4 configuration
- Confirmed that CLIENT01 was using static addressing
- Changed Ethernet 2 to obtain an IPv4 address automatically
- Changed DNS configuration to obtain DNS information automatically
- Released the existing network configuration
- Requested a new DHCP lease
- Verified the DHCP-assigned IPv4 address
- Verified the DHCP server that issued the lease
- Verified the DHCP-provided DNS server
- Verified the DNS suffix
- Tested connectivity between CLIENT01 and DC01
- Tested DNS resolution for jimeneztech.local

## Client DHCP Configuration
The IPv4 properties for Ethernet 2 were changed to:

Obtain an IP address automatically

Obtain DNS server address automatically

The existing configuration was then released and renewed using:

ipconfig /release

ipconfig /renew

The resulting configuration was reviewed using:

ipconfig /all

## DHCP Lease Verification
CLIENT01 successfully received the following configuration:

DHCP Enabled: Yes
IPv4 Address: 192.168.56.130
Subnet Mask: 255.255.255.0
DHCP Server: 192.168.56.101
DNS Server: 192.168.56.101
DNS Suffix: jimeneztech.local

Lease Obtained:
September 2, 2026

Lease Expires:
September 10, 2026

The assigned address of 192.168.56.130 is significant because it is the first available address in the DHCP dynamic allocation range.

The DHCP scope was configured as:

192.168.56.120 - 192.168.56.199

The exclusion range was configured as:

192.168.56.120 - 192.168.56.129

Therefore, the effective dynamic range begins at:

192.168.56.130

CLIENT01 successfully receiving 192.168.56.130 confirmed that the configured DHCP scope and exclusion range were functioning as intended.

## Network Connectivity Verification
Connectivity between CLIENT01 and DC01 was tested using:

ping 192.168.56.101

The results showed:

Packets Sent: 4
Packets Received: 4
Packets Lost: 0
Packet Loss: 0%

This confirmed successful IP connectivity between the DHCP client and the domain controller.

## DNS Verification
Active Directory DNS resolution was tested using:

nslookup jimeneztech.local

CLIENT01 queried:

DNS Server: 192.168.56.101

The jimeneztech.local domain successfully resolved to addresses associated with DC01, including:

192.168.56.101
10.0.2.15

This confirmed that CLIENT01 received the correct DNS server through DHCP and could successfully resolve the Active Directory domain.

The nslookup output displayed the DNS server name as "UnKnown." This did not prevent DNS resolution and was not considered a failure of the DHCP or forward DNS configuration.

## Verification
Final verification confirmed:

- CLIENT01 is configured as a DHCP client
- DHCP Enabled: Yes
- CLIENT01 received 192.168.56.130
- Assigned address is within the intended dynamic range
- DHCP Server is 192.168.56.101
- DNS Server is 192.168.56.101
- DNS suffix is jimeneztech.local
- DHCP lease duration reflects the configured 8-day lease
- CLIENT01 can successfully communicate with DC01
- CLIENT01 can successfully resolve jimeneztech.local

## Evidence
01_CLIENT01_DHCP_Lease_Verification.png
- Shows DHCP Enabled as Yes
- Shows dynamically assigned IPv4 address 192.168.56.130
- Shows DHCP Server 192.168.56.101
- Shows DNS Server 192.168.56.101
- Shows jimeneztech.local DNS suffix
- Shows DHCP lease obtained and expiration dates

02_CLIENT01_DHCP_Connectivity_DNS_Verification.png
- Shows successful ping to DC01 at 192.168.56.101
- Shows 0% packet loss
- Shows successful nslookup of jimeneztech.local
- Shows DNS queries being handled by 192.168.56.101

## Commands Used
ipconfig /all

ipconfig /release

ipconfig /renew

ping 192.168.56.101

nslookup jimeneztech.local

## Skills Demonstrated
- Windows 11 Network Configuration
- DHCP Client Configuration
- Windows Server DHCP
- IPv4 Addressing
- DHCP Lease Management
- DNS Configuration
- Active Directory DNS
- TCP/IP Troubleshooting
- Command-Line Network Diagnostics
- Ping Connectivity Testing
- DNS Resolution Testing
- Client/Server Troubleshooting
- Infrastructure Validation
- Technical Documentation

## Outcome
CLIENT01 was successfully migrated from a manually assigned static IPv4 configuration to dynamic addressing provided by the Windows DHCP Server on DC01.

The workstation received 192.168.56.130 from the JimenezTech Workstations DHCP scope along with the correct DNS server and domain information.

Connectivity testing confirmed successful communication with DC01, and DNS testing confirmed that CLIENT01 could resolve the jimeneztech.local domain.

This verified the DHCP deployment end-to-end from the Windows Server infrastructure to the client workstation.