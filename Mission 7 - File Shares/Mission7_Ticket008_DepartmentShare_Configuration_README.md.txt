# Ticket #008 – Create Department Shared Folder

## Category

File Server Administration

---

## Priority

Medium

---

## Request

Create a centralized departmental file share that will be used to store company data for multiple departments.

The share should contain separate folders for each department and be accessible over the network for future permission configuration.

---

## Analysis

A centralized file share provides a consistent location for departmental resources while simplifying administration and future access management.

The folder structure was designed to separate departmental data before configuring security permissions.

---

## Resolution

Created the following directory structure on the Windows Server:

```text
C:\DepartmentShares
│
├── HR
├── Finance
├── Sales
├── Marketing
├── IT
└── Public
```

Enabled Advanced Sharing on the **DepartmentShares** folder.

Configured the share using:

- Share Name:
  - DepartmentShares

- Share Permissions:
  - Authenticated Users — Full Control

Share permissions were intentionally kept broad because access control will be enforced through NTFS permissions in subsequent tickets.

---

## Verification

Verified:

- DepartmentShares folder exists.
- All six departmental folders were successfully created.
- Advanced Sharing is enabled.
- The DepartmentShares folder is available as a network share.

---

## Evidence Collected

### Evidence 1

Department folder structure.

Screenshot:

- Mission7_Ticket008_Department_Folder_Structure.png

---

### Evidence 2

Advanced Sharing configuration.

Screenshot:

- Mission7_Ticket008_DepartmentShare_Config.png

---

## Skills Demonstrated

- Windows Server 2025
- File Server Administration
- Network Shares
- Advanced Sharing
- Share Permissions
- File System Organization

---

## Lesson Learned

Creating a network share is separate from securing access to its contents.

Share permissions determine whether users can reach the shared resource over the network, while NTFS permissions provide the detailed security that controls access to individual folders and files.

Separating these responsibilities simplifies administration and follows common enterprise practices.

---

## Reflection

This ticket introduced the process of publishing a centralized departmental file share within an Active Directory environment.

I learned how to organize departmental resources, configure a network share, and prepare the file server for granular permission management using Active Directory Security Groups in later tasks.