# Mission 12 - Ticket 063: Troubleshoot Windows Print Spooler Queue

## Ticket Summary

Investigated and resolved a simulated stuck print queue on CLIENT01.

The Windows Print Spooler was initially verified as healthy and configured for Automatic startup.

A controlled print-queue failure was simulated by stopping the Print Spooler and creating orphaned .SPL and .SHD files inside the Windows spool directory.

Troubleshooting confirmed that the Spooler was stopped while queue files remained present.

The orphaned queue files were removed while the service was stopped. The Print Spooler was then restarted and final verification confirmed:

- Spooler status: Running
- Startup type: Automatic
- Queue file count: 0

---

## Environment

- Client: CLIENT01
- Operating System: Windows 11 Enterprise Evaluation
- Domain: jimeneztech.local
- Service: Print Spooler
- Service Name: Spooler
- Service Executable:
  C:\WINDOWS\System32\spoolsv.exe
- Service Account: LocalSystem
- Spool Directory:
  C:\Windows\System32\spool\PRINTERS
- Administrative Tool: Windows PowerShell

---

## Ticket Request

A user reports that documents are stuck in the Windows print queue and are not printing.

Investigate:

- Print Spooler service status
- Print Spooler startup configuration
- Required services
- Print spool directory
- Remaining queue files

Clear the stuck queue safely and restore the Windows printing subsystem.

---

## Initial Print Spooler Baseline

The Print Spooler service was inspected:

Get-Service Spooler |
Select-Object Name, DisplayName, Status, StartType

Results:

Name:
Spooler

Display Name:
Print Spooler

Status:
Running

Startup Type:
Automatic

This established that the service was initially operating normally.

---

## Service Configuration Investigation

Additional service information was retrieved:

Get-CimInstance Win32_Service -Filter "Name='Spooler'" |
Select-Object Name, State, StartMode, StartName, PathName

Results included:

State:
Running

Start Mode:
Auto

Start Account:
LocalSystem

Executable:
C:\WINDOWS\System32\spoolsv.exe

---

## Service Dependency Investigation

Required services were inspected:

Get-Service Spooler -RequiredServices |
Select-Object Name, Status, StartType

Dependencies included:

RPCSS
Status: Running
Startup: Automatic

HTTP
Status: Running
Startup: Manual

This confirmed that the required services displayed by Windows were available during the healthy baseline.

---

## Initial Spool Directory Inspection

The Windows print spool directory was inspected:

Get-ChildItem "C:\Windows\System32\spool\PRINTERS" -Force -ErrorAction SilentlyContinue |
Select-Object Name, Length, LastWriteTime

No queue files were present during the healthy baseline.

---

## Simulated Incident

The Print Spooler was stopped:

Stop-Service Spooler

Its status was verified:

Get-Service Spooler

The service reported:

Stopped

Two harmless simulated queue files were then created:

C:\Windows\System32\spool\PRINTERS\LABJOB001.SPL

C:\Windows\System32\spool\PRINTERS\LABJOB001.SHD

Commands:

New-Item "C:\Windows\System32\spool\PRINTERS\LABJOB001.SPL" -ItemType File

New-Item "C:\Windows\System32\spool\PRINTERS\LABJOB001.SHD" -ItemType File

These files were intentionally empty and were used only to simulate orphaned print-queue artifacts.

---

## Investigation

The Print Spooler state and spool directory were inspected.

The service was:

Stopped

The spool directory contained:

LABJOB001.SPL
LABJOB001.SHD

This represented the simulated stuck queue condition.

---

## Root Cause

The simulated printing failure consisted of orphaned print queue files remaining in:

C:\Windows\System32\spool\PRINTERS

while the Print Spooler was stopped.

The queue artifacts needed to be removed before restoring the printing service.

This was a controlled simulation and did not represent corruption of an actual physical printer or printer driver.

---

## Resolution

With the Print Spooler stopped, the queue contents were removed:

Remove-Item "C:\Windows\System32\spool\PRINTERS\*" -Force

The directory was checked again:

Get-ChildItem "C:\Windows\System32\spool\PRINTERS" -Force

No files were returned.

The Print Spooler was then restarted:

Start-Service Spooler

---

## Final Verification

The queue directory was retrieved:

$QueueFiles = Get-ChildItem "C:\Windows\System32\spool\PRINTERS" -Force

A final status object was created:

[PSCustomObject]@{
    SpoolerStatus  = (Get-Service Spooler).Status
    StartupType    = (Get-Service Spooler).StartType
    QueueFileCount = $QueueFiles.Count
}

Final results:

Spooler Status:
Running

Startup Type:
Automatic

Queue File Count:
0

This confirmed that the simulated stuck queue had been cleared and the Windows Print Spooler was restored.

---

## Evidence

### 01_CLIENT01_Stuck_Print_Queue.png

Shows:

- Print Spooler stopped
- LABJOB001.SPL present
- LABJOB001.SHD present

This establishes the simulated stuck print queue.

### 02_CLIENT01_Print_Queue_Restored.png

Shows:

- SpoolerStatus = Running
- StartupType = Automatic
- QueueFileCount = 0

This confirms successful remediation.

---

## Commands Used

Get-Service Spooler

Get-CimInstance Win32_Service -Filter "Name='Spooler'"

Get-Service Spooler -RequiredServices

Get-ChildItem "C:\Windows\System32\spool\PRINTERS" -Force

Stop-Service Spooler

New-Item "C:\Windows\System32\spool\PRINTERS\LABJOB001.SPL" -ItemType File

New-Item "C:\Windows\System32\spool\PRINTERS\LABJOB001.SHD" -ItemType File

Remove-Item "C:\Windows\System32\spool\PRINTERS\*" -Force

Start-Service Spooler

$QueueFiles = Get-ChildItem "C:\Windows\System32\spool\PRINTERS" -Force

---

## Skills Demonstrated

- Windows printing troubleshooting
- Print Spooler administration
- Print queue troubleshooting
- Windows service management
- PowerShell
- CIM service investigation
- Service dependency analysis
- Spool directory investigation
- Queue-file identification
- Targeted queue cleanup
- Service recovery
- Root cause analysis
- Help desk troubleshooting
- Technical documentation

---

## Cheat Sheet Concepts Introduced

### Print Spooler

The Windows Print Spooler manages print jobs and communication between Windows applications and configured printers.

Windows service name:

Spooler

Executable:

spoolsv.exe

---

### Print Queue

A print queue contains jobs waiting to be processed by the Windows printing subsystem.

A stuck or problematic job can sometimes prevent later jobs from processing normally.

---

### Spool Directory

Windows stores spool-related files under:

C:\Windows\System32\spool\PRINTERS

When troubleshooting a stuck queue, this directory may contain files associated with queued jobs.

---

### SPL File

An .SPL file is associated with spooled print data.

It represents data being processed through the Windows printing subsystem.

---

### SHD File

An .SHD file contains information associated with a spooled print job.

SPL and SHD files may appear together for queued jobs.

---

### Service Dependency

A service can depend on other Windows components or services.

PowerShell can inspect required services using:

Get-Service <service> -RequiredServices

A dependency failure can affect the service that relies on it.

---

### spoolsv.exe

The executable associated with the Windows Print Spooler service.

Default location:

C:\Windows\System32\spoolsv.exe

---

### Clearing a Stuck Print Queue

A common administrative troubleshooting sequence is:

Stop Print Spooler
→ inspect queue
→ remove confirmed stuck queue files
→ restart Print Spooler
→ verify queue/service state

Stopping the Spooler before manually manipulating active spool files helps avoid conflicts with the printing service.

---

## Key Troubleshooting Principle

A printer problem is not always a printer hardware problem.

Windows printing involves several components:

Application
→ Print Queue
→ Print Spooler
→ Printer/Driver/Port
→ Physical or virtual printer

When jobs appear stuck, investigate the Windows printing subsystem before assuming the physical printer has failed.

A useful troubleshooting sequence is:

User reports stuck print job
→ inspect Print Spooler
→ inspect service dependencies
→ inspect print queue
→ inspect spool directory
→ stop Spooler when manual queue cleanup is required
→ remove confirmed stuck queue artifacts
→ restart Spooler
→ verify service and queue state

---

## Important Scope Note

This ticket used manually created empty .SPL and .SHD files to simulate orphaned print-queue artifacts.

It did not create a real print job and did not demonstrate a printer-driver or physical-printer failure.

The documented scenario should therefore be described as:

Simulated stuck/orphaned print queue troubleshooting

rather than claiming that an actual printer produced corrupted spool files.

---

## Outcome

A simulated stuck Windows print queue was successfully investigated and resolved on CLIENT01.

The Print Spooler was stopped and simulated .SPL and .SHD queue files were identified inside the Windows spool directory.

The queue artifacts were removed while the Spooler was stopped.

The Print Spooler was restarted and final verification showed:

SpoolerStatus = Running
StartupType = Automatic
QueueFileCount = 0

The ticket demonstrated Print Spooler administration, queue troubleshooting, spool-file analysis, service dependencies, safe queue cleanup, and Windows printing subsystem recovery.