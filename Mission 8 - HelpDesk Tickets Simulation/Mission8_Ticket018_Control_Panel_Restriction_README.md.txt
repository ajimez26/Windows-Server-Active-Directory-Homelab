# Ticket #018 – Restrict Access to Control Panel and PC Settings Using Group Policy

## Category

Group Policy Management

---

## Priority

Medium

---

## Request

Restrict access to the Control Panel and Windows Settings application for standard users to reduce unauthorized system configuration changes and improve workstation security.

---

## Analysis

The organization identified that users were making unauthorized changes to workstation settings, resulting in increased Help Desk requests and inconsistent system configurations.

To reduce configuration drift and enforce standardized workstation settings, a Group Policy was implemented to prevent users from accessing both the Control Panel and the Windows Settings application.

This policy targets the user experience rather than the computer itself and is therefore configured under **User Configuration**.

---

## Resolution

Navigated to:

User Configuration  
→ Policies  
→ Administrative Templates  
→ Control Panel

Configured the following policy:

**Prohibit access to Control Panel and PC settings**

```
Enabled
```

Applied the configuration through the **Workstation Security Baseline** Group Policy Object.

---

## Verification

Confirmed that the policy state changed from **Not Configured** to **Enabled** within the Group Policy Management Editor.

After Group Policy is applied using:

```cmd
gpupdate /force
```

Users affected by the policy will be prevented from opening both the Control Panel and the Windows Settings application.

---

## Evidence Collected

- Mission8_Ticket018_Control_Panel_Restriction.png

---

## Skills Demonstrated

- Group Policy Management
- Administrative Templates
- User Configuration Policies
- Windows Endpoint Security
- Security Hardening
- Principle of Least Privilege
- Technical Documentation

---

## Lesson Learned

Not every Group Policy targets the computer. Policies that modify the user experience, such as restricting access to the Control Panel, are configured under **User Configuration**.

Restricting administrative interfaces helps reduce unauthorized configuration changes while promoting a consistent and secure workstation environment.

---

## Reflection

This ticket demonstrated how Group Policy can be used to enforce security controls that limit user access to system configuration tools. It also reinforced the importance of understanding the difference between **Computer Configuration** and **User Configuration**, ensuring that policies are applied to the appropriate target based on organizational requirements.