# Mission8_Ticket019_Removable_Disk_Write_Restriction_README.md

# Ticket #019 – Restrict Write Access to Removable Storage

## Category
Group Policy | Endpoint Security

---

## Priority
Medium

---

## Request

The Information Security team requested that users be prevented from copying company data to removable USB storage devices while still allowing them to read files from approved removable media.

The objective was to reduce the risk of data exfiltration without disrupting legitimate business operations that require reading data from removable storage.

---

## Analysis

A review of the available Group Policy settings under **Removable Storage Access** showed separate controls for Read, Write, and Execute permissions across various removable media types.

To satisfy the business requirement, only write access needed to be restricted. Blocking read access would prevent employees from opening files stored on approved removable devices, while blocking write access specifically prevents users from copying files onto USB storage devices.

This approach follows the principle of least privilege by limiting unnecessary functionality while preserving legitimate business use.

---

## Resolution

Configured the following Group Policy setting:

Computer Configuration

Policies

Administrative Templates

System

Removable Storage Access

Enabled:

**Removable Disks: Deny write access**

Policy State:

**Enabled**

Applied the policy and confirmed the configuration was successfully saved.

---

## Verification

Verified that the policy state changed from **Not Configured** to **Enabled**.

Confirmed that the policy is configured to prevent writing data to removable USB storage devices while allowing read access to remain available.

---

## Evidence Collected

**Screenshot**

Mission8_Ticket019_Removable_Disk_Write_Restriction.png

---

## Skills Demonstrated

- Group Policy Management
- Endpoint Security Configuration
- Removable Storage Security
- Windows Administrative Templates
- Security Policy Implementation
- Principle of Least Privilege
- Data Loss Prevention (DLP) Fundamentals

---

## Lesson Learned

Organizations frequently restrict write access to removable storage devices instead of disabling USB functionality entirely. This approach protects sensitive information from unauthorized copying while maintaining productivity by allowing employees to access approved files stored on removable media.

This ticket also reinforced the importance of distinguishing between hardware failures and intentional security controls during troubleshooting.

---

## Reflection

This exercise demonstrated how Group Policy can be used to reduce the risk of data exfiltration through removable storage devices without significantly affecting normal business operations. I learned how to identify the appropriate policy setting based on business requirements and gained a better understanding of how endpoint security controls are commonly implemented in enterprise Windows environments.