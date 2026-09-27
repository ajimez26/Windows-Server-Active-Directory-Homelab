# Ticket #015 – Configure Domain Account Lockout Policy

## Category

Group Policy Management

---

## Priority

High

---

## Request

Configure a domain-wide account lockout policy to reduce the risk of brute-force password attacks while maintaining an appropriate balance between security and user accessibility.

---

## Analysis

A review of the Default Domain Policy identified the Account Lockout Policy settings within Group Policy Management. The organization required domain-wide controls to automatically lock user accounts after repeated failed authentication attempts.

---

## Resolution

Modified the Default Domain Policy and configured the following account lockout settings:

- Account lockout threshold: **5 invalid logon attempts**
- Account lockout duration: **60 minutes**
- Reset account lockout counter after: **30 minutes**

These settings help protect against password guessing attacks while allowing legitimate users to regain access after the lockout period expires.

---

## Verification

Confirmed that the Account Lockout Policy settings were successfully configured within the Default Domain Policy.

Policy updates can be applied immediately on domain-joined systems using:

```cmd
gpupdate /force
```

---

## Evidence Collected

- Mission8_Ticket015_Account_Lockout_Policy.png

---

## Skills Demonstrated

- Group Policy Management
- Account Lockout Configuration
- Windows Server Administration
- Identity and Access Management (IAM)
- Domain Security Configuration

---

## Lesson Learned

Account lockout policies help mitigate brute-force password attacks by limiting repeated authentication attempts. Properly balancing the lockout threshold, lockout duration, and reset counter helps improve security without creating unnecessary administrative overhead.

---

## Reflection

This ticket demonstrated how Group Policy can centrally enforce authentication security across an Active Directory domain. Configuring an account lockout policy strengthened the organization's security posture while maintaining a practical balance between protection and usability.