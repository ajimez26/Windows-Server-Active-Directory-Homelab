# Mission 12 - Ticket 054: Troubleshoot Windows Service Startup Failure

## Ticket Summary
Investigated a simulated Windows service failure on CLIENT01 involving the Windows Search service (WSearch).

The service was found in a Stopped state with its startup mode configured as Disabled. PowerShell was used to distinguish between the current service status and its startup configuration.

The service startup type was corrected to Automatic, the service was started, and PowerShell verification confirmed that Windows Search returned to a Running state with the proper automatic startup configuration.

## Environment
- Client: CLIENT01
- Host Name: DESKTOP-9NPE7VN
- Domain: jimeneztech.local
- Operating System: Windows 11 Enterprise Evaluation
- Administrative Tool: Windows PowerShell
- Service: Windows Search
- Service Name: WSearch

## Ticket Request
A workstation feature dependent on the Windows Search service is unavailable.

Investigate the associated Windows service, determine why it is not running, restore the appropriate configuration, and verify normal service operation.

## Initial Service Condition
PowerShell was used to inspect the Windows Search service.

Command:

Get-CimInstance Win32_Service -Filter "Name='WSearch'" |
Select-Object Name, DisplayName, State, StartMode

Result:

Name: WSearch
DisplayName: Windows Search
State: Stopped
StartMode: Disabled

The service was therefore not simply stopped.

Its startup configuration was set to Disabled, preventing normal service startup until the configuration was corrected.

## Diagnosis
Two PowerShell methods were used during diagnosis.

Command:

Get-Service -Name WSearch

This confirmed that the current service status was:

Stopped

Additional configuration information was retrieved with:

Get-CimInstance Win32_Service -Filter "Name='WSearch'" |
Select-Object Name, State, StartMode, StartName

The important results were:

State: Stopped
StartMode: Disabled

This identified the root cause as an incorrect service startup configuration.

## Service Status vs Startup Type
Service Status describes what a service is doing at the current moment.

Common states include:

- Running
- Stopped
- Paused

Startup Type determines how Windows is allowed or expected to start the service.

Common startup types include:

Automatic
- Windows normally starts the service automatically.

Manual
- The service can be started when required by Windows, an application, or an administrator.

Disabled
- The service cannot normally be started until its startup configuration is changed.

This distinction was important because simply attempting to start a disabled service would not address the underlying configuration problem.

## Root Cause
The Windows Search service had been configured with:

StartMode: Disabled

and was currently:

State: Stopped

The Disabled startup configuration prevented the service from operating normally.

The correct remediation required both:

1. Correcting the startup type
2. Starting the service

## Remediation
The startup type was changed to Automatic.

Command:

Set-Service -Name WSearch -StartupType Automatic

The service was then started.

Command:

Start-Service -Name WSearch

This restored the intended service configuration and operation.

## Verification
PowerShell was used to verify both the current state and startup configuration.

Command:

Get-CimInstance Win32_Service -Filter "Name='WSearch'" |
Select-Object Name, State, StartMode

Final result:

Name: WSearch
State: Running
StartMode: Auto

This confirmed that:

- Windows Search was running
- The service was no longer disabled
- Automatic startup was restored
- The remediation was successful

## Evidence

01_CLIENT01_Windows_Search_Service_Failure.png
- Shows Windows Search in a Stopped state
- Shows StartMode configured as Disabled
- Establishes the initial problem condition

02_CLIENT01_Windows_Search_Service_Remediated.png
- Shows Set-Service changing the startup type to Automatic
- Shows Start-Service starting Windows Search
- Shows final State as Running
- Shows final StartMode as Auto
- Demonstrates successful remediation and verification

## Commands Used

Get-CimInstance Win32_Service -Filter "Name='WSearch'" |
Select-Object Name, DisplayName, State, StartMode

Get-Service -Name WSearch

Get-CimInstance Win32_Service -Filter "Name='WSearch'" |
Select-Object Name, State, StartMode, StartName

Set-Service -Name WSearch -StartupType Automatic

Start-Service -Name WSearch

Get-CimInstance Win32_Service -Filter "Name='WSearch'" |
Select-Object Name, State, StartMode

## Skills Demonstrated
- Windows 11 Administration
- Windows Service Management
- Windows Service Troubleshooting
- PowerShell
- Get-Service
- Get-CimInstance
- Set-Service
- Start-Service
- Win32_Service
- Service Status Analysis
- Startup Configuration Analysis
- Root Cause Analysis
- Endpoint Troubleshooting
- Help Desk Troubleshooting
- Remediation Verification
- Technical Documentation

## Cheat Sheet Concepts Introduced

Windows Service
- A background component that performs operating-system or application functions without requiring direct user interaction.

Service Name
- Internal name Windows uses to identify a service.
- Example: WSearch.

Display Name
- Human-readable name displayed for the service.
- Example: Windows Search.

Service Status
- Describes the service's current operational state.
- Examples: Running or Stopped.

Startup Type
- Determines how or whether Windows starts a service.

Automatic
- Configures a service to start automatically as appropriate during Windows startup.

Manual
- Allows a service to be started when required rather than automatically at every startup.

Disabled
- Prevents the service from being started normally until its startup type is changed.

Get-Service
- PowerShell cmdlet used to retrieve the current status of Windows services.

Get-CimInstance
- PowerShell cmdlet used to retrieve management information through CIM.
- Win32_Service can expose additional service properties such as State, StartMode, and StartName.

Win32_Service
- CIM/WMI class containing configuration and status information about Windows services.

Set-Service
- PowerShell cmdlet used to modify properties of a Windows service, including its startup type.

Start-Service
- PowerShell cmdlet used to start a Windows service.

Key Troubleshooting Principle:

Stopped does not automatically mean broken.

A technician should determine WHY the service is stopped before attempting remediation.

Key Configuration Principle:

Service Status and Startup Type are different properties.

A service can be:

Stopped + Automatic
Stopped + Manual
Stopped + Disabled
Running + Automatic

The combination provides more diagnostic information than service status alone.

Key Troubleshooting Workflow:

Reported feature failure
→ identify supporting service
→ check service status
→ inspect startup configuration
→ determine root cause
→ correct configuration
→ start service
→ verify status and configuration
→ document resolution

## Outcome
The Windows Search service on CLIENT01 was identified as Stopped with its startup mode configured as Disabled.

PowerShell diagnostics confirmed that the issue involved both the current service state and the underlying startup configuration.

The Windows Search startup type was changed to Automatic using Set-Service, and the service was restored using Start-Service.

Final PowerShell verification showed:

State: Running
StartMode: Auto

The service was successfully restored to normal operation.

The ticket demonstrated the ability to distinguish between service status and startup configuration, diagnose a Windows service failure, remediate the underlying configuration, and verify the result using PowerShell.