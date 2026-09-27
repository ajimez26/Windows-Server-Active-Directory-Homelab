# Ticket #010 – Verify HR Folder NTFS Configuration

## Category

Verification

---

## Priority

Medium

---

## Request

Verify that the NTFS permissions configured for the HR folder match the requested security design.

---

## Analysis

The HR folder permissions were reviewed to ensure they align with the organization's Role-Based Access Control (RBAC) policy.

---

## Verification

Confirmed the following:

- HR_SG has Modify permissions.
- Administrators retain Full Control.
- SYSTEM retains Full Control.
- CREATOR OWNER remains configured.
- Users (JIMENEZTECH\Users) has been removed.
- Permission inheritance has been disabled.

---

## Evidence Collected

Reviewed the final NTFS permission configuration from Ticket #009.

Evidence:

- Mission7_Ticket009_HR_NTFS_Permissions.png

---

## Skills Demonstrated

- NTFS Permission Verification
- Windows Server Administration
- Security Validation
- RBAC Verification

---

## Lesson Learned

Configuration changes should always be verified after implementation. Validation confirms that permissions match the intended security design before the resource is placed into production.

---

## Reflection

This ticket reinforced the importance of verifying security configurations after implementation. Although end-user testing will be performed from a domain-joined client in a future mission, the configured NTFS permissions were confirmed to match the intended access model.