# Mission 12 — Windows & Help Desk Troubleshooting

## Objective

Apply the Windows Server, Active Directory, networking, security, and workstation-management skills developed throughout the homelab to a comprehensive series of simulated IT support incidents.

This mission focused on structured troubleshooting, root-cause analysis, remediation, verification, and technical documentation across a Windows enterprise environment.

---

## Environment

- Windows Server 2025
- Windows 11 Enterprise
- Active Directory Domain Services
- Domain: `jimeneztech.local`
- DNS
- DHCP
- Group Policy
- SMB / NTFS
- Windows Firewall
- PowerShell / Command Prompt
- Oracle VirtualBox

---

## Mission Scope

Mission 12 expanded the help desk simulation with tickets covering a wide range of Windows and enterprise support scenarios.

Troubleshooting areas included:

- Active Directory accounts and authentication
- DNS and name resolution
- DHCP and IP configuration
- Network connectivity
- SMB file sharing
- NTFS permissions
- Mapped network drives
- Windows Firewall
- Windows services
- Processes and system performance
- Event Viewer
- Windows Update
- Task Scheduler
- Device and driver troubleshooting
- User profiles
- Stored credentials
- Environment variables
- Windows time synchronization
- Storage troubleshooting
- Print services
- Windows system-file repair
- Access and authorization issues

---

## Troubleshooting Tools

Tools and commands used throughout the mission included:

- `ipconfig /all`
- `ping`
- `nslookup`
- `Test-NetConnection`
- `Test-Path`
- `gpupdate /force`
- `gpresult /r`
- `w32tm`
- `sfc`
- `DISM`
- `Get-SmbShare`
- `Get-SmbShareAccess`
- `Get-SmbMapping`
- Task Manager
- Resource Monitor
- Event Viewer
- Windows Services
- Device Manager
- Active Directory Users and Computers

---

## Troubleshooting Methodology

A structured troubleshooting process was used throughout the mission:

```text
Identify the symptoms
        ↓
Establish expected behavior
        ↓
Determine the scope
        ↓
Reproduce the issue
        ↓
Check underlying dependencies
        ↓
Gather evidence
        ↓
Narrow down the root cause
        ↓
Apply the least disruptive remediation
        ↓
Recreate the original workflow
        ↓
Verify the resolution
        ↓
Document the outcome
