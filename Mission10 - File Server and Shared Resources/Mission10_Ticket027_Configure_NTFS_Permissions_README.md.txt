# Mission 10 - Ticket 027: Configure NTFS Permissions

## Category

File Server Administration

## Priority

High

---

## Request

Configure NTFS permissions for each departmental folder within the CompanyShares directory to ensure that only authorized department security groups can access their respective resources.

---

## Analysis

While Share Permissions determine whether users can connect to a shared folder across the network, NTFS Permissions provide granular control over what users can do after gaining access.

To implement role-based access control, each departmental folder was assigned its corresponding Active Directory security group using the principle of least privilege. This approach limits access to authorized users while simplifying long-term permission management.

---

## Resolution

Disabled permission inheritance for each departmental folder and converted inherited permissions into explicit permissions.

Removed the default **Users** permission entries while preserving the following built-in security principals:

- Administrators
- SYSTEM
- CREATOR OWNER

Assigned the appropriate Active Directory security groups to each department folder with **Modify** permissions.

| Folder | Security Group | Permission |
|---------|----------------|------------|
| HR | HR_SG | Modify |
| Finance | Finance_SG | Modify |
| IT | IT_SG | Modify |
| Marketing | Marketing_SG | Modify |
| Sales | Sales_SG | Modify |

The **Modify** permission provides users with the ability to create, edit, and delete files while preventing changes to folder ownership or security permissions.

---

## Verification

Verified that:

- Permission inheritance was successfully converted to explicit permissions.
- Department security groups were assigned to their corresponding folders.
- Built-in administrative accounts retained Full Control.
- Department security groups received Modify permissions.
- The folder structure is prepared for access validation from domain-joined client workstations.

---

## Evidence Collected

### Screenshot

**03_HR_Folder_Security.png**

Displays:

- HR folder Security properties
- HR_SG assigned
- Modify permission enabled
- Built-in administrative permissions retained

---

## Skills Demonstrated

- NTFS Permission Management
- Active Directory Security Groups
- Windows File Server Administration
- Role-Based Access Control (RBAC)
- Principle of Least Privilege
- Permission Inheritance Management
- Windows Security Configuration

---

## Lesson Learned

Share Permissions and NTFS Permissions work together to determine a user's effective access to shared resources. By assigning permissions through Active Directory security groups instead of individual user accounts, access management becomes more scalable, secure, and easier to maintain as the organization grows.

---

## Reflection

This ticket established the security foundation for the organization's departmental file shares. Using security groups to assign NTFS permissions ensures that future access requests can be managed through group membership rather than modifying folder permissions for individual users, aligning with Windows Server administration best practices.