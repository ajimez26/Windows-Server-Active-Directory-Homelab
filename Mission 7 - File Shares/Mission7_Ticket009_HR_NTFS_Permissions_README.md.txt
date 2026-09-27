# Ticket #009 – Configure HR Folder NTFS Permissions

## Category

File Server Administration

---

## Priority

High

---

## Request

Configure NTFS permissions for the Human Resources department folder so that only authorized HR personnel can modify its contents while maintaining administrative access for system administrators.

---

## Analysis

The HR folder initially inherited permissions from the parent directory, granting general access to domain users.

To protect sensitive HR data, inheritance was disabled and unnecessary user permissions were removed. Access was then assigned using the HR security group in accordance with Role-Based Access Control (RBAC).

---

## Resolution

Performed the following actions:

- Disabled permission inheritance.
- Converted inherited permissions into explicit permissions.
- Removed both **Users (JIMENEZTECH\Users)** permission entries.
- Added **HR_SG**.
- Assigned **Modify** permissions to **HR_SG**.
- Retained **Administrators**, **SYSTEM**, and **CREATOR OWNER** permissions.

---

## Verification

Verified that the HR folder permissions consisted of:

- Administrators — Full Control
- SYSTEM — Full Control
- HR_SG — Modify
- CREATOR OWNER — Full Control (Subfolders and files only)

Confirmed that access to the HR folder is now controlled through the HR security group.

---

## Evidence Collected

### Evidence 1

HR folder permissions before modification.

Screenshot:

- Mission7_Ticket009_HR_Inherited_Permissions.png

---

### Evidence 2

Final NTFS permissions after configuration.

Screenshot:

- Mission7_Ticket009_HR_NTFS_Permissions.png

---

## Skills Demonstrated

- NTFS Permissions
- Windows Server 2025
- File Server Administration
- Active Directory Security Groups
- Role-Based Access Control (RBAC)
- Permission Inheritance

---

## Lesson Learned

NTFS permissions provide granular access control to files and folders. By assigning permissions to security groups instead of individual users, administration becomes more scalable, consistent, and easier to manage.

---

## Reflection

This ticket demonstrated how to configure secure folder access using Active Directory Security Groups and NTFS permissions. I also learned how permission inheritance affects folder security and why it should be reviewed before making access changes.