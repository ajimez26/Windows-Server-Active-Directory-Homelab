# Mission 12 - Ticket 057: Troubleshoot Windows Firewall Connectivity

## Ticket Summary

Investigated and resolved a simulated inbound TCP connectivity failure between DC01 and CLIENT01.

A temporary TCP service was configured on CLIENT01 to listen on port 8085. Local verification confirmed that the port was actively listening, but a remote connection attempt from DC01 failed.

Troubleshooting verified the network path, active Windows network profile, listening TCP port, and Windows Defender Firewall configuration.

No existing firewall port filter was found for TCP port 8085.

A Windows Defender Firewall inbound allow rule was created for TCP port 8085 on the Domain profile.

After the rule was created, DC01 successfully established TCP connectivity to CLIENT01 on port 8085.

The temporary firewall rule and TCP listener were removed after verification to return the lab environment to its original state.

---

## Environment

- Server: DC01
- Server IP: 192.168.56.101
- Client: CLIENT01
- Client IP: 192.168.56.150
- Domain: jimeneztech.local
- Client OS: Windows 11 Enterprise Evaluation
- Server OS: Windows Server 2025 Standard Evaluation
- Virtualization Platform: VirtualBox
- Internal Network: VirtualBox Host-Only
- Test Port: TCP 8085
- Firewall Profile: Domain
- Administrative Tool: Windows PowerShell

---

## Ticket Request

A remote system is unable to establish a TCP connection to a service running on CLIENT01.

Determine whether the problem is caused by:

- The application/service not listening
- Incorrect IP addressing
- Network connectivity
- Incorrect TCP port
- Windows Defender Firewall

Identify the root cause, restore connectivity, verify the resolution, and remove temporary testing configuration after completing the incident.

---

## Firewall Baseline

Windows Defender Firewall profiles were inspected on CLIENT01 using:

Get-NetFirewallProfile |
Select-Object Name, Enabled, DefaultInboundAction, DefaultOutboundAction

The following profiles were enabled:

- Domain
- Private
- Public

The DefaultInboundAction and DefaultOutboundAction properties displayed:

NotConfigured

This indicated that these properties were not explicitly overridden through the inspected configuration.

---

## Temporary TCP Listener

A temporary TCP listener was created on CLIENT01 using PowerShell.

Commands:

$Listener = [System.Net.Sockets.TcpListener]::new(
    [System.Net.IPAddress]::Any,
    8085
)

$Listener.Start()

The listener was configured to accept TCP connections on port:

8085

The use of IPAddress.Any allowed the listener to bind to available IPv4 interfaces rather than only a single local IPv4 address.

---

## Local Port Verification

The listener was verified using:

Get-NetTCPConnection -LocalPort 8085 -State Listen |
Select-Object LocalAddress, LocalPort, State, OwningProcess

Result:

LocalAddress: 0.0.0.0
LocalPort: 8085
State: Listen
OwningProcess: 2300

The address:

0.0.0.0

indicated that the listener was bound to all local IPv4 interfaces.

This confirmed that an application was actively listening on TCP port 8085 before remote connectivity was tested.

---

## Initial Remote Connectivity Test

From DC01, TCP connectivity to CLIENT01 was tested using:

Test-NetConnection 192.168.56.150 -Port 8085

The test returned:

ComputerName: 192.168.56.150
RemoteAddress: 192.168.56.150
RemotePort: 8085
InterfaceAlias: Ethernet 2
SourceAddress: 192.168.56.101
PingSucceeded: False
TcpTestSucceeded: False

The TCP connection failed even though CLIENT01 had already been verified as listening on port 8085.

This narrowed the troubleshooting scope.

---

## Network Profile Investigation

CLIENT01's network profiles were inspected using:

Get-NetConnectionProfile |
Select-Object InterfaceAlias, NetworkCategory, IPv4Connectivity

Results showed:

Ethernet
NetworkCategory: DomainAuthenticated

Ethernet 2
NetworkCategory: DomainAuthenticated

This established that CLIENT01 was operating under the Domain firewall profile for the relevant network configuration.

---

## Firewall Port Investigation

Existing Windows Defender Firewall port filters were searched for TCP port 8085:

Get-NetFirewallPortFilter |
Where-Object {$_.LocalPort -eq "8085"}

The command returned no matching port filter.

No existing firewall rule specifically permitting the test port was identified.

---

## Root Cause

CLIENT01 had an application actively listening on TCP port 8085.

However, DC01 was unable to establish an inbound TCP connection to that port.

The investigation established that:

- CLIENT01 had the expected IP address
- The TCP listener was active
- Port 8085 was in the Listen state
- The listener was bound to all IPv4 interfaces
- DC01 was attempting to reach the correct IP address
- DC01 was attempting to reach the correct TCP port
- CLIENT01 was using the Domain network profile
- No matching inbound firewall port rule existed for TCP 8085

The failure was therefore isolated to inbound Windows Defender Firewall filtering.

---

## Remediation

A Windows Defender Firewall inbound rule was created on CLIENT01:

New-NetFirewallRule `
-DisplayName "LAB - Allow TCP 8085" `
-Direction Inbound `
-Protocol TCP `
-LocalPort 8085 `
-Action Allow `
-Profile Domain

The rule was restricted to:

- Direction: Inbound
- Protocol: TCP
- Local Port: 8085
- Action: Allow
- Profile: Domain

This followed the principle of allowing the specific traffic required rather than broadly disabling Windows Defender Firewall.

---

## Firewall Rule Verification

The new firewall rule was verified using:

Get-NetFirewallRule -DisplayName "LAB - Allow TCP 8085" |
Select-Object DisplayName, Enabled, Direction, Action, Profile

Result:

DisplayName: LAB - Allow TCP 8085
Enabled: True
Direction: Inbound
Action: Allow
Profile: Domain

This confirmed that the intended inbound rule was active.

---

## Final Connectivity Verification

DC01 tested the port again:

Test-NetConnection 192.168.56.150 -Port 8085

Final result:

ComputerName: 192.168.56.150
RemoteAddress: 192.168.56.150
RemotePort: 8085
InterfaceAlias: Ethernet 2
SourceAddress: 192.168.56.101
TcpTestSucceeded: True

The successful TCP test demonstrated that DC01 could now establish a connection to CLIENT01 on TCP port 8085.

This confirmed that the firewall configuration change resolved the incident.

---

## ICMP vs TCP

During the initial connectivity test:

PingSucceeded: False

and:

TcpTestSucceeded: False

were observed.

These represent different types of network traffic.

Ping typically uses ICMP Echo traffic.

The test service used:

TCP port 8085

A firewall can therefore block ICMP while allowing TCP 8085, or allow ICMP while blocking TCP 8085.

For this reason, a failed ping does not automatically prove that a specific TCP service is unreachable.

Testing the actual application port provides more useful information when troubleshooting service-specific connectivity.

---

## Cleanup

Because the listener and firewall rule were created only for the troubleshooting simulation, both were removed after successful verification.

The temporary firewall rule was removed using:

Remove-NetFirewallRule -DisplayName "LAB - Allow TCP 8085"

Removal was verified using:

Get-NetFirewallRule -DisplayName "LAB - Allow TCP 8085" -ErrorAction SilentlyContinue

No output confirmed that the temporary rule had been removed.

The TCP listener was stopped using:

$Listener.Stop()

The listening port was checked using:

Get-NetTCPConnection -LocalPort 8085 -State Listen -ErrorAction SilentlyContinue

No output confirmed that the temporary listener was no longer active.

This returned CLIENT01 to its previous configuration.

---

## Evidence

### 01_DC01_TCP_8085_Connectivity_Failure.png

- Shows DC01 attempting to connect to CLIENT01
- Shows RemoteAddress 192.168.56.150
- Shows RemotePort 8085
- Shows SourceAddress 192.168.56.101
- Shows TcpTestSucceeded False
- Establishes the initial connectivity failure

### 02_DC01_TCP_8085_Connectivity_Restored.png

- Shows the same TCP connectivity test after remediation
- Shows RemoteAddress 192.168.56.150
- Shows RemotePort 8085
- Shows SourceAddress 192.168.56.101
- Shows TcpTestSucceeded True
- Confirms successful remote TCP connectivity

### 03_CLIENT01_TCP_8085_Firewall_Rule.png

- Shows LAB - Allow TCP 8085
- Shows Enabled True
- Shows Direction Inbound
- Shows Action Allow
- Shows Profile Domain
- Documents the firewall configuration responsible for restoring connectivity

---

## Commands Used

Get-NetFirewallProfile |
Select-Object Name, Enabled, DefaultInboundAction, DefaultOutboundAction

$Listener = [System.Net.Sockets.TcpListener]::new(
    [System.Net.IPAddress]::Any,
    8085
)

$Listener.Start()

Get-NetTCPConnection -LocalPort 8085 -State Listen |
Select-Object LocalAddress, LocalPort, State, OwningProcess

Test-NetConnection 192.168.56.150 -Port 8085

Get-NetConnectionProfile |
Select-Object InterfaceAlias, NetworkCategory, IPv4Connectivity

Get-NetFirewallPortFilter |
Where-Object {$_.LocalPort -eq "8085"}

New-NetFirewallRule `
-DisplayName "LAB - Allow TCP 8085" `
-Direction Inbound `
-Protocol TCP `
-LocalPort 8085 `
-Action Allow `
-Profile Domain

Get-NetFirewallRule -DisplayName "LAB - Allow TCP 8085" |
Select-Object DisplayName, Enabled, Direction, Action, Profile

Remove-NetFirewallRule -DisplayName "LAB - Allow TCP 8085"

$Listener.Stop()

Get-NetTCPConnection -LocalPort 8085 -State Listen -ErrorAction SilentlyContinue

---

## Skills Demonstrated

- Windows Defender Firewall
- Windows Firewall Rule Management
- TCP/IP Troubleshooting
- TCP Port Troubleshooting
- Inbound Connectivity Troubleshooting
- PowerShell
- Test-NetConnection
- Get-NetTCPConnection
- Get-NetConnectionProfile
- Get-NetFirewallProfile
- Get-NetFirewallRule
- Get-NetFirewallPortFilter
- New-NetFirewallRule
- Remove-NetFirewallRule
- TCP Listener Configuration
- Network Profile Identification
- Domain Firewall Profile
- ICMP vs TCP Troubleshooting
- Application vs Network Troubleshooting
- Root Cause Analysis
- Connectivity Verification
- Help Desk Troubleshooting
- Technical Documentation
- Lab Cleanup

---

## Cheat Sheet Concepts Introduced

### TCP Port

A logical numbered endpoint used by TCP applications to identify network services.

In this ticket:

TCP 8085

was used as a temporary lab service port.

---

### Listening Port

A TCP port on which a process is waiting for incoming connection requests.

A port in the Listen state indicates that an application has created a socket capable of accepting connections.

---

### TCP Listener

A program or software object that waits for incoming TCP connection attempts on a specified port.

In this lab, PowerShell/.NET was used to create the listener.

---

### 0.0.0.0

When displayed as a listening local address, 0.0.0.0 generally indicates that the application is listening on all applicable local IPv4 interfaces rather than one specific IPv4 address.

---

### Inbound Traffic

Network traffic entering a computer from another device.

DC01 connecting to CLIENT01:8085 represented inbound traffic from CLIENT01's perspective.

---

### Outbound Traffic

Network traffic originating from a computer toward another destination.

The connection attempt from DC01 was outbound traffic from DC01's perspective.

---

### Firewall Rule

A configuration that determines whether particular network traffic should be allowed or blocked.

Rules can consider properties such as:

- Direction
- Protocol
- Port
- Application
- Address
- Network profile

---

### Firewall Profile

Windows Defender Firewall maintains different profiles based on the type of network Windows identifies.

Common profiles:

- Domain
- Private
- Public

---

### DomainAuthenticated

Indicates that Windows has authenticated the network against the computer's Active Directory domain and is applying the Domain network category/profile.

---

### Test-NetConnection

PowerShell networking diagnostic cmdlet.

It can test several forms of connectivity, including a specific TCP port.

Example:

Test-NetConnection 192.168.56.150 -Port 8085

---

### TcpTestSucceeded

A Test-NetConnection result indicating whether a TCP connection to the specified destination and port succeeded.

True:
TCP connection established.

False:
TCP connection could not be established.

---

### Get-NetTCPConnection

PowerShell cmdlet used to inspect TCP connections and listening TCP endpoints.

It can display:

- Local address
- Local port
- Remote address
- Remote port
- TCP state
- Owning process

---

### Owning Process

The process ID associated with a TCP connection or listening endpoint.

This can help connect network activity to the application responsible for it.

---

### ICMP

Internet Control Message Protocol.

Ping commonly uses ICMP Echo Request and Echo Reply messages.

ICMP is not the same as TCP.

A failed ping does not necessarily mean a TCP application is unavailable.

---

### Principle of Least Access

Only the network access required for a service should be permitted.

Instead of disabling Windows Defender Firewall, this ticket created a narrowly scoped rule permitting:

TCP 8085
Inbound
Domain profile

---

## Key Troubleshooting Principle

Do not immediately blame the firewall when a network application cannot be reached.

First determine whether the application is actually listening.

A useful troubleshooting sequence is:

Remote application unavailable
→ verify destination IP
→ verify application is running
→ verify expected port is listening
→ identify active network profile
→ inspect firewall configuration
→ test remote TCP connectivity
→ make targeted configuration change
→ retest the same port
→ verify resolution

In this incident:

TCP 8085 listening locally
→ remote TCP test failed
→ firewall configuration investigated
→ targeted inbound rule created
→ same remote TCP test succeeded

This comparison provided evidence that the firewall was the blocking layer.

---

## Outcome

A simulated inbound TCP connectivity problem was successfully diagnosed and resolved between DC01 and CLIENT01.

CLIENT01 was confirmed to have an active TCP listener on port 8085, but DC01 initially returned:

TcpTestSucceeded: False

Windows network-profile and firewall configuration were investigated.

A targeted Domain-profile inbound firewall rule was created permitting TCP port 8085.

The same connectivity test was repeated from DC01 and returned:

TcpTestSucceeded: True

This demonstrated that the firewall configuration was responsible for the initial TCP connectivity failure.

After verification, the temporary firewall rule and TCP listener were removed.

The ticket demonstrated practical troubleshooting of listening ports, remote TCP connectivity, Windows Defender Firewall, network profiles, PowerShell networking tools, and targeted firewall remediation.