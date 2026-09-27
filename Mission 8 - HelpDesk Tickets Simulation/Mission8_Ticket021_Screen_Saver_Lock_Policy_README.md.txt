# Mission8_Ticket021_Screen_Saver_Lock_Policy_README.md

# Ticket #021 – Configure Screen Saver Lock Policy Using Group Policy

## Category
Group Policy | Endpoint Security

---

## Priority
Medium

---

## Request

The Information Security department requested that all domain workstations automatically lock after a period of inactivity to reduce the risk of unauthorized access when employees leave their computers unattended.

The objective was to enforce a standardized screen saver policy that automatically secures inactive user sessions across the domain.

---

## Analysis

Automatic workstation locking is an important security control that protects authenticated user sessions from unauthorized access. If an employee leaves their workstation unattended without manually locking it, another individual could access sensitive company information, send emails under the user's account, modify files, or perform unauthorized actions.

Because screen saver settings are part of the user's profile, the required policies were configured under **User Configuration** within Group Policy rather than **Computer Configuration**.

To effectively enforce this security measure, multiple policies were combined to ensure users could not bypass the organization's requirements.

---

## Resolution

Configured the following Group Policy settings:

**User Configuration**

Policies

Administrative Templates

Control Panel

Personalization

Enabled:

- **Enable screen saver**
- **Password protect the screen saver**
- **Prevent changing screen saver**

Configured:

**Screen saver timeout**

Value:

`600 seconds (10 minutes)`

After configuring the policies, Group Policy was refreshed using:

`gpupdate /force`

---

## Verification

Verified that:

- Screen saver policy was enabled.
- Password protection was required when resuming from the screen saver.
- Screen saver timeout was configured for 600 seconds.
- Users were prevented from modifying the screen saver configuration.
- Group Policy updated successfully after running **gpupdate /force**.

---

## Evidence Collected

**Screenshot**

Mission8_Ticket021_Screen_Saver_Lock_Policy.png

---

## Skills Demonstrated

- Group Policy Management
- User Configuration Policies
- Endpoint Security
- Workstation Security
- Security Hardening
- Administrative Templates
- Group Policy Enforcement
- Windows Security Best Practices

---

## Lesson Learned

This exercise demonstrated that securing inactive workstations requires multiple complementary Group Policy settings rather than a single configuration. While enabling a screen saver provides visual protection, requiring password authentication and preventing users from modifying the policy ensures the workstation remains protected from unauthorized access.

It also reinforced the importance of understanding whether a policy should be configured under **User Configuration** or **Computer Configuration**, depending on what aspect of the operating system is being managed.

---

## Reflection

This ticket strengthened my understanding of workstation security within an Active Directory environment by demonstrating how Group Policy can centrally enforce idle session protection across domain users. I also gained a better appreciation for defense-in-depth by combining multiple security policies to achieve a single organizational objective while reducing opportunities for users to bypass security controls.