# Mission 12 - Ticket 046: Diagnose Network Connections with Netstat

## Ticket Summary
Investigated active and listening network connections on CLIENT01 using the Windows netstat utility.

The workstation's network connections were reviewed to identify listening TCP ports, connection states, and the Process IDs (PIDs) associated with network activity.

A listening TCP port was selected from the netstat results and its PID was used to identify the Windows process responsible for the listener.

The investigation identified TCP port 49699 as listening under PID 808. PowerShell was then used to map PID 808 to the Windows "services" process.

This ticket demonstrated how network activity can be correlated with running Windows processes during endpoint and network troubleshooting.

## Environment
- Client: CLIENT01
- Domain: jimeneztech.local
- CLIENT01 IPv4 Address: 192.168.56.150
- Network: 192.168.56.0/24
- Client Operating System: Windows 11 Enterprise
- Command-Line Tools: PowerShell and netstat

## Tasks Completed
- Reviewed active network connections on CLIENT01
- Used netstat to display network connection information
- Filtered netstat results for listening TCP ports
- Reviewed local and foreign address information
- Reviewed TCP connection states
- Identified Process IDs associated with network activity
- Selected PID 808 for further investigation
- Filtered netstat output specifically for PID 808
- Identified TCP port 49699 associated with PID 808
- Observed IPv4 and IPv6 listeners for the same process
- Used Get-Process to identify the process associated with PID 808
- Identified the process as "services"
- Correlated a listening network port with a running Windows process
- Documented the investigation

## Initial Network Connection Review
CLIENT01's network connections were initially reviewed using:

netstat -ano

The command displayed information including:

- Protocol
- Local Address
- Foreign Address
- Connection State
- Process ID

The "-ano" options provided additional information useful during troubleshooting.

-a
Displays active connections and listening ports.

-n
Displays addresses and port numbers numerically instead of attempting to resolve names.

-o
Displays the Process ID associated with each connection.

Together, these options provide a useful view of network activity and the processes associated with it.

## Listening Port Analysis
Because the complete netstat output contained many entries, PowerShell filtering was used to focus specifically on TCP ports in the LISTENING state.

Command:

netstat -ano | Select-String "LISTENING"

The filtered output displayed TCP ports that were waiting for incoming connections.

A LISTENING state indicates that a local process has opened a TCP port and is prepared to accept incoming connection requests.

This differs from an ESTABLISHED connection, which represents an active TCP session between two endpoints.

## Process ID Investigation
Each netstat entry included a Process ID in the final column.

A listening entry associated with PID 808 was selected for investigation.

The netstat results were filtered specifically for this PID using:

netstat -ano | Select-String "808$"

The results showed:

TCP    0.0.0.0:49699    0.0.0.0:0    LISTENING    808

TCP    [::]:49699       [::]:0       LISTENING    808

These results demonstrated that TCP port 49699 was listening under PID 808.

## IPv4 and IPv6 Listener Analysis
Two listening entries were visible for TCP port 49699.

The IPv4 entry used:

0.0.0.0:49699

The IPv6 entry used:

[::]:49699

The 0.0.0.0 address indicates that the process is listening on the specified port across available IPv4 interfaces.

The [::] entry represents the corresponding IPv6 listener.

This demonstrated how the same process may expose listening sockets for both IPv4 and IPv6 networking.

## PID to Process Mapping
After identifying PID 808 through netstat, PowerShell was used to determine which running Windows process owned that PID.

Command:

Get-Process -Id 808

The output showed:

Id:
808

ProcessName:
services

This successfully correlated the network listener with the Windows services process.

The troubleshooting relationship was:

TCP Port 49699
        ↓
LISTENING
        ↓
PID 808
        ↓
services

## Understanding Process IDs
A Process ID, or PID, is a numerical identifier assigned to a running process by the operating system.

PIDs are useful during troubleshooting because multiple Windows utilities can reference the same process using its PID.

For network troubleshooting, the PID displayed by netstat can be used with Windows process-management tools to determine which application or process owns a particular network connection or listening port.

This provides a bridge between network troubleshooting and Windows process troubleshooting.

## Local and Foreign Addresses
Netstat distinguishes between local and foreign addresses.

Local Address:
Represents the local IP address and port being used by the computer.

Foreign Address:
Represents the remote endpoint associated with a connection.

For a listening socket, a foreign address such as:

0.0.0.0:0

indicates that the listener is not currently associated with one specific remote endpoint.

Understanding these columns helps determine which system initiated or owns a connection and which remote system it may be communicating with.

## TCP Connection States
Netstat can display several TCP connection states.

Two important states reviewed during this ticket were:

LISTENING
A local process is waiting for incoming TCP connections.

ESTABLISHED
An active TCP connection currently exists between local and remote endpoints.

Recognizing connection states can help determine whether a service is waiting for connections, actively communicating, closing a connection, or experiencing another network condition.

## Troubleshooting Use Cases
The techniques demonstrated in this ticket can be useful when:

- Determining which process owns a network port
- Investigating unexpected listening ports
- Identifying active TCP connections
- Troubleshooting port conflicts
- Investigating applications using network resources
- Correlating network activity with Windows processes
- Determining whether a service or application is listening
- Investigating suspicious or unexpected network behavior

## Troubleshooting Methodology
The investigation followed this process:

1. Display active connections and listening ports
2. Filter the results to identify listening TCP ports
3. Review the PID associated with a selected listener
4. Filter netstat results using the selected PID
5. Identify the local TCP port associated with that PID
6. Use PowerShell to query the running process
7. Correlate the network listener with the Windows process
8. Document the findings

This approach demonstrates how command-line tools can be combined to move from network-level information to process-level information.

## Verification
Final verification confirmed:

- CLIENT01 successfully displayed active network information using netstat
- Listening TCP ports were successfully filtered
- PID information was available for network listeners
- PID 808 was selected for investigation
- TCP port 49699 was associated with PID 808
- Port 49699 had both IPv4 and IPv6 listeners
- PID 808 was successfully queried through PowerShell
- PID 808 mapped to the "services" process
- Network activity was successfully correlated with a running Windows process

## Evidence
01_CLIENT01_Listening_Ports_Baseline.png
- Shows netstat output filtered for LISTENING connections
- Documents listening TCP ports on CLIENT01
- Shows Process IDs associated with network listeners

02_CLIENT01_Port_PID_Process_Mapping.png
- Shows netstat results filtered for PID 808
- Shows TCP port 49699 in the LISTENING state
- Shows IPv4 and IPv6 listener entries
- Shows Get-Process output for PID 808
- Identifies the associated process as "services"
- Demonstrates the relationship between a network port, PID, and Windows process

## Commands Used
netstat -ano

netstat -ano | Select-String "LISTENING"

netstat -ano | Select-String "808$"

Get-Process -Id 808

## Skills Demonstrated
- Windows Network Troubleshooting
- Windows Command-Line Administration
- PowerShell
- Netstat
- TCP/IP Analysis
- Network Connection Analysis
- Listening Port Identification
- TCP Connection State Analysis
- Process ID Analysis
- PID-to-Process Mapping
- IPv4 Socket Analysis
- IPv6 Socket Analysis
- Endpoint Troubleshooting
- Network Activity Investigation
- Windows Process Investigation
- Technical Documentation

## Cheat Sheet Concepts Introduced
netstat
- Windows command used to examine network connections and listening ports.

PID
- Process Identifier assigned to a running process.

LISTENING
- A TCP state indicating that a process is waiting for incoming connections.

ESTABLISHED
- A TCP state indicating that an active connection exists between two endpoints.

Local Address
- The local IP address and port associated with a connection or listener.

Foreign Address
- The remote IP address and port associated with a connection.

0.0.0.0
- When shown as a listening address, indicates the socket is listening across available IPv4 interfaces.

[::]
- IPv6 unspecified address, commonly seen when a process is listening across available IPv6 interfaces.

netstat -ano
- Displays connections/listeners numerically and includes their associated PIDs.

Get-Process -Id
- PowerShell command used to identify a running process from its PID.

## Outcome
Network connections and listening ports on CLIENT01 were successfully investigated using netstat and PowerShell.

The netstat utility provided visibility into TCP connection states, local and foreign addresses, listening ports, and Process IDs.

A listening TCP port was selected for further investigation. TCP port 49699 was identified as listening under PID 808 for both IPv4 and IPv6.

PowerShell was then used to query PID 808, which identified the associated Windows process as "services."

This ticket demonstrated how network-level information can be correlated with process-level information during Windows troubleshooting.

The techniques practiced provide a foundation for diagnosing port conflicts, investigating unexpected network activity, identifying applications using network resources, and troubleshooting Windows endpoints.