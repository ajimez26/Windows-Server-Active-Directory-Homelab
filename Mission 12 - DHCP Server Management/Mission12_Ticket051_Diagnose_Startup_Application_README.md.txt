# Mission 12 - Ticket 051: Diagnose a Windows Startup Application

## Ticket Summary
Investigated and modified startup application behavior on CLIENT01 after identifying an application configured to launch automatically when the user signs in.

PowerShell was first used to inventory startup applications through the Win32_StartupCommand CIM class.

The startup inventory identified several entries, including OneDrive, SecurityHealth, and VBoxTray.

Windows Task Manager was then used to review the startup status and startup impact of the applications. OneDrive was identified as Enabled with a High startup impact.

The OneDrive startup configuration was further investigated through the Windows Registry. PowerShell confirmed that OneDrive had a startup entry under the current user's Run registry key and was configured to launch OneDrive.exe with the /background argument.

Because OneDrive was a noncritical and reversible startup application for this lab, it was selected for remediation instead of Windows Security or VirtualBox Guest Additions components.

OneDrive startup was disabled through Task Manager.

Post-change verification confirmed that Task Manager reported OneDrive as Disabled while its underlying startup command remained discoverable through Win32_StartupCommand.

This demonstrated that the existence of a startup entry and its enabled/disabled startup state are separate concepts in Windows.

## Environment
- Client: CLIENT01
- Domain: jimeneztech.local
- Client Operating System: Windows 11 Enterprise Evaluation
- User Context: Administrator
- Application Investigated: Microsoft OneDrive
- Administrative Tools:
  - Windows PowerShell
  - Windows Task Manager
  - Windows Registry
  - CIM / WMI

## Tasks Completed
- Inventoried Windows startup applications
- Identified startup application names and commands
- Reviewed startup configuration locations
- Reviewed user context associated with startup entries
- Opened Task Manager Startup Apps
- Compared enabled and disabled startup applications
- Reviewed startup impact ratings
- Identified OneDrive as Enabled
- Identified OneDrive as having High startup impact
- Selected a noncritical application for remediation
- Located OneDrive's current-user Registry Run entry
- Identified the OneDrive executable path
- Identified the /background startup argument
- Disabled OneDrive automatic startup using Task Manager
- Verified OneDrive reported as Disabled
- Re-queried OneDrive using PowerShell
- Confirmed the underlying startup command remained discoverable
- Distinguished startup configuration from startup approval state
- Avoided modifying security and VirtualBox startup components
- Documented the configuration change and verification

## Initial Startup Application Inventory
PowerShell was used to inventory applications configured for Windows startup.

Command:

Get-CimInstance Win32_StartupCommand |
Select-Object Name, Command, Location, User

The results included startup entries such as:

- OneDriveSetup
- OneDrive
- SecurityHealth
- VBoxTray

The command also displayed information about:

- Startup entry name
- Command executed
- Configuration location
- Associated user

This provided a command-line baseline before making any configuration changes.

## Win32_StartupCommand
Win32_StartupCommand is a Windows CIM/WMI class that can be queried to identify commands configured to execute automatically during startup or user sign-in.

It can provide information including:

Name
Command
Location
User

This is useful when troubleshooting unexpected applications that automatically launch when a user signs in.

## Task Manager Startup Analysis
Windows Task Manager was opened and the Startup Apps section was reviewed.

Several startup applications were visible.

OneDrive showed:

Status:
Enabled

Startup Impact:
High

Other enabled entries included:

SecurityHealthSystray
- Enabled
- Low impact

VBoxTray
- Enabled
- Low impact

Several other applications were already disabled.

OneDrive was selected for further investigation because it was enabled, had a High startup impact, and was a safer candidate for modification than Windows Security or VirtualBox Guest Additions components.

## Startup Impact
Task Manager assigns startup-impact ratings to help indicate how much system resource usage an application has demonstrated during startup.

Common ratings may include:

- None
- Low
- Medium
- High

OneDrive displayed a High startup impact on CLIENT01.

The startup-impact value was used as supporting information when selecting the application for investigation.

A High rating does not by itself prove that an application is causing a performance problem, but it can help prioritize startup applications for review.

## Registry Investigation
Before modifying OneDrive's startup behavior, its configuration was investigated using PowerShell.

Command:

Get-ItemProperty "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run"

The result showed:

OneDrive:
"C:\Users\Administrator\AppData\Local\Microsoft\OneDrive\OneDrive.exe" /background

Registry location:

HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run

This confirmed that OneDrive had a startup command associated with the current user's profile.

## Registry Run Key
The Windows Registry contains Run locations that applications can use to configure automatic execution when a user signs in.

The location investigated during this ticket was:

HKCU\Software\Microsoft\Windows\CurrentVersion\Run

HKCU represents:

HKEY_CURRENT_USER

This registry hive contains configuration associated with the currently logged-in user.

The OneDrive value referenced:

OneDrive.exe

with the argument:

/background

This indicated that OneDrive was configured to launch in the background for the current user.

## Registry Hive Concepts
Several Windows Registry hive abbreviations were encountered during startup analysis.

HKCU
HKEY_CURRENT_USER

Contains settings associated with the currently logged-in user.

HKLM
HKEY_LOCAL_MACHINE

Contains system-wide configuration applying to the computer.

HKU
HKEY_USERS

Contains user-profile registry information for user security identifiers loaded on the system.

Understanding the scope of a registry entry is important when determining whether startup behavior applies to one user or the entire computer.

## Application Selection
OneDrive was intentionally selected instead of modifying other enabled startup entries.

SecurityHealthSystray was left enabled because it is associated with Windows Security notifications.

VBoxTray was left enabled because it is associated with VirtualBox Guest Additions and supports functionality within the virtual machine environment.

Selecting a noncritical and reversible startup application reduced the risk of disrupting CLIENT01.

## Remediation
OneDrive automatic startup was disabled through:

Task Manager
→ Startup apps
→ OneDrive
→ Disable

After the change, Task Manager showed:

OneDrive

Status:
Disabled

Startup Impact:
High

The High startup-impact rating remained visible after the application was disabled.

This demonstrated that startup impact and startup status represent different properties.

## Startup Status vs Startup Impact
Startup Status indicates whether Windows currently permits the startup application to launch automatically.

Examples:

Enabled
Disabled

Startup Impact describes the application's measured impact on startup performance.

Therefore:

Disabled + High

is a valid combination.

The High rating does not mean the application is still enabled.

## Post-Change PowerShell Verification
After disabling OneDrive through Task Manager, PowerShell was used to query the startup command again.

Command:

Get-CimInstance Win32_StartupCommand |
Where-Object {$_.Name -eq "OneDrive"} |
Select-Object Name, Command, Location, User

OneDrive remained visible in the results.

The command continued to reference:

"C:\Users\Administrator\AppData\Local\Microsoft\OneDrive\OneDrive.exe" /background

This demonstrated that discovering an underlying startup command does not necessarily prove that the startup entry is currently enabled.

Task Manager provided the relevant enabled/disabled state for the startup application.

## Startup Entry vs Startup State
An important troubleshooting distinction was demonstrated during this ticket.

Startup Entry Exists:
Indicates that startup configuration for the application is present or discoverable.

Startup Status:
Indicates whether Windows currently permits that startup application to execute automatically.

Therefore:

Startup entry exists ≠ startup entry enabled

A technician should verify startup status rather than assuming that the presence of a registry Run entry means an application will automatically launch.

## Disable vs Stop vs Uninstall
This ticket also demonstrated three different application-management concepts.

Disable Startup:
Prevents the application from automatically launching during user sign-in.

Stop Process:
Terminates a currently running instance of an application or process.

Uninstall:
Removes the application from the computer.

These actions are not interchangeable.

Disabling OneDrive startup did not uninstall OneDrive.

It also did not necessarily terminate an already-running OneDrive process.

The configuration change specifically targeted automatic startup behavior.

## Troubleshooting Methodology
The ticket followed this process:

1. Inventory startup applications
2. Review application startup commands
3. Identify configuration locations
4. Compare the results with Task Manager
5. Review startup status
6. Review startup impact
7. Select a safe application for investigation
8. Inspect its underlying Registry Run entry
9. Identify whether the entry is user-specific
10. Review the executable and startup argument
11. Disable automatic startup through Task Manager
12. Verify the application reports as Disabled
13. Query the startup command again
14. Compare configuration presence with enabled/disabled state
15. Avoid modifying critical or lab-dependent startup components
16. Document the change and findings

## Verification
Final verification confirmed:

- Win32_StartupCommand successfully inventoried startup applications
- OneDrive was configured as a startup application
- Task Manager initially reported OneDrive as Enabled
- OneDrive had a High startup-impact rating
- OneDrive had a current-user Registry Run entry
- The Run entry referenced OneDrive.exe
- The command included the /background argument
- OneDrive startup was disabled through Task Manager
- Task Manager reported OneDrive as Disabled after the change
- The High startup-impact rating remained visible
- Win32_StartupCommand continued to return the OneDrive startup command
- The underlying startup entry was not manually deleted
- Windows Security startup components were not disabled
- VBoxTray was not disabled
- OneDrive itself was not uninstalled

## Evidence
01_CLIENT01_Startup_Applications_Baseline.png
- Shows startup applications queried through PowerShell
- Shows OneDrive startup configuration
- Shows SecurityHealth and VBoxTray
- Shows startup command and configuration-location information

02_CLIENT01_Startup_Apps_Task_Manager.png
- Shows Windows Task Manager Startup Apps
- Shows OneDrive initially Enabled
- Shows OneDrive with High startup impact
- Shows other enabled and disabled startup applications

03_CLIENT01_OneDrive_Startup_Registry_Verification.png
- Shows the HKCU Run registry key
- Shows the OneDrive registry value
- Shows the OneDrive executable path
- Shows the /background argument
- Confirms current-user startup configuration

04_CLIENT01_OneDrive_Startup_Disabled.png
- Shows OneDrive after remediation
- Shows OneDrive status as Disabled
- Shows the High startup-impact rating remains
- Confirms the Task Manager configuration change

05_CLIENT01_OneDrive_Startup_Command_Post_Disable.png
- Shows the post-change Win32_StartupCommand query
- Shows OneDrive remains discoverable as a startup command
- Demonstrates the distinction between configuration presence and startup enabled state

## Commands Used
Get-CimInstance Win32_StartupCommand |
Select-Object Name, Command, Location, User

Get-ItemProperty "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run"

Get-CimInstance Win32_StartupCommand |
Where-Object {$_.Name -eq "OneDrive"} |
Select-Object Name, Command, Location, User

## Skills Demonstrated
- Windows 11 Administration
- Windows Startup Troubleshooting
- Startup Application Management
- Windows Task Manager
- PowerShell
- Get-CimInstance
- CIM / WMI
- Windows Registry
- Registry Run Keys
- HKCU Registry Analysis
- Startup Impact Analysis
- User-Specific Configuration Analysis
- Application Performance Troubleshooting
- Safe Configuration Changes
- Post-Change Verification
- Help Desk Troubleshooting
- Technical Documentation

## Cheat Sheet Concepts Introduced
Startup Application
- Application configured to launch automatically during system startup or user sign-in.

Startup Apps
- Task Manager interface used to review and manage applications configured for automatic startup.

Startup Status
- Indicates whether a startup application is Enabled or Disabled.

Startup Impact
- Task Manager rating representing the application's measured impact during startup.

Win32_StartupCommand
- CIM/WMI class containing information about applications and commands configured for startup.

Get-CimInstance
- PowerShell cmdlet used to retrieve management information through the Common Information Model.

Windows Registry
- Hierarchical Windows configuration database containing operating-system, application, computer, and user settings.

HKCU
- HKEY_CURRENT_USER.
- Contains configuration associated with the current user.

HKLM
- HKEY_LOCAL_MACHINE.
- Contains configuration associated with the computer and system-wide settings.

HKU
- HKEY_USERS.
- Contains registry information for user profiles loaded on the system.

Run Key
- Registry location commonly used by applications to configure commands that execute during user sign-in.

Current-user Run key:

HKCU\Software\Microsoft\Windows\CurrentVersion\Run

/background
- Command-line argument used by OneDrive in this configuration to launch in the background.

Get-ItemProperty
- PowerShell cmdlet used to retrieve properties and values from locations such as Registry keys.

Where-Object
- PowerShell cmdlet used to filter objects according to specified conditions.

Key Troubleshooting Distinction:

Startup entry exists ≠ Startup entry enabled

Key Management Distinction:

Disable Startup ≠ Stop Process ≠ Uninstall Application

Key Scope Distinction:

HKCU = current-user configuration

HKLM = computer-wide configuration

HKU = loaded user-profile registry configuration

Key Safety Principle:

Identify the purpose of a startup entry before disabling it.

Security, management, driver, virtualization, or other system-related startup components should not be disabled simply because they appear in a startup list.

## Outcome
Startup application behavior on CLIENT01 was successfully investigated using PowerShell, Windows Task Manager, and the Windows Registry.

PowerShell initially identified several startup commands, including OneDrive, SecurityHealth, and VBoxTray.

Task Manager showed that OneDrive was Enabled and had a High startup-impact rating.

The OneDrive configuration was traced to the current user's Registry Run key:

HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run

The startup command launched OneDrive.exe using the /background argument.

OneDrive was selected as a safe and reversible target instead of modifying Windows Security or VirtualBox Guest Additions components.

Automatic OneDrive startup was disabled through Task Manager.

Post-change verification showed OneDrive as Disabled while retaining its High startup-impact rating.

A subsequent Win32_StartupCommand query continued to identify the underlying OneDrive startup command, demonstrating that the presence of startup configuration does not necessarily mean the application is currently enabled for automatic startup.

This ticket demonstrated startup application inventory, Task Manager analysis, Registry investigation, configuration scope analysis, safe remediation, and post-change verification.