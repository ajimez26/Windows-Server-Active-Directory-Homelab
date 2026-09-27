# Ticket 070 – Troubleshoot HR Shared Folder Access

## Ticket Summary

A domain user, Sarah Lopez, reported that she could no longer access the HR department's shared files from CLIENT01.

The shared resources had worked previously, and no other users had reported the same issue.

This ticket served as the final troubleshooting capstone for the Windows Server / Help Desk homelab. The incident was investigated from the initial user complaint without being provided the root cause in advance.

---

## Environment

- Hypervisor: Oracle VirtualBox
- Domain Controller: DC01
- Client Workstation: CLIENT01
- Client OS: Windows 11 Enterprise Evaluation
- Server OS: Windows Server 2025 Standard Evaluation
- Active Directory Domain: jimeneztech.local
- User: Sarah Lopez
- User Account: JIMENEZTECH\slopez
- Security Group: HR_SG
- Shared Resource: HR department folder
- Mapped Company Share: S:

---

## Ticket Request

### User Report

Sarah Lopez reported:

> "I logged into my computer this morning and I can use Windows normally, but I can't access the company shared folders. I tried opening the department share and Windows says it can't access it. I need the HR files for work."

Additional information gathered:

- The problem started that day.
- The HR files had previously been accessible.
- CLIENT01 started normally.
- No other users had reported the same problem.
- The user did not remember making any configuration changes.

The objective was to determine why Sarah could not access the HR resources and restore the appropriate access.

---

## Initial Troubleshooting Hypothesis

Because the issue involved access to a department-specific resource, I first suspected an authorization or security group membership issue.

Rather than immediately modifying permissions, I investigated Sarah's Active Directory account and the security group responsible for HR resource access.

Sarah's account was located in the Human Resources organizational structure, but investigation showed that she was not currently a member of the HR_SG security group.

This became the leading cause of the access problem.

---

## Permission Verification

Before changing Sarah's group membership, I verified that HR_SG was actually responsible for providing access to the HR folder.

On DC01, I inspected the NTFS Access Control List for:

C:\DepartmentShares\HR

PowerShell command used:

Get-Acl "C:\DepartmentShares\HR"

For easier review of the individual access control entries:

(Get-Acl "C:\DepartmentShares\HR").Access |
Select-Object IdentityReference, FileSystemRights, AccessControlType, IsInherited |
Format-Table -AutoSize

The ACL showed that:

- JIMENEZTECH\HR_SG was assigned permissions.
- FileSystemRights included Modify and Synchronize.
- AccessControlType was Allow.
- IsInherited was False.

The non-inherited permission confirmed that HR_SG had been explicitly assigned access to the HR folder.

This established the authorization chain:

Sarah Lopez
→ HR_SG membership
→ HR folder NTFS permissions
→ Access to HR resources

Because Sarah was missing from HR_SG, her account was missing the group membership required by the intended access-control design.

---

## Root Cause

Sarah Lopez's domain account was no longer a member of the HR_SG Active Directory security group.

The HR folder had explicit NTFS permissions assigned to HR_SG.

Because Sarah was not a member of that security group, her Windows security token did not contain the group membership required for the intended HR folder access.

---

## Resolution

Using Active Directory Users and Computers on DC01, Sarah Lopez was added back to the HR_SG security group.

Navigation:

Server Manager
→ Tools
→ Active Directory Users and Computers
→ HR_SG
→ Properties
→ Members

Sarah Lopez was confirmed as a member of HR_SG.

Because Active Directory group membership is represented in the user's logon security token, Sarah signed out of CLIENT01 and signed back in.

This allowed Windows to create a new logon session containing the updated HR_SG membership.

---

## Verification

After Sarah signed back into CLIENT01, the original user workflow was tested again.

Sarah navigated to the mapped CompanyShares drive:

S:\HR

The HR folder opened successfully without an access-denied error.

This confirmed that restoring Sarah's HR_SG membership resolved the reported access problem.

The ticket was considered successfully resolved only after reproducing and verifying the user's original workflow.

---

## Evidence

### Screenshot 1
01_DC01_Sarah_Lopez_HR_SG_Membership_Restored.png

Shows the HR_SG Properties window in Active Directory Users and Computers with Sarah Lopez listed as a member.

### Screenshot 2
02_CLIENT01_Sarah_Lopez_HR_Access_Verified.png

Shows CLIENT01 successfully accessing the HR folder through the mapped CompanyShares drive after Sarah's group membership was restored.

---

## Commands Used

### Inspect NTFS Permissions

Get-Acl "C:\DepartmentShares\HR"

### Display Detailed ACL Entries

(Get-Acl "C:\DepartmentShares\HR").Access |
Select-Object IdentityReference, FileSystemRights, AccessControlType, IsInherited |
Format-Table -AutoSize

---

## Skills Demonstrated

- Help desk troubleshooting methodology
- Active Directory user administration
- Active Directory security group management
- Role-based access control
- NTFS permission troubleshooting
- Access Control List inspection
- PowerShell troubleshooting
- Windows security token awareness
- User access troubleshooting
- Root cause analysis
- Hypothesis-driven troubleshooting
- Verification before remediation
- End-user validation
- Technical documentation

---

## Cheat Sheet Concepts Introduced

### Access Control List (ACL)

An ACL defines which users and groups have permissions to a Windows file system object and what actions those identities are allowed or denied to perform.

### Security Group

An Active Directory security group allows permissions to be assigned to a group rather than individually assigning permissions to every user.

Example:

HR users
→ HR_SG
→ HR folder permissions

### Modify Permission

The NTFS Modify permission generally allows a user to read, create, modify, and delete files and folders within the permitted resource.

### Explicit Permission

When IsInherited is False, the permission was explicitly assigned to that object rather than inherited from its parent folder.

### Security Token

When a domain user signs into Windows, Windows creates an access token containing information about the user's identity and security group memberships.

If a user's Active Directory group membership changes while the user is already signed in, the existing token may not immediately reflect the new membership.

Signing out and signing back in creates a new logon session and security token.

### Organizational Unit vs Security Group

An Organizational Unit (OU) organizes Active Directory objects and can be used for administration and Group Policy targeting.

A Security Group is primarily used to assign access permissions.

Being located inside the Human Resources OU does not automatically give a user HR file permissions.

---

## Key Troubleshooting Principle

Do not make a configuration change simply because the first piece of evidence appears to identify the problem.

Verify the complete relationship between the symptom and the suspected root cause before applying remediation.

For this incident:

User cannot access HR folder
→ Verify user identity
→ Check relevant group membership
→ Identify missing HR_SG membership
→ Verify HR_SG actually has HR folder permissions
→ Restore group membership
→ Refresh the user's logon security token
→ Reproduce the original user workflow
→ Confirm successful access

This prevents assumptions from being mistaken for confirmed root causes.

---

## What I Learned

This ticket reinforced the importance of troubleshooting logically rather than immediately applying a suspected fix.

My initial hypothesis was that Sarah's inability to access HR resources could be related to her permissions or Active Directory security group membership.

After discovering that she was missing from HR_SG, I did not immediately add her back. I verified that HR_SG actually had explicit Modify and Synchronize permissions on the HR folder.

This connected the user's account, security group membership, NTFS permissions, and resource access into a complete authorization chain.

I also reinforced the difference between an Active Directory Organizational Unit and a Security Group. A user being located in the Human Resources OU does not itself provide access to HR resources.

Finally, I learned the importance of validating a solution from the user's perspective. The ticket was not considered resolved simply because Sarah was added back to HR_SG. The final confirmation occurred when Sarah signed back into CLIENT01 and successfully accessed the HR folder herself.

---

## Outcome

The incident was successfully resolved.

Sarah Lopez's missing HR_SG membership was identified as the root cause of the HR folder access problem.

Her membership was restored, her Windows logon session was refreshed, and access to the HR shared folder was successfully verified from CLIENT01.

Ticket 070 completed the final troubleshooting capstone of the homelab project and demonstrated the ability to investigate an end-user problem, develop and test a hypothesis, identify the root cause, implement a targeted remediation, and verify the solution.