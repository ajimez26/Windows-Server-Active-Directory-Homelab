# Mission 12 - Ticket 059: Troubleshoot Windows Environment Variable

## Ticket Summary

Investigated and resolved a simulated Windows environment-variable configuration issue on CLIENT01.

A custom user environment variable named JIMENEZTECH_TOOLS was configured with an invalid directory:

C:\InvalidTools

A newly launched PowerShell process inherited the incorrect value, and Test-Path confirmed that the configured directory did not exist.

Troubleshooting determined that the incorrect value existed at the User scope rather than the Machine scope.

A valid tools directory was created and the persistent User environment variable was corrected.

The existing PowerShell process continued to report the old value, demonstrating that running processes retain the environment they inherited when they were launched.

A newly launched PowerShell process inherited the corrected value and successfully verified that the new directory existed.

---

## Environment

- Workstation: CLIENT01
- Operating System: Windows 11 Enterprise Evaluation
- Domain: jimeneztech.local
- Virtualization Platform: VirtualBox
- Administrative Tool: Windows PowerShell
- Test Variable: JIMENEZTECH_TOOLS
- Variable Scope: User

---

## Ticket Request

A user reports that an application cannot locate a required tools directory.

The application relies on a Windows environment variable to determine the directory location.

Investigate:

- The configured environment-variable value
- Whether the variable exists at User or Machine scope
- Whether the configured directory exists
- Whether running processes have inherited the current value

Correct the configuration and verify that newly launched processes receive the repaired environment variable.

---

## Environment Variable Baseline

Before creating the troubleshooting scenario, the existing TEMP environment variable was inspected to understand Windows environment-variable scopes.

Commands:

[Environment]::GetEnvironmentVariable("TEMP", "User")

[Environment]::GetEnvironmentVariable("TEMP", "Machine")

$env:TEMP

Results:

User TEMP:
C:\Users\Administrator\AppData\Local\Temp

Machine TEMP:
C:\WINDOWS\TEMP

Current PowerShell Process TEMP:
C:\Users\Administrator\AppData\Local\Temp

This demonstrated that Windows can maintain different environment-variable values at different scopes.

The current PowerShell process was using the user-specific TEMP value.

---

## Safe Incident Simulation

The real TEMP variable was not modified because Windows and applications depend on it.

Instead, a custom lab variable was created:

JIMENEZTECH_TOOLS

The persistent User value was configured as:

C:\InvalidTools

Command:

[Environment]::SetEnvironmentVariable(
    "JIMENEZTECH_TOOLS",
    "C:\InvalidTools",
    "User"
)

The persistent value was verified using:

[Environment]::GetEnvironmentVariable(
    "JIMENEZTECH_TOOLS",
    "User"
)

Result:

C:\InvalidTools

---

## Process Environment Observation

Immediately after creating the persistent variable, the current PowerShell session checked:

$env:JIMENEZTECH_TOOLS

No value was returned.

This demonstrated that changing a persistent environment variable does not necessarily modify the environment block of a process that is already running.

The original PowerShell process had been launched before JIMENEZTECH_TOOLS existed.

---

## Reproducing the User Problem

A new PowerShell process was launched after the persistent environment variable had been created.

The new process checked:

$env:JIMENEZTECH_TOOLS

Result:

C:\InvalidTools

The configured directory was then tested:

Test-Path $env:JIMENEZTECH_TOOLS

Result:

False

This reproduced the simulated application problem.

The new process successfully inherited the environment variable, but the variable pointed to a nonexistent location.

---

## Scope Investigation

The variable was inspected at User scope:

[Environment]::GetEnvironmentVariable(
    "JIMENEZTECH_TOOLS",
    "User"
)

Result:

C:\InvalidTools

The Machine scope was then inspected:

[Environment]::GetEnvironmentVariable(
    "JIMENEZTECH_TOOLS",
    "Machine"
)

Result:

No value configured.

The current process value was also checked:

$env:JIMENEZTECH_TOOLS

Result:

C:\InvalidTools

This established that the incorrect configuration originated from the persistent User environment variable.

The Machine environment was not responsible for the problem.

---

## Root Cause

The JIMENEZTECH_TOOLS environment variable was configured at User scope with an invalid path:

C:\InvalidTools

The directory did not exist.

New processes inherited this invalid value and applications relying on the variable would therefore be directed toward an unavailable filesystem location.

---

## Remediation

A valid tools directory was created:

New-Item -ItemType Directory -Path "C:\JimenezTechTools" -Force

The persistent User environment variable was then corrected:

[Environment]::SetEnvironmentVariable(
    "JIMENEZTECH_TOOLS",
    "C:\JimenezTechTools",
    "User"
)

The persistent value was verified:

[Environment]::GetEnvironmentVariable(
    "JIMENEZTECH_TOOLS",
    "User"
)

Result:

C:\JimenezTechTools

This confirmed that the stored User configuration had been corrected.

---

## Existing Process Behavior

After correcting the persistent variable, the existing PowerShell process checked:

$env:JIMENEZTECH_TOOLS

The process continued to report:

C:\InvalidTools

This was expected.

The PowerShell process had inherited the previous environment-variable value when it was launched.

Changing the persistent User variable did not retroactively replace the environment block already being used by that process.

---

## New Process Verification

A third PowerShell process was launched after the persistent variable was corrected.

The new process checked:

$env:JIMENEZTECH_TOOLS

Result:

C:\JimenezTechTools

The directory was then verified:

Test-Path $env:JIMENEZTECH_TOOLS

Result:

True

This demonstrated that newly launched processes inherited the corrected persistent value.

The incident was successfully resolved.

---

## User vs Machine vs Process Scope

This ticket demonstrated three important environment-variable contexts.

### User Scope

A persistent value associated with the current user's profile.

Example:

[Environment]::GetEnvironmentVariable("JIMENEZTECH_TOOLS", "User")

The simulated problem existed at this scope.

### Machine Scope

A persistent system-wide value available to the operating system and users/processes according to Windows environment inheritance behavior.

Example:

[Environment]::GetEnvironmentVariable("JIMENEZTECH_TOOLS", "Machine")

No Machine value existed for the test variable.

### Process Environment

The environment currently available to a specific running process.

Example:

$env:JIMENEZTECH_TOOLS

A process normally receives its environment when it is created.

Therefore, its current value can differ from a persistent User or Machine value that was changed after the process started.

---

## Environment Variable Inheritance

One of the primary lessons demonstrated in this ticket was process environment inheritance.

The sequence was:

Persistent variable created
→ existing PowerShell process did not contain it
→ new PowerShell process inherited C:\InvalidTools

The variable was then corrected:

Persistent variable changed to C:\JimenezTechTools
→ already-running PowerShell process retained C:\InvalidTools
→ newly launched PowerShell process inherited C:\JimenezTechTools

This demonstrated why restarting or reopening an affected application can sometimes be necessary after changing an environment variable.

---

## Verification

The final verification established:

Persistent User value:
C:\JimenezTechTools

New Process value:
C:\JimenezTechTools

Directory existence:
True

This confirmed that both the configuration and the referenced filesystem location were valid.

---

## Evidence

### 01_CLIENT01_Invalid_Environment_Variable.png

Shows:

- JIMENEZTECH_TOOLS = C:\InvalidTools
- Test-Path result = False

This establishes the simulated failure condition.

### 02_CLIENT01_Environment_Variable_Remediated.png

Shows:

- Newly launched process receiving C:\JimenezTechTools
- Test-Path result = True

This confirms successful remediation.

---

## Commands Used

[Environment]::GetEnvironmentVariable("TEMP", "User")

[Environment]::GetEnvironmentVariable("TEMP", "Machine")

$env:TEMP

[Environment]::SetEnvironmentVariable(
    "JIMENEZTECH_TOOLS",
    "C:\InvalidTools",
    "User"
)

[Environment]::GetEnvironmentVariable(
    "JIMENEZTECH_TOOLS",
    "User"
)

$env:JIMENEZTECH_TOOLS

Test-Path $env:JIMENEZTECH_TOOLS

[Environment]::GetEnvironmentVariable(
    "JIMENEZTECH_TOOLS",
    "Machine"
)

New-Item -ItemType Directory -Path "C:\JimenezTechTools" -Force

[Environment]::SetEnvironmentVariable(
    "JIMENEZTECH_TOOLS",
    "C:\JimenezTechTools",
    "User"
)

---

## Skills Demonstrated

- Windows Environment Variables
- PowerShell
- User Environment Variables
- Machine Environment Variables
- Process Environment Variables
- Environment Variable Scope
- Environment Variable Inheritance
- Persistent Configuration
- Process-Level Configuration
- Filesystem Path Validation
- Test-Path
- .NET Environment Class
- GetEnvironmentVariable
- SetEnvironmentVariable
- Windows Process Troubleshooting
- Root Cause Analysis
- Help Desk Troubleshooting
- Technical Documentation

---

## Cheat Sheet Concepts Introduced

### Environment Variable

A named value available to Windows and applications that can store configuration information such as:

- Directory locations
- Temporary-file locations
- Executable search paths
- Application configuration
- User-specific information

Examples include:

TEMP
PATH

and the custom variable used in this ticket:

JIMENEZTECH_TOOLS

---

### User Environment Variable

An environment variable configured for a particular user profile.

Example:

[Environment]::GetEnvironmentVariable("TEMP", "User")

User variables can provide user-specific configuration without changing the system-wide Machine value.

---

### Machine Environment Variable

A persistent system-level environment variable.

Example:

[Environment]::GetEnvironmentVariable("TEMP", "Machine")

Machine variables provide system-wide configuration rather than being associated only with one user's profile.

---

### Process Environment

The collection of environment variables currently available to a running process.

PowerShell accesses process environment variables using:

$env:VARIABLE_NAME

Example:

$env:TEMP

---

### Environment Variable Inheritance

A newly launched process normally inherits an environment from its parent/process environment.

As a result, changing a persistent environment variable does not necessarily update applications that are already running.

New processes can receive the new value while older processes continue using the value they inherited previously.

---

### Persistent vs Session-Only Variable

A persistent environment variable is stored outside the current PowerShell session and can be available to future processes.

Example:

[Environment]::SetEnvironmentVariable(
    "JIMENEZTECH_TOOLS",
    "C:\JimenezTechTools",
    "User"
)

A process/session value can also be assigned directly:

$env:JIMENEZTECH_TOOLS = "C:\JimenezTechTools"

However, that directly modifies the current PowerShell process environment rather than serving as the same type of persistent User configuration.

---

### [Environment]

A .NET class that PowerShell can use to interact with environment variables.

Methods used in this ticket included:

GetEnvironmentVariable()

SetEnvironmentVariable()

---

### Test-Path

PowerShell cmdlet used to determine whether a filesystem or provider path exists.

Example:

Test-Path $env:JIMENEZTECH_TOOLS

Results:

True = path exists
False = path does not exist

---

## Key Troubleshooting Principle

Do not assume that because an environment variable has been corrected in Windows, every running application immediately knows about the change.

Always consider:

Where is the variable configured?
→ User or Machine?

What does the persistent configuration contain?

What does the affected process currently contain?

Does the referenced resource actually exist?

Was the application launched before or after the configuration changed?

A useful troubleshooting sequence is:

Application cannot locate resource
→ identify environment variable
→ inspect current process value
→ inspect User value
→ inspect Machine value
→ validate referenced path
→ correct persistent configuration
→ launch a new process
→ verify inherited value
→ verify resource exists

---

## Outcome

A simulated Windows environment-variable problem was successfully diagnosed and resolved on CLIENT01.

The custom environment variable:

JIMENEZTECH_TOOLS

was initially configured at User scope as:

C:\InvalidTools

A newly launched PowerShell process inherited the value, but:

Test-Path

returned:

False

Troubleshooting established that the invalid configuration existed at User scope and that no corresponding Machine value was configured.

A valid directory was created:

C:\JimenezTechTools

The persistent User variable was corrected.

An already-running PowerShell process retained the previous value, demonstrating process environment inheritance.

A newly launched PowerShell process received:

C:\JimenezTechTools

and:

Test-Path

returned:

True

The ticket demonstrated practical troubleshooting of Windows environment variables, configuration scope, persistent values, process inheritance, path validation, and the importance of restarting affected processes after environment changes.