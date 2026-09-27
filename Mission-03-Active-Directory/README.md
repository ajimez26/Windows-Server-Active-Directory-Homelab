# Mission 3 — Active Directory Domain Services

## Objective

Install and configure Active Directory Domain Services and establish the centralized Windows domain used throughout the homelab.

## Environment

- Windows Server 2025 Standard Evaluation
- Active Directory Domain Services (AD DS)
- DNS
- Oracle VirtualBox
- Domain: `jimeneztech.local`

## Tasks Completed

- Installed the Active Directory Domain Services role using Server Manager.
- Promoted the Windows Server system to a domain controller.
- Created the `jimeneztech.local` Active Directory domain.
- Configured the Directory Services Restore Mode (DSRM) password during domain-controller promotion.
- Verified that DNS and Global Catalog options were configured as part of the domain-controller deployment.
- Restarted the server following domain-controller promotion.
- Verified successful domain authentication using the domain Administrator account.
- Opened Active Directory Users and Computers to verify the new domain.
- Confirmed that `jimeneztech.local` was available for centralized identity and resource administration.
- Prepared the domain structure for users, organizational units, security groups, Group Policy, and domain-joined workstations used in later missions.

## Domain Architecture

```text
jimeneztech.local
        |
        └── Domain Controller
                |
                ├── Active Directory Domain Services
                ├── DNS
                ├── Users
                ├── Computers
                ├── Security Groups
                └── Group Policy
