# Mission 10 - Ticket 028: Validate File Server Access

## Category

File Server Administration

## Priority

High

---

## Request

Validate that domain users can successfully access the centralized CompanyShares network share while ensuring NTFS permissions restrict access to authorized departmental folders based on Active Directory security group membership.

---

## Analysis

After configuring the SMB share and NTFS permissions, the file server required validation from a domain-joined Windows 11 workstation. The objective was to confirm that authenticated users could connect to the network share while only accessing folders assigned to their department through Active Directory security groups.

---

## Resolution

Logged into CLIENT01 using a domain user account and successfully mapped the CompanyShares network share using the Domain Controller's hostname:

`\\WIN-J9AV7RB7VVJ\CompanyShares`

Validated that the network drive was successfully mapped and accessible through File Explorer.

Performed access validation against departmental folders using the HR user account.

Expected behavior:

- HR folder — Accessible
- Finance folder — Access Denied
- IT folder — Access Denied
- Marketing folder — Access Denied
- Sales folder — Access Denied

---

## Troubleshooting

During validation, the HR user unexpectedly retained access to the Finance folder.

Investigation revealed that the user account was still a member of **Finance_SG** from a temporary access request completed during an earlier Active Directory administration exercise.

To resolve the issue:

- Reviewed the user's Active Directory group memberships.
- Removed the temporary **Finance_SG** membership.
- Signed the user out of CLIENT01 and logged back in to refresh the security token.
- Repeated the access validation.

After the temporary group membership was removed, the expected behavior was confirmed:

- HR folder — Access Granted
- Finance folder — Access Denied

This verified that NTFS permissions were functioning correctly and that folder access was being controlled through Active Directory security groups.

---

## Verification

Confirmed that:

- CLIENT01 successfully authenticated to the Active Directory domain.
- The CompanyShares SMB share was successfully mapped.
- Authorized departmental folders were accessible.
- Unauthorized departmental folders were inaccessible.
- NTFS permissions correctly enforced department-based access.
- Temporary Active Directory group membership directly affected effective permissions.

---

## Evidence Collected

### Screenshots

**04_Mapped_Network_Drive.png**

Displays:

- Successfully mapped CompanyShares network drive.
- Departmental folder structure visible.

---

**05_HR_Access_Verified.png**

Displays:

- HR department folder successfully opened by an authorized HR user.

---

**06_Finance_Access_Denied.png**

Displays:

- Finance folder denying access after removal of the temporary Finance_SG membership.

---

## Skills Demonstrated

- Windows File Server Administration
- SMB Network Drive Mapping
- Active Directory Authentication
- NTFS Permission Validation
- Active Directory Security Groups
- Role-Based Access Control (RBAC)
- Effective Permission Analysis
- Windows File Explorer Administration
- Identity and Access Management (IAM)
- IT Troubleshooting

---

## Lesson Learned

NTFS permissions determine effective access based on Active Directory security group membership rather than individual user accounts. Temporary group memberships can unintentionally grant additional permissions, making it essential to verify user group membership when troubleshooting access-related issues.

This exercise reinforced the importance of validating both NTFS permissions and Active Directory group memberships when diagnosing unexpected authorization behavior.

---

## Reflection

This ticket demonstrated the complete validation process for a Windows file server deployment. Rather than assuming the NTFS configuration was incorrect when unexpected access occurred, a structured troubleshooting process identified an existing Active Directory group membership as the root cause. Correcting the group membership restored the expected access controls and confirmed that the file server security model was functioning as designed.