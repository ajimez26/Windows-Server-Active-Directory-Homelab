# Mission 11 - Ticket 033: Verify Effective Group Policy with RSoP

## Category

Group Policy Administration

## Priority

Medium

---

## Request

Verify that workstation security settings deployed through Group Policy are effectively enforced on the domain-joined Windows 11 workstation using Resultant Set of Policy (RSoP).

---

## Analysis

Previous validation confirmed that the **Workstation Security Baseline** GPO was inherited by the Workstations Organizational Unit and successfully processed by the Windows 11 client.

However, confirming that a GPO appears in `gpresult` does not by itself demonstrate which individual policy settings are effective on the workstation.

Resultant Set of Policy (RSoP) provides administrators with a consolidated view of the policies that ultimately apply to a computer or user after Group Policy processing and inheritance are evaluated.

---

## Resolution

Opened Resultant Set of Policy on the Windows 11 workstation using:

```cmd
rsop.msc
```

Allowed RSoP to collect policy information from the local computer.

Navigated through the **Computer Configuration** Administrative Templates and reviewed the effective workstation security policies.

Verified that the configured removable-storage security restriction was present and enabled on the client workstation.

No configuration changes were required during this ticket because RSoP was used strictly as a diagnostic and verification tool.

---

## Verification

Confirmed that:

* RSoP successfully generated policy results on the workstation.
* Computer Configuration policies were available for inspection.
* The workstation's effective security settings reflected the domain Group Policy configuration.
* The removable-storage restriction was shown as enabled.
* Group Policy was functioning beyond simply appearing in the list of applied GPOs.

---

## Evidence Collected

### Screenshot

**05_RSoP_Effective_Security_Policy.png**

Displays:

* Resultant Set of Policy on the Windows 11 workstation.
* Computer Configuration policy hierarchy.
* Removable Storage Access policy.
* Effective **Removable Disks: Deny write access** security setting shown as enabled.

---

## Skills Demonstrated

* Resultant Set of Policy (RSoP)
* Group Policy Verification
* Group Policy Troubleshooting
* Effective Policy Analysis
* Windows Workstation Administration
* Security Policy Validation
* Active Directory Group Policy Management

---

## Command Used

```cmd
rsop.msc
```

---

## Lesson Learned

Successful Group Policy troubleshooting requires verifying both that a GPO is applied and that the intended individual settings are effective on the target computer.

`gpresult` can identify which Group Policy Objects were processed, while RSoP provides a more detailed view of the resulting policy configuration. Using both tools provides administrators with stronger evidence when validating or troubleshooting Group Policy deployment.

---

## Reflection

This ticket demonstrated how Resultant Set of Policy can be used to inspect the effective configuration of a domain-joined workstation. After previously confirming GPO inheritance and successful policy processing, RSoP provided an additional level of validation by showing that a specific workstation security setting was actually enforced.

This reinforced the importance of validating the final policy result rather than assuming that a successfully applied GPO automatically guarantees that every intended setting is taking effect.
