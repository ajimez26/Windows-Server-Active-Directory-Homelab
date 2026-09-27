# Ticket #017 – Configure a Legal Logon Banner Using Group Policy

## Category

Group Policy Management

---

## Priority

Medium

---

## Request

Configure a legal logon banner for all domain-managed workstations to notify users that access is restricted to authorized personnel and that system activity may be monitored in accordance with organizational security policies.

---

## Analysis

The organization required a pre-logon notification to inform users that company systems are intended for authorized use only and that activity performed on those systems may be monitored and audited.

This configuration requires two separate Group Policy settings:

- Interactive logon: Message title for users attempting to log on
- Interactive logon: Message text for users attempting to log on

The policy was configured within the **Workstation Security Baseline** Group Policy Object to ensure all domain-joined computers receive a consistent security notice before user authentication.

---

## Resolution

Navigated to:

Computer Configuration  
→ Policies  
→ Windows Settings  
→ Security Settings  
→ Local Policies  
→ Security Options

Configured the following policies:

**Interactive logon: Message title for users attempting to log on**

```
Authorized Access Only
```

**Interactive logon: Message text for users attempting to log on**

```
This computer system is the property of JimenezTech.

Access is restricted to authorized users only. All activities performed on this system may be monitored, recorded, and audited.

Unauthorized access or misuse of this system may result in disciplinary action and/or legal prosecution.

By logging on, you acknowledge and consent to these conditions.
```

Applied the configuration through the **Workstation Security Baseline** GPO.

---

## Verification

Verified that both Interactive Logon policies displayed the configured values within the Group Policy Management Editor.

The policy can be immediately applied to domain-managed computers using:

```cmd
gpupdate /force
```

The logon banner will be displayed before user authentication once the policy is applied to domain-joined workstations.

---

## Evidence Collected

- Mission8_Ticket017_Logon_Message_Title.png
- Mission8_Ticket017_Logon_Message_Text.png

---

## Skills Demonstrated

- Group Policy Management
- Windows Security Configuration
- Security Policy Administration
- Active Directory Administration
- Endpoint Security
- Security Awareness Implementation
- Technical Documentation

---

## Lesson Learned

Legal logon banners provide users with notice that organizational systems are restricted to authorized use and that activity may be monitored. Implementing these policies through Group Policy ensures a standardized security message across all managed workstations while supporting organizational security, compliance, and acceptable-use policies.

---

## Reflection

This ticket demonstrated how Group Policy can be used to centrally enforce organizational security policies beyond traditional password and firewall settings. Configuring a legal logon banner reinforces user awareness, supports compliance initiatives, and provides organizations with a consistent method of communicating acceptable-use expectations before users gain access to company resources.