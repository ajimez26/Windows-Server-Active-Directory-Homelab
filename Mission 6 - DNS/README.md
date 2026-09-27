# Mission 6 — DNS

## Objective

Configure and troubleshoot DNS services within the `jimeneztech.local` Active Directory environment to provide reliable hostname and IP address resolution.

## Environment & Tools

- Windows Server 2025
- Active Directory Domain Services
- DNS Server
- DNS Manager
- Windows 11 Enterprise
- `nslookup`
- Domain: `jimeneztech.local`

## Tasks Completed

- Configured and managed DNS within the Active Directory environment.
- Worked with Forward and Reverse Lookup Zones.
- Created and verified A records for hostname-to-IP resolution.
- Created and verified PTR records for IP-to-hostname resolution.
- Configured an IPv4 Reverse Lookup Zone.
- Worked with secure dynamic DNS updates.
- Used `nslookup` to test forward and reverse DNS resolution.
- Troubleshot DNS and name-resolution issues between lab systems.

## Skills Demonstrated

- DNS administration
- Forward and reverse lookup zones
- A and PTR record management
- DNS name resolution
- Active Directory DNS fundamentals
- `nslookup`
- DNS troubleshooting
- Network service verification

## Key Concept

DNS translates names and IP addresses so systems and services can locate each other.

- **A Record:** Hostname → IP address
- **PTR Record:** IP address → Hostname
- **SRV Record:** Helps domain clients locate services such as domain controllers

Successful network connectivity does not automatically mean DNS is functioning correctly, so DNS resolution should be tested independently during troubleshooting.

## Mission Summary

This mission provided hands-on experience configuring and troubleshooting DNS in an Active Directory environment. It established the name-resolution infrastructure required for domain clients to reliably locate servers and Active Directory services.
