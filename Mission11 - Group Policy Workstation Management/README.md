# Mission 11 — Group Policy Workstation Management

## Objective

Use Active Directory Group Policy to centrally configure, secure, and manage Windows workstations within the `jimeneztech.local` domain.

---

## Environment

- Server OS: Windows Server 2025
- Client OS: Windows 11 Enterprise
- Active Directory Domain: `jimeneztech.local`
- Group Policy Management
- Active Directory Users and Computers
- PowerShell / Command Prompt

---

## Tasks Completed

- Created and configured Group Policy Objects (GPOs).
- Applied centralized settings to domain users and workstations.
- Worked with both Computer Configuration and User Configuration policies.
- Configured password and account-lockout policies.
- Applied workstation security restrictions.
- Configured Windows Firewall policies.
- Implemented removable-storage controls.
- Configured logon policies and workstation settings.
- Linked GPOs to the appropriate Active Directory scope.
- Forced policy updates on the Windows 11 workstation.
- Verified which policies were successfully applied.
- Troubleshot GPO scope and policy-application issues.

---

## Group Policy Architecture

```text
Active Directory
       |
       v
Organizational Unit
       |
       v
Linked GPO
       |
       +-- Computer Configuration
       |
       +-- User Configuration
       |
       v
Domain Workstation / User
