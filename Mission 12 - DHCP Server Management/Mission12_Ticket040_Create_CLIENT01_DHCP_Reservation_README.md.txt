# Mission 12 - Ticket 040: Create DHCP Reservation for CLIENT01

## Ticket Summary
Created and tested a DHCP reservation for CLIENT01 so that the workstation consistently receives the same IPv4 address while remaining configured as a DHCP client.

CLIENT01 was assigned the reserved IPv4 address 192.168.56.150 based on its network adapter MAC address. The existing DHCP lease was released and renewed to verify that the Windows DHCP Server correctly assigned the reserved address.

## Environment
- Server: DC01
- Client: CLIENT01
- Client Host Name: DESKTOP-9NPE7VN
- Domain: jimeneztech.local
- DHCP Server: 192.168.56.101
- DHCP Scope: 192.168.56.0/24
- Scope Name: JimenezTech Workstations
- Reserved IP Address: 192.168.56.150
- CLIENT01 MAC Address: 08-00-27-18-12-69
- DNS Server: 192.168.56.101
- Server Operating System: Windows Server 2025
- Client Operating System: Windows 11

## Tasks Completed
- Identified CLIENT01's MAC address
- Selected an available address within the DHCP scope
- Created a DHCP reservation for CLIENT01
- Associated the reservation with CLIENT01's MAC address
- Configured the reservation to support DHCP and BOOTP
- Released CLIENT01's existing DHCP lease
- Requested a new DHCP lease
- Verified CLIENT01 received the reserved IPv4 address
- Confirmed CLIENT01 remained configured for DHCP
- Verified the DHCP server and DNS server information
- Confirmed the configured 8-day lease duration

## Initial DHCP Configuration
Before creating the reservation, CLIENT01 had previously received the following dynamic address:

192.168.56.130

CLIENT01's network adapter MAC address was:

08-00-27-18-12-69

The DHCP scope was configured as:

192.168.56.120 - 192.168.56.199

The exclusion range was:

192.168.56.120 - 192.168.56.129

The effective dynamically available portion of the scope therefore began at:

192.168.56.130

A DHCP reservation was created to provide CLIENT01 with a predictable address while keeping the workstation centrally managed through DHCP.

## DHCP Reservation Configuration
The following reservation was created in DHCP Manager:

Reservation Name: CLIENT01
Reserved IP Address: 192.168.56.150
MAC Address: 08-00-27-18-12-69
Description: Reserved IP Address for CLIENT01
Supported Type: Both

The reservation associated CLIENT01's physical network adapter address with 192.168.56.150.

This allows CLIENT01 to continue requesting its configuration through DHCP while consistently receiving the reserved address.

## Client Lease Renewal
After creating the reservation, CLIENT01's existing DHCP configuration was released and renewed.

Commands used:

ipconfig /release

ipconfig /renew

The updated configuration was then reviewed using:

ipconfig /all

## Reservation Verification
After renewing the DHCP lease, CLIENT01 received:

DHCP Enabled: Yes
IPv4 Address: 192.168.56.150
Subnet Mask: 255.255.255.0
DHCP Server: 192.168.56.101
DNS Server: 192.168.56.101
DNS Suffix: jimeneztech.local

Physical Address:

08-00-27-18-12-69

The physical address matched the MAC address configured in the DHCP reservation.

The combination of:

DHCP Enabled: Yes

and:

IPv4 Address: 192.168.56.150

confirmed that the address was assigned through DHCP rather than being manually configured on CLIENT01.

## Lease Verification
CLIENT01 received a new lease after the reservation was created.

Lease Obtained:
September 5, 2026 1:08 PM

Lease Expires:
September 13, 2026 1:08 PM

The lease dates reflected the configured 8-day DHCP lease duration.

## Static IP vs DHCP Reservation
A manually configured static IP address is entered directly on the client workstation.

A DHCP reservation allows the workstation to remain configured to obtain its address automatically while the DHCP server consistently assigns a predetermined address based on the client's MAC address.

Using a DHCP reservation provides centralized address management while still giving the device a predictable IPv4 address.

This can be useful for systems that need consistent addressing without requiring administrators to manually configure network information directly on each endpoint.

## Verification
Final verification confirmed:

- DHCP reservation successfully created
- Reservation Name: CLIENT01
- Reserved IP Address: 192.168.56.150
- CLIENT01 MAC Address: 08-00-27-18-12-69
- CLIENT01 remained DHCP-enabled
- CLIENT01 successfully received 192.168.56.150
- DHCP Server remained 192.168.56.101
- DNS Server remained 192.168.56.101
- DNS suffix remained jimeneztech.local
- MAC address matched the configured reservation
- Lease duration remained 8 days

## Evidence
01_CLIENT01_DHCP_Reservation.png
- Shows the CLIENT01 reservation in DHCP Manager
- Shows reserved IPv4 address 192.168.56.150
- Demonstrates server-side DHCP reservation configuration

02_CLIENT01_Reserved_IP_Verification.png
- Shows DHCP Enabled as Yes
- Shows CLIENT01 receiving 192.168.56.150
- Shows physical address 08-00-27-18-12-69
- Shows DHCP Server 192.168.56.101
- Shows DNS Server 192.168.56.101
- Shows DHCP lease obtained and expiration dates

## Commands Used
ipconfig /release

ipconfig /renew

ipconfig /all

## Skills Demonstrated
- Windows Server Administration
- DHCP Administration
- DHCP Reservation Management
- DHCP Lease Management
- IPv4 Address Management
- MAC Address Identification
- Windows 11 Network Configuration
- Client/Server Networking
- DHCP Client Troubleshooting
- Centralized Network Administration
- Windows DHCP Management Console
- TCP/IP Configuration
- Technical Documentation

## Outcome
A DHCP reservation was successfully created for CLIENT01 using its MAC address.

After releasing and renewing the workstation's DHCP lease, CLIENT01 changed from its previous dynamically assigned address of 192.168.56.130 to the reserved address of 192.168.56.150.

CLIENT01 remained configured as a DHCP client and continued receiving the correct DNS and domain configuration from DC01.

This demonstrated how DHCP reservations can provide predictable client addressing while maintaining centralized network configuration and administration through Windows Server DHCP.