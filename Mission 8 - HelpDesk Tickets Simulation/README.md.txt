# Mission 8 – Help Desk Ticket Simulation

## Objective

Simulate common Active Directory help desk requests involving user account creation, Security Group management, and temporary access assignments while following enterprise security best practices.

---

## Environment

- Host OS: Windows 11
- Hypervisor: Oracle VirtualBox 7.1
- Guest OS: Windows Server 2025
- Domain: jimeneztech.local

---

# Ticket #001 – New HR Employee

## Request

Human Resources hired Sarah Lopez. Create her domain account, require a password change at first sign-in, and grant her access to Human Resources resources.

### Resolution

- Created Sarah Lopez's user account.
- Placed the account in the Human Resources Organizational Unit.
- Assigned the username:
  - slopez
- Configured a temporary password.
- Enabled "User must change password at next logon."
- Added Sarah Lopez to the HR_SG Security Group.

### Verification

Verified:

- Sarah Lopez exists in the Human Resources OU.
- Sarah Lopez is a member of HR_SG.

---

# Ticket #002 – Temporary Finance Access

## Request

Sarah Lopez will temporarily assist the Finance department for two weeks while remaining an HR employee.

### Resolution

- Left Sarah Lopez in the Human Resources OU.
- Kept Sarah Lopez as a member of HR_SG.
- Added Sarah Lopez to Finance_SG.

### Verification

Verified Sarah Lopez is a member of:

- HR_SG
- Finance_SG

---

# Ticket #003 – Remove Temporary Finance Access

## Request

Sarah Lopez's temporary Finance assignment has ended.

### Resolution

- Removed Sarah Lopez from Finance_SG.
- Left HR_SG membership unchanged.
- Left Sarah Lopez in the Human Resources OU.

### Verification

Verified Sarah Lopez:

- Is no longer a member of Finance_SG.
- Remains a member of HR_SG.
- Remains in the Human Resources OU.

---

## Skills Demonstrated

- Active Directory User Administration
- Organizational Unit Management
- Security Group Administration
- User Account Creation
- Group Membership Management
- Identity and Access Management (IAM)
- Role-Based Access Control (RBAC)
- Principle of Least Privilege
- Temporary Access Management
- Account Verification

---

## Screenshots

- Mission8_Ticket001_Sarah_User.png
- Mission8_Ticket001_HRSG_Membership.png
- Mission8_Ticket002_Temporary_Finance_Access.png
- Mission8_Ticket003_Finance_Access_Removed.png

---

## What I Learned

Throughout these help desk simulations, I learned that Organizational Units and Security Groups serve different purposes within Active Directory.

Organizational Units are used to organize users and computers and to apply Group Policy. Security Groups are used to grant access to resources such as shared folders, printers, applications, and other network resources.

Temporary business requirements should generally be handled by modifying Security Group memberships instead of moving users between Organizational Units. This approach follows the Principle of Least Privilege while maintaining a clean and organized Active Directory environment.

---

## Resume Talking Points

This project demonstrates hands-on experience with:

- Windows Server 2025
- Active Directory Domain Services (AD DS)
- User Account Administration
- Organizational Units (OUs)
- Global Security Groups
- Role-Based Access Control (RBAC)
- Identity and Access Management (IAM)
- Principle of Least Privilege
- Active Directory Troubleshooting
- Help Desk User Provisioning
- Temporary Access Administration

---

## Troubleshooting

### Issue

Initially created Security Groups as Organizational Units by mistake.

### Resolution

Enabled Advanced Features in Active Directory Users and Computers, removed accidental deletion protection, deleted the incorrect Organizational Units, and recreated them correctly as Global Security Groups.

### Lesson Learned

Always verify the object type before creation. Organizational Units and Security Groups have different purposes and different administrative functions within Active Directory.