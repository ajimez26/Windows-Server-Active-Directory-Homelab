# Mission 7 — File Shares & NTFS Permissions

## Objective

Configure and manage departmental file resources using NTFS permissions, Active Directory security groups, and group-based access control within the `jimeneztech.local` domain.

## Environment & Tools

- Windows Server 2025
- Active Directory Domain Services
- Active Directory Users and Computers (ADUC)
- NTFS Permissions
- Security Groups
- Domain: `jimeneztech.local`

## Tasks Completed

- Created departmental folders for HR, Finance, IT, Sales, Marketing, and Operations.
- Configured NTFS permissions using departmental Active Directory security groups.
- Verified authorized user access to departmental resources.
- Tested and confirmed Access Denied behavior for unauthorized users.
- Added users to security groups to grant access to protected resources.
- Removed group membership when temporary access was no longer required.
- Verified permission changes through user access testing.

## Skills Demonstrated

- NTFS permission management
- Active Directory security groups
- ACL and permission configuration
- Group-based access control
- Access verification
- Authorization troubleshooting
- Temporary access management
- Principle of least privilege

## Key Concept

Access to departmental resources can be centrally managed through Active Directory security groups rather than assigning permissions individually to each user.

When a user's group membership changes, the user may need to sign out and sign back in to receive a refreshed security token containing the updated group membership.

## Mission Summary

This mission provided hands-on experience securing departmental resources with NTFS permissions and Active Directory security groups. It demonstrated how administrators can grant, verify, troubleshoot, and remove access while maintaining centralized and scalable permission management.
