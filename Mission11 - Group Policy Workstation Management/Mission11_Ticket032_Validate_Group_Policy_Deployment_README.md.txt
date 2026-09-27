# Mission 11 - Ticket 032: Validate Group Policy Deployment

## Category

Group Policy Administration

## Priority

High

---

## Request

Validate that the Workstation Security Baseline Group Policy Object (GPO) is successfully applied to the domain-joined Windows 11 workstation. Investigate and resolve any issues preventing successful policy deployment.

---

## Analysis

After verifying that the Workstation Security Baseline GPO was correctly inherited by the Workstations Organizational Unit, the next step was to confirm that the workstation was successfully receiving and applying domain policies.

Initial validation using `gpupdate /force` reported that computer-side Group Policy processing failed due to a time synchronization error between the client workstation and the domain controller. Because Kerberos authentication relies on synchronized system clocks, Group Policy could not successfully process computer policies.

Further investigation identified that the client workstation was configured with the incorrect time zone (Pacific Standard Time) while the domain controller was configured for Eastern Standard Time.

---

## Resolution

Performed troubleshooting on the Windows 11 client workstation.

The following validation steps were completed:

- Verified Group Policy update status using `gpupdate /force`.
- Identified a clock synchronization error affecting computer policy processing.
- Compared system date and time between CLIENT01 and the Domain Controller.
- Verified the client workstation was configured with the incorrect time zone.
- Updated the workstation time zone to Eastern Standard Time.
- Confirmed successful synchronization with the Domain Controller using Windows Time Service.
- Verified the Domain Controller as the workstation's time source.
- Confirmed a healthy secure channel between the workstation and Active Directory using `nltest`.
- Executed `gpresult /r` to validate successful Group Policy application.

Following time synchronization, the workstation successfully processed Group Policy and confirmed communication with the Domain Controller.

---

## Verification

Confirmed that:

- The workstation successfully contacted the Domain Controller.
- Group Policy processing completed successfully.
- The workstation received the Workstation Security Baseline GPO.
- Default Domain Policy was also successfully applied.
- The workstation remained within the Workstations Organizational Unit.
- Secure channel verification returned a successful status (NERR_Success).
- The workstation synchronized its system time with the Domain Controller.

---

## Business Impact

Correct time synchronization is essential for Active Directory environments because Kerberos authentication depends on accurate system clocks. Resolving the workstation's time configuration restored successful Group Policy processing, ensured reliable authentication, and prevented future domain communication issues.

---

## Evidence Collected

### Screenshot

**03_GPResult_Applied_GPOs.png**

Displays:

- Successful Group Policy processing.
- Applied Group Policy Objects.
- Workstation Organizational Unit location.
- Domain Controller used to process policies.

---

### Screenshot

**04_Secure_Channel_Verification.png**

Displays:

- Successful secure channel verification.
- Trusted Domain Controller.
- NERR_Success status.

---

## Skills Demonstrated

- Active Directory Administration
- Group Policy Management
- Group Policy Validation
- Windows Time Service (W32Time)
- Kerberos Authentication
- Secure Channel Verification
- Group Policy Troubleshooting
- Windows Command Line
- Enterprise Troubleshooting
- Root Cause Analysis

---

## Commands Used

```cmd
gpupdate /force
gpresult /r
w32tm /query /source
w32tm /query /status
w32tm /resync
nltest /sc_verify:jimeneztech.local
tzutil /g
time /t
date /t
```

---

## Lesson Learned

Successful Group Policy deployment depends on more than correct Group Policy configuration. Time synchronization, Kerberos authentication, DNS resolution, and a healthy secure channel are all required for reliable policy processing. Validating each dependency provides a structured approach to troubleshooting Group Policy issues in Active Directory environments.

---

## Reflection

This ticket evolved from a routine Group Policy validation exercise into a real-world troubleshooting scenario. Rather than assuming the Group Policy configuration was incorrect, a systematic troubleshooting process identified that the client workstation's time zone configuration prevented proper Kerberos authentication. After correcting the workstation's time settings and validating communication with the Domain Controller, Group Policy successfully processed. This exercise reinforced the importance of investigating root causes rather than focusing solely on reported symptoms.