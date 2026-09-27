# Mission 12 - Ticket 061: Troubleshoot Windows User Profile Path

## Ticket Summary

Investigated and resolved a simulated Windows user profile registration issue on CLIENT01.

The domain user JIMENEZTECH\slopez had an existing local Windows profile located at:

C:\Users\slopez

The Windows ProfileList registry configuration was deliberately changed so the user's SID referenced a nonexistent profile directory:

C:\Users\slopez_broken

Troubleshooting confirmed that the user's actual profile data still existed and that the problem was caused by an incorrect SID-to-profile-path registry mapping.

The ProfileImagePath registry value was restored to the correct location and Windows profile information was verified after remediation.

---

## Environment

- Client: CLIENT01
- Operating System: Windows 11 Enterprise Evaluation
- Domain: jimeneztech.local
- Test User: JIMENEZTECH\slopez
- Correct Profile Path: C:\Users\slopez
- Administrative Tool: Windows PowerShell
- Registry Location:
  HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList
- User SID:
  S-1-5-21-3463391948-2177679161-975072197-1111

---

## Ticket Request

A user's Windows profile is not correctly associated with its expected profile directory.

Investigate:

- Existing Windows user profiles
- The user's SID
- The ProfileList registry configuration
- The configured ProfileImagePath
- Whether the registered directory exists
- Whether the user's actual profile data still exists

Correct the profile registration without deleting the user's existing profile data.

---

## Initial Profile Investigation

Existing Windows profiles were inspected using:

Get-CimInstance Win32_UserProfile |
Select-Object LocalPath, SID, Loaded, Special |
Format-Table -AutoSize

CLIENT01 contained normal profiles including:

C:\Users\Administrator
C:\Users\slopez
C:\Users\drodriguez

Sarah's profile was:

C:\Users\slopez

Loaded:
False

Special:
False

The profile was not currently loaded, making it appropriate for the controlled troubleshooting exercise.

---

## User Profile SID Identification

Sarah's Windows profile information was retrieved:

Get-CimInstance Win32_UserProfile |
Where-Object LocalPath -eq "C:\Users\slopez" |
Select-Object LocalPath, SID, Loaded, Special

Her SID was identified as:

S-1-5-21-3463391948-2177679161-975072197-1111

The SID was stored in PowerShell:

$SarahSID = (
    Get-CimInstance Win32_UserProfile |
    Where-Object LocalPath -eq "C:\Users\slopez"
).SID

---

## ProfileList Registry Investigation

Windows stores user profile registration information under:

HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList

The registry subkey associated with Sarah's SID was inspected:

Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList\$SarahSID" |
Select-Object ProfileImagePath, State, RefCount

Healthy baseline:

ProfileImagePath : C:\Users\slopez
State            : 0

This confirmed that the SID initially pointed to the correct profile directory.

---

## Registry Backup

Before modifying the profile registration, the registry configuration was backed up.

A backup directory was created:

New-Item -ItemType Directory -Path "C:\LabBackups" -Force

The ProfileList entry was exported:

reg export "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList\$SarahSID" "C:\LabBackups\slopez_ProfileList.reg" /y

The backup was verified:

Test-Path "C:\LabBackups\slopez_ProfileList.reg"

Result:

True

This provided a recovery copy of the original profile registry configuration before troubleshooting changes were introduced.

---

## Simulated Failure

The ProfileImagePath registry value was deliberately changed to an invalid directory:

C:\Users\slopez_broken

Command:

Set-ItemProperty `
    -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList\$SarahSID" `
    -Name "ProfileImagePath" `
    -Value "C:\Users\slopez_broken"

The configured path was retrieved and tested.

Result:

ProfileImagePath:
C:\Users\slopez_broken

Test-Path:
False

This established the simulated failure condition.

---

## Investigation

The actual profile directory was tested:

Test-Path "C:\Users\slopez"

Result:

True

The profile contents were also inspected using:

Get-ChildItem "C:\Users\slopez" -Force

This demonstrated that Sarah's existing profile data had not been deleted.

The registered path and actual path were then compared:

[PSCustomObject]@{
    RegisteredProfilePath = $ProfilePath
    RegisteredPathExists  = Test-Path $ProfilePath
    ActualProfilePath     = "C:\Users\slopez"
    ActualPathExists      = Test-Path "C:\Users\slopez"
}

Results:

RegisteredProfilePath : C:\Users\slopez_broken
RegisteredPathExists  : False

ActualProfilePath     : C:\Users\slopez
ActualPathExists      : True

---

## Root Cause

The Windows ProfileList registry entry associated with Sarah's SID contained an incorrect ProfileImagePath.

Windows was configured to associate the SID with:

C:\Users\slopez_broken

However, this directory did not exist.

Sarah's actual profile remained available at:

C:\Users\slopez

The issue therefore involved incorrect profile registration rather than deletion of the user's profile data.

---

## Resolution

The ProfileImagePath value was restored to:

C:\Users\slopez

Command:

Set-ItemProperty `
    -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList\$SarahSID" `
    -Name "ProfileImagePath" `
    -Value "C:\Users\slopez"

The corrected profile path was retrieved and validated.

The registered directory now existed successfully.

---

## Final Verification

Windows profile inventory was queried again:

Get-CimInstance Win32_UserProfile |
Where-Object SID -eq $SarahSID |
Select-Object LocalPath, SID, Loaded, Special

Final result:

LocalPath:
C:\Users\slopez

SID:
S-1-5-21-3463391948-2177679161-975072197-1111

Loaded:
False

Special:
False

This confirmed that Windows once again associated Sarah's SID with the correct local profile directory.

---

## Evidence

### 01_CLIENT01_Invalid_User_Profile_Path.png

Shows:

- ProfileImagePath configured as C:\Users\slopez_broken
- Test-Path returning False

This establishes the simulated profile registration failure.

### 02_CLIENT01_User_Profile_Path_Restored.png

Shows:

- Correct LocalPath of C:\Users\slopez
- Sarah's SID
- Loaded = False
- Special = False

This confirms successful remediation.

---

## Commands Used

Get-CimInstance Win32_UserProfile |
Select-Object LocalPath, SID, Loaded, Special |
Format-Table -AutoSize

Get-ChildItem C:\Users |
Select-Object Name

Get-CimInstance Win32_UserProfile |
Where-Object LocalPath -eq "C:\Users\slopez" |
Select-Object LocalPath, SID, Loaded, Special

$SarahSID = (
    Get-CimInstance Win32_UserProfile |
    Where-Object LocalPath -eq "C:\Users\slopez"
).SID

Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList\$SarahSID" |
Select-Object ProfileImagePath, State, RefCount

New-Item -ItemType Directory -Path "C:\LabBackups" -Force

reg export "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList\$SarahSID" "C:\LabBackups\slopez_ProfileList.reg" /y

Test-Path "C:\LabBackups\slopez_ProfileList.reg"

Set-ItemProperty `
    -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList\$SarahSID" `
    -Name "ProfileImagePath" `
    -Value "C:\Users\slopez_broken"

Test-Path "C:\Users\slopez"

Get-ChildItem "C:\Users\slopez" -Force

Set-ItemProperty `
    -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList\$SarahSID" `
    -Name "ProfileImagePath" `
    -Value "C:\Users\slopez"

Get-CimInstance Win32_UserProfile |
Where-Object SID -eq $SarahSID |
Select-Object LocalPath, SID, Loaded, Special

---

## Skills Demonstrated

- Windows user profile troubleshooting
- Windows Registry troubleshooting
- PowerShell
- CIM / WMI
- Win32_UserProfile
- Windows SIDs
- ProfileList
- ProfileImagePath
- Registry backup
- Registry modification
- Filesystem validation
- Test-Path
- User profile identification
- Domain user troubleshooting
- Root cause analysis
- Safe configuration changes
- Help desk troubleshooting
- Technical documentation

---

## Cheat Sheet Concepts Introduced

### Windows User Profile

A Windows user profile contains user-specific files and settings.

A typical profile directory is:

C:\Users\username

Examples of profile content include:

- Desktop
- Documents
- Downloads
- AppData
- User-specific application settings

---

### SID

SID stands for Security Identifier.

Windows and Active Directory use SIDs as unique identifiers for security principals such as users and groups.

The username is human-readable, but Windows commonly relies on the SID internally to identify the account.

---

### Win32_UserProfile

A Windows CIM/WMI class containing information about local user profiles.

PowerShell example:

Get-CimInstance Win32_UserProfile

Useful properties include:

LocalPath
SID
Loaded
Special

---

### Loaded

Indicates whether the profile is currently loaded.

True:
The profile is currently loaded.

False:
The profile is not currently loaded.

Profile configuration changes are generally safer when the affected user's profile is not loaded.

---

### Special

Identifies profiles used for special Windows/system purposes.

A normal user profile typically reports:

Special = False

---

### ProfileList

Windows maintains profile registration information under:

HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList

Subkeys underneath ProfileList are associated with user SIDs.

---

### ProfileImagePath

Registry value that identifies the filesystem location associated with a Windows user profile.

Example:

C:\Users\slopez

If this value references an incorrect or unavailable location, Windows may be unable to use the expected profile correctly.

---

### Registry Backup

Before making potentially disruptive registry changes, important keys can be exported.

Example:

reg export <registry-key> <backup-file>

This creates a .reg backup that can assist with recovery if necessary.

---

### CIM

Common Information Model provides structured management information about Windows.

PowerShell can query CIM using:

Get-CimInstance

Example:

Get-CimInstance Win32_UserProfile

---

## Key Troubleshooting Principle

A user profile problem does not automatically mean the user's files are gone.

Separate the user's identity, profile registration, and profile data:

Domain User
→ SID
→ ProfileList registry entry
→ ProfileImagePath
→ Actual filesystem directory

When troubleshooting profile problems:

Identify the affected user
→ determine SID
→ locate ProfileList entry
→ inspect ProfileImagePath
→ verify configured directory
→ verify actual profile data
→ back up configuration
→ correct registration
→ verify SID-to-profile relationship

Do not delete or recreate a user's profile until you understand whether the existing data can be recovered.

---

## Important Scope Note

This ticket simulated an incorrect Windows profile registration path.

It did NOT demonstrate corruption of NTUSER.DAT or prove a real Windows temporary-profile event.

The documented root cause is therefore:

Incorrect ProfileImagePath / SID-to-profile-path registration

rather than:

Corrupted NTUSER.DAT

This distinction keeps the troubleshooting documentation technically accurate.

---

## Outcome

A simulated Windows user profile registration problem was successfully diagnosed and repaired on CLIENT01.

The domain user's SID had been configured to reference:

C:\Users\slopez_broken

which did not exist.

Investigation established that the user's real profile data remained available at:

C:\Users\slopez

The ProfileImagePath registry value was corrected.

Final Win32_UserProfile verification showed:

LocalPath = C:\Users\slopez
Loaded = False
Special = False

The ticket demonstrated Windows profile troubleshooting, SID identification, ProfileList registry analysis, safe registry backup, filesystem validation, root cause analysis, and profile registration repair.