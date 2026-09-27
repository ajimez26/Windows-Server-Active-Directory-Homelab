# Mission 12 - Ticket 050: Diagnose and Repair Windows System Files

## Ticket Summary
Investigated Windows system integrity issues on CLIENT01 using Deployment Image Servicing and Management (DISM) and System File Checker (SFC).

DISM was first used to assess the health of the Windows component store. The initial CheckHealth operation reported that the component store was repairable.

A deeper ScanHealth operation confirmed the component-store condition.

DISM RestoreHealth was then used to attempt remediation. The operation reached 100% but failed with error 0x800f0915 because the required repair content could not be located.

System File Checker was subsequently used to evaluate protected Windows system files. SFC detected corrupt files and successfully repaired them.

A second SFC scan was performed to verify the repair and reported that Windows Resource Protection did not find any integrity violations.

Finally, DISM CheckHealth was run again. The component store continued to report as repairable, demonstrating that SFC successfully repaired the protected system files while the separate Windows component-store issue remained unresolved.

The incident was documented as a partial remediation requiring an appropriate DISM repair source for further component-store repair.

## Environment
- Client: CLIENT01
- Domain: jimeneztech.local
- Client Operating System: Windows 11 Enterprise Evaluation
- Administrative Shell: Windows PowerShell
- Troubleshooting Tools:
  - DISM
  - System File Checker
  - Windows Resource Protection

## Tasks Completed
- Assessed Windows component-store health
- Used DISM CheckHealth for initial diagnostics
- Identified a repairable component store
- Performed a deeper DISM ScanHealth
- Confirmed component-store corruption
- Attempted component-store repair using DISM RestoreHealth
- Encountered DISM error 0x800f0915
- Identified unavailable repair content as the repair limitation
- Performed a System File Checker scan
- Identified corrupt protected Windows system files
- Successfully repaired corrupt system files using SFC
- Performed a second SFC scan
- Verified no remaining protected system-file integrity violations
- Performed a final DISM component-store health check
- Confirmed the component store remained repairable
- Distinguished system-file repair from component-store repair
- Documented the partial remediation and remaining condition

## Initial Component Store Assessment
The Windows component store was initially evaluated using:

DISM /Online /Cleanup-Image /CheckHealth

The result reported:

The component store is repairable.
The operation completed successfully.

This indicated that Windows had detected a problem with the component store and considered the condition repairable.

CheckHealth provided a quick assessment without performing remediation.

## DISM CheckHealth
The command used was:

DISM /Online /Cleanup-Image /CheckHealth

The switches used included:

/Online
Targets the currently running Windows installation.

/Cleanup-Image
Performs servicing operations against the Windows image.

/CheckHealth
Checks whether the Windows component store has already been flagged as corrupted or repairable.

CheckHealth does not perform a full corruption scan and does not repair the image.

## Deeper Component Store Scan
Because CheckHealth reported that the component store was repairable, a more thorough scan was performed.

Command:

DISM /Online /Cleanup-Image /ScanHealth

The scan reached 100% and reported:

The component store is repairable.
The operation completed successfully.

This confirmed the initial CheckHealth finding.

## DISM ScanHealth
ScanHealth performs a more thorough analysis of the Windows component store than CheckHealth.

The troubleshooting progression was:

CheckHealth
        ↓
Repairable condition detected
        ↓
ScanHealth
        ↓
Repairable condition confirmed

No repair was performed during either of these diagnostic operations.

## Component Store Repair Attempt
After confirming the component-store condition, DISM was instructed to attempt a repair.

Command:

DISM /Online /Cleanup-Image /RestoreHealth

The operation reached 100% but returned:

Error: 0x800f0915

The accompanying message reported:

The repair content could not be found anywhere.

DISM indicated that internet connectivity or the Source option could be used to provide the files required to restore the image.

The DISM log was also identified at:

C:\WINDOWS\Logs\DISM\dism.log

## DISM RestoreHealth
RestoreHealth differs from the previous DISM operations because it attempts to repair detected Windows image corruption.

The workflow demonstrated:

CheckHealth
- Quick health assessment

ScanHealth
- Deeper corruption assessment

RestoreHealth
- Attempts remediation

In this incident, the diagnostic operations succeeded, but RestoreHealth could not complete the repair because the necessary repair content was unavailable.

## Error 0x800f0915
DISM returned:

0x800f0915

The associated DISM output indicated that the repair content required for the operation could not be located.

This meant that the component store could not be repaired using the repair sources available to CLIENT01 during the operation.

Potential next-level remediation could involve supplying an appropriate Windows repair source that matches the installed operating-system image.

No unsupported or mismatched repair source was introduced during this ticket.

## System File Checker Analysis
Following the unsuccessful DISM repair attempt, protected Windows system files were evaluated using System File Checker.

Command:

sfc /scannow

The scan completed at 100%.

Windows Resource Protection reported:

Windows Resource Protection found corrupt files and successfully repaired them.

This confirmed that protected Windows system-file corruption was present and that SFC was able to repair the detected files.

## SFC Repair Result
The initial SFC scan produced a successful repair result.

The SFC output also identified the CBS log location:

C:\Windows\Logs\CBS\CBS.log

The CBS log can contain additional details about Windows Resource Protection and system-file repair activity.

The important result for this ticket was:

Corrupt files detected
        ↓
SFC repair performed
        ↓
Repair reported successful

## Post-Repair SFC Verification
A second System File Checker scan was performed after the initial repair.

Command:

sfc /scannow

The verification scan reported:

Windows Resource Protection did not find any integrity violations.

This confirmed that the protected Windows system files checked by SFC no longer contained detected integrity violations.

The second scan served as post-remediation verification rather than assuming the first successful repair message was sufficient.

## DISM vs SFC
This ticket demonstrated an important distinction between DISM and SFC.

DISM:
Works with the Windows image/component store used to maintain and repair Windows.

SFC:
Checks protected Windows system files and replaces corrupted protected files when a valid replacement is available.

Although the tools are related, successful SFC remediation does not necessarily mean that a component-store problem reported by DISM has also been repaired.

This distinction became directly observable during this incident.

## Final Component Store Verification
After SFC successfully repaired the protected Windows system files, DISM CheckHealth was run again.

Command:

DISM /Online /Cleanup-Image /CheckHealth

The result continued to report:

The component store is repairable.
The operation completed successfully.

This confirmed that the component-store condition remained.

Therefore, the final system state contained two separate findings:

Protected Windows System Files:
Repaired successfully

Windows Component Store:
Still reports as repairable

## Final Diagnosis
The troubleshooting process established:

- DISM detected a repairable component store
- ScanHealth confirmed the condition
- RestoreHealth attempted remediation
- RestoreHealth failed with error 0x800f0915
- Required repair content could not be located
- SFC detected corrupt protected Windows system files
- SFC successfully repaired those files
- A second SFC scan found no integrity violations
- A final DISM CheckHealth still reported the component store as repairable

The incident therefore resulted in partial remediation.

## Resolution
Protected Windows system-file corruption was successfully repaired using:

sfc /scannow

The repair was verified by performing a second SFC scan, which reported:

Windows Resource Protection did not find any integrity violations.

The Windows component store was not fully repaired because DISM RestoreHealth could not obtain the required repair content.

No attempt was made to force the repair using an unverified or mismatched source.

The remaining component-store condition was documented for additional remediation or escalation.

## Escalation Consideration
In a production support environment, the unresolved DISM condition could require additional investigation.

Potential next steps could include:

- Confirming Windows Update connectivity
- Reviewing DISM.log
- Reviewing Windows servicing configuration
- Identifying an appropriate repair source
- Confirming the repair source matches the installed Windows version/build
- Escalating to a higher support tier if appropriate

The technician should avoid using an incompatible Windows image simply to force RestoreHealth to complete.

## Troubleshooting Methodology
The incident followed this troubleshooting process:

1. Assess Windows component-store health
2. Identify that the component store is repairable
3. Perform a deeper component-store scan
4. Confirm the repairable condition
5. Attempt component-store remediation
6. Capture and interpret the DISM repair failure
7. Evaluate protected Windows system files
8. Identify corrupt system files
9. Allow SFC to repair the files
10. Repeat SFC to verify remediation
11. Confirm no remaining SFC integrity violations
12. Reassess the Windows component store
13. Confirm the component-store issue remains
14. Separate resolved and unresolved findings
15. Document the partial remediation
16. Identify appropriate escalation considerations

## Verification
Final verification confirmed:

- DISM CheckHealth reported the component store as repairable
- DISM ScanHealth confirmed the component store was repairable
- DISM RestoreHealth reached 100%
- RestoreHealth returned error 0x800f0915
- DISM reported that repair content could not be found
- SFC detected corrupt protected Windows system files
- SFC successfully repaired the detected files
- A second SFC scan found no integrity violations
- Final DISM CheckHealth continued to report the component store as repairable
- Protected system-file remediation was successful
- Component-store remediation remained incomplete
- No unverified repair source was introduced

## Evidence
01_CLIENT01_DISM_CheckHealth.png
- Shows DISM CheckHealth
- Shows the component store reported as repairable
- Documents the initial component-store diagnosis

02_CLIENT01_DISM_ScanHealth.png
- Shows DISM ScanHealth reaching 100%
- Shows the component store reported as repairable
- Confirms the initial DISM diagnosis

03_CLIENT01_DISM_RestoreHealth_Source_Error.png
- Shows DISM RestoreHealth reaching 100%
- Shows error 0x800f0915
- Shows that repair content could not be found
- Documents the unsuccessful component-store repair attempt
- Shows the DISM log location

04_CLIENT01_SFC_Scan_Result.png
- Shows SFC reaching 100%
- Shows corrupt protected Windows system files were found
- Shows that SFC successfully repaired the files
- Shows the CBS log location

05_CLIENT01_SFC_Post_Repair_Verification.png
- Shows the second SFC scan
- Shows Windows Resource Protection found no integrity violations
- Confirms successful system-file remediation

06_CLIENT01_DISM_Post_Repair_Verification.png
- Shows final DISM CheckHealth
- Shows the component store continues to report as repairable
- Demonstrates that the DISM component-store condition remains unresolved

## Commands Used
DISM /Online /Cleanup-Image /CheckHealth

DISM /Online /Cleanup-Image /ScanHealth

DISM /Online /Cleanup-Image /RestoreHealth

sfc /scannow

## Skills Demonstrated
- Windows 11 Troubleshooting
- Windows System Integrity Troubleshooting
- DISM
- System File Checker
- Windows Resource Protection
- Component Store Analysis
- System File Integrity Analysis
- Windows Image Servicing
- Error Code Analysis
- Repair Source Troubleshooting
- Command-Line Administration
- Remediation Verification
- Partial Remediation Documentation
- Root Cause Analysis
- Escalation Decision-Making
- Help Desk Troubleshooting
- Technical Documentation

## Cheat Sheet Concepts Introduced
DISM
- Deployment Image Servicing and Management. Windows utility used to service and repair Windows images and the component store.

Component Store
- Windows servicing repository containing components used to maintain and repair the operating system.

DISM /CheckHealth
- Performs a quick check to determine whether component-store corruption has already been detected.

DISM /ScanHealth
- Performs a deeper scan of the component store for corruption.

DISM /RestoreHealth
- Attempts to repair detected component-store corruption.

SFC
- System File Checker. Windows utility used to verify and repair protected system files.

sfc /scannow
- Scans protected Windows system files and attempts to repair detected corruption.

Windows Resource Protection
- Windows mechanism responsible for protecting important operating-system files and resources.

CBS.log
- Log containing servicing and System File Checker information.

Location:

C:\Windows\Logs\CBS\CBS.log

DISM.log
- Log containing detailed information about DISM operations.

Location:

C:\Windows\Logs\DISM\dism.log

Repair Source
- Location containing known-good Windows components that DISM can use during image repair.

Partial Remediation
- A troubleshooting outcome where part of an incident is successfully resolved while another identified condition remains unresolved.

Key Troubleshooting Distinction:

DISM and SFC do not perform exactly the same function.

DISM primarily addresses the Windows image/component store.

SFC verifies and repairs protected Windows system files.

Key Verification Principle:

A successful repair command should be followed by verification whenever practical.

Key Documentation Principle:

Do not report an incident as fully resolved when verification shows that part of the underlying condition remains.

## Outcome
Windows integrity problems on CLIENT01 were successfully investigated using DISM and System File Checker.

DISM CheckHealth initially reported that the Windows component store was repairable. A deeper ScanHealth operation confirmed the condition.

DISM RestoreHealth attempted to repair the component store but returned error 0x800f0915 because the required repair content could not be located.

System File Checker was then used to inspect protected Windows system files. SFC detected corrupt files and successfully repaired them.

A second SFC scan was performed as post-remediation verification and reported that Windows Resource Protection did not find any integrity violations.

A final DISM CheckHealth was then performed. The component store continued to report as repairable.

The final result was therefore documented as partial remediation:

Protected Windows system files:
Successfully repaired and verified.

Windows component store:
Repairable condition remains and requires an appropriate repair source or additional troubleshooting.

This ticket demonstrated Windows integrity assessment, DISM and SFC troubleshooting, error analysis, repair verification, accurate documentation of unresolved conditions, and appropriate escalation decision-making.