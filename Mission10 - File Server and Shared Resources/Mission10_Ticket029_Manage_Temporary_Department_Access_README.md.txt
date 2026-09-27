# Mission 10 - Ticket 029: Manage Temporary Department Access

## Category

Identity and Access Management (IAM)

## Priority

Medium

---

## Request

Grant an IT employee temporary access to another department's shared folder to support a short-term business need. After completing the assigned work, remove the temporary access and verify that permissions have been revoked.

---

## Analysis

Organizations frequently receive requests for temporary access to departmental resources. Rather than modifying NTFS permissions directly, the preferred method is to manage access through Active Directory security group membership.

Using security groups simplifies administration, maintains consistent permissions, and supports the Principle of Least Privilege by ensuring elevated access exists only for the duration of the assigned task.

---

## Resolution

Selected an IT department user and temporarily added the account to the **Marketing_SG** security group using Active Directory Users and Computers.

Signed into CLIENT01 using the IT user account and mapped the CompanyShares network drive.

Verified that the user successfully accessed the Marketing department folder.

After validation:

- Removed the user from the Marketing_SG security group.
- Signed out of CLIENT01 and signed back in to refresh the user's security token.
- Confirmed that access to the Marketing folder was revoked.

No NTFS permissions required modification during the request.

---

## Verification

Confirmed that:

- Temporary Active Directory group membership granted access to the Marketing folder.
- Removing the group membership revoked access after user reauthentication.
- NTFS permissions correctly enforced security group membership.
- The file server continued to function without requiring changes to existing folder permissions.

---

## Evidence Collected

### Screenshot

**07_Temporary_Marketing_Access.png**

Displays:

- Marketing folder successfully opened after temporary group assignment.

---

**08_Temporary_Access_Removed.png**

Displays:

- Access denied after the temporary Marketing_SG membership was removed.

---

## Skills Demonstrated

- Active Directory Administration
- Security Group Management
- Identity and Access Management (IAM)
- Role-Based Access Control (RBAC)
- Temporary Access Management
- NTFS Permission Validation
- Windows File Server Administration
- Access Verification

---

## Lesson Learned

Managing temporary access through Active Directory security groups is more efficient and secure than modifying NTFS permissions for individual users. Group membership changes immediately affect a user's effective permissions after a new logon session, making this approach both scalable and easier to audit.

---

## Reflection

This ticket simulated a common enterprise access request where an employee required temporary access outside of their normal responsibilities. By granting and later revoking access through Active Directory security groups instead of altering NTFS permissions, the existing security model remained intact while meeting operational needs. This exercise reinforced the value of centralized identity management and demonstrated how Active Directory group membership directly influences effective permissions within a Windows Server environment.