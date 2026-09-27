# Mission 12 - Ticket 056: Troubleshoot PATH Environment Variable

## Ticket Summary

Investigated and resolved a simulated Windows command-resolution failure on CLIENT01.

The user was unable to execute the standard Windows `ping` command from PowerShell even though the PING.EXE executable still existed on the workstation.

Testing demonstrated that PING.EXE worked successfully when its full file path was specified, indicating that the executable itself was not missing or corrupted.

Further investigation revealed that the PATH environment variable for the current PowerShell session had been incorrectly configured as:

C:\Temp

This removed C:\Windows\System32 from the command-search path and prevented PowerShell from locating PING.EXE when the command was entered by name.

The original PATH was restored and command resolution was verified using Get-Command and a successful ping test.

---

## Environment

- Client: CLIENT01
- Host Name: DESKTOP-9NPE7VN
- Domain: jimeneztech.local
- Operating System: Windows 11 Enterprise Evaluation
- Virtualization Platform: VirtualBox
- Administrative Tool: Windows PowerShell
- Affected Command: ping
- Executable Location: C:\Windows\System32\PING.EXE

---

## Ticket Request

A user reports that a standard Windows command is no longer recognized when entered in PowerShell.

Determine whether the application is missing, corrupted, or inaccessible because of an environment configuration problem.

Identify the root cause, restore normal command resolution, and verify successful operation.

---

## Healthy Baseline

Before introducing the simulated failure, command resolution was tested using:

where.exe ping

Windows successfully located PING.EXE inside:

C:\Windows\System32

The command was also tested using:

ping.exe 127.0.0.1

The ping completed successfully.

This established that command resolution and PING.EXE were functioning normally before the simulated incident.

---

## Failure Simulation

The current PATH was first stored in a PowerShell variable:

$OriginalPath = $env:PATH

The PATH environment variable was then temporarily replaced for the current PowerShell session:

$env:PATH = "C:\Temp"

This was a session-level modification rather than a permanent modification to the Windows system environment.

The command was then tested:

ping 127.0.0.1

PowerShell returned a CommandNotFoundException indicating that:

ping

was not recognized as a cmdlet, function, script file, or executable program.

---

## Initial Troubleshooting

Instead of assuming that PING.EXE had been deleted or corrupted, the executable was tested using its full path:

C:\Windows\System32\ping.exe 127.0.0.1

The command executed successfully.

The test returned:

Packets Sent = 4
Packets Received = 4
Packets Lost = 0

This demonstrated that:

- PING.EXE still existed
- The executable was functional
- Windows networking was functioning
- The problem involved command discovery rather than the executable itself

---

## PATH Investigation

The active PATH environment variable was inspected using:

$env:PATH

The result was:

C:\Temp

This immediately revealed that normal Windows executable directories were missing from the current session's search path.

In particular:

C:\Windows\System32

was absent.

Because PING.EXE resides inside System32, PowerShell could no longer locate the executable when only the command name was supplied.

---

## Executable Verification

The existence of PING.EXE was verified using:

Test-Path C:\Windows\System32\ping.exe

Result:

True

This confirmed that the executable physically existed on the workstation.

The issue therefore was not caused by:

- Missing PING.EXE
- Deleted system files
- Application corruption
- Network connectivity failure

The executable existed but could not be discovered through the current PATH.

---

## Command Resolution Testing

PowerShell command resolution was tested using:

Get-Command ping -ErrorAction SilentlyContinue

PowerShell could not resolve `ping` as a normally discoverable command under the modified PATH configuration.

Because the current PowerShell location was C:\Windows\System32, PowerShell also indicated that the command existed in the current directory.

However, PowerShell does not automatically execute programs from the current directory solely because they are located there.

This reinforced that the command-resolution failure was caused by PATH rather than the executable being absent.

---

## Root Cause

The PATH environment variable for the active PowerShell session had been incorrectly configured as:

C:\Temp

This removed:

C:\Windows\System32

from the directories Windows and PowerShell could search when resolving executable names.

As a result:

ping 127.0.0.1

failed because PowerShell could not locate PING.EXE through PATH.

However:

C:\Windows\System32\ping.exe 127.0.0.1

succeeded because specifying the absolute path eliminated the need for PATH-based command discovery.

---

## Remediation

The original PATH value had been preserved before the simulated incident using:

$OriginalPath = $env:PATH

The original PATH was restored with:

$env:PATH = $OriginalPath

The environment variable was then inspected again:

$env:PATH

The restored PATH once again contained standard Windows directories including:

C:\WINDOWS\system32

and other Windows command locations.

---

## Command Resolution Verification

PowerShell was used to verify that the `ping` command could once again be discovered:

Get-Command ping

The result identified:

CommandType: Application
Name: PING.EXE
Source: C:\WINDOWS\system32\PING.EXE

This confirmed that PowerShell command resolution had been restored.

---

## Final Functional Verification

The command was executed normally without specifying its full path:

ping 127.0.0.1

The test completed successfully.

Final results included:

Packets Sent = 4
Packets Received = 4
Packets Lost = 0

This confirmed that:

- PATH had been restored
- PowerShell could resolve PING.EXE
- The command executed normally
- The workstation returned to its expected configuration

---

## Evidence

### 01_CLIENT01_PATH_Command_Resolution_Failure.png

- Shows `ping 127.0.0.1` producing a CommandNotFoundException
- Shows the command failing when referenced only by name
- Shows `C:\Windows\System32\ping.exe 127.0.0.1` executing successfully
- Demonstrates that PING.EXE itself remained functional

### 02_CLIENT01_PATH_Diagnosis.png

- Shows `$env:PATH` returning `C:\Temp`
- Shows Test-Path confirming that PING.EXE exists
- Shows Get-Command unable to resolve ping normally
- Provides evidence identifying PATH as the root cause

### 03_CLIENT01_PATH_Remediated.png

- Shows the restored PATH
- Shows C:\WINDOWS\system32 present in PATH
- Shows Get-Command successfully resolving PING.EXE
- Shows a successful ping to 127.0.0.1
- Confirms successful remediation

---

## Commands Used

where.exe ping

ping.exe 127.0.0.1

$OriginalPath = $env:PATH

$env:PATH = "C:\Temp"

ping 127.0.0.1

C:\Windows\System32\ping.exe 127.0.0.1

$env:PATH

Test-Path C:\Windows\System32\ping.exe

Get-Command ping -ErrorAction SilentlyContinue

$env:PATH = $OriginalPath

Get-Command ping

ping 127.0.0.1

---

## Skills Demonstrated

- Windows 11 Administration
- PowerShell
- Environment Variable Troubleshooting
- PATH Troubleshooting
- Windows Command Resolution
- Get-Command
- Test-Path
- where.exe
- Executable Verification
- Command-Line Troubleshooting
- Session-Level Configuration
- Root Cause Analysis
- Help Desk Troubleshooting
- Troubleshooting by Elimination
- Remediation Verification
- Technical Documentation

---

## Cheat Sheet Concepts Introduced

### Environment Variable

A named value used by Windows and applications to store configuration information about the operating environment.

Examples include:

PATH
TEMP
USERNAME
USERPROFILE
COMPUTERNAME

---

### PATH

An environment variable containing a list of directories Windows searches when attempting to locate executable commands.

Instead of entering:

C:\Windows\System32\ping.exe

a user can normally enter:

ping

because C:\Windows\System32 is included in PATH.

---

### Command Resolution

The process Windows or a command shell uses to determine what executable, script, function, alias, or command should run when a user enters a command.

A command can physically exist on the computer while still failing command resolution.

---

### Absolute Path / Full Path

The complete location of a file within the filesystem.

Example:

C:\Windows\System32\ping.exe

Using an absolute path bypasses the need to locate the executable through PATH.

This makes full-path execution useful during troubleshooting.

---

### $env:PATH

PowerShell representation of the PATH environment variable available to the current process.

It can be inspected using:

$env:PATH

---

### Get-Command

PowerShell cmdlet used to determine how PowerShell resolves a command.

Example:

Get-Command ping

It can identify information such as:

- Command type
- Command name
- Executable location
- Source

---

### Test-Path

PowerShell cmdlet used to determine whether a filesystem path or other supported path exists.

Example:

Test-Path C:\Windows\System32\ping.exe

True indicates that the specified path exists.

---

### where.exe

Windows utility used to locate files that match a command name through configured search locations.

Example:

where.exe ping

---

### CommandNotFoundException

A PowerShell error generated when PowerShell cannot resolve an entered command.

A CommandNotFoundException does not automatically mean that the underlying executable has been deleted.

Possible causes include:

- Incorrect PATH
- Typographical error
- Missing executable
- Incorrect working directory
- Application installation problem

---

### Current Directory

The filesystem location in which the shell is currently operating.

PowerShell does not automatically execute a program from the current directory simply because the executable is present there.

A relative path such as:

.\program.exe

can be used when explicitly executing something from the current directory.

---

### Session-Level Environment Variable

An environment variable value that applies only to the current process or shell session.

In this ticket:

$env:PATH = "C:\Temp"

modified PATH only for the active PowerShell process.

It did not permanently overwrite the Windows system PATH.

Closing the PowerShell session would discard this temporary modification.

---

### Persistent Environment Variable

An environment variable stored at the Windows user or system level rather than only inside the current process.

Persistent PATH problems can affect future applications or sessions and may require modification through Windows Environment Variables or appropriate administrative tools.

---

### User Environment Variable

An environment variable associated with a particular Windows user.

Its configuration primarily affects processes launched within that user's environment.

---

### System Environment Variable

An environment variable configured at the system level and available broadly to Windows processes.

Changes to system environment variables can affect multiple users and applications and should be made carefully.

---

## Key Troubleshooting Principle

A "command not found" error does not necessarily mean the program is missing.

A useful troubleshooting workflow is:

Command fails
→ determine expected executable location
→ verify executable exists
→ execute using full path
→ inspect PATH
→ test command resolution
→ identify configuration problem
→ restore PATH
→ verify command discovery
→ perform functional test

In this ticket:

ping

failed.

But:

C:\Windows\System32\ping.exe

worked.

That immediately helped separate an executable problem from a command-resolution problem.

---

## Troubleshooting by Elimination

The testing process eliminated several possible causes.

PING.EXE exists:

Test-Path → True

PING.EXE functions:

Full-path execution → Successful

Local TCP/IP operation functions:

127.0.0.1 → 0% packet loss

Command discovery fails:

Get-Command ping → Unable to resolve normally

PATH incorrect:

$env:PATH → C:\Temp

Therefore, the root cause was narrowed to the PATH configuration rather than networking or executable corruption.

---

## Outcome

A simulated command-resolution failure was successfully diagnosed and remediated on CLIENT01.

The `ping` command initially produced a CommandNotFoundException even though PING.EXE remained installed and functional.

Executing PING.EXE through its full path demonstrated that the executable itself was healthy.

PowerShell investigation revealed that the active PATH environment variable had been replaced with:

C:\Temp

and therefore no longer contained C:\Windows\System32.

The original PATH was restored.

Final verification using:

Get-Command ping

successfully resolved:

C:\WINDOWS\system32\PING.EXE

and:

ping 127.0.0.1

completed successfully with 0% packet loss.

The ticket demonstrated practical troubleshooting of Windows environment variables, PATH-based command resolution, PowerShell command discovery, executable verification, root cause analysis, and post-remediation validation.