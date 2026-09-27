# Ticket #013 – Remove Temporary Finance Access

## Category

Access Management

---

## Priority

Medium

---

## Request

Remove David Rodriguez's temporary access to the Finance department folder after the approved maintenance work has been completed.

---

## Analysis

The temporary maintenance assignment was completed successfully. To maintain the principle of least privilege, the user's temporary access should be revoked by removing the account from the Finance security group.

Since access is controlled through Active Directory security groups, no changes to NTFS permissions are required.

---

## Resolution

Removed David Rodriguez from the **Finance_SG** security group using the **Members** tab in Active Directory Users and Computers.

No modifications were made to the Finance folder's NTFS permissions.

---

## Verification

Confirmed that:

- David Rodriguez is no longer a member of **Finance_SG**.
- The Finance folder's NTFS permissions remain unchanged.
- Access continues to be managed through Active Directory security groups.

---

## Evidence Collected

- Mission7_Ticket013_FinanceSG_Access_Removed.png

---

## Skills Demonstrated

- Active Directory Administration
- Security Group Management
- User Access Removal
- Role-Based Access Control (RBAC)
- Principle of Least Privilege
- Windows Server Administration

---

## Lesson Learned

Temporary permissions should be removed as soon as they are no longer required. Managing access through security groups makes it easy to grant and revoke permissions without modifying folder-level security.

---

## Reflection

This ticket demonstrated the importance of removing temporary access after a task has been completed. Following the principle of least privilege helps reduce unnecessary access and strengthens the overall security of the environment.