# Mission 12 - Ticket 058: Troubleshoot Domain Time Synchronization

## Ticket Summary

Investigated and resolved a Windows Time synchronization problem on the domain-joined CLIENT01 workstation.

CLIENT01 initially reported that its time source was the Local CMOS Clock and that the system was not synchronized.

Windows Time configuration showed that CLIENT01 was correctly configured to use NT5DS, meaning it should obtain time through the Active Directory domain hierarchy.

Further investigation revealed that CLIENT01 temporarily could not discover the jimeneztech.local domain controller. DNS and Active Directory service-record resolution were investigated before making changes to the Windows Time configuration.

After domain DNS resolution and domain controller discovery were successfully restored, Windows Time was instructed to rediscover its time source and resynchronize.

CLIENT01 successfully synchronized with DC01 at 192.168.56.101.

---

## Environment

- Domain Controller: DC01
- DC01 Host-Only IP: 192.168.56.101
- DC01 NAT IP: 10.0.2.15
- Server OS: Windows Server 2025 Standard Evaluation

- Client: CLIENT01
- CLIENT01 Host-Only IP: 192.168.56.150
- Client OS: Windows 11 Enterprise Evaluation

- Domain: jimeneztech.local
- Virtualization Platform: VirtualBox
- Time Service: Windows Time (W32Time)
- Time Configuration Type: NT5DS
- DNS Server: 192.168.56.101
- Administrative Tool: Windows PowerShell

---

## Ticket Request

A domain-joined workstation is not synchronizing its system clock with the Active Directory domain.

Investigate the workstation's current time source, Windows Time service, domain time configuration, domain controller discovery, and DNS connectivity.

Identify the underlying problem and restore domain-based time synchronization.

---

## Initial Time Synchronization Failure

CLIENT01's current time source was inspected using:

w32tm /query /source

Result:

Local CMOS Clock

Additional status information was retrieved using:

w32tm /query /status

Important results included:

Leap Indicator: 3 (not synchronized)
Stratum: 0 (unspecified)
Last Successful Sync Time: unspecified
Source: Local CMOS Clock

These results confirmed that CLIENT01 was not successfully synchronized with a network or domain time source.

---

## Why Local CMOS Clock Was Significant

A domain-joined workstation should normally obtain time according to the Active Directory domain time hierarchy.

CLIENT01 reporting:

Local CMOS Clock

indicated that it was relying on its own local clock rather than successfully synchronizing with the domain.

This required investigation before attempting to manually configure a time server.

---

## Windows Time Service Investigation

The Windows Time service was inspected using:

Get-Service W32Time |
Select-Object Name, Status, StartType

Result:

Name: W32Time
Status: Running
StartType: Manual

The Windows Time service was therefore already running.

This eliminated a stopped W32Time service as the immediate cause.

---

## Windows Time Configuration Investigation

The time configuration was inspected using:

w32tm /query /configuration

The NTP client configuration showed:

Enabled: 1
Type: NT5DS

The value:

NT5DS

was especially important.

NT5DS configures a domain member to synchronize time according to the Active Directory domain hierarchy.

This demonstrated that CLIENT01 was already configured for domain-based synchronization.

The problem therefore was not simply an incorrect NTP configuration.

---

## Domain Time Discovery Failure

Domain time-source discovery was tested using:

w32tm /monitor

The initial test returned:

GetDcList failed with error code: 0x8007054B

Domain controller discovery was then tested independently:

nltest /dsgetdc:jimeneztech.local

The result included:

Status = 1355
ERROR_NO_SUCH_DOMAIN

This showed that CLIENT01 was unable to locate the jimeneztech.local domain controller at that moment.

Because NT5DS relies on Active Directory domain discovery, this provided a likely explanation for the time synchronization failure.

---

## Dependency Chain Identified

The investigation demonstrated an important dependency:

Windows Time using NT5DS
→ requires Active Directory domain discovery
→ Active Directory discovery relies heavily on DNS
→ DNS must locate the appropriate domain controller records
→ CLIENT01 must be able to communicate with DC01

Therefore, troubleshooting continued with DNS and network connectivity rather than immediately modifying W32Time.

---

## Initial DNS Investigation

CLIENT01 was configured to use:

192.168.56.101

as its DNS server.

An initial lookup was attempted:

nslookup jimeneztech.local

The DNS request timed out.

An Active Directory SRV lookup was also attempted:

nslookup -type=SRV _ldap._tcp.dc._msdcs.jimeneztech.local

This request also timed out.

The failure of the SRV lookup was particularly important because Active Directory uses DNS SRV records to locate domain services such as domain controllers.

---

## DC01 DNS Service Verification

Troubleshooting moved to DC01 to determine whether the DNS server itself was unavailable.

The DNS service was inspected using:

Get-Service DNS |
Select-Object Name, Status, StartType

Result:

Name: DNS
Status: Running
StartType: Automatic

This confirmed that the DNS Server service was operational.

---

## DC01 Network Configuration Verification

DC01's IPv4 configuration was inspected using:

Get-NetIPAddress -AddressFamily IPv4 |
Where-Object {$_.IPAddress -notlike "127.*"} |
Select-Object InterfaceAlias, IPAddress, PrefixLength

Results included:

Ethernet 2
192.168.56.101/24

Ethernet
10.0.2.15/24

The expected Host-Only address:

192.168.56.101

was still configured correctly.

---

## Local DNS Verification on DC01

DNS was tested directly on DC01 using the loopback address:

nslookup jimeneztech.local 127.0.0.1

The lookup succeeded.

The jimeneztech.local domain resolved to addresses associated with DC01, including:

192.168.56.101
10.0.2.15

This established that:

- The DNS service was running
- The DNS zone could resolve locally
- DC01 retained the expected Host-Only IP
- The DNS server itself was not completely unavailable

---

## CLIENT01 to DC01 Connectivity Testing

CLIENT01 tested basic connectivity to DC01:

Test-NetConnection 192.168.56.101

Result:

InterfaceAlias: Ethernet 2
SourceAddress: 192.168.56.150
PingSucceeded: True

This confirmed that CLIENT01 could reach DC01 through the Host-Only network.

TCP port 53 was then tested:

Test-NetConnection 192.168.56.101 -Port 53

Result:

RemotePort: 53
InterfaceAlias: Ethernet 2
SourceAddress: 192.168.56.150
TcpTestSucceeded: True

This demonstrated that TCP connectivity to the DNS service was available.

---

## TCP 53 vs DNS Query Traffic

Test-NetConnection with:

-Port 53

tests TCP connectivity.

DNS commonly uses UDP port 53 for ordinary queries and may use TCP 53 in specific circumstances.

Therefore:

TcpTestSucceeded: True

did not by itself prove that every DNS query would succeed.

The actual DNS queries still needed to be tested directly.

---

## DNS Resolution Recovery

DNS was tested again from CLIENT01 while explicitly specifying DC01:

nslookup jimeneztech.local 192.168.56.101

The lookup now succeeded.

The Active Directory SRV record was then queried:

nslookup -type=SRV _ldap._tcp.dc._msdcs.jimeneztech.local 192.168.56.101

The lookup succeeded and returned a domain controller service record using:

Port: 389

The result identified the jimeneztech.local domain controller and resolved it to:

192.168.56.101

among its registered addresses.

This demonstrated that DNS and Active Directory service discovery were functioning again.

The earlier DNS failure was therefore treated as a transient connectivity/service-discovery condition rather than a permanent DNS configuration error.

No unnecessary DNS configuration changes were made.

---

## Domain Discovery Verification

Domain controller discovery was tested again using:

nltest /dsgetdc:jimeneztech.local

The command successfully located the domain controller.

This confirmed that Active Directory domain discovery had recovered.

---

## Windows Time Remediation

With DNS and domain controller discovery functioning, CLIENT01 was instructed to rediscover its domain time source and synchronize:

w32tm /resync /rediscover

Result:

Sending resync command to local computer
The command completed successfully.

The `/rediscover` option forced Windows Time to rediscover an appropriate time source rather than continuing to rely on the previous unsuccessful discovery state.

---

## Final Time Source Verification

The current time source was checked:

w32tm /query /source

CLIENT01 now reported:

WIN-J9AV7RB7VVJ.jimeneztech.local

instead of:

Local CMOS Clock

The source corresponded to DC01.

---

## Final Synchronization Status

Windows Time status was inspected again:

w32tm /query /status

Final results included:

Leap Indicator: 0 (no warning)
Stratum: 2
ReferenceId: source IP 192.168.56.101
Last Successful Sync Time: 9/12/2026 1:04:19 PM
Source: WIN-J9AV7RB7VVJ.jimeneztech.local

These results confirmed successful domain-based time synchronization.

---

## Final Domain Verification

The following commands were executed again:

w32tm /monitor

nltest /dsgetdc:jimeneztech.local

Both completed successfully.

This confirmed that:

- Domain controller discovery was functioning
- Domain time discovery was functioning
- CLIENT01 could locate the jimeneztech.local domain
- Windows Time could use the domain hierarchy successfully

---

## Root Cause

CLIENT01's Windows Time configuration itself was not incorrectly configured.

W32Time was running and configured with:

Type: NT5DS

However, CLIENT01 temporarily could not successfully discover the jimeneztech.local domain controller.

Evidence included:

ERROR_NO_SUCH_DOMAIN

and failed Active Directory/DNS discovery.

Because NT5DS depends on Active Directory domain discovery, CLIENT01 could not obtain its expected domain time source and remained:

Not synchronized

using:

Local CMOS Clock

After DNS/domain discovery became available again, Windows Time was forced to rediscover the domain time source and synchronization succeeded.

---

## Resolution

The incident was resolved by:

1. Verifying the Windows Time service
2. Confirming NT5DS domain time configuration
3. Identifying failed domain controller discovery
4. Investigating DNS resolution
5. Verifying the DNS service on DC01
6. Verifying DC01's IP configuration
7. Testing DNS locally on DC01
8. Verifying CLIENT01 → DC01 network connectivity
9. Verifying TCP port 53 connectivity
10. Retesting normal DNS resolution
11. Verifying Active Directory SRV record resolution
12. Restoring domain controller discovery
13. Running:

w32tm /resync /rediscover

14. Verifying the new domain time source
15. Confirming a successful synchronization timestamp

---

## Evidence

### 01_CLIENT01_Time_Synchronization_Failure.png

Shows the initial Windows Time condition:

- Source: Local CMOS Clock
- Leap Indicator: 3 (not synchronized)
- Stratum: 0
- Last Successful Sync Time: unspecified

This establishes the initial synchronization failure.

### 02_CLIENT01_Domain_Time_Synchronization_Restored.png

Shows:

- Successful `w32tm /resync /rediscover`
- Domain controller as the time source
- Source IP 192.168.56.101
- Leap Indicator 0
- Stratum 2
- Valid Last Successful Sync Time
- Successful synchronization with the domain hierarchy

This confirms successful remediation.

---

## Commands Used

w32tm /query /source

w32tm /query /status

Get-Service W32Time |
Select-Object Name, Status, StartType

w32tm /query /configuration

w32tm /monitor

w32tm /query /peers

nltest /dsgetdc:jimeneztech.local

ipconfig /all

nslookup jimeneztech.local

nslookup -type=SRV _ldap._tcp.dc._msdcs.jimeneztech.local

Get-Service DNS |
Select-Object Name, Status, StartType

Get-NetIPAddress -AddressFamily IPv4 |
Where-Object {$_.IPAddress -notlike "127.*"} |
Select-Object InterfaceAlias, IPAddress, PrefixLength

nslookup jimeneztech.local 127.0.0.1

Test-NetConnection 192.168.56.101

Test-NetConnection 192.168.56.101 -Port 53

Get-NetIPAddress -InterfaceAlias "Ethernet 2" -AddressFamily IPv4 |
Select-Object InterfaceAlias, IPAddress, PrefixLength

nslookup jimeneztech.local 192.168.56.101

nslookup -type=SRV _ldap._tcp.dc._msdcs.jimeneztech.local 192.168.56.101

w32tm /resync /rediscover

---

## Skills Demonstrated

- Windows Time Troubleshooting
- W32Time
- Active Directory Time Hierarchy
- NT5DS
- Domain Controller Discovery
- Active Directory DNS
- DNS SRV Record Troubleshooting
- DNS Service Verification
- TCP/IP Troubleshooting
- Test-NetConnection
- nslookup
- nltest
- Windows PowerShell
- Dependency-Based Troubleshooting
- Root Cause Analysis
- Domain Workstation Troubleshooting
- Multi-Layer Troubleshooting
- Verification Before Configuration Changes
- Help Desk Troubleshooting
- Technical Documentation

---

## Cheat Sheet Concepts Introduced

### Windows Time Service (W32Time)

Windows service responsible for maintaining date and time synchronization.

In an Active Directory environment, accurate time is particularly important for authentication and domain operations.

---

### NTP

Network Time Protocol.

A protocol used to synchronize clocks between computers over a network.

---

### NT5DS

Windows Time configuration mode used by domain members to synchronize according to the Active Directory domain hierarchy.

CLIENT01 reported:

Type: NT5DS

which confirmed that it was configured for domain-based time synchronization.

---

### Domain Time Hierarchy

Active Directory uses a hierarchy for time synchronization rather than having every workstation independently choose an Internet time server.

Domain members normally obtain time through the domain hierarchy.

---

### Local CMOS Clock

The computer's local hardware/virtualized clock source.

Seeing:

Local CMOS Clock

on a domain workstation can be an important troubleshooting clue when the machine is expected to synchronize with the domain.

---

### Leap Indicator

A Windows Time status value indicating synchronization conditions.

In this ticket:

3

indicated that CLIENT01 was not synchronized.

After remediation:

0

indicated no synchronization warning.

---

### Stratum

A value representing a system's position relative to an authoritative time source.

The initial unsynchronized state reported:

Stratum 0

After synchronization CLIENT01 reported:

Stratum 2

---

### w32tm

Windows command-line utility used to configure, monitor, diagnose, and synchronize Windows Time.

Useful commands include:

w32tm /query /source

w32tm /query /status

w32tm /query /configuration

w32tm /monitor

w32tm /resync /rediscover

---

### /rediscover

Instructs Windows Time to rediscover network/domain time sources before attempting synchronization.

This was useful after domain connectivity and discovery were restored.

---

### nltest

Windows diagnostic utility commonly used to investigate Active Directory domain relationships and domain controller discovery.

Example:

nltest /dsgetdc:jimeneztech.local

---

### ERROR_NO_SUCH_DOMAIN / Error 1355

An error indicating that Windows was unable to locate or identify the requested domain in the current discovery attempt.

In this ticket, it was an important clue that the time problem extended into Active Directory discovery.

---

### DNS SRV Record

A DNS Service Locator record used to identify systems providing particular network services.

Active Directory relies heavily on SRV records.

Example queried in this ticket:

_ldap._tcp.dc._msdcs.jimeneztech.local

This helps clients locate domain controllers providing LDAP services.

---

### LDAP Port 389

TCP/UDP port commonly associated with LDAP.

The Active Directory SRV query in this ticket returned the domain controller service on port:

389

---

### Dependency-Based Troubleshooting

Troubleshooting by identifying the services and technologies on which the failed component depends.

Instead of immediately changing Windows Time, this ticket followed:

Windows Time
→ NT5DS
→ Active Directory discovery
→ DNS
→ Network connectivity

This helped avoid unnecessary configuration changes.

---

## Kerberos and Time Synchronization

Accurate time is especially important in Active Directory because Kerberos authentication uses timestamps as part of its security protections.

Large clock differences between domain systems can contribute to authentication failures.

Therefore, incorrect workstation time in an Active Directory environment should not be treated merely as a cosmetic clock problem.

---

## Key Troubleshooting Principle

Do not immediately modify the component displaying the symptom.

The component reporting the problem may depend on another service that is actually failing.

In this incident:

Windows Time failed
→ W32Time service was running
→ NT5DS configuration was correct
→ domain discovery failed
→ DNS/AD discovery was investigated
→ connectivity was verified
→ domain discovery recovered
→ time source was rediscovered
→ synchronization succeeded

This prevented unnecessary modification of a correctly configured Windows Time service.

---

## Troubleshooting by Elimination

Several possible causes were eliminated systematically.

W32Time service stopped?
No — Running.

Incorrect time mode?
No — NT5DS.

DC01 DNS service stopped?
No — Running / Automatic.

DC01 wrong IP?
No — 192.168.56.101 remained configured.

DNS zone completely broken?
No — local DNS lookup on DC01 succeeded.

CLIENT01 unable to reach DC01?
No — PingSucceeded True.

TCP 53 unavailable?
No — TcpTestSucceeded True.

AD DNS records missing?
No — subsequent SRV query successfully returned the domain controller.

Domain discovery restored?
Yes — nltest succeeded.

Time synchronization restored?
Yes — domain source and successful sync time confirmed.

---

## Outcome

A genuine Windows Time synchronization issue on CLIENT01 was successfully investigated and resolved.

CLIENT01 initially reported:

Source: Local CMOS Clock
Leap Indicator: 3 (not synchronized)
Stratum: 0
Last Successful Sync Time: unspecified

Windows Time was confirmed to be running and correctly configured for:

NT5DS

Further investigation identified a temporary Active Directory domain discovery failure, including:

ERROR_NO_SUCH_DOMAIN

DNS, Active Directory SRV records, DC01's DNS service, IP addressing, and network connectivity were systematically investigated.

After DNS and domain controller discovery were functioning, CLIENT01 was instructed to rediscover its domain time source:

w32tm /resync /rediscover

Final verification showed:

Source: WIN-J9AV7RB7VVJ.jimeneztech.local
Reference source IP: 192.168.56.101
Leap Indicator: 0
Stratum: 2
Last Successful Sync Time: 9/12/2026 1:04:19 PM

CLIENT01 successfully returned to domain-based time synchronization.

The ticket demonstrated practical Windows Time troubleshooting while reinforcing an important help desk principle: investigate the dependencies behind a symptom before changing the component that appears to be failing.