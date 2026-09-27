# Mission 12 - Ticket 055: Troubleshoot Windows Scheduled Task Failure

## Ticket Summary
Investigated and resolved a simulated Windows Scheduled Task failure on CLIENT01.

A workstation maintenance task was created to automatically execute a simple command and write a confirmation message to a text file.

During troubleshooting, two separate problems were identified.

The first problem prevented the scheduled task from launching because the task was configured to run under an account that did not satisfy its interactive logon requirements.

Windows Task Scheduler Operational logs identified this problem through Event ID 332.

After correcting the task's security context by configuring it to run under the SYSTEM account, the task successfully launched but returned a nonzero result because its configured action intentionally referenced a nonexistent command.

The action was corrected, the task was executed again, and PowerShell verification confirmed a successful result with LastTaskResult 0.

The expected maintenance output file was also successfully created.

---

## Environment

- Client: CLIENT01
- Host Name: DESKTOP-9NPE7VN
- Domain: jimeneztech.local
- Operating System: Windows 11 Enterprise Evaluation
- Virtualization Platform: VirtualBox
- Administrative Tools:
  - Task Scheduler
  - Windows PowerShell
  - Windows Event Log
- Task Name: LAB - CLIENT01 Maintenance Task
- Final Task Security Context: SYSTEM

---

## Ticket Request

A scheduled workstation maintenance task is not executing as expected.

Investigate the scheduled task, determine why it is failing, correct its configuration, and verify that the maintenance action executes successfully.

---

## Initial Task Configuration

A scheduled task was created in Windows Task Scheduler named:

LAB - CLIENT01 Maintenance Task

The task was designed to execute:

cmd.exe

with the arguments:

/c echo Maintenance task completed successfully > C:\Windows\Temp\MaintenanceTask.txt

Successful execution should create:

C:\Windows\Temp\MaintenanceTask.txt

containing:

Maintenance task completed successfully

The task was manually tested before troubleshooting and successfully created the expected output file.

This established a known-good baseline before the simulated failures were introduced.

---

## Failure Simulation

The scheduled task was intentionally misconfigured to simulate a workstation maintenance task failure.

Initially, the executable was changed to:

cmd_WRONG.exe

The previous successful output file was removed so that an existing file could not be mistaken for evidence of a successful new execution.

PowerShell was used to confirm the configured action:

(Get-ScheduledTask -TaskName "LAB - CLIENT01 Maintenance Task").Actions |
Select-Object Execute, Arguments

The output confirmed that the incorrect executable had been saved.

However, additional testing revealed that the task's LastRunTime was not changing.

This indicated that the scheduled task was not actually reaching the action execution stage.

---

## Initial Diagnosis

The following command was used to inspect the task:

Get-ScheduledTask -TaskName "LAB - CLIENT01 Maintenance Task" |
Select-Object TaskName, State

The task reported:

State: Ready

The configured action was inspected using:

(Get-ScheduledTask -TaskName "LAB - CLIENT01 Maintenance Task").Actions |
Select-Object Execute, Arguments

Additional detailed information was collected using:

schtasks /query /tn "LAB - CLIENT01 Maintenance Task" /v /fo list

Important results included:

Status: Ready
Scheduled Task State: Enabled
Logon Mode: Interactive only
Last Run Time: 9/10/2026 4:13:43 PM
Last Result: 0

The task was enabled and permitted to run, but repeated manual execution attempts did not update the Last Run Time.

This indicated that Task Scheduler was accepting the request but preventing the task from launching before its configured action executed.

---

## Task Scheduler Event Log Investigation

The Task Scheduler Operational event log was queried with PowerShell:

Get-WinEvent -LogName "Microsoft-Windows-TaskScheduler/Operational" -MaxEvents 30 |
Where-Object {$_.Message -like "*LAB - CLIENT01 Maintenance Task*"} |
Select-Object TimeCreated, Id, LevelDisplayName, Message

The log revealed repeated:

Event ID: 332
Level: Warning

The events indicated that Task Scheduler did not launch the task because the configured user was not considered logged on when the task's launching conditions were evaluated.

This identified the first root cause.

---

## Security Context Investigation

The scheduled task was configured with:

Run only when user is logged on

The task configuration referenced an Administrator account associated with CLIENT01.

The currently logged-in identity was verified using:

whoami

The result identified the interactive session as:

JIMENEZTECH\Administrator

Further investigation showed that Task Scheduler was evaluating the task against the CLIENT01 local Administrator security context rather than the currently logged-in domain Administrator session.

These represent different Windows security principals.

For example:

JIMENEZTECH\Administrator

is the domain Administrator account.

DESKTOP-9NPE7VN\Administrator

is the local Administrator account on CLIENT01.

Even though both accounts contain the name "Administrator," Windows treats them as separate identities.

---

## Root Cause #1 - Task Security Context

The task was configured to depend on an interactive Administrator logon, but the security principal associated with the task did not correspond to the active domain Administrator session.

Because the task required:

Run only when user is logged on

Task Scheduler prevented the task from launching under the existing configuration.

Event ID 332 provided the primary diagnostic evidence for this condition.

---

## Security Context Remediation

The task was reconfigured to use:

SYSTEM

The SYSTEM account was successfully resolved through the Windows user/group selector.

The scheduled task was then configured to operate as a background maintenance task rather than depending on a specific interactive Administrator session.

Using SYSTEM was appropriate for this simulated maintenance task because the task performs a local background administrative function and does not require an interactive user's profile.

After changing the security context, the task was executed again.

---

## Secondary Failure

After correcting the security context, the task finally recorded a new execution.

PowerShell was used to retrieve the execution result:

Get-ScheduledTaskInfo -TaskName "LAB - CLIENT01 Maintenance Task" |
Select-Object LastRunTime, LastTaskResult

The result was:

LastRunTime: 9/10/2026 4:40:57 PM
LastTaskResult: 1

The updated LastRunTime proved that Task Scheduler was now successfully launching the task.

However:

LastTaskResult: 1

indicated that the action itself did not complete successfully.

This exposed the second layer of the incident.

---

## Root Cause #2 - Invalid Task Action

The task action had intentionally been configured as:

cmd.exe /c nonexistent_command_12345.exe

Task Scheduler was now functioning correctly and launching cmd.exe.

However, cmd.exe was being instructed to execute:

nonexistent_command_12345.exe

Because this command did not exist, the action returned a failure.

This demonstrated an important troubleshooting distinction:

A scheduled task can successfully launch while the command or program executed by the task still fails.

Therefore, task-launch failures and action-level failures must be investigated separately.

---

## Action Remediation

The scheduled task's action was restored to:

Program/script:

cmd.exe

Arguments:

/c echo Maintenance task completed successfully > C:\Windows\Temp\MaintenanceTask.txt

The corrected task was saved and manually executed again.

---

## Final Verification

PowerShell was used to inspect the final execution:

Get-ScheduledTaskInfo -TaskName "LAB - CLIENT01 Maintenance Task" |
Select-Object LastRunTime, LastTaskResult

The final result was:

LastRunTime: 9/10/2026 4:45:50 PM
LastTaskResult: 0

A LastTaskResult of:

0

confirmed successful task execution.

The expected output file was then inspected:

Get-Content C:\Windows\Temp\MaintenanceTask.txt

Output:

Maintenance task completed successfully

This provided a second verification method demonstrating that the scheduled task's configured action actually executed.

---

## Evidence

### 01_CLIENT01_Scheduled_Task_Logon_Failure.png

- Shows Task Scheduler Operational event log results
- Displays Event ID 332 warnings
- Demonstrates that the task was prevented from launching because of its user/logon configuration
- Provides evidence for the initial root cause

### 02_CLIENT01_Scheduled_Task_Action_Failure.png

- Shows the task successfully reaching a new execution attempt
- Shows an updated LastRunTime
- Shows LastTaskResult 1
- Demonstrates that the security-context problem had been resolved
- Establishes the secondary action-level failure

### 03_CLIENT01_Scheduled_Task_Remediated.png

- Shows the final updated LastRunTime
- Shows LastTaskResult 0
- Shows the contents of MaintenanceTask.txt
- Confirms successful task execution
- Confirms successful execution of the maintenance command

---

## Commands Used

Get-ScheduledTask -TaskName "LAB - CLIENT01 Maintenance Task" |
Select-Object TaskName, State

(Get-ScheduledTask -TaskName "LAB - CLIENT01 Maintenance Task").Actions |
Select-Object Execute, Arguments

Get-ScheduledTaskInfo -TaskName "LAB - CLIENT01 Maintenance Task" |
Select-Object LastRunTime, LastTaskResult

Test-Path C:\Windows\Temp\MaintenanceTask.txt

Remove-Item C:\Windows\Temp\MaintenanceTask.txt -ErrorAction SilentlyContinue

Start-ScheduledTask -TaskName "LAB - CLIENT01 Maintenance Task"

schtasks /run /tn "LAB - CLIENT01 Maintenance Task"

schtasks /query /tn "LAB - CLIENT01 Maintenance Task" /v /fo list

Get-WinEvent -LogName "Microsoft-Windows-TaskScheduler/Operational" -MaxEvents 30 |
Where-Object {$_.Message -like "*LAB - CLIENT01 Maintenance Task*"} |
Select-Object TimeCreated, Id, LevelDisplayName, Message

whoami

Get-Content C:\Windows\Temp\MaintenanceTask.txt

---

## Skills Demonstrated

- Windows 11 Administration
- Windows Task Scheduler
- Scheduled Task Creation
- Scheduled Task Troubleshooting
- PowerShell
- Get-ScheduledTask
- Get-ScheduledTaskInfo
- Start-ScheduledTask
- schtasks
- Windows Event Log Analysis
- Get-WinEvent
- Event ID Analysis
- Windows Security Principals
- Local vs Domain Account Troubleshooting
- SYSTEM Account Usage
- Task Security Context
- Command-Line Troubleshooting
- Root Cause Analysis
- Multi-Layer Troubleshooting
- Remediation Verification
- Help Desk Troubleshooting
- Technical Documentation

---

## Cheat Sheet Concepts Introduced

### Scheduled Task
A Windows mechanism used to automatically execute programs, scripts, commands, or administrative actions based on triggers or conditions.

Scheduled tasks are commonly used for:

- Maintenance
- Backups
- Updates
- Scripts
- Cleanup operations
- Monitoring
- Administrative automation

### Trigger
Defines when a scheduled task should attempt to execute.

Examples include:

- Specific time
- User logon
- System startup
- Event occurrence
- Recurring schedule

### Action
Defines what the scheduled task actually executes.

Examples:

- Program
- Command
- PowerShell script
- Batch file

### Task State
Describes the current operational state of a scheduled task.

Examples:

Ready
- Task is available to run.

Running
- Task is currently executing.

Disabled
- Task is prevented from running.

### Last Run Time
Records the most recent time Task Scheduler actually launched the task.

A timestamp that does not change during repeated troubleshooting attempts can indicate that the task is failing before execution begins.

### LastTaskResult
Provides the result code from the task's most recent execution.

In this ticket:

0
- Successful execution

1
- The launched command returned a failure

A nonzero result should be investigated rather than automatically interpreted as one specific problem.

### Get-ScheduledTask
PowerShell cmdlet used to retrieve scheduled task configuration and state information.

### Get-ScheduledTaskInfo
PowerShell cmdlet used to retrieve runtime information such as:

- LastRunTime
- LastTaskResult
- NextRunTime

### Start-ScheduledTask
PowerShell cmdlet used to manually trigger a scheduled task.

### schtasks
Windows command-line utility used to create, query, run, modify, and manage scheduled tasks.

### Task Scheduler Operational Log
Windows event log containing detailed information about Task Scheduler activity.

It can reveal information that may not be obvious from the Task Scheduler GUI.

### Event ID
A numeric identifier associated with a specific Windows event type.

Event IDs help technicians categorize and investigate system behavior.

### Event ID 332
In this incident, Event ID 332 identified a task launch problem related to the configured user and interactive logon conditions.

### Security Context
The Windows identity under which a process or scheduled task executes.

The security context determines permissions and access to system resources.

### Security Principal
A Windows identity that can be authenticated and assigned permissions.

Examples include:

- Local user
- Domain user
- Computer account
- SYSTEM

### Local Account vs Domain Account
Accounts with similar usernames are not necessarily the same Windows identity.

For example:

DESKTOP-9NPE7VN\Administrator

and:

JIMENEZTECH\Administrator

are separate security principals.

### SYSTEM
A highly privileged built-in Windows security account commonly used by Windows services and background system tasks.

SYSTEM does not depend on a normal interactive user session.

### Interactive Logon
A session in which a user is actively signed into Windows.

A task configured with:

Run only when user is logged on

depends on the appropriate user having an interactive session.

### Root Cause
The underlying reason an incident occurred rather than merely the visible symptom.

This ticket contained two distinct root causes at different stages of troubleshooting.

---

## Key Troubleshooting Lesson

A successful request to start a scheduled task does not necessarily mean the task successfully executed its intended action.

Troubleshooting should distinguish between:

Run request
→ Task Scheduler accepts request
→ Task Scheduler evaluates conditions/security context
→ Task launches
→ Configured action starts
→ Command executes
→ Expected result occurs

A failure can happen at any stage.

This ticket demonstrated two different failure points:

Task launch request
→ security/logon condition failure
→ Event ID 332

After remediation:

Task launches
→ invalid command executes
→ LastTaskResult 1

After final remediation:

Task launches
→ valid command executes
→ output file created
→ LastTaskResult 0

---

## Outcome

The scheduled maintenance task on CLIENT01 was successfully diagnosed and restored.

Task Scheduler Operational logs first revealed that the task was not launching because its configured security context did not satisfy its interactive logon requirements.

The task was reconfigured to run under the SYSTEM account, allowing the background maintenance task to execute independently of the interactive Administrator session.

Once the launch problem was resolved, a second failure became visible.

The task returned LastTaskResult 1 because its action referenced an intentionally nonexistent command.

The task action was corrected to execute the intended maintenance command.

Final verification showed:

LastTaskResult: 0

and:

Maintenance task completed successfully

The incident demonstrated practical experience troubleshooting scheduled tasks across multiple layers, including task configuration, security context, Windows Event Logs, command execution, PowerShell diagnostics, and final verification.