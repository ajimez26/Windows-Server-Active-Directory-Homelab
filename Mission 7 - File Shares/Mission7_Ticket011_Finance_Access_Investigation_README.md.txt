# Ticket #011 – Investigate Finance Folder Access Request

## Category

Access Control / Troubleshooting

---

## Priority

Medium

---

## Request

Investigate a report that David Rodriguez was unable to access the Finance department folder and received an "Access Denied" message.

---

## Analysis

Reviewed the membership of the **Finance_SG** security group in Active Directory.

Confirmed that David Rodriguez is **not** a member of **Finance_SG**. Since access to the Finance folder is restricted to authorized Finance personnel, the reported behavior is consistent with the organization's Role-Based Access Control (RBAC) policy.

---

## Resolution

No configuration changes were required.

The issue was determined to be expected behavior because David Rodriguez is not authorized to access Finance resources. The existing security configuration was left unchanged to maintain the principle of least privilege.

---

## Verification

Confirmed that:

- David Rodriguez is not a member of **Finance_SG**.
- Finance folder access is restricted to authorized users.
- No changes to NTFS permissions or security group membership were necessary.

---

## Evidence Collected

- Mission7_Ticket011_FinanceSG_Members.png

---

## Skills Demonstrated

- Active Directory Administration
- Security Group Management
- Access Control Verification
- RBAC Implementation
- Principle of Least Privilege
- Help Desk Troubleshooting

---

## Lesson Learned

Not every access issue requires a configuration change. Before modifying permissions, administrators should verify whether the user is authorized to access the requested resource. Proper investigation helps maintain a secure environment and prevents unnecessary permission changes.

---

## Reflection

This ticket demonstrated the importance of validating user access requests before making administrative changes. By reviewing security group membership and confirming the intended access policy, the issue was correctly identified as expected behavior rather than a system malfunction.