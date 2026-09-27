# Mission 12 - Ticket 069: Windows Recovery and System Restore Readiness

## Ticket Summary

Assessed and improved Windows recovery readiness on CLIENT01 after a simulated report of system instability following recent system changes.

The investigation evaluated multiple Windows recovery components rather than immediately performing a reset or reinstall.

The assessment determined:

- Windows Recovery Environment (WinRE) was enabled
- WinRE was registered to the dedicated recovery partition
- CLIENT01 contained a dedicated Recovery partition
- No System Restore points were initially available
- Volume Shadow Copy infrastructure was operational
- No shadow-copy storage association was initially reported
- System Protection was enabled for C:
- Shadow storage was configured
- A controlled System Restore point was successfully created

The final configuration provided both WinRE recovery capability and a verified System Restore point.

---

## Environment

- Client: CLIENT01
- Operating System: Windows 11 Enterprise Evaluation
- Domain: jimeneztech.local
- Virtualization: Oracle VirtualBox
- Virtual Disk: VBOX HARDDISK
- Disk Size: 64 GB
- Partition Style: GPT
- Windows RE Version: 10.0.26100.9444
- Administrative Tool: Windows PowerShell

---

## Ticket Request

A user reports that Windows has become unstable following recent system changes.

Determine what recovery options are available before considering destructive remediation such as resetting or reinstalling Windows.

Evaluate:

- Windows Recovery Environment
- Recovery partition
- System Restore
- Volume Shadow Copy infrastructure
- Restore-point availability

Improve recovery readiness where appropriate without performing an actual system rollback.

---

## Windows Recovery Environment Investigation

WinRE configuration was inspected using:

reagentc /info

Results showed:

Windows RE status:
Enabled

Windows RE location:
\\?\GLOBALROOT\device\harddisk0\partition4\Recovery\WindowsRE

Windows RE Version:
10.0.26100.9444

REAGENTC.EXE:
Operation Successful

This confirmed that Windows Recovery Environment was enabled and registered.

---

## Recovery Image Location Investigation

The following path was checked:

Test-Path "C:\Windows\System32\Recovery\Winre.wim"

Result:

False

This did not indicate a WinRE failure.

The reagentc output showed that WinRE was registered to the dedicated recovery partition rather than the tested C: path.

This demonstrated why a technician should correlate multiple sources of evidence rather than interpreting one False result in isolation.

---

## Disk and Partition Investigation

The disk layout was inspected using:

Get-Partition |
Select-Object DiskNumber, PartitionNumber, DriveLetter, Type,
    @{Name="SizeGB";Expression={[math]::Round($_.Size / 1GB,2)}} |
Format-Table -AutoSize

CLIENT01 contained:

Partition 1:
System
0.20 GB

Partition 2:
Reserved
0.02 GB

Partition 3:
C:
Basic
62.96 GB

Partition 4:
Recovery
0.83 GB

The physical/virtual disk was also inspected:

Get-Disk |
Select-Object Number, FriendlyName, PartitionStyle,
    @{Name="SizeGB";Expression={[math]::Round($_.Size / 1GB,2)}}

Results:

Disk:
0

Friendly Name:
VBOX HARDDISK

Partition Style:
GPT

Size:
64 GB

The WinRE location reported by reagentc corresponded with Disk 0, Partition 4.

---

## Initial System Restore Assessment

Existing restore points were queried:

Get-ComputerRestorePoint -ErrorAction SilentlyContinue |
Select-Object SequenceNumber, Description, CreationTime, RestorePointType

No restore points were returned.

This established that although WinRE was healthy, CLIENT01 did not currently have a System Restore point available for rollback.

---

## Important Recovery Distinction

WinRE and System Restore are separate recovery technologies.

### Windows Recovery Environment

WinRE provides an advanced recovery environment that can be used when Windows experiences serious startup or operating-system problems.

Recovery options may include:

- Startup Repair
- Advanced startup options
- Command Prompt
- System Restore
- Reset/recovery workflows

### System Restore

System Restore uses restore points to return certain Windows system configuration to an earlier state.

A healthy WinRE configuration does not automatically mean usable restore points exist.

---

## VSS Investigation

Volume Shadow Copy storage was inspected:

vssadmin list shadowstorage

Initial result:

No items found that satisfy the query.

The Volume Shadow Copy service was inspected:

Get-Service VSS |
Select-Object Name, DisplayName, Status, StartType

Result:

Service:
Volume Shadow Copy

Status:
Running

StartType:
Manual

The Microsoft Software Shadow Copy Provider was also inspected:

Get-Service swprv |
Select-Object Name, DisplayName, Status, StartType

Result:

Service:
Microsoft Software Shadow Copy Provider

Status:
Running

StartType:
Manual

The Manual startup type was not treated as a failure because these services can operate on demand.

The investigation therefore distinguished service availability from actual restore-point availability.

---

## Root Cause / Recovery Gap

No failure was identified with Windows Recovery Environment.

WinRE was:

- Enabled
- Registered
- Associated with the dedicated recovery partition

The identified recovery-readiness gap was the absence of System Restore history.

The initial restore-point query returned no entries, and no shadow-copy storage association was initially reported.

Therefore, CLIENT01 had WinRE recovery capability but lacked an available System Restore checkpoint for the scenario.

---

## Resolution

System Protection was enabled for the Windows system drive:

Enable-ComputerRestore -Drive "C:\"

Shadow-copy storage was configured for C: with a maximum allocation of 5%:

vssadmin resize shadowstorage /for=C: /on=C: /maxsize=5%

Shadow storage was then verified using:

vssadmin list shadowstorage

A controlled restore point was created:

Checkpoint-Computer -Description "Ticket069_PreRecoveryTest" -RestorePointType "MODIFY_SETTINGS"

No actual system rollback was performed.

---

## Final Verification

Windows Recovery Environment was verified again:

reagentc /info

Results continued to show:

Windows RE status:
Enabled

Windows RE location:
harddisk0\partition4\Recovery\WindowsRE

The restore-point inventory was then queried:

Get-ComputerRestorePoint |
Select-Object SequenceNumber, Description, CreationTime, RestorePointType |
Format-Table -AutoSize

The resulting restore point showed:

Sequence Number:
1

Description:
Ticket069_PreRecoveryTest

Restore Point Type:
12

The restore point type corresponds to the MODIFY_SETTINGS category used when the checkpoint was created.

---

## Recovery Escalation Strategy

A technician should generally begin with the least destructive appropriate troubleshooting method.

A conceptual escalation path is:

Normal troubleshooting
→ service/application repair
→ system file repair
→ System Restore
→ Windows Recovery Environment
→ Startup Repair / advanced recovery
→ Reset or reinstall Windows

The exact recovery method depends on the failure.

A reset or reinstall should not automatically be the first response to system instability.

---

## Evidence

### 01_CLIENT01_Windows_Recovery_Readiness_Baseline.png

Shows:

- Windows RE enabled
- WinRE location on Disk 0 Partition 4
- Windows RE version
- C:\Windows\System32\Recovery\Winre.wim path test returning False
- Dedicated Recovery partition
- GPT disk layout
- Initial restore-point query returning no entries

This establishes the original recovery configuration and absence of System Restore history.

### 02_CLIENT01_Windows_Recovery_Readiness_Verified.png

Shows:

- Windows RE remains Enabled
- WinRE remains registered to the recovery partition
- Ticket069_PreRecoveryTest restore point exists
- Restore point Sequence Number 1
- Restore Point Type 12

This verifies improved Windows recovery readiness.

---

## Commands Used

reagentc /info

Test-Path "C:\Windows\System32\Recovery\Winre.wim"

Get-Partition

Get-Disk

Get-ComputerRestorePoint

Get-CimInstance

vssadmin list shadowstorage

Get-Service VSS

Get-Service swprv

Enable-ComputerRestore -Drive "C:\"

vssadmin resize shadowstorage /for=C: /on=C: /maxsize=5%

Checkpoint-Computer -Description "Ticket069_PreRecoveryTest" -RestorePointType "MODIFY_SETTINGS"

---

## Skills Demonstrated

- Windows recovery troubleshooting
- Windows Recovery Environment assessment
- System Restore administration
- Recovery partition identification
- GPT partition analysis
- Volume Shadow Copy investigation
- Shadow storage configuration
- Restore-point creation
- Recovery readiness assessment
- PowerShell administration
- Recovery escalation planning
- Evidence correlation
- Risk-aware troubleshooting
- Help desk troubleshooting
- Technical documentation

---

## Cheat Sheet Concepts Introduced

### WinRE

WinRE stands for:

Windows Recovery Environment

It is a recovery environment used to troubleshoot serious Windows problems, particularly when normal Windows startup is unavailable or unreliable.

---

### reagentc

reagentc.exe manages Windows Recovery Environment configuration.

Common diagnostic command:

reagentc /info

Useful information includes:

- WinRE status
- WinRE location
- BCD identifier
- WinRE version

---

### Recovery Partition

A dedicated partition that can contain Windows recovery resources.

It commonly does not have a normal drive letter.

On CLIENT01:

Disk 0
Partition 4
Type: Recovery
Size: approximately 0.83 GB

---

### BCD

BCD stands for:

Boot Configuration Data

It stores Windows boot configuration information.

WinRE configuration can reference a BCD identifier associated with the recovery environment.

---

### System Restore

Windows recovery functionality that uses restore points to roll certain system configuration back to an earlier state.

System Restore is not equivalent to a complete system backup.

---

### Restore Point

A recovery checkpoint representing certain Windows system configuration at a particular time.

Restore points can be useful after problematic:

- Drivers
- Applications
- Updates
- Configuration changes

---

### VSS

VSS stands for:

Volume Shadow Copy Service

It provides snapshot infrastructure used by several Windows backup and recovery technologies.

Service name:

VSS

---

### Shadow Storage

Disk space allocated for volume shadow-copy data.

It can be inspected using:

vssadmin list shadowstorage

---

### System Protection

Windows functionality associated with System Restore and restore-point management for protected drives.

PowerShell can enable it using:

Enable-ComputerRestore

---

### Checkpoint-Computer

PowerShell cmdlet used to request creation of a System Restore point.

Example:

Checkpoint-Computer -Description "RestorePointName" -RestorePointType "MODIFY_SETTINGS"

---

### Recovery Escalation

Recovery escalation means progressing from less destructive troubleshooting methods toward more invasive recovery methods only when necessary.

Examples:

Troubleshoot
→ repair
→ restore
→ recovery environment
→ reset/reinstall

---

## Key Troubleshooting Principle

Do not assume that because a Recovery partition exists, Windows recovery is fully configured.

Likewise, do not assume that because WinRE is enabled, a System Restore point is available.

Validate each layer independently:

Recovery partition exists
→ WinRE registered
→ WinRE enabled
→ recovery services available
→ System Protection configured
→ restore point available

CLIENT01 initially had:

WinRE = Available
Recovery Partition = Available
VSS Infrastructure = Available
Restore Point = Not Available

After remediation:

WinRE = Available
Recovery Partition = Available
VSS Infrastructure = Available
System Protection = Enabled
Restore Point = Available

---

## Safety Considerations

No actual System Restore operation was performed.

No recovery partition was modified.

WinRE was not disabled or reconfigured because it was already healthy.

No Windows reset or reinstall was performed.

The ticket demonstrated recovery readiness without introducing unnecessary risk to CLIENT01.

---

## Outcome

CLIENT01's Windows recovery configuration was successfully assessed and improved.

WinRE was confirmed healthy and registered to:

Disk 0
Partition 4
Recovery\WindowsRE

The initial investigation found no System Restore points.

System Protection and shadow storage were configured, and a controlled restore point named:

Ticket069_PreRecoveryTest

was successfully created.

Final verification confirmed that CLIENT01 now had both a functioning Windows Recovery Environment and an available System Restore checkpoint.