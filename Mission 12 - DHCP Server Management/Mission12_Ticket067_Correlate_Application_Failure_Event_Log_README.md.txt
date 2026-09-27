# Mission 12 - Ticket 067: Correlate Application Failure with Windows Event Logs

## Ticket Summary

Investigated a simulated application failure on CLIENT01 using Windows event-log correlation.

The ticket focused on correlating a reported incident with:

- Incident time
- Windows Application log
- Event provider
- Event ID
- Severity
- Application name
- Exit code

A forced Notepad termination was initially tested but did not generate an Application Error or Windows Error Reporting event.

Rather than assuming an event should exist, the absence of telemetry was documented.

A controlled Application log incident was then generated using a dedicated lab event source:

JimenezTech-Lab

The event contained:

Incident:
JT-INCIDENT-067

Application:
InventoryClient.exe

Exit Code:
1

The event was located using timestamp-based filtering and later extracted programmatically into a concise incident summary.

---

## Environment

- Client: CLIENT01
- Operating System: Windows 11 Enterprise Evaluation
- Domain: jimeneztech.local
- Log: Application
- Event Provider: JimenezTech-Lab
- Event ID: 1001
- Severity: Error
- Incident ID: JT-INCIDENT-067
- Simulated Application: InventoryClient.exe
- Exit Code: 1
- Administrative Tool: Windows PowerShell

---

## Ticket Request

A user reports that an application failed unexpectedly.

Investigate the incident using Windows Event Logs.

Determine:

- Approximate incident time
- Relevant Windows event log
- Matching event provider
- Event ID
- Severity
- Application involved
- Exit code

Correlate the event to the reported incident instead of relying on unrelated errors already present in Event Viewer.

---

## Initial Event Log Baseline

The current system time was recorded:

Get-Date

Recent Application log errors were queried:

Get-WinEvent -FilterHashtable @{
    LogName   = "Application"
    Level     = 2
    StartTime = (Get-Date).AddHours(-2)
} -ErrorAction SilentlyContinue |
Select-Object -First 10 TimeCreated, Id, ProviderName, Message |
Format-List

This demonstrated that Windows logs can contain unrelated events and that an error should not automatically be associated with the user's reported problem.

---

## Incident Timestamp

The incident start time was stored:

$IncidentStart = Get-Date

This provided a time boundary for subsequent log searches.

A query was performed for recent Application Error and Windows Error Reporting events after the timestamp.

No matching events were initially returned.

This established a clean correlation window.

---

## Forced Process Termination Test

Notepad was started:

$App = Start-Process notepad.exe -PassThru

The process information was inspected:

$App |
Select-Object Id, ProcessName, StartTime

After a short delay, Notepad was forcibly terminated:

Stop-Process -Id $App.Id -Force

The incident end time was recorded:

$IncidentEnd = Get-Date

The Application log was searched around the exact time window.

No Application Error or Windows Error Reporting event was generated.

---

## Important Diagnostic Finding

The forced process termination did not produce the expected application-crash telemetry.

This demonstrated an important troubleshooting principle:

Absence of a log event is also evidence.

A technician should not invent or assume an event exists simply because an application stopped.

A forced process termination is not necessarily equivalent to an application crash caused by an unhandled exception.

---

## Controlled Event Simulation

A unique incident identifier was created:

$IncidentID = "JT-INCIDENT-067"

The incident time was recorded:

$IncidentTime = Get-Date

A dedicated Application event source was created:

if (-not [System.Diagnostics.EventLog]::SourceExists("JimenezTech-Lab")) {
    New-EventLog -LogName Application -Source "JimenezTech-Lab"
}

A simulated application failure event was written:

Write-EventLog `
    -LogName Application `
    -Source "JimenezTech-Lab" `
    -EventId 1001 `
    -EntryType Error `
    -Message "Application failure detected. Incident=$IncidentID; Application=InventoryClient.exe; ExitCode=1."

---

## Time-Based Event Correlation

The Application log was filtered beginning shortly before the incident timestamp.

The relevant event was located with:

Get-WinEvent -FilterHashtable @{
    LogName      = "Application"
    ProviderName = "JimenezTech-Lab"
    StartTime    = $IncidentTime.AddSeconds(-5)
} |
Select-Object TimeCreated, Id, ProviderName, LevelDisplayName, Message |
Format-List

The resulting event showed:

TimeCreated:
9/13/2026 11:28:58 AM

Event ID:
1001

Provider:
JimenezTech-Lab

Severity:
Error

Message:
Application failure detected.
Incident=JT-INCIDENT-067
Application=InventoryClient.exe
ExitCode=1

---

## Root Cause / Incident Identification

The event was correlated to the reported incident using multiple pieces of evidence:

- Timestamp
- Application log
- Provider name
- Event ID
- Incident identifier
- Application name
- Exit code

The matched incident represented:

Application:
InventoryClient.exe

Exit Code:
1

This ticket focused on identifying and correlating failure telemetry rather than repairing the simulated application itself.

---

## Programmatic Event Correlation

The relevant event was stored:

$CorrelatedEvent = Get-WinEvent -FilterHashtable @{
    LogName      = "Application"
    ProviderName = "JimenezTech-Lab"
    StartTime    = $IncidentTime.AddSeconds(-5)
} |
Where-Object {
    $_.Id -eq 1001 -and
    $_.Message -match "JT-INCIDENT-067"
} |
Select-Object -First 1

The event data was then extracted into technician-friendly values.

Because multiline pasting caused syntax issues in the console, individual commands were used:

$IncidentFound = $null -ne $CorrelatedEvent

$Application = ($CorrelatedEvent.Message -split 'Application=')[1].Split(';')[0]

$ExitCode = ($CorrelatedEvent.Message -split 'ExitCode=')[1].TrimEnd('.')

The final summary was created:

[PSCustomObject]@{IncidentFound=$IncidentFound;TimeCreated=$CorrelatedEvent.TimeCreated;EventID=$CorrelatedEvent.Id;Provider=$CorrelatedEvent.ProviderName;Severity=$CorrelatedEvent.LevelDisplayName;Application=$Application;ExitCode=$ExitCode}

Results:

IncidentFound:
True

TimeCreated:
9/13/2026 11:28:58 AM

EventID:
1001

Provider:
JimenezTech-Lab

Severity:
Error

Application:
InventoryClient.exe

ExitCode:
1

---

## Event Correlation Workflow

The troubleshooting workflow used in this ticket was:

User reports application failure
→ determine approximate incident time
→ choose relevant Windows log
→ filter by time
→ identify relevant provider
→ inspect Event ID
→ inspect severity
→ review message
→ correlate application name
→ correlate exit code
→ confirm incident identity

This prevents unrelated errors from being incorrectly blamed for the user's issue.

---

## Evidence

### 01_CLIENT01_Application_Error_Correlated.png

Shows:

- TimeCreated
- Event ID 1001
- Provider JimenezTech-Lab
- Severity Error
- Incident JT-INCIDENT-067
- Application InventoryClient.exe
- ExitCode 1

This establishes the matching Application log event.

### 02_CLIENT01_Application_Incident_Correlation_Verified.png

Shows the programmatically extracted incident summary:

- IncidentFound = True
- EventID = 1001
- Provider = JimenezTech-Lab
- Severity = Error
- Application = InventoryClient.exe
- ExitCode = 1

This confirms successful incident correlation.

---

## Commands Used

Get-Date

Get-WinEvent

Start-Process

Stop-Process

Start-Sleep

New-EventLog

Write-EventLog

Select-Object

Where-Object

$IncidentStart = Get-Date

$IncidentTime = Get-Date

$CorrelatedEvent = Get-WinEvent ...

$IncidentFound = $null -ne $CorrelatedEvent

$Application = ($CorrelatedEvent.Message -split 'Application=')[1].Split(';')[0]

$ExitCode = ($CorrelatedEvent.Message -split 'ExitCode=')[1].TrimEnd('.')

Remove-EventLog -Source "JimenezTech-Lab"

---

## Skills Demonstrated

- Windows Event Viewer troubleshooting
- Event log correlation
- Get-WinEvent
- Application log analysis
- Event provider identification
- Event ID analysis
- Severity analysis
- Timestamp correlation
- PowerShell event filtering
- Programmatic log parsing
- Incident identification
- Root cause investigation
- Evidence-based troubleshooting
- Help desk troubleshooting
- Technical documentation

---

## Cheat Sheet Concepts Introduced

### Event Correlation

Event correlation is the process of connecting a reported incident with relevant system telemetry.

Useful correlation points include:

- Time
- User
- Host
- Application
- Provider
- Event ID
- Severity
- Exit code

---

### Temporal Correlation

Temporal correlation means comparing events based on when they occurred.

If a user reports a failure at approximately 11:28 AM, logs around that time should be examined before unrelated events from hours earlier.

---

### Get-WinEvent

PowerShell cmdlet used to query Windows event logs.

Example:

Get-WinEvent -FilterHashtable @{
    LogName = "Application"
}

FilterHashtable is generally more efficient than retrieving large logs and filtering everything afterward.

---

### Application Log

The Windows Application log contains events generated by:

- Applications
- Services
- Windows components
- Installer technologies
- Error-reporting components

Not every event in the Application log is related to the user's issue.

---

### ProviderName

ProviderName identifies the component that generated the event.

Examples may include:

Application Error
Windows Error Reporting
MsiInstaller
Custom application providers

Provider identity helps narrow event ownership.

---

### Event ID

An Event ID is a numeric identifier associated with a particular class of event generated by a provider.

An Event ID should be interpreted together with:

- Provider
- Message
- Timestamp
- Severity

The number alone is often insufficient.

---

### Severity

Common Windows event levels include:

- Information
- Warning
- Error
- Critical

An Error event is not automatically relevant to the active incident.

---

### Incident Timestamp

Recording the approximate failure time is one of the most useful first steps in log troubleshooting.

It narrows the search window and helps separate relevant telemetry from noise.

---

### Exit Code

An exit code is a value returned when an application or process terminates.

Exit codes can provide useful diagnostic information when correlated with:

- Logs
- Application behavior
- Time of failure

---

### Log Noise

Windows systems often contain warnings and errors unrelated to the current issue.

The presence of a red Event Viewer entry does not automatically make it the root cause.

Evidence must be correlated.

---

## Key Troubleshooting Principle

Do not select an error simply because it looks serious.

Instead:

Identify when the problem occurred
→ filter logs around that time
→ identify the correct provider
→ inspect event ID
→ inspect message
→ correlate process/application
→ correlate additional evidence

In this ticket, the exact incident was confirmed using:

TimeCreated
+
Provider
+
Event ID
+
Incident ID
+
Application
+
Exit Code

This is substantially stronger than simply stating:

"Event Viewer showed an error."

---

## Important Scope Note

The application failure event in this ticket was generated intentionally using:

Write-EventLog

It was not created by a real crashing InventoryClient.exe application.

The forced Notepad termination also did not generate a corresponding Application Error event.

Therefore, this ticket demonstrates:

Event correlation and Windows log investigation

rather than claiming:

A real application crash was captured automatically by Windows Error Reporting.

---

## Cleanup

The custom event source was removed after the exercise:

Remove-EventLog -Source "JimenezTech-Lab"

The historical event already written to the Application log may remain as evidence of the lab activity.

---

## Outcome

A simulated Windows application failure was successfully correlated using the Application event log.

The incident was identified using:

Incident:
JT-INCIDENT-067

Time:
9/13/2026 11:28:58 AM

Event ID:
1001

Provider:
JimenezTech-Lab

Severity:
Error

Application:
InventoryClient.exe

Exit Code:
1

The ticket demonstrated timestamp-based event analysis, provider filtering, Event ID interpretation, programmatic log parsing, incident correlation, and evidence-based troubleshooting.