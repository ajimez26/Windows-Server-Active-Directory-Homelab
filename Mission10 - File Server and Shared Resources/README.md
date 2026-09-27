# Mission 10 — File Server & Shared Resources

## Objective

Configure and manage centralized file-sharing resources within the `jimeneztech.local` domain using SMB shares, NTFS permissions, Active Directory security groups, and mapped network drives.

---

## Environment

- Server OS: Windows Server 2025
- Client OS: Windows 11 Enterprise
- Active Directory Domain: `jimeneztech.local`
- Active Directory Domain Services
- SMB File Sharing
- NTFS Permissions
- PowerShell
- Windows File Explorer

---

## Tasks Completed

- Configured centralized shared folders on Windows Server.
- Created and managed SMB network shares.
- Configured NTFS permissions for departmental resources.
- Used Active Directory security groups to control access.
- Accessed shared resources using UNC paths.
- Configured mapped network drives for easier user access.
- Verified authorized and unauthorized access to shared resources.
- Troubleshot network share and file-access issues from the Windows 11 workstation.
- Verified SMB connectivity between the client and file server.

---

## File Access Architecture

```text
Domain User
     |
     v
Windows 11 Workstation
     |
     |  UNC Path / Mapped Drive
     v
SMB File Share
     |
     v
Windows Server
     |
     +-- Share Permissions
     |
     +-- NTFS Permissions
     |
     v
Departmental Resources
