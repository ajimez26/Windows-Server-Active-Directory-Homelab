# Mission 12 - Ticket 047: Troubleshoot Windows Service Failure

## Ticket Summary
Investigated and resolved a simulated Windows service failure on CLIENT01 involving the Windows Search service.

A healthy service baseline was first established using PowerShell. Windows Search was confirmed to be running and configured with an Automatic startup mode.

The Windows Search service was then intentionally stopped to reproduce a service-related incident.

PowerShell troubleshooting demonstrated that the service was stopped even though its startup configuration remained Automatic. This highlighted the important distinction between a service's current status and its configured startup behavior.

The Windows Services management console was then used to inspect the affected service, confirm its stopped state, and restore operation.

Final PowerShell verification confirmed that Windows Search returned to a Running state.

## Environment
- Client: CLIENT01
- Domain: jimeneztech.local
- Client Operating System: Windows 11 Enterprise
- Service: Windows Search
- Service Name: WSearch
- Administration Tools:
  - PowerShell
  - Windows Services Console
  - Windows Management Instrumentation / CIM

## Tasks Completed
- Identified the Windows Search service
- Established a known-good service baseline
- Verified the service was initially running
- Verified the configured startup mode
- Intentionally stopped the service
- Reproduced a Windows service failure
- Verified the stopped state using Get-Service
- Queried detailed service configuration using Get-CimInstance
- Distinguished service status from startup configuration
- Investigated the service using the Services management console
- Confirmed the service was stopped through the GUI
- Restored the Windows Search service
- Verified the service returned to a Running state
- Documented the troubleshooting and recovery process

## Initial Service Baseline
Windows Search was initially examined using PowerShell.

Command:

Get-Service -Name WSearch

The output showed:

Status:
Running

Name:
WSearch

DisplayName:
Windows Search

This established that the service was functioning normally before the simulated incident.

## Service Configuration Analysis
Additional information about the service was retrieved using:

Get-CimInstance Win32_Service -Filter "Name = 'WSearch'" | Select-Object Name, DisplayName, State, StartMode

The results showed:

Name:
WSearch

DisplayName:
Windows Search

State:
Running

StartMode:
Auto

This confirmed that Windows Search was both currently running and configured to start automatically.

## Simulated Incident
To reproduce a Windows service failure, the Windows Search service was intentionally stopped using:

Stop-Service -Name WSearch

The service state was then checked using:

Get-Service -Name WSearch

The result showed:

Status:
Stopped

Name:
WSearch

DisplayName:
Windows Search

The simulated service failure was successfully reproduced.

## Detailed Service Diagnosis
The service was queried again using:

Get-CimInstance Win32_Service -Filter "Name = 'WSearch'" | Select-Object Name, DisplayName, State, StartMode

The resulting configuration showed:

State:
Stopped

StartMode:
Auto

This provided an important troubleshooting observation.

Although the Windows Search service was configured with an Automatic startup mode, its current operational state was Stopped.

## Service Status vs Startup Type
This ticket demonstrated the difference between two important Windows service properties.

Service Status:
Describes what the service is doing at the current moment.

Common states include:

- Running
- Stopped
- Paused
- Start Pending
- Stop Pending

Startup Type:
Determines how Windows is configured to start the service.

Common startup configurations include:

Automatic:
Windows is configured to start the service automatically.

Automatic (Delayed Start):
The service starts automatically after other startup operations have completed.

Manual:
The service starts when Windows, another service, an application, or an administrator requests it.

Disabled:
The service cannot be started until its startup configuration is changed.

A service configured as Automatic can still be stopped manually or because of a failure.

Therefore:

Automatic does not necessarily mean Running.

This distinction is useful when diagnosing Windows applications or features that depend on background services.

## GUI Investigation
The Windows Services management console was opened using:

services.msc

Windows Search was located within the list of installed services.

The service properties were reviewed to confirm:

Service Name:
WSearch

Display Name:
Windows Search

Startup Type:
Automatic / Automatic (Delayed Start), as configured by Windows

Service Status:
Stopped

This provided graphical confirmation of the same service condition previously identified through PowerShell.

## Root Cause
The affected Windows feature depended on the Windows Search service, which was not currently running.

PowerShell and the Services management console both confirmed that:

WSearch was Stopped

while its startup configuration remained automatic.

The stopped Windows service was therefore identified as the cause of the simulated service availability issue.

## Resolution
The Windows Search service was restarted through the Windows Services management console.

The service status changed from:

Stopped

to:

Running

No network, DHCP, DNS, firewall, or domain configuration changes were required.

## Post-Resolution Verification
After restoring the service, PowerShell was used to verify its operational state.

Command:

Get-Service -Name WSearch

The final result showed:

Status:
Running

Name:
WSearch

DisplayName:
Windows Search

This confirmed that the affected Windows service had been successfully restored.

## Troubleshooting Methodology
The incident was resolved using the following process:

1. Establish a known-good service baseline
2. Identify the service name
3. Verify the current service state
4. Review the configured startup mode
5. Reproduce the reported service failure
6. Confirm the stopped state using PowerShell
7. Compare current status with startup configuration
8. Verify the condition through the Services console
9. Restore the affected service
10. Retest the service
11. Confirm the Running state
12. Document the incident and resolution

This approach demonstrates how Windows service problems can be investigated using both command-line and graphical administration tools.

## Verification
Final verification confirmed:

- Windows Search service name was WSearch
- WSearch was initially Running
- WSearch was configured with an automatic startup mode
- WSearch was successfully stopped for troubleshooting
- Get-Service reported WSearch as Stopped
- Get-CimInstance reported State as Stopped
- StartMode remained Auto while the service was stopped
- Services.msc confirmed the stopped service condition
- The Windows Search service was successfully restored
- Get-Service confirmed WSearch returned to Running
- No unrelated network or domain configuration was modified

## Evidence
01_CLIENT01_Windows_Search_Service_Baseline.png
- Shows Get-Service output for WSearch
- Shows Windows Search initially Running
- Shows Get-CimInstance service information
- Shows State as Running
- Shows StartMode as Auto
- Establishes the known-good service baseline

02_CLIENT01_Windows_Search_Service_Stopped.png
- Shows Stop-Service being used on WSearch
- Shows Get-Service reporting the service as Stopped
- Shows Get-CimInstance reporting State as Stopped
- Shows StartMode remaining Auto
- Demonstrates the simulated service failure

03_CLIENT01_Windows_Search_Service_Diagnosis.png
- Shows Windows Search within the Services management console
- Shows the service configuration
- Shows the service in a Stopped state
- Provides GUI-based confirmation of the problem

04_CLIENT01_Windows_Search_Service_Restored.png
- Shows final PowerShell verification
- Shows Windows Search returned to Running
- Confirms successful remediation

## Commands Used
Get-Service -Name WSearch

Get-CimInstance Win32_Service -Filter "Name = 'WSearch'" | Select-Object Name, DisplayName, State, StartMode

Stop-Service -Name WSearch

services.msc

## Skills Demonstrated
- Windows 11 Administration
- Windows Service Management
- Windows Service Troubleshooting
- PowerShell
- Get-Service
- Stop-Service
- CIM / WMI Queries
- Get-CimInstance
- Services.msc
- Service Status Analysis
- Startup Type Analysis
- Windows Endpoint Troubleshooting
- Incident Reproduction
- Root Cause Analysis
- Service Recovery
- Post-Resolution Verification
- Help Desk Troubleshooting
- Technical Documentation

## Cheat Sheet Concepts Introduced
Windows Service
- A background process used by Windows or applications to provide system functionality without requiring direct user interaction.

Service Status
- The service's current operational condition, such as Running or Stopped.

Startup Type
- Determines how and when Windows attempts to start a service.

Automatic
- Configures Windows to start the service automatically.

Automatic (Delayed Start)
- Starts the service automatically after higher-priority startup operations.

Manual
- Allows the service to start when requested by Windows, an application, another service, or an administrator.

Disabled
- Prevents the service from starting until its configuration is changed.

Get-Service
- PowerShell command used to retrieve the current status of Windows services.

Stop-Service
- PowerShell command used to stop a Windows service.

Start-Service
- PowerShell command used to start a Windows service.

Get-CimInstance Win32_Service
- Retrieves detailed Windows service information, including operational state and startup configuration.

services.msc
- Windows graphical management console used to view and administer services.

Key Troubleshooting Distinction:
Startup Type does not equal Current Status.

A service configured as Automatic can still currently be Stopped.

## Outcome
A simulated Windows service failure was successfully reproduced, diagnosed, and resolved on CLIENT01.

Windows Search was initially confirmed as Running with an automatic startup configuration.

The service was intentionally stopped, after which both Get-Service and Get-CimInstance confirmed that its current state was Stopped while its StartMode remained Auto.

This demonstrated that startup configuration and current operational state are separate properties and should both be checked when troubleshooting Windows services.

The Services management console provided graphical confirmation of the failure. The Windows Search service was then restored and PowerShell verification confirmed that it returned to a Running state.

This ticket demonstrated practical Windows service troubleshooting using both PowerShell and graphical administrative tools.