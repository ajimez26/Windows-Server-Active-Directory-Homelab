# Ticket #016 – Configure Windows Defender Firewall Domain Profile Using Group Policy

## Category

Group Policy Management

---

## Priority

High

---

## Request

Configure a centralized Windows Defender Firewall policy to ensure all domain-joined computers have the firewall enabled while connected to the organization's Active Directory domain.

---

## Analysis

A new Group Policy Object named **Workstation Security Baseline** was created and linked to the domain. The Windows Defender Firewall settings were initially set to **Not Configured**, meaning the GPO was not enforcing any firewall behavior.

The Domain Profile was selected because it applies to computers authenticated to the organization's Active Directory domain.

---

## Resolution

Configured the Domain Profile with the following settings:

- Firewall State: **On (recommended)**
- Inbound Connections: **Block (default)**
- Outbound Connections: **Allow (default)**

Applied the policy through the **Workstation Security Baseline** GPO.

---

## Verification

Confirmed the Domain Profile reflected the configured firewall settings within the Group Policy Editor.

The policy can be applied immediately on domain-joined computers using:

```cmd
gpupdate /force
```

---

## Evidence Collected

- Mission8_Ticket016_Domain_Firewall_Policy.png

---

## Skills Demonstrated

- Group Policy Management
- Windows Defender Firewall Administration
- Active Directory
- Windows Security Baselines
- Endpoint Security
- Enterprise Security Configuration

---

## Lesson Learned

A Group Policy Object must be linked to a domain, site, or organizational unit before it can affect users or computers. Configuring the Domain Firewall Profile ensures centralized enforcement of firewall settings for domain-joined devices while maintaining a secure default posture.

---

## Reflection

This ticket demonstrated how Windows Defender Firewall can be managed centrally through Group Policy. Rather than relying on users to configure their own systems, administrators can enforce consistent security settings across all managed workstations, reducing organizational risk and improving security compliance.