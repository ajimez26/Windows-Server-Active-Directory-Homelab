# Mission 12 - Ticket 052: Diagnose Disk Space and Storage Usage

## Ticket Summary
Investigated a reported low-disk-space concern on CLIENT01 using PowerShell and Windows Storage settings.

PowerShell was first used to establish a storage baseline for the C: drive. The drive was confirmed to use the NTFS file system and reported a Healthy status.

The initial measurements showed a total capacity of 62.96 GB, with 33.04 GB used and 29.92 GB free. Approximately 52.47% of the drive was in use.

Based on these measurements, CLIENT01 was not actually experiencing critically low disk space. Approximately 47.5% of the drive remained available.

Windows Storage settings were then used to analyze which categories were consuming disk space. The largest categories included System & reserved, Installed apps, Temporary files, and storage associated with another user profile.

Temporary files were investigated further to identify data that could be safely removed without deleting user documents.

A controlled cleanup was performed using Windows Temporary Files management.

After cleanup, PowerShell was used again to measure the C: drive.

The final measurements showed 32.54 GB used and 30.42 GB free, with approximately 51.69% of the drive in use.

Approximately 0.50 GB of disk space was recovered.

The ticket demonstrated storage analysis, safe cleanup practices, PowerShell disk monitoring, GUI and command-line correlation, and before-and-after remediation verification.

## Environment
- Client: CLIENT01
- Domain: jimeneztech.local
- Client Operating System: Windows 11 Enterprise Evaluation
- Drive Investigated: C:
- File System: NTFS
- Drive Health: Healthy
- Total Capacity: 62.96 GB
- Administrative Tools:
  - Windows PowerShell
  - Windows Settings
  - Storage Management
  - Temporary Files Cleanup

## Tasks Completed
- Investigated a reported low-storage concern
- Queried the C: volume using PowerShell
- Identified the file system
- Verified volume health
- Measured total disk capacity
- Measured used disk space
- Measured available disk space
- Calculated percentage of disk space used
- Established a pre-remediation baseline
- Reviewed Windows Storage settings
- Correlated GUI storage measurements with PowerShell
- Analyzed storage usage by category
- Identified temporary files as a cleanup candidate
- Reviewed temporary-file categories before deletion
- Avoided indiscriminate deletion of user data
- Performed a controlled temporary-file cleanup
- Verified that the cleanup completed
- Re-measured disk usage using PowerShell
- Compared pre-cleanup and post-cleanup values
- Confirmed approximately 0.50 GB of storage was recovered
- Determined that the original low-storage concern was not supported by the baseline evidence
- Documented the investigation and remediation

## Initial Disk Space Assessment
PowerShell was used to retrieve information about the Windows system volume.

Command:

Get-Volume -DriveLetter C |
Select-Object DriveLetter, FileSystem, HealthStatus,
@{Name="SizeGB";Expression={[math]::Round($_.Size/1GB,2)}},
@{Name="FreeGB";Expression={[math]::Round($_.SizeRemaining/1GB,2)}}

The initial results showed:

Drive Letter:
C:

File System:
NTFS

Health Status:
Healthy

Total Capacity:
62.96 GB

Free Space:
29.92 GB

This established that the C: volume itself reported a healthy state.

## Disk Usage Calculation
Additional PowerShell commands were used to calculate used storage and percentage utilization.

Commands:

$disk = Get-Volume -DriveLetter C

$used = $disk.Size - $disk.SizeRemaining

[PSCustomObject]@{
    Drive = "C:"
    TotalGB = [math]::Round($disk.Size/1GB,2)
    UsedGB = [math]::Round($used/1GB,2)
    FreeGB = [math]::Round($disk.SizeRemaining/1GB,2)
    PercentUsed = [math]::Round(($used/$disk.Size)*100,2)
}

The baseline showed:

Total:
62.96 GB

Used:
33.04 GB

Free:
29.92 GB

Percentage Used:
52.47%

Approximately 47.53% of the drive therefore remained free.

## Initial Diagnosis
The user's reported concern was low disk space.

However, the measured baseline showed that nearly half of the C: drive remained available.

The available evidence therefore did not support a diagnosis of critically low disk space.

This demonstrated an important troubleshooting principle:

Verify the reported condition before attempting remediation.

A technician should not assume that a user's description automatically identifies the root cause.

## Windows Storage Analysis
Windows Settings was used to provide a graphical breakdown of storage usage.

Navigation:

Settings
→ System
→ Storage
→ Local Disk (C:)

Windows reported approximately:

33.0 GB used
29.9 GB free

These measurements closely matched the PowerShell baseline.

Major storage categories included:

System & reserved:
19.1 GB

Installed apps:
8.07 GB

Temporary files:
3.85 GB

Other people:
1.96 GB

Other:
3.30 MB

This demonstrated correlation between Windows Storage settings and PowerShell measurements.

## Storage Category Analysis
The largest category was:

System & reserved — 19.1 GB

System & reserved contains storage required by Windows and was not treated as an appropriate target for manual deletion.

Installed applications consumed approximately:

8.07 GB

Temporary files were reported at approximately:

3.85 GB

Because temporary data may contain safe cleanup candidates, the Temporary Files category was selected for additional investigation.

## Temporary Files Investigation
The Temporary Files interface was opened before deleting any data.

Visible categories included:

Windows error reports and feedback diagnostics:
462 MB

Windows Update Cleanup:
380 MB

Delivery Optimization Files:
7.47 MB

Thumbnails:
7.02 MB

Microsoft Defender Antivirus:
4.01 MB

Temporary files:
1.75 MB

DirectX Shader Cache:
24.0 KB

Temporary Internet Files:
Approximately 5 KB

The listed categories were reviewed before performing cleanup.

## Safe Cleanup Considerations
Temporary-file cleanup should not be treated as permission to indiscriminately delete files.

Before remediation, potentially user-controlled categories should be reviewed carefully.

Examples include:

- Downloads
- Recycle Bin
- User documents
- Personal media
- Application data that may still be required

Downloads and Recycle Bin were not presented as selected cleanup categories in the reviewed interface.

The selected items consisted of Windows-generated temporary, cache, diagnostic, and cleanup data.

This allowed a controlled cleanup to proceed.

## Controlled Remediation
Windows Temporary Files management was used to remove selected temporary data.

Navigation:

Settings
→ System
→ Storage
→ Local Disk (C:)
→ Temporary files
→ Remove files

Windows completed the operation and displayed:

Finished temporary files cleanup.

After cleanup, no additional files were selected for immediate removal.

Windows Update Cleanup remained visible as an available category but was not selected for additional deletion.

No attempt was made to remove additional files simply to increase the amount of recovered storage.

## Post-Cleanup Verification
PowerShell was used again after remediation.

Commands:

$disk = Get-Volume -DriveLetter C

$used = $disk.Size - $disk.SizeRemaining

[PSCustomObject]@{
    Drive = "C:"
    TotalGB = [math]::Round($disk.Size/1GB,2)
    UsedGB = [math]::Round($used/1GB,2)
    FreeGB = [math]::Round($disk.SizeRemaining/1GB,2)
    PercentUsed = [math]::Round(($used/$disk.Size)*100,2)
}

The final measurements showed:

Total:
62.96 GB

Used:
32.54 GB

Free:
30.42 GB

Percentage Used:
51.69%

## Before and After Comparison
Pre-Cleanup:

Total:
62.96 GB

Used:
33.04 GB

Free:
29.92 GB

Percentage Used:
52.47%

Post-Cleanup:

Total:
62.96 GB

Used:
32.54 GB

Free:
30.42 GB

Percentage Used:
51.69%

Difference:

Used Space:
Decreased by approximately 0.50 GB

Free Space:
Increased by approximately 0.50 GB

Percentage Used:
Decreased by approximately 0.78 percentage points

Approximately 0.50 GB of storage was recovered through the controlled cleanup.

## Important Storage Observation
Windows initially displayed approximately 3.85 GB under the Temporary Files storage category.

This did not mean that the entire 3.85 GB was necessarily selected or ultimately removed during the cleanup.

Post-cleanup measurements showed an actual storage improvement of approximately 0.50 GB.

The measured before-and-after values were therefore used when documenting the amount of recovered storage rather than assuming that the entire displayed Temporary Files category had been deleted.

## Get-Volume
Get-Volume is a PowerShell cmdlet used to retrieve information about Windows storage volumes.

Useful properties include:

DriveLetter
FileSystem
HealthStatus
Size
SizeRemaining

Example:

Get-Volume -DriveLetter C

This can be useful when remotely or locally investigating disk-space problems.

## Size vs SizeRemaining
Size represents the total capacity of the volume.

SizeRemaining represents currently available storage.

Used space can be calculated using:

Used Space = Size - SizeRemaining

Percentage used can be calculated using:

Used Space / Total Size × 100

These measurements provide more useful troubleshooting information than simply stating that a disk is "full" or "low."

## NTFS
The C: drive used the NTFS file system.

NTFS stands for:

New Technology File System

NTFS is a Windows file system that supports features such as:

- File and folder permissions
- Access Control Lists
- File ownership
- Encryption support
- Compression
- Large files and volumes
- Journaling

NTFS permissions had also been used elsewhere in the lab for shared-resource access control.

## Disk Health vs Disk Capacity
Disk health and available capacity represent different concepts.

Health Status:
Indicates whether Windows reports the volume as functioning normally.

Capacity:
Represents how much storage exists and how much remains available.

A drive can be:

Healthy but nearly full

or:

Have significant free space while experiencing another storage-related problem.

Therefore, technicians should evaluate both health and capacity.

## Troubleshooting Methodology
The ticket followed this process:

1. Receive a reported low-storage concern
2. Query the system volume
3. Verify the file system
4. Verify volume health
5. Measure total capacity
6. Measure free space
7. Calculate used space
8. Calculate percentage used
9. Establish a baseline
10. Determine whether the reported problem is supported by evidence
11. Review Windows Storage settings
12. Correlate GUI and PowerShell measurements
13. Identify major storage categories
14. Investigate temporary files
15. Review cleanup categories before deletion
16. Avoid indiscriminate deletion of user data
17. Perform a controlled cleanup
18. Verify cleanup completion
19. Re-measure disk usage
20. Compare before and after measurements
21. Calculate recovered storage
22. Document the final condition

## Verification
Final verification confirmed:

- C: uses NTFS
- C: reported a Healthy volume status
- Total capacity was 62.96 GB
- Initial used space was 33.04 GB
- Initial free space was 29.92 GB
- Initial utilization was 52.47%
- Windows Storage showed approximately 33.0 GB used
- Windows Storage showed approximately 29.9 GB free
- System & reserved was the largest storage category
- Temporary files were investigated before deletion
- Selected temporary data was removed
- Windows reported that temporary-file cleanup finished
- Post-cleanup used space was 32.54 GB
- Post-cleanup free space was 30.42 GB
- Post-cleanup utilization was 51.69%
- Approximately 0.50 GB of storage was recovered
- The original low-storage concern was not supported by the baseline measurements
- CLIENT01 retained substantial available storage after remediation

## Evidence
01_CLIENT01_Disk_Space_Baseline.png
- Shows C: volume information
- Shows NTFS as the file system
- Shows Healthy as the volume status
- Shows 62.96 GB total capacity
- Shows 33.04 GB used
- Shows 29.92 GB free
- Shows 52.47% utilization
- Establishes the pre-remediation baseline

02_CLIENT01_Windows_Storage_Analysis.png
- Shows Windows Storage settings
- Shows approximately 33.0 GB used
- Shows approximately 29.9 GB free
- Shows major storage categories
- Shows System & reserved at 19.1 GB
- Shows Installed apps at 8.07 GB
- Shows Temporary files at 3.85 GB
- Corroborates the PowerShell measurements

03_CLIENT01_Temporary_Files_Analysis.png
- Shows temporary-file cleanup categories
- Shows Windows error reports and feedback diagnostics
- Shows Windows Update Cleanup
- Shows Delivery Optimization Files
- Shows Thumbnails
- Shows Microsoft Defender Antivirus temporary data
- Shows additional cache and temporary-file categories
- Documents the pre-cleanup review

04_CLIENT01_Temporary_Files_Cleanup_Completed.png
- Shows the message "Finished temporary files cleanup"
- Shows that the cleanup operation completed
- Shows no files currently selected for additional removal
- Shows Windows Update Cleanup remaining available but unselected

05_CLIENT01_Disk_Space_Post_Cleanup_Verification.png
- Shows post-remediation PowerShell measurements
- Shows 62.96 GB total capacity
- Shows 32.54 GB used
- Shows 30.42 GB free
- Shows 51.69% utilization
- Provides measurable evidence of recovered storage

## Commands Used
Get-Volume -DriveLetter C |
Select-Object DriveLetter, FileSystem, HealthStatus,
@{Name="SizeGB";Expression={[math]::Round($_.Size/1GB,2)}},
@{Name="FreeGB";Expression={[math]::Round($_.SizeRemaining/1GB,2)}}

$disk = Get-Volume -DriveLetter C

$used = $disk.Size - $disk.SizeRemaining

[PSCustomObject]@{
    Drive = "C:"
    TotalGB = [math]::Round($disk.Size/1GB,2)
    UsedGB = [math]::Round($used/1GB,2)
    FreeGB = [math]::Round($disk.SizeRemaining/1GB,2)
    PercentUsed = [math]::Round(($used/$disk.Size)*100,2)
}

## Skills Demonstrated
- Windows 11 Administration
- Disk Space Troubleshooting
- Storage Capacity Analysis
- PowerShell
- Get-Volume
- PowerShell Calculated Properties
- PSCustomObject
- NTFS
- Windows Storage Settings
- Storage Category Analysis
- Temporary File Management
- Safe Disk Cleanup
- Disk Utilization Calculation
- Baseline Establishment
- GUI and CLI Correlation
- Before-and-After Analysis
- Remediation Verification
- User Data Protection
- Help Desk Troubleshooting
- Technical Documentation

## Cheat Sheet Concepts Introduced
Get-Volume
- PowerShell cmdlet used to retrieve information about storage volumes.

Drive Letter
- Letter assigned to a Windows volume, such as C:.

File System
- Structure used by an operating system to organize and manage files on storage.

NTFS
- New Technology File System.
- Common Windows file system supporting permissions, ownership, journaling, compression, and other features.

HealthStatus
- Property indicating the health state Windows reports for a volume.

Size
- Total capacity of a volume.

SizeRemaining
- Amount of currently available storage on a volume.

Used Space
- Storage currently occupied on the volume.

Formula:

Used Space = Size - SizeRemaining

Disk Utilization
- Percentage of the total volume currently in use.

Formula:

Used Space / Total Capacity × 100

Windows Storage
- Windows Settings interface used to analyze disk usage by category.

System & Reserved
- Storage used by Windows system components and reserved operating-system resources.

Temporary Files
- Temporary, cached, diagnostic, and cleanup data that Windows may identify as removable.

Windows Update Cleanup
- Older update-related files that Windows may make available for cleanup.

Delivery Optimization Files
- Cached Windows update/download content used by Windows Delivery Optimization.

Thumbnails
- Cached previews Windows creates for images, videos, and other files. Windows can recreate them when necessary.

Baseline
- Initial measurement collected before troubleshooting or remediation.

Post-Remediation Verification
- Measurement or test performed after a change to determine whether the action produced the expected result.

Key Troubleshooting Principle:

Reported symptom ≠ confirmed technical condition.

Verify the problem using objective measurements before attempting remediation.

Key Storage Principle:

Do not assume every large storage category should be deleted.

Identify what is consuming space and determine whether the data can be safely removed.

Key User Data Principle:

Do not blindly remove Downloads, Recycle Bin contents, documents, or other user-controlled files during disk cleanup.

Key Measurement Principle:

Measure storage before and after cleanup rather than assuming the amount displayed by a cleanup interface equals the amount actually recovered.

## Outcome
A reported low-disk-space concern on CLIENT01 was successfully investigated using PowerShell and Windows Storage settings.

The initial PowerShell baseline showed that the 62.96 GB NTFS C: volume was Healthy and contained 29.92 GB of free space.

Only 52.47% of the drive was being used.

The evidence therefore showed that CLIENT01 was not experiencing critically low disk space.

Windows Storage settings were used to analyze disk usage and showed System & reserved as the largest category, followed by Installed apps and Temporary files.

Temporary-file categories were reviewed before remediation to avoid indiscriminate deletion of user data.

A controlled cleanup of selected Windows-generated temporary data was performed.

After cleanup, PowerShell measurements showed:

32.54 GB used
30.42 GB free
51.69% utilization

Compared with the original baseline, approximately 0.50 GB of disk space was recovered.

The original low-storage concern was not supported by the measured evidence, so the cleanup was treated as preventative maintenance rather than emergency remediation.

This ticket demonstrated disk-space assessment, PowerShell storage analysis, Windows Storage investigation, safe cleanup practices, before-and-after measurement, and evidence-based help desk troubleshooting.