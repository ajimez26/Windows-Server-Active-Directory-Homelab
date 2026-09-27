# Mission 12 - Ticket 066: Troubleshoot Windows Installer MSI Failure

## Ticket Summary

Investigated and resolved a simulated Windows application installation failure on CLIENT01.

A deliberately invalid file named:

C:\LabMSI\BrokenApplication.msi

was created to simulate a damaged or invalid Windows Installer package.

The installation was attempted using msiexec.exe with verbose logging enabled.

Windows Installer returned:

Exit Code 1620

The verbose MSI log also showed:

MainEngineThread is returning 1620

The Windows error code was translated using:

net helpmsg 1620

which identified the problem as an installation package that could not be opened as a valid Windows Installer package.

Additional troubleshooting verified that:

- msiexec.exe existed
- Windows Installer remained configured for Manual startup
- There was no evidence that Windows Installer itself was damaged

The invalid package was removed without unnecessarily modifying the Windows Installer service.

---

## Environment

- Client: CLIENT01
- Operating System: Windows 11 Enterprise Evaluation
- Domain: jimeneztech.local
- Service: Windows Installer
- Service Name: msiserver
- Service Account: LocalSystem
- Startup Type: Manual
- Installer Executable:
  C:\WINDOWS\System32\msiexec.exe
- msiexec Version:
  5.0.26100.1
- Lab Directory:
  C:\LabMSI
- Administrative Tool:
  Windows PowerShell

---

## Ticket Request

A user reports that an application fails during installation.

Investigate:

- Windows Installer service state
- Windows Installer startup configuration
- msiexec.exe availability
- Installer package validity
- MSI exit code
- Verbose Windows Installer logs

Determine whether the failure is caused by Windows Installer itself or by the supplied installation package.

Remediate the identified problem without making unnecessary system changes.

---

## Windows Installer Baseline

The Windows Installer service was inspected:

Get-Service msiserver |
Select-Object Name, DisplayName, Status, StartType

Results:

Name:
msiserver

Display Name:
Windows Installer

Status:
Stopped

Startup Type:
Manual

A stopped Windows Installer service with Manual startup can be normal while no MSI installation is actively running.

The service should not automatically be considered broken simply because it is stopped while idle.

---

## Service Configuration Investigation

Additional Windows Installer information was retrieved:

Get-CimInstance Win32_Service -Filter "Name='msiserver'" |
Select-Object Name, State, StartMode, StartName, PathName

Results:

Name:
msiserver

State:
Stopped

Start Mode:
Manual

Start Account:
LocalSystem

Path:
C:\WINDOWS\system32\msiexec.exe /V

This confirmed that the service configuration referenced the expected Windows Installer executable.

---

## Windows Installer Executable Verification

The existence of msiexec.exe was checked:

Test-Path "$env:SystemRoot\System32\msiexec.exe"

Result:

True

File information was retrieved:

Get-Item "$env:SystemRoot\System32\msiexec.exe" |
Select-Object Name, FullName,
    @{Name="Version";Expression={$_.VersionInfo.FileVersion}}

Results:

Name:
msiexec.exe

Path:
C:\WINDOWS\System32\msiexec.exe

Version:
5.0.26100.1

This confirmed that the Windows Installer executable existed on CLIENT01.

---

## Event Log Baseline

Recent MsiInstaller events were queried:

Get-WinEvent -FilterHashtable @{
    LogName      = "Application"
    ProviderName = "MsiInstaller"
} -MaxEvents 10 -ErrorAction SilentlyContinue |
Select-Object TimeCreated, Id, LevelDisplayName, Message |
Format-List

No useful recent MsiInstaller events were returned during the initial baseline.

The investigation therefore continued using a verbose MSI installation log.

---

## Simulated Incident

A lab directory was created:

New-Item -ItemType Directory -Path "C:\LabMSI" -Force

A file with an .msi extension was deliberately created without containing a valid MSI database:

"This is not a valid Windows Installer package." |
Set-Content "C:\LabMSI\BrokenApplication.msi"

The file represented a simulated invalid or damaged application installer.

---

## Installation Attempt

The invalid MSI package was passed to Windows Installer:

$Process = Start-Process msiexec.exe `
    -ArgumentList '/i "C:\LabMSI\BrokenApplication.msi" /qn /L*V "C:\LabMSI\InstallFailure.log"' `
    -Wait `
    -PassThru

The process exit code was retrieved:

$Process.ExitCode

Result:

1620

A successful MSI operation normally returns exit code 0.

The nonzero result established that the installation failed.

---

## Verbose MSI Logging

Verbose logging was enabled using:

/L*V

The log was written to:

C:\LabMSI\InstallFailure.log

The file was verified using:

Get-Item "C:\LabMSI\InstallFailure.log" |
Select-Object Name, Length, LastWriteTime

The generated log measured approximately:

5018 bytes

The final portion of the log was inspected:

Get-Content "C:\LabMSI\InstallFailure.log" -Tail 30

The log showed:

Product:
C:\LabMSI\BrokenApplication.msi

and:

MainEngineThread is returning 1620

This correlated the Windows Installer failure with exit code 1620.

---

## Error Code Investigation

The numeric Windows error code was translated using:

net helpmsg 1620

Windows returned:

This installation package could not be opened. Contact the application vendor to verify that this is a valid Windows Installer package.

This provided a human-readable explanation of the failure.

---

## Root Cause

The supplied BrokenApplication.msi file was not a valid Windows Installer package.

Evidence included:

- msiexec.exe existed
- Windows Installer configuration remained intact
- The MSI operation launched
- Windows Installer returned exit code 1620
- The verbose MSI log recorded return code 1620
- net helpmsg translated 1620 as an invalid/unopenable Windows Installer package

The evidence therefore pointed to the package rather than a damaged Windows Installer subsystem.

---

## Resolution

The invalid installation package was removed:

Remove-Item "C:\LabMSI\BrokenApplication.msi" -Force

Its removal was verified:

Test-Path "C:\LabMSI\BrokenApplication.msi"

Result:

False

In a production environment, the next remediation step would be to obtain a known-good installer from an approved and trusted source before attempting installation again.

The Windows Installer service was not unnecessarily reconfigured.

---

## Final Verification

The final state was checked:

$BrokenPackageExists = Test-Path "C:\LabMSI\BrokenApplication.msi"

$Installer = Get-Service msiserver

[PSCustomObject]@{
    InvalidPackagePresent = $BrokenPackageExists
    MSIExecutablePresent  = Test-Path "$env:SystemRoot\System32\msiexec.exe"
    InstallerStartType    = $Installer.StartType
}

Results:

InvalidPackagePresent:
False

MSIExecutablePresent:
True

InstallerStartType:
Manual

This confirmed that:

- The invalid package had been removed
- msiexec.exe remained present
- Windows Installer retained its normal Manual startup configuration

---

## Evidence

### 01_CLIENT01_Invalid_MSI_Package_Diagnosed.png

Shows:

- Verbose MSI log returning error 1620
- net helpmsg 1620 translation
- MSIExitCode = 1620
- InstallerExists = True
- InstallerStartType = Manual

This establishes the root cause of the simulated installation failure.

### 02_CLIENT01_Invalid_MSI_Package_Removed.png

Shows:

- InvalidPackagePresent = False
- MSIExecutablePresent = True
- InstallerStartType = Manual

This confirms successful remediation without unnecessary modification of Windows Installer.

---

## Commands Used

Get-Service msiserver

Get-CimInstance Win32_Service -Filter "Name='msiserver'"

Test-Path "$env:SystemRoot\System32\msiexec.exe"

Get-Item "$env:SystemRoot\System32\msiexec.exe"

Get-WinEvent

New-Item

Set-Content

Start-Process msiexec.exe

$Process.ExitCode

Get-Content

net helpmsg 1620

Remove-Item

Test-Path

---

## Skills Demonstrated

- Windows application installation troubleshooting
- Windows Installer
- MSI troubleshooting
- msiexec.exe
- Windows services
- PowerShell
- Process exit-code analysis
- Verbose installer logging
- Windows error-code interpretation
- net helpmsg
- Installer package validation
- Root cause analysis
- Evidence-based troubleshooting
- Safe remediation
- Help desk troubleshooting
- Technical documentation

---

## Cheat Sheet Concepts Introduced

### MSI

MSI commonly refers to a Windows Installer package.

Windows Installer packages contain structured installation information used by Windows to install, modify, repair, or remove applications.

An .msi extension alone does not guarantee that the file contains a valid MSI database.

---

### Windows Installer

Windows Installer is the Windows installation technology responsible for processing MSI packages.

Service name:

msiserver

The service commonly uses Manual startup and can start on demand when required.

Stopped + Manual while idle does not automatically indicate a failure.

---

### msiexec.exe

msiexec.exe is the Windows Installer command-line executable.

Default location:

C:\Windows\System32\msiexec.exe

It can be used to:

- Install MSI packages
- Uninstall MSI packages
- Repair applications
- Generate installer logs
- Perform silent installations

---

### MSI Exit Code

Windows Installer operations return numeric exit codes.

Exit code:

0

generally represents success.

Nonzero codes indicate that additional investigation may be required.

In this ticket:

1620

identified an invalid or unopenable installation package.

---

### Error 1620

Windows error 1620 indicates:

The installation package could not be opened as a valid Windows Installer package.

Possible real-world causes can include:

- Corrupted installer
- Invalid MSI file
- Incomplete download
- Incorrect file
- Package inaccessible to the installation process

The specific cause should be determined from evidence rather than assumed.

---

### net helpmsg

net helpmsg can translate many Windows numeric error codes into human-readable messages.

Example:

net helpmsg 1620

This is useful when troubleshooting commands or applications that return only a numeric Windows error.

---

### Verbose MSI Logging

Windows Installer can generate detailed diagnostic logs.

The option used in this ticket was:

/L*V

Example:

msiexec /i package.msi /L*V install.log

Verbose logs can reveal:

- Package being processed
- Installer actions
- Return codes
- Installation sequence
- Failure information

---

### Process Exit Code

Applications and command-line processes can return numeric values when they terminate.

PowerShell can capture an exit code using:

Start-Process -Wait -PassThru

followed by:

$Process.ExitCode

Exit codes are useful because they provide structured evidence about whether an operation succeeded or failed.

---

## Key Troubleshooting Principle

Do not repair a Windows subsystem simply because an operation using that subsystem failed.

In this ticket:

Application installation failed
→ verify Windows Installer configuration
→ verify msiexec.exe
→ reproduce failure
→ capture exit code
→ generate verbose MSI log
→ correlate log with exit code
→ translate error
→ identify invalid package
→ remove bad package
→ leave healthy Windows Installer configuration unchanged

The important distinction was:

"MSI installation failed"

does not automatically mean:

"Windows Installer is broken."

Troubleshooting should follow the evidence to the failing component.

---

## Important Scope Note

BrokenApplication.msi was deliberately created as an invalid text file with an .msi extension.

This ticket did not demonstrate corruption of a legitimate third-party MSI package.

It demonstrated the Windows troubleshooting workflow for identifying an invalid/unopenable installer package.

---

## Outcome

A simulated Windows application installation failure was successfully diagnosed on CLIENT01.

Windows Installer returned:

Exit Code 1620

The verbose MSI log independently recorded:

MainEngineThread is returning 1620

Windows translated the error as an installation package that could not be opened as a valid Windows Installer package.

Investigation verified that:

- msiexec.exe existed
- Windows Installer retained Manual startup
- The failure originated from the supplied package

The invalid package was removed without unnecessarily changing the Windows Installer service.

The ticket demonstrated MSI troubleshooting, verbose installation logging, exit-code analysis, Windows error interpretation, component isolation, and evidence-based remediation.