# Mission 9 - Windows 11 Domain Join & Active Directory Client Deployment

## Objective

Deploy a Windows 11 Enterprise workstation, configure network connectivity within a VirtualBox lab environment, and successfully join the client to the **jimeneztech.local** Active Directory domain. Throughout the deployment, diagnose and resolve domain connectivity issues using Microsoft's built-in networking and Active Directory diagnostic tools.

---

## Environment

### Hypervisor
- Oracle VirtualBox 7.1.18

### Domain Controller
- Windows Server 2025 Standard Evaluation
- Hostname: DC01
- Domain: jimeneztech.local

### Client Workstation
- Windows 11 Enterprise Evaluation
- Hostname: CLIENT01

### Network Configuration

**DC01**
- Adapter 1: NAT
- Adapter 2: Host-Only Adapter

**CLIENT01**
- Adapter 1: NAT
- Adapter 2: Host-Only Adapter

---

## Tasks Completed

### Windows 11 Client Deployment

- Created the CLIENT01 virtual machine.
- Installed Windows 11 Enterprise Evaluation.
- Completed the initial Windows setup.
- Installed VirtualBox Guest Additions.
- Verified system functionality before network configuration.

---

### Network Configuration

Configured dual-network adapters to support:

- Internet connectivity through NAT
- Internal Active Directory communication through the Host-Only network

Configured CLIENT01 with:

- Static IP Address
- Subnet Mask
- Preferred DNS Server pointing to DC01

Validated network configuration using:

- ipconfig /all
- ping
- nslookup

---

### Active Directory Connectivity Troubleshooting

During the initial domain join attempt, CLIENT01 was unable to locate the Active Directory domain despite successful communication with the domain controller.

Performed a systematic troubleshooting process by validating:

- DNS Server health
- Forward Lookup Zones
- Reverse Lookup Zones
- SRV Records
- DNS Interface Bindings
- Windows Firewall rules
- Active Directory-integrated DNS
- Network adapter configuration

Used Microsoft's built-in diagnostic utilities to verify the health of the Domain Controller and DNS infrastructure.

---

### Root Cause Analysis

The Domain Controller and DNS services were operating correctly throughout the investigation.

The issue was isolated to CLIENT01's network configuration. Windows was prioritizing the NAT adapter for DNS resolution instead of the Host-Only adapter used for Active Directory communication.

After forcing the client to use the Host-Only network for DNS resolution, Active Directory name resolution succeeded and the workstation successfully joined the domain.

---

### Domain Join

Successfully joined CLIENT01 to:

**jimeneztech.local**

Verified:

- Active Directory authentication
- DNS name resolution
- Domain membership
- Successful domain logon

---

## Skills Demonstrated

### Windows Administration

- Windows Server 2025 Administration
- Windows 11 Enterprise Deployment
- Active Directory Domain Services
- Client Domain Join

### Networking

- IPv4 Configuration
- DNS Administration
- Active Directory DNS
- NAT Networking
- Host-Only Networking
- Network Adapter Configuration

### Troubleshooting

- Root Cause Analysis
- Microsoft DCDIAG
- DNS Validation
- SRV Record Verification
- Network Connectivity Testing
- Active Directory Diagnostics
- Structured Troubleshooting Methodology

---

## Screenshots

- Windows 11 Installation
- Virtual Network Adapter Configuration
- DNS Manager
- Forward Lookup Zone
- Reverse Lookup Zone
- DNS Records
- Successful DCDIAG Results
- Successful NSLOOKUP
- Domain Join Confirmation
- Domain Login Screen

---

## What I Learned

This mission reinforced the critical role DNS plays within an Active Directory environment. While basic network connectivity was functioning correctly, domain authentication depended entirely on proper DNS communication.

Rather than rebuilding the environment when the initial domain join failed, I followed a structured troubleshooting process to validate each component individually. By eliminating potential causes one at a time, I isolated the issue to the client workstation's network adapter configuration and restored proper communication with the Domain Controller.

This experience strengthened my understanding of Active Directory, Windows networking, and the importance of systematic troubleshooting when resolving infrastructure issues.

---

## Mission Summary

Successfully deployed a Windows 11 Enterprise workstation within a VirtualBox Active Directory lab, configured networking for domain communication, diagnosed and resolved a DNS-related domain join issue, and successfully integrated CLIENT01 into the **jimeneztech.local** Active Directory domain using Microsoft's built-in administrative and diagnostic tools.