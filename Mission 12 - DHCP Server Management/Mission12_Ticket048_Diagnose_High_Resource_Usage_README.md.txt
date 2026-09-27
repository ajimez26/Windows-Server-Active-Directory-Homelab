# Mission 12 - Ticket 048: Diagnose High Resource Usage

## Ticket Summary
Investigated and resolved a simulated workstation performance issue on CLIENT01 involving high CPU utilization.

A healthy performance baseline was first established using PowerShell and Windows Task Manager. CLIENT01 initially showed approximately 8% overall CPU utilization, 42% memory utilization, 1% disk utilization, and no significant network activity.

A controlled CPU-intensive PowerShell workload was then introduced to simulate a user reporting that the workstation had become slow or unresponsive.

Task Manager showed overall CPU utilization increasing to approximately 53%, with a Windows PowerShell process consuming approximately 50.3% CPU.

PowerShell was then used to compare running PowerShell processes and their Process IDs. PID 3592 showed significantly greater accumulated CPU time than the other PowerShell process and was identified as the process responsible for the simulated performance issue.

Because the workload's PowerShell session was occupied by the continuous loop, the responsive administrative PowerShell session was used to terminate PID 3592.

Task Manager was then used to verify that CPU utilization returned toward normal levels.

## Environment
- Client: CLIENT01
- Domain: jimeneztech.local
- Client Operating System: Windows 11 Enterprise
- Troubleshooting Tools:
  - Windows Task Manager
  - Windows PowerShell
  - Get-Process
  - Stop-Process

## Tasks Completed
- Established a workstation performance baseline
- Reviewed CPU, memory, disk, and network utilization
- Identified processes consuming the most memory
- Converted process working-set values from bytes to megabytes
- Compared PowerShell process information with Task Manager
- Created a controlled CPU-intensive workload
- Reproduced a high-CPU performance incident
- Identified elevated CPU utilization through Task Manager
- Distinguished CPU pressure from memory, disk, and network utilization
- Identified the PowerShell process responsible for the workload
- Compared multiple PowerShell processes by accumulated CPU time
- Identified the high-CPU process by PID
- Terminated only the offending process
- Verified CPU utilization returned toward normal levels
- Documented the troubleshooting and remediation process

## Initial Process Analysis
PowerShell was used to identify the processes consuming the most working-set memory.

Command:

Get-Process | Sort-Object WorkingSet -Descending | Select-Object -First 10 ProcessName, Id, CPU, @{Name="MemoryMB";Expression={[math]::Round($_.WorkingSet/1MB,2)}}

The command displayed:

- Process Name
- Process ID
- Accumulated CPU Time
- Working Set Memory in MB

Processes observed included:

- MsMpEng
- explorer
- dwm
- StartMenuExperienceHost
- msedgewebview2
- SearchHost
- powershell
- WindowsTerminal
- ShellExperienceHost

Microsoft Defender's MsMpEng process had the largest working set during the PowerShell baseline at approximately 286.89 MB.

## Understanding Working Set
Working Set represents the amount of physical memory currently associated with a process.

PowerShell normally reports the WorkingSet property in bytes.

For easier analysis, the following expression was used:

[math]::Round($_.WorkingSet/1MB,2)

This converted the value into megabytes and rounded the result to two decimal places.

This made process memory consumption easier to compare during troubleshooting.

## CPU Property Consideration
The CPU property displayed by Get-Process represents accumulated processor time used by a process.

It does not represent the process's current CPU utilization percentage.

This distinction is important when comparing PowerShell Get-Process results with Task Manager.

Task Manager can display current CPU utilization as a percentage, while the CPU property returned by Get-Process represents accumulated processor time.

## Task Manager Performance Baseline
Windows Task Manager was used to establish the workstation's normal performance condition.

The baseline showed approximately:

CPU:
8%

Memory:
42%

Disk:
1%

Network:
0%

No significant resource bottleneck was present during the baseline.

This provided a known-good comparison point before reproducing the performance incident.

## Simulated High-CPU Incident
A controlled PowerShell workload was created using:

while ($true) { $result = [math]::Sqrt((Get-Random)) }

This command continuously generated random numbers and performed square-root calculations.

Because the loop was configured to continue indefinitely, the PowerShell process continuously consumed processor resources until interrupted or terminated.

The workload was intentionally created for troubleshooting purposes.

## Performance Impact
After starting the workload, Windows Task Manager showed a significant increase in processor utilization.

Observed utilization was approximately:

Overall CPU:
53%

Memory:
42%

Disk:
0%

Network:
0%

The Windows PowerShell process responsible for the workload showed approximately:

50.3% CPU utilization

The significant increase from the approximately 8% CPU baseline demonstrated that the simulated workload was creating processor pressure.

Memory utilization remained approximately 42%, while disk and network utilization remained near zero.

This helped isolate the performance issue specifically to CPU utilization.

## Resource Bottleneck Analysis
The Task Manager results demonstrated why multiple resource categories should be reviewed when troubleshooting a slow workstation.

CPU utilization increased significantly.

Memory utilization remained relatively stable.

Disk utilization remained near zero.

Network utilization remained near zero.

This suggested that the performance problem was CPU-bound rather than being caused by:

- Insufficient memory
- Heavy disk activity
- Network congestion

## Process Identification
Multiple PowerShell sessions were running during the investigation.

The following command was used to compare them:

Get-Process powershell | Sort-Object CPU -Descending | Select-Object ProcessName, Id, CPU, @{Name="MemoryMB";Expression={[math]::Round($_.WorkingSet/1MB,2)}}

The results showed:

Process:
powershell

PID:
3592

Accumulated CPU Time:
224.0625

Memory:
73.24 MB

A second PowerShell process showed:

PID:
4108

Accumulated CPU Time:
1.1875

Memory:
85.46 MB

PID 3592 had accumulated dramatically more processor time than PID 4108.

Combined with the Task Manager evidence showing a PowerShell process consuming approximately 50.3% CPU, PID 3592 was identified as the process responsible for the simulated high-CPU workload.

## Root Cause
The performance degradation was caused by the intentionally created continuous PowerShell calculation loop.

The workload continuously executed:

while ($true)

which caused the process to repeatedly perform calculations without returning to an idle state.

The affected PowerShell session therefore consumed a substantial amount of available processor capacity.

PID 3592 was identified as the offending process.

## Resolution
The PowerShell window executing the workload remained occupied by the continuous loop and did not provide a normal command prompt.

A separate responsive PowerShell session was therefore used to terminate the offending process.

Command:

Stop-Process -Id 3592 -Force

This targeted only the identified high-CPU process instead of terminating every PowerShell process or restarting the workstation.

This represented a more precise remediation approach.

## Post-Resolution Verification
After terminating PID 3592, Windows Task Manager was reviewed again.

CPU utilization decreased substantially from the approximately 53% observed during the simulated incident.

This confirmed that the high-CPU PowerShell process had been responsible for the performance issue and that terminating the identified process successfully removed the simulated workload.

## Troubleshooting Methodology
The incident was investigated using the following process:

1. Establish normal workstation resource utilization
2. Review running processes
3. Identify baseline CPU and memory usage
4. Reproduce the reported performance problem
5. Compare current resource utilization against the baseline
6. Determine which resource was experiencing increased utilization
7. Identify the process consuming processor resources
8. Correlate the process with its PID
9. Compare similar processes using accumulated CPU time
10. Identify the offending process
11. Terminate only the affected process
12. Review Task Manager after remediation
13. Confirm CPU utilization returned toward normal levels
14. Document the incident and resolution

This approach avoided unnecessary system changes and focused remediation specifically on the process responsible for the resource issue.

## Verification
Final verification confirmed:

- CLIENT01 initially showed approximately 8% CPU utilization
- Memory utilization was approximately 42% at baseline
- Disk utilization was approximately 1%
- Network utilization was approximately 0%
- A controlled CPU workload was successfully created
- Overall CPU utilization increased to approximately 53%
- Windows PowerShell consumed approximately 50.3% CPU during the incident
- Memory utilization remained approximately 42%
- Disk and network utilization remained near zero
- PID 3592 showed significantly elevated accumulated CPU time
- PID 3592 was identified as the offending PowerShell process
- PID 3592 was specifically terminated
- CPU utilization decreased after remediation
- No unrelated processes were intentionally terminated

## Evidence
01_CLIENT01_Process_Memory_Baseline.png
- Shows PowerShell process resource analysis
- Shows processes sorted by working-set memory
- Shows Process IDs
- Shows accumulated CPU time
- Shows memory utilization converted to MB

02_CLIENT01_Task_Manager_Resource_Baseline.png
- Shows Task Manager before the simulated incident
- Shows approximately 8% CPU utilization
- Shows approximately 42% memory utilization
- Shows approximately 1% disk utilization
- Establishes the healthy performance baseline

03_CLIENT01_High_CPU_Incident.png
- Shows the controlled PowerShell workload
- Shows overall CPU utilization increased to approximately 53%
- Shows Windows PowerShell consuming approximately 50.3% CPU
- Shows memory remaining approximately 42%
- Demonstrates the simulated performance incident

04_CLIENT01_High_CPU_Process_Identified.png
- Shows multiple PowerShell processes
- Shows PID 3592 with significantly elevated accumulated CPU time
- Shows PID 4108 with minimal accumulated CPU time
- Demonstrates process-level identification of the performance issue

05_CLIENT01_CPU_Usage_Restored.png
- Shows Task Manager after terminating the offending process
- Demonstrates reduced CPU utilization
- Confirms successful remediation

## Commands Used
Get-Process | Sort-Object WorkingSet -Descending | Select-Object -First 10 ProcessName, Id, CPU, @{Name="MemoryMB";Expression={[math]::Round($_.WorkingSet/1MB,2)}}

while ($true) { $result = [math]::Sqrt((Get-Random)) }

Get-Process powershell | Sort-Object CPU -Descending | Select-Object ProcessName, Id, CPU, @{Name="MemoryMB";Expression={[math]::Round($_.WorkingSet/1MB,2)}}

Stop-Process -Id 3592 -Force

## Skills Demonstrated
- Windows 11 Troubleshooting
- Workstation Performance Troubleshooting
- Windows Task Manager
- PowerShell
- Get-Process
- Stop-Process
- CPU Utilization Analysis
- Memory Utilization Analysis
- Working Set Analysis
- Process ID Analysis
- Process Resource Comparison
- Performance Baseline Establishment
- Resource Bottleneck Identification
- High-CPU Process Identification
- Process Termination
- Root Cause Analysis
- Incident Reproduction
- Post-Resolution Verification
- Help Desk Troubleshooting
- Technical Documentation

## Cheat Sheet Concepts Introduced
CPU Utilization
- Percentage of processor capacity currently being used.

Memory Utilization
- Amount or percentage of system memory currently in use.

Working Set
- Physical memory currently associated with a running process.

CPU Time
- Accumulated processor time consumed by a process. This is different from current CPU percentage.

PID
- Process Identifier assigned to a running process.

Task Manager
- Windows graphical utility for monitoring processes, applications, and system resource utilization.

Get-Process
- PowerShell command used to retrieve information about running processes.

Sort-Object
- PowerShell command used to sort objects based on a selected property.

WorkingSet
- Get-Process property representing a process's working-set memory in bytes.

Stop-Process
- PowerShell command used to terminate a running process.

Stop-Process -Id <PID> -Force
- Terminates a specific process using its Process ID.

Resource Bottleneck
- A system resource whose limited availability or excessive utilization is restricting overall system performance.

CPU-Bound
- A workload whose performance is primarily limited by processor utilization.

Key Troubleshooting Distinction:
Get-Process CPU ≠ Current CPU Percentage

The CPU property from Get-Process represents accumulated CPU time, while Task Manager's CPU column represents current processor utilization.

## Outcome
A simulated high-CPU workstation performance incident was successfully reproduced, investigated, and resolved on CLIENT01.

CLIENT01 initially operated at approximately 8% CPU utilization. A controlled PowerShell calculation loop increased overall CPU utilization to approximately 53%, with Windows PowerShell consuming approximately 50.3% CPU.

Task Manager showed that memory, disk, and network utilization remained relatively normal, helping isolate the performance issue to processor utilization.

PowerShell process analysis identified PID 3592 as having substantially greater accumulated CPU time than the other PowerShell session.

The offending PID was specifically terminated using Stop-Process rather than indiscriminately closing unrelated applications or restarting the workstation.

After termination, Task Manager confirmed that CPU utilization decreased substantially.

This ticket demonstrated a structured approach to workstation performance troubleshooting involving baseline establishment, resource monitoring, bottleneck identification, process analysis, PID correlation, targeted remediation, and post-resolution verification.