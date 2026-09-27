# Mission8_Ticket020_Desktop_Wallpaper_Policy_README.md

# Ticket #020 – Deploy Corporate Desktop Wallpaper Using Group Policy

## Category
Group Policy | User Environment Management

---

## Priority
Low

---

## Request

The Human Resources and Marketing departments requested that all company workstations display the official JimenezTech desktop wallpaper to maintain consistent corporate branding and present a professional desktop environment for employees.

The objective was to configure a Group Policy that would automatically apply a standardized desktop wallpaper to domain users.

---

## Analysis

The desktop wallpaper policy was identified as a **User Configuration** setting because desktop personalization is stored within the user's profile rather than the computer's operating system configuration.

Initially, the policy did not appear to apply after running **gpupdate /force**. Further troubleshooting revealed that the **Workstation Security Baseline** Group Policy Object existed but was no longer linked to the **jimeneztech.local** domain.

After relinking the GPO to the domain, Group Policy processing successfully recognized the policy during subsequent updates.

This troubleshooting exercise reinforced the importance of verifying Group Policy deployment before assuming a configuration error exists.

---

## Resolution

Configured the following Group Policy setting:

User Configuration

Policies

Administrative Templates

Desktop

Desktop

Enabled:

**Desktop Wallpaper**

Configured:

Wallpaper Name:

`C:\Windows\Web\Wallpaper\Windows\img0.jpg`

Wallpaper Style:

**Fill**

After configuration, the **Workstation Security Baseline** GPO was linked to the **jimeneztech.local** domain to ensure the policy could be processed by domain computers and users.

Executed:

`gpupdate /force`

to refresh Group Policy.

---

## Verification

Verified that:

- Desktop Wallpaper policy was configured and enabled.
- Workstation Security Baseline GPO was successfully linked to the domain.
- Group Policy refreshed successfully using **gpupdate /force**.
- Confirmed the GPO appeared under **Applied Group Policy Objects** after correcting the missing link.

Although desktop wallpaper behavior on the Domain Controller did not fully reflect a standard workstation experience, the Group Policy deployment and troubleshooting process was successfully validated.

---

## Evidence Collected

**Screenshot**

Mission8_Ticket020_Desktop_Wallpaper_Policy.png

---

## Skills Demonstrated

- Group Policy Management
- User Configuration Policies
- Desktop Environment Management
- Group Policy Linking
- Group Policy Troubleshooting
- gpupdate
- gpresult
- Root Cause Analysis
- Windows Administrative Templates

---

## Lesson Learned

This exercise demonstrated that creating and configuring a Group Policy Object is only part of the deployment process. A GPO must also be properly linked to a domain, organizational unit, or site before it can affect users or computers.

Additionally, Group Policy troubleshooting should follow a structured process by verifying configuration, confirming GPO links, refreshing policy, and validating policy application before assuming a configuration failure.

---

## Reflection

This ticket strengthened my understanding of how User Configuration policies differ from Computer Configuration policies and provided valuable experience troubleshooting Group Policy deployment issues. By identifying that the GPO had not been linked to the domain, I gained practical experience diagnosing a common Active Directory administration issue that can prevent policies from applying in enterprise environments.