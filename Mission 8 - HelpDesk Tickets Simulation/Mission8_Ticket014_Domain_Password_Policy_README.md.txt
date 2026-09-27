# Ticket #014 – Configure Domain Password Policy

## Category

Group Policy Management

---

## Priority

High

---

## Request

Configure a domain-wide password policy to strengthen account security and enforce consistent password requirements for all domain users.

---

## Analysis

The organization's existing password policy required stronger security controls. A review of the Default Domain Policy identified the Password Policy settings within Group Policy Management, allowing domain-wide enforcement of password standards.

---

## Resolution

Modified the Default Domain Policy and configured the following password settings:

- Enforce password history: **12 passwords remembered**
- Maximum password age: **60 days**
- Minimum password age: **1 day**
- Minimum password length: **11 characters**
- Password must meet complexity requirements: **Enabled**
- Store passwords using reversible encryption: **Disabled**

These settings establish a stronger baseline for domain account security while maintaining usability for end users.

---

## Verification

Confirmed that the password policy settings were successfully configured within the Default Domain Policy.

Future policy updates can be applied immediately on domain-joined systems using:

```cmd
gpupdate /force
```

---

## Evidence Collected

- Mission8_Ticket014_Domain_Password_Policy.png

---

## Skills Demonstrated

- Group Policy Management
- Domain Password Policy Configuration
- Windows Server Administration
- Identity and Access Management (IAM)
- Security Baseline Configuration

---

## Lesson Learned

Group Policy provides centralized management for domain-wide security settings. Configuring password policies through the Default Domain Policy ensures consistent password requirements across all domain users without requiring individual account configuration.

---

## Reflection

This ticket introduced centralized security management through Group Policy. Implementing a domain-wide password policy demonstrated how administrators can enforce consistent security standards across an entire Active Directory environment while balancing security with usability.