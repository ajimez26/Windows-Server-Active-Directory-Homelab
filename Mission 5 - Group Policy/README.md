# Mission 5 — Group Policy

## Objective

Configure and manage Group Policy within the `jimeneztech.local` domain to centrally control security settings and workstation behavior.

## Environment & Tools

- Windows Server 2025
- Active Directory Domain Services
- Group Policy Management
- Windows 11 Enterprise
- Domain: `jimeneztech.local`

## Tasks Completed

- Created and configured Group Policy Objects (GPOs).
- Worked with both Computer Configuration and User Configuration policies.
- Linked policies to the appropriate Active Directory scope.
- Configured password and account security policies.
- Applied workstation restrictions and security settings.
- Used `gpupdate /force` to refresh Group Policy.
- Used `gpresult` to verify applied policies.
- Troubleshot GPO scope and policy application issues.

## Skills Demonstrated

- Group Policy administration
- GPO creation and configuration
- User and computer policy management
- Policy scope and linking
- Security policy configuration
- Group Policy troubleshooting
- Policy verification

## Key Concept

Group Policy allows administrators to centrally configure and enforce settings across domain users and computers.

A successful policy update does not necessarily prove that the intended setting was applied. Tools such as `gpresult` can be used to verify which policies actually reached a user or computer.

## Mission Summary

This mission introduced centralized Windows configuration through Group Policy. It provided hands-on experience creating, applying, verifying, and troubleshooting policies used to manage security settings and workstation behavior across an Active Directory environment.
