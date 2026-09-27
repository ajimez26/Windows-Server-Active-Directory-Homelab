# Mission8_Ticket022_Automatic_Windows_Updates_README.md

# Ticket #022 – Configure Automatic Windows Updates Using Group Policy

## Category
Group Policy | Patch Management

---

## Priority
High

---

## Request

The IT Infrastructure team requested that all domain-joined workstations automatically receive Windows security updates on a consistent schedule. The objective was to reduce the organization's exposure to known vulnerabilities by centrally managing update deployment through Group Policy.

---

## Analysis

Allowing individual users to decide when or if to install Windows Updates creates inconsistent patch levels across the organization. Systems that remain unpatched are more susceptible to malware, ransomware, and exploitation of known vulnerabilities. In addition, inconsistent update management can create compliance issues and increase the organization's overall attack surface.

Centralizing Windows Update settings through Group Policy ensures that all managed workstations follow the same update schedule, improving security, consistency, and administrative oversight.

---

## Resolution

Configured the following Group Policy setting:

**Computer Configuration**

Policies

Administrative Templates

Windows Components

Windows Update

Manage end user experience

Configured:

**Configure Automatic Updates**

Policy State:

**Enabled**

Automatic Updating Option:

**4 – Auto download and schedule the install**

Scheduled Install Day:

**Every day**

Scheduled Install Time:

**03:00**

After configuration, Group Policy was refreshed using:

`gpupdate /force`

---

## Verification

Verified that:

- Configure Automatic Updates policy was enabled.
- Windows Updates were configured to automatically download.
- Installation schedule was configured for daily deployment at 3:00 AM.
- Group Policy refreshed successfully after running **gpupdate /force**.
- The policy was successfully configured within the Workstation Security Baseline Group Policy Object.

---

## Evidence Collected

**Screenshot**

Mission8_Ticket022_Automatic_Windows_Updates.png

---

## Skills Demonstrated

- Group Policy Management
- Windows Update Management
- Patch Management
- Endpoint Security
- Security Hardening
- Administrative Templates
- Centralized System Administration
- Group Policy Enforcement

---

## Lesson Learned

This exercise demonstrated the importance of centralized patch management within an Active Directory environment. Rather than allowing individual users to control software updates, organizations can enforce a consistent update schedule through Group Policy, reducing the risk posed by unpatched systems and improving overall security posture.

It also reinforced the relationship between timely patch management and cybersecurity by showing how routine updates reduce an organization's attack surface and help maintain compliance with established security standards.

---

## Reflection

This ticket strengthened my understanding of enterprise patch management and how Group Policy can be used to standardize Windows Update behavior across domain-joined computers. I also gained a better appreciation for balancing security with business operations by scheduling updates during maintenance windows that minimize user disruption while ensuring critical security updates are consistently applied.