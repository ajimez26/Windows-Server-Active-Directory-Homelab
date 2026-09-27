# Mission 5 – Active Directory Security Groups

## Objective

Learn how Active Directory Security Groups are used to simplify permission management and apply the Principle of Least Privilege.

---

## Environment

- Host OS: Windows 11
- Hypervisor: Oracle VirtualBox 7.1
- Guest OS: Windows Server 2025
- Domain: jimeneztech.local

---

## Tasks Completed

- Created a dedicated Organizational Unit named "Groups"
- Learned the difference between Organizational Units and Security Groups
- Created the following Global Security Groups:
  - IT_SG
  - HR_SG
  - Finance_SG
  - Sales_SG
  - Marketing_SG
  - Management_SG
- Added David Rodriguez to the IT_SG security group
- Learned how Security Groups are used to assign permissions instead of assigning permissions directly to users

---

## Skills Demonstrated

- Active Directory administration
- Security Group creation
- Group membership management
- User administration
- Enterprise access control

---

## Screenshots

- Mission5_SecurityGroups.png
- Mission5_ITSG_Members.png

---

## What I Learned

Organizational Units and Security Groups serve different purposes.

Organizational Units organize users and computers and are commonly used to apply Group Policy.

Security Groups are used to grant access to shared folders, printers, applications, and other network resources.

Instead of assigning permissions directly to individual users, administrators assign permissions to Security Groups and add users to those groups. This simplifies administration and follows the Principle of Least Privilege.

---

## Troubleshooting

### Issue

I accidentally created Organizational Units instead of Security Groups inside the Groups OU.

### Resolution

Enabled Advanced Features in Active Directory Users and Computers, removed accidental deletion protection from the incorrect Organizational Units, deleted them, and recreated the objects correctly as Global Security Groups.

### Lesson Learned

Always verify the object type before creating Active Directory objects. Organizational Units and Security Groups have different purposes and different property tabs.


## Resume Talking Points

This project demonstrates experience with:

- Active Directory Domain Services (AD DS)
- Organizational Units (OUs)
- Global Security Groups
- User and Group Administration
- Role-Based Access Control (RBAC)
- Principle of Least Privilege