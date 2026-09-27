# Mission8_Ticket023_CommandPrompt_PowerShell_Restriction_README.md

# Ticket #023 – Restrict Command Prompt and PowerShell Access Using Group Policy

## Category
Group Policy | Endpoint Security

---

## Priority
High

---

## Request

Following an internal security review, the Information Security team requested that standard domain users be prevented from accessing Command Prompt and Windows PowerShell. These tools are essential for IT administration but can also be used to execute unauthorized commands, scripts, or malicious code if unrestricted.

The objective was to reduce the organization's attack surface while ensuring administrative users could continue using these tools as required.

---

## Analysis

Command Prompt and Windows PowerShell are powerful administrative tools that can be used to automate tasks, troubleshoot systems, and manage Windows environments. However, unrestricted access allows standard users to execute scripts, modify system settings, and potentially run malicious commands.

Applying the Principle of Least Privilege ensures that only users whose job responsibilities require these tools are granted access, reducing security risk without impacting legitimate administrative operations.

Because these restrictions affect user behavior rather than computer configuration, the required policies were implemented under **User Configuration** within Group Policy.

---

## Resolution

Configured the following Group Policy settings:

**User Configuration**

Policies

Administrative Templates

System

Configured:

**Prevent access to the command prompt**

Policy State:

**Enabled**

Disable the command prompt script processing:

**Yes**

Configured:

**Don't run specified Windows applications**

Policy State:

**Enabled**

Blocked Application:

`powershell.exe`

After configuration, Group Policy was refreshed using:

`gpupdate /force`

---

## Verification

Verified that:

- Command Prompt access was restricted for standard users.
- Batch script processing was disabled.
- Windows PowerShell was added to the blocked application list.
- Group Policy refreshed successfully after running **gpupdate /force**.
- Restrictions were successfully configured within the Workstation Security Baseline Group Policy Object.

---

## Evidence Collected

**Screenshot**

Mission8_Ticket023_CommandPrompt_PowerShell_Restriction.png

---

## Skills Demonstrated

- Group Policy Management
- Endpoint Security
- Principle of Least Privilege (PoLP)
- Windows Security Hardening
- Application Restriction Policies
- Administrative Templates
- Group Policy Enforcement
- Security Best Practices

---

## Lesson Learned

This exercise demonstrated how Group Policy can be used to reduce an organization's attack surface by restricting access to administrative tools that are unnecessary for standard users. Rather than removing functionality from all users, the Principle of Least Privilege was applied to balance security with operational requirements.

It also reinforced the value of organizing related application restrictions within a single Group Policy Object, simplifying administration, troubleshooting, and future policy changes.

---

## Reflection

This ticket strengthened my understanding of how organizations use Group Policy to enforce application restrictions while maintaining administrative flexibility. I also gained a deeper appreciation for balancing security controls with business needs by allowing IT personnel to retain access to essential management tools while limiting unnecessary privileges for standard users. This exercise reinforced the importance of designing security policies that are both effective and maintainable in an enterprise environment.