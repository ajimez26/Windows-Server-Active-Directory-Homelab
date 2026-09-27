# Mission 12 - Ticket 062: Troubleshoot Windows Disk Space Consumption

## Ticket Summary

Investigated and resolved a simulated abnormal disk-space consumption issue on CLIENT01.

The system initially had approximately 26.2 GB of free space on the C: drive.

Three oversized application log files were created under:

C:\ProgramData\JimenezTech\Logs

Each file consumed approximately 1 GB, resulting in approximately 3 GB of additional disk usage.

PowerShell was used to analyze disk capacity, measure directories, locate the largest files under ProgramData, quantify the storage consumed by the application logs, perform targeted cleanup, and calculate the amount of disk space recovered.

After remediation, approximately 3 GB of storage was recovered.

---

## Environment

- Client: CLIENT01
- Operating System: Windows 11 Enterprise Evaluation
- Domain: jimeneztech.local
- Filesystem: NTFS
- System Drive: C:
- System Drive Capacity: 62.96 GB
- Initial Free Space: 26.2 GB
- Troubleshooting Tool: Windows PowerShell
- Simulated Log Directory:
  C:\ProgramData\JimenezTech\Logs

---

## Ticket Request

A user reports that CLIENT01 is unexpectedly consuming disk space.

Investigate:

- Current disk capacity
- Used and available storage
- Large directories
- Large files
- Source of abnormal storage consumption

Identify the cause, remove only the unnecessary files, and verify how much disk space is recovered.

---

## Initial Storage Baseline

The C: drive was inspected using:

Get-PSDrive C |
Select-Object Name,
    @{Name="UsedGB";Expression={[math]::Round($_.Used/1GB,2)}},
    @{Name="FreeGB";Expression={[math]::Round($_.Free/1GB,2)}}

Results:

Used:
36.76 GB

Free:
26.2 GB

The volume was also inspected using:

Get-Volume -DriveLetter C |
Select-Object DriveLetter, FileSystemLabel, FileSystem,
    @{Name="SizeGB";Expression={[math]::Round($_.Size/1GB,2)}},
    @{Name="FreeGB";Expression={[math]::Round($_.SizeRemaining/1GB,2)}}

Results:

Drive Letter:
C

Filesystem:
NTFS

Capacity:
62.96 GB

Free:
26.2 GB

This established the healthy storage baseline before the simulated incident.

---

## Initial Directory Analysis

Common storage locations were measured:

C:\Users
C:\Windows\Temp

PowerShell recursively calculated the total size of files within each directory.

Results:

C:\Users
Approximately 3.07 GB

C:\Windows\Temp
Approximately 0 GB

Neither location showed abnormal storage consumption.

---

## Simulated Incident

A dedicated application log directory was created:

C:\ProgramData\JimenezTech\Logs

Command:

New-Item -ItemType Directory -Path "C:\ProgramData\JimenezTech\Logs" -Force

Three simulated oversized application logs were created:

ApplicationLog01.log
ApplicationLog02.log
ApplicationLog03.log

Each file was approximately 1 GB.

Commands:

fsutil file createnew "C:\ProgramData\JimenezTech\Logs\ApplicationLog01.log" 1073741824

fsutil file createnew "C:\ProgramData\JimenezTech\Logs\ApplicationLog02.log" 1073741824

fsutil file createnew "C:\ProgramData\JimenezTech\Logs\ApplicationLog03.log" 1073741824

The additional files reduced available disk space from approximately:

26.2 GB

to:

23.2 GB

---

## Large File Investigation

Rather than deleting files without identifying the cause, ProgramData was searched recursively for its largest files.

Command:

Get-ChildItem "C:\ProgramData" -File -Recurse -Force -ErrorAction SilentlyContinue |
Sort-Object Length -Descending |
Select-Object -First 10 FullName,
    @{Name="SizeGB";Expression={[math]::Round($_.Length/1GB,2)}}

The three largest files identified were:

C:\ProgramData\JimenezTech\Logs\ApplicationLog01.log

C:\ProgramData\JimenezTech\Logs\ApplicationLog02.log

C:\ProgramData\JimenezTech\Logs\ApplicationLog03.log

Each file measured approximately:

1 GB

Other files returned by the search were significantly smaller.

This identified the JimenezTech application logs as the abnormal source of storage consumption.

---

## Storage Impact Analysis

The total size of the affected directory was calculated:

$LogSize = (
    Get-ChildItem "C:\ProgramData\JimenezTech\Logs" -File -Recurse |
    Measure-Object Length -Sum
).Sum

The results were formatted using:

[PSCustomObject]@{
    Folder = "C:\ProgramData\JimenezTech\Logs"
    FileCount = (
        Get-ChildItem "C:\ProgramData\JimenezTech\Logs" -File
    ).Count
    TotalSizeGB = [math]::Round($LogSize / 1GB, 2)
}

Results:

Folder:
C:\ProgramData\JimenezTech\Logs

File Count:
3

Total Size:
3 GB

This quantified the exact storage impact before remediation.

---

## Root Cause

Three oversized simulated application log files were consuming approximately 3 GB of storage under:

C:\ProgramData\JimenezTech\Logs

The abnormal disk usage was therefore caused by excessive application log storage rather than:

- User profile data
- Windows temporary files
- Overall filesystem corruption
- A storage-device failure

The large-file investigation isolated the specific files responsible for the increase in disk consumption.

---

## Pre-Remediation Measurement

Available disk space was recorded before cleanup:

$FreeBefore = (Get-PSDrive C).Free

[math]::Round($FreeBefore / 1GB, 2)

Result:

23.2 GB

Recording this value allowed the effectiveness of the cleanup to be measured rather than assumed.

---

## Resolution

Only the identified simulated log files were removed.

Command:

Remove-Item "C:\ProgramData\JimenezTech\Logs\*.log" -Force

The directory was then checked:

Get-ChildItem "C:\ProgramData\JimenezTech\Logs" -File

No files were returned.

This confirmed that the targeted log files had been removed.

---

## Final Verification

Available storage was measured again:

$FreeAfter = (Get-PSDrive C).Free

The before-and-after values were compared:

[PSCustomObject]@{
    FreeBeforeGB = [math]::Round($FreeBefore / 1GB, 2)
    FreeAfterGB  = [math]::Round($FreeAfter / 1GB, 2)
    RecoveredGB  = [math]::Round(($FreeAfter - $FreeBefore) / 1GB, 2)
}

Results:

Free Before:
23.2 GB

Free After:
26.2 GB

Recovered:
3 GB

This confirmed that the targeted remediation successfully recovered the storage consumed by the oversized logs.

---

## Evidence

### 01_CLIENT01_Large_Log_Files_Identified.png

Shows the recursive large-file investigation of:

C:\ProgramData

The three largest results were:

ApplicationLog01.log
ApplicationLog02.log
ApplicationLog03.log

Each measured approximately 1 GB.

This establishes the root cause of the simulated disk-space issue.

### 02_CLIENT01_Disk_Space_Recovered.png

Shows:

- Problem directory containing 3 files
- Total log size of 3 GB
- Free space before remediation: 23.2 GB
- Free space after remediation: 26.2 GB
- Recovered storage: 3 GB

This verifies successful remediation.

---

## Commands Used

Get-PSDrive C

Get-Volume -DriveLetter C

Get-ChildItem

Measure-Object

Sort-Object

Select-Object

New-Item

fsutil file createnew

Remove-Item

Test-Path

$FreeBefore = (Get-PSDrive C).Free

$FreeAfter = (Get-PSDrive C).Free

---

## Skills Demonstrated

- Windows storage troubleshooting
- Disk utilization analysis
- PowerShell
- Filesystem investigation
- Large-file identification
- Recursive directory analysis
- File-size calculation
- Get-PSDrive
- Get-Volume
- Get-ChildItem
- Measure-Object
- Sort-Object
- PowerShell calculated properties
- Targeted file cleanup
- Storage recovery verification
- Root cause analysis
- Help desk troubleshooting
- Technical documentation

---

## Cheat Sheet Concepts Introduced

### Disk Utilization

Disk utilization describes how much of a storage volume is currently being used compared with its total capacity.

Important values include:

Total Capacity
Used Space
Free Space

Low free space can cause problems including:

- Application failures
- Update failures
- Performance problems
- Insufficient temporary storage
- User inability to save files

---

### Get-PSDrive

PowerShell cmdlet that displays information about PowerShell drives.

For filesystem drives, properties can include:

Used
Free

Example:

Get-PSDrive C

---

### Get-Volume

Displays information about Windows storage volumes.

Useful properties include:

DriveLetter
FileSystem
Size
SizeRemaining

Example:

Get-Volume -DriveLetter C

---

### Get-ChildItem

Enumerates files and directories.

Useful parameters include:

-File
-Recurse
-Force

Example:

Get-ChildItem "C:\ProgramData" -File -Recurse

---

### Measure-Object

Can calculate properties across multiple PowerShell objects.

For file-size analysis:

Measure-Object Length -Sum

adds the Length values of the files returned through the pipeline.

---

### Recursive Search

A recursive search examines a directory and its subdirectories.

PowerShell example:

Get-ChildItem -Recurse

Recursive searches are useful for locating files when the exact source of storage consumption is unknown.

---

### Large-File Analysis

Files can be sorted by their Length property:

Sort-Object Length -Descending

This allows the largest files to appear first.

A useful troubleshooting pattern is:

Get-ChildItem
→ Sort-Object Length -Descending
→ Select-Object -First 10

---

### Calculated Properties

Select-Object can calculate more readable values from raw properties.

For example, converting bytes to gigabytes:

@{Name="SizeGB";Expression={
    [math]::Round($_.Length/1GB,2)
}}

This makes storage information easier to interpret and document.

---

### ProgramData

C:\ProgramData

is commonly used for application data that is not specific to one individual user's profile.

Depending on the application, it may contain:

- Configuration
- Caches
- Logs
- Databases
- Application support files

When troubleshooting disk usage, ProgramData can therefore be an important location to investigate.

---

### Targeted Cleanup

Storage troubleshooting should not begin with indiscriminately deleting files.

A safer workflow is:

Identify
→ Measure
→ Validate
→ Remove only unnecessary data
→ Verify recovery

This reduces the chance of deleting important system or application files.

---

## Key Troubleshooting Principle

"Disk is full" identifies a symptom, not a root cause.

A technician should determine what is consuming the storage before deleting anything.

Useful workflow:

User reports low disk space
→ Check total/used/free capacity
→ Identify likely directories
→ Search for large files
→ Sort by size
→ identify abnormal files
→ quantify their impact
→ validate that they are safe to remove
→ perform targeted cleanup
→ measure free space again
→ calculate recovered capacity

The goal is not simply to create free space.

The goal is to understand:

What consumed the space?

Why?

How much?

Was the remediation successful?

---

## Outcome

A simulated abnormal disk-space consumption incident was successfully investigated and resolved on CLIENT01.

Three oversized application log files were identified under:

C:\ProgramData\JimenezTech\Logs

The files consumed a combined:

3 GB

Available disk space before remediation was:

23.2 GB

After targeted cleanup, available disk space increased to:

26.2 GB

Total storage recovered:

3 GB

The ticket demonstrated systematic Windows storage troubleshooting, large-file analysis, PowerShell filesystem investigation, targeted remediation, and quantitative verification of the recovered disk capacity.