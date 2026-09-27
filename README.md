# Windows Server & Active Directory IT Support Homelab

## Overview

This project is a hands-on Windows enterprise homelab designed to simulate common responsibilities encountered in Help Desk, IT Support, Desktop Support, and junior systems administration roles.

The environment was built using **Windows Server 2025, Windows 11 Enterprise, Active Directory Domain Services, DNS, DHCP, Group Policy, SMB file sharing, NTFS permissions, and VirtualBox**.

Rather than focusing only on initial configuration, the project developed into a structured IT support environment containing **70 simulated support tickets across 12 progressive missions**.

The tickets document troubleshooting scenarios involving user accounts, authentication and authorization, networking, DNS, DHCP, Group Policy, file shares, Windows services, firewall connectivity, workstation configuration, and general Windows support.

For each scenario, I practiced identifying symptoms, gathering evidence, isolating the affected dependency, determining root cause, implementing remediation, and verifying the resolution.

---

## Lab Environment

### Domain

**Domain:** `jimeneztech.local`

### Server

**Windows Server 2025**

Primary roles:

- Active Directory Domain Services (AD DS)
- DNS Server
- DHCP Server
- Group Policy Management
- File and SMB services

### Client

**Windows 11 Enterprise**

The workstation was joined to the `jimeneztech.local` Active Directory domain and used to simulate end-user support and workstation troubleshooting.

### Virtualization

**Oracle VirtualBox**

The environment uses separate virtual network adapters to support both internal domain communication and Internet connectivity.

---

## Lab Architecture

```text
                         Internet
                            |
                     VirtualBox NAT
                            |
                     +-------------+
                     |  CLIENT01   |
                     | Windows 11  |
                     +-------------+
                            |
                     Host-Only Network
                      192.168.56.0/24
                            |
                     +-------------+
                     |    DC01     |
                     | Server 2025 |
                     +-------------+
                            |
          +-----------------+------------------+
          |                 |                  |
     Active Directory      DNS               DHCP
          |
     Group Policy
          |
   Users / Groups / OUs
          |
    SMB File Shares
          |
   NTFS Permissions
```

The dual-network design allows the Windows 11 client to maintain Internet access while using the internal network for Active Directory, DNS, DHCP, SMB, and other domain services.

---

## Technologies & Tools

### Windows Administration

- Windows Server 2025
- Windows 11 Enterprise
- Active Directory Domain Services
- Active Directory Users and Computers
- Group Policy Management
- Windows Services
- Event Viewer
- Task Manager
- Device Manager
- Windows Update

### Networking

- TCP/IP
- IPv4 addressing
- DNS
- DHCP
- NAT
- TCP ports
- SMB
- Name resolution
- Network connectivity testing

### Identity & Access Management

- Domain users
- Organizational Units
- Security groups
- Group-based access control
- Authentication
- Authorization
- Password policies
- Account lockout policies

### File Services

- SMB shares
- UNC paths
- NTFS permissions
- ACLs
- Explicit and inherited permissions
- Mapped network drives

### Troubleshooting Tools

- PowerShell
- Command Prompt
- `ipconfig`
- `ping`
- `nslookup`
- `Test-NetConnection`
- `gpupdate`
- `gpresult`
- `w32tm`
- `sfc`
- `DISM`
- Event Viewer
- Task Manager
- Windows Services

---

# Mission Overview

The homelab was developed through **12 progressive missions**, beginning with virtualization and Windows Server deployment and progressing into integrated help desk and Windows troubleshooting scenarios.

## Mission 1 — VirtualBox & Virtualization

Built the virtualization foundation for the lab.

Key concepts:

- Virtual machine creation
- CPU and memory allocation
- Virtual disks
- NAT networking
- Host-Only networking
- Virtual network adapters
- VM troubleshooting

---

## Mission 2 — Windows Server Foundation

Deployed Windows Server and prepared the system for domain services.

Key concepts:

- Windows Server installation
- Server Manager
- Static IP configuration
- Server roles
- Network configuration
- Server administration fundamentals

---

## Mission 3 — Active Directory Domain Services

Created the Active Directory environment for the lab.

Key concepts:

- AD DS installation
- Domain controller promotion
- Domain creation
- Authentication
- Centralized identity management
- Domain administration

Domain created:

`jimeneztech.local`

---

## Mission 4 — Users, Organizational Units & Security Groups

Built the identity structure used throughout the environment.

Key concepts:

- Active Directory users
- Organizational Units
- Security groups
- Group membership
- Department organization
- Authentication vs. authorization
- Temporary access management

Departmental groups included:

- IT
- HR
- Sales
- Finance
- Marketing
- Operations

---

## Mission 5 — Group Policy

Implemented centralized Windows configuration using Group Policy.

Policies included:

- Password requirements
- Account lockout
- Legal logon banner
- Windows Firewall configuration
- Removable-storage restrictions
- Desktop configuration
- Workstation security restrictions

Troubleshooting included investigating GPO scope, OU placement, security filtering, and unintended policy application.

---

## Mission 6 — DNS

Configured and troubleshot DNS services for the Active Directory environment.

Key concepts:

- Forward lookup zones
- Reverse lookup zones
- A records
- PTR records
- Active Directory DNS
- SRV records
- Name resolution
- `nslookup`
- DNS troubleshooting

This mission reinforced the difference between basic IP connectivity and successful name resolution.

---

## Mission 7 — File Shares & NTFS Permissions

Created departmental resources and implemented group-based access.

Key concepts:

- SMB file sharing
- NTFS permissions
- ACLs
- Explicit permissions
- Inherited permissions
- Security groups
- Authorization
- Temporary access
- Access-denied troubleshooting

Departmental shares were created for areas including HR, Finance, IT, Sales, Marketing, and Operations.

---

## Mission 8 — Help Desk Ticket Simulation

Transitioned from infrastructure configuration into simulated IT support incidents.

Scenarios included:

- Password issues
- Account lockouts
- Network connectivity
- Missing mapped drives
- Access-denied errors
- Group Policy issues
- SMB connectivity
- User access requests
- Escalation and documentation

The focus shifted from simply configuring technology to using evidence to determine root cause.

---

## Mission 9 — Windows 11 Domain Join

Integrated a Windows 11 Enterprise workstation into the Active Directory environment.

Key concepts:

- Domain-join prerequisites
- DNS configuration
- Domain discovery
- Domain credentials
- Computer objects
- Domain vs. local accounts
- Workstation authentication

This mission reinforced the dependency order:

```text
Network Connectivity
        ↓
DNS / Domain Discovery
        ↓
Authorized Domain Credentials
        ↓
Domain Join
```

---

## Mission 10 — File Server & Shared Resources

Expanded centralized file-resource administration and troubleshooting.

Key concepts:

- SMB shares
- Share connectivity
- NTFS permissions
- Security groups
- UNC paths
- Mapped drives
- TCP port 445
- Effective permissions
- Access troubleshooting

A major troubleshooting principle reinforced during this mission was separating:

```text
Network Connectivity
        ↓
DNS
        ↓
SMB Connectivity
        ↓
Authentication
        ↓
Authorization
        ↓
Effective Permissions
```

---

## Mission 11 — Group Policy Workstation Management

Expanded centralized workstation administration using Group Policy.

Key concepts:

- Computer policies
- User policies
- GPO links
- OU placement
- Security filtering
- Policy scope
- `gpupdate`
- `gpresult`
- Resultant policy troubleshooting

A troubleshooting scenario also involved identifying why a workstation security policy unintentionally affected the domain controller.

---

## Mission 12 — Windows & Help Desk Troubleshooting

The final mission expanded into broader Windows support scenarios and integrated concepts from the entire lab.

Topics included:

- Windows performance troubleshooting
- Processes and services
- Resource utilization
- Event Viewer
- Windows Update
- SFC and DISM
- DHCP/APIPA troubleshooting
- DNS troubleshooting
- SMB connectivity
- Windows Firewall
- Time synchronization
- User profiles
- Storage troubleshooting
- Task Scheduler
- Environment variables
- Print services
- Credentials
- Device Manager
- Drivers
- Software installation
- Local and domain accounts
- Windows recovery

This mission emphasized selecting troubleshooting tools based on evidence rather than applying unrelated fixes.

---

# 70-Ticket Help Desk Simulation

Across the lab, I completed **70 simulated IT support tickets**.

Each ticket was designed around a support request or technical problem and documented using a structured troubleshooting process.

Typical ticket documentation included:

1. **Ticket Request**
2. **Symptoms / Initial Evidence**
3. **Investigation**
4. **Root Cause**
5. **Resolution**
6. **Verification**
7. **Evidence / Screenshots**
8. **Commands Used**
9. **Skills Demonstrated**
10. **Key Troubleshooting Principle**

The goal was not simply to resolve each problem, but to document **why each troubleshooting step was performed and what the result proved**.

---

# Featured Troubleshooting Scenarios

## SMB Connectivity & File Access

A client could reach the domain controller but could not access a shared resource.

Troubleshooting separated:

- IP connectivity
- DNS resolution
- SMB connectivity over TCP 445
- Authentication
- Authorization
- NTFS permissions
- Drive mapping

Tools included:

```powershell
Test-NetConnection <server> -Port 445
Test-Path "\\<server>\<share>"
Get-SmbShare
Get-SmbShareAccess
Get-SmbMapping
```

This reinforced that successful ping connectivity does not prove that the SMB service is reachable.

---

## DNS Troubleshooting

Several scenarios involved systems that had IP connectivity but could not resolve domain resources.

Troubleshooting included:

```powershell
ipconfig /all
nslookup <hostname>
nslookup <domain>
```

This helped distinguish:

```text
IP Connectivity ≠ DNS Resolution
```

---

## Group Policy Troubleshooting

A workstation security policy unintentionally affected the domain controller.

Instead of simply deleting the GPO, the investigation focused on:

- GPO links
- OU placement
- Policy scope
- Security filtering
- User vs. computer configuration
- Resultant policy

Verification tools included:

```powershell
gpupdate /force
gpresult /r
```

---

## Access Denied — Group Membership

A simulated HR user could sign into Windows but could not access the HR departmental resource.

Investigation showed that the user was not a member of the required HR security group.

The user was added to the appropriate security group and then signed out and back in so the new group membership would be reflected in the user's logon security token.

This scenario reinforced the distinction between:

```text
Authentication
"Who are you?"

vs.

Authorization
"What are you allowed to access?"
```

---

## Firewall & Port Connectivity

A service was reachable at the host level but communication to a required TCP port failed.

Testing with:

```powershell
Test-NetConnection <host> -Port <port>
```

helped isolate the issue to service/port connectivity rather than basic network reachability.

The appropriate Windows Firewall rule was then configured and connectivity retested.

---

# Troubleshooting Methodology

Throughout the project I developed a consistent troubleshooting methodology:

```text
Identify the user's symptoms
        ↓
Establish normal behavior
        ↓
Determine the scope
        ↓
Reproduce the problem when possible
        ↓
Check fundamental dependencies first
        ↓
Gather evidence
        ↓
Use each result to narrow the investigation
        ↓
Identify the root cause
        ↓
Apply the least disruptive appropriate remediation
        ↓
Recreate the original workflow
        ↓
Verify resolution
        ↓
Document findings
```

A major principle developed throughout the project was:

> **Avoid changing configurations that the evidence has already shown are working.**

This helped reduce unnecessary changes and encouraged evidence-based troubleshooting.

---

# Skills Demonstrated

This project provided hands-on practice with:

- Windows Server administration
- Windows 11 support
- Active Directory administration
- User and group management
- Identity and access management
- Group Policy
- DNS and DHCP
- TCP/IP networking
- Windows Firewall
- SMB file sharing
- NTFS permissions
- Windows services
- Event Viewer
- PowerShell
- Command-line troubleshooting
- Workstation troubleshooting
- Authentication and authorization
- Root cause analysis
- Technical documentation
- Help desk ticket workflows
- Incident escalation

---

# Documentation

Each mission contains documentation and supporting evidence from the lab.

Individual ticket README files include:

- Problem description
- Investigation steps
- Root cause
- Resolution
- Verification
- Commands used
- Screenshots
- Skills demonstrated
- Troubleshooting principles

This repository is intended to demonstrate both **technical configuration skills and the troubleshooting thought process used to support Windows users and infrastructure**.

---

# Career Focus

This project was built as practical preparation for entry-level roles including:

- IT Support Specialist
- Help Desk Technician
- Service Desk Analyst
- Desktop Support Technician
- IT Support Analyst
- Junior Systems Administrator

It also provides a Windows infrastructure foundation for continued development in cybersecurity and security operations.

---

## Author

**Amauris Jimenez**

New Jersey

GitHub: `github.com/ajimez26`
