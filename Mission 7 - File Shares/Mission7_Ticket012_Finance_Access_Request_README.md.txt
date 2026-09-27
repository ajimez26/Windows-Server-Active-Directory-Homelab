# Ticket #012 – Grant Temporary Finance Access

## Category

Access Management

---

## Priority

Medium

---

## Request

Grant David Rodriguez temporary access to the Finance department folder to perform authorized application maintenance.

---

## Analysis

The access request was reviewed and approved. To maintain the organization's Role-Based Access Control (RBAC) model, access would be granted by updating the appropriate Active Directory security group rather than modifying NTFS permissions directly.

---

## Resolution

David Rodriguez was added to the **Finance_SG** security group using the **Members** tab in Active Directory Users and Computers.

No changes were made to the Finance folder's NTFS permissions, ensuring that access continues to be managed through the existing security group.

---

## Verification

Confirmed that:

- David Rodriguez appears as a member of **Finance_SG**.
- The Finance folder's NTFS permissions remain unchanged.
- Access continues to be managed through the assigned security group.

---

## Evidence Collected

- Mission7_Ticket012_FinanceSG_Updated_Members.png

---

## Skills Demonstrated

- Active Directory Administration
- Security Group Management
- User Access Management
- Role-Based Access Control (RBAC)
- Windows Server Administration

---

## Lesson Learned

Managing permissions through Active Directory security groups is more efficient and secure than assigning permissions directly to individual users. This approach simplifies future access reviews, user onboarding, and permission changes while maintaining consistent security policies.

---

## Reflection

This ticket demonstrated the proper method for fulfilling an approved access request. By granting access through an existing security group instead of modifying NTFS permissions, the environment remained organized, scalable, and aligned with security best practices.