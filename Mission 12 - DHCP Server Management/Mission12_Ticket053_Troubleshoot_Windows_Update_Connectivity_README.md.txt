# Mission 12 - Ticket 053: Troubleshoot Windows Update Connectivity

## Ticket Summary
Investigated Windows Update status on CLIENT01 after determining that the workstation's installed update baseline appeared older than expected.

PowerShell was initially used to inventory recently installed Windows updates using Get-HotFix.

Windows Update initially reported that the device was up to date, but the workstation had last successfully checked for updates on July 22, 2026. A fresh update scan was initiated to verify the current patch status.

The fresh scan failed with a Windows Update connection error stating that the workstation could not connect to the update service.

Network troubleshooting was then performed to determine whether the failure was caused by internal connectivity, DNS resolution, Internet connectivity, or Windows Update itself.

CLIENT01 successfully communicated with DC01 at 192.168.56.101 and successfully resolved public DNS names through DC01. However, direct external connectivity to 8.8.8.8 failed.

Further investigation using ipconfig /all showed that CLIENT01's active Host-Only network adapter had no default gateway.

The VirtualBox NAT adapter was confirmed to exist but was disabled inside Windows.

The NAT adapter was enabled to restore Internet routing. Internet connectivity was successfully restored, but enabling the second adapter introduced a DNS-selection problem in which CLIENT01 attempted to use the NAT-provided DNS server instead of DC01.

The NAT adapter was therefore configured to use 192.168.56.101 as its preferred DNS server while continuing to obtain its IP configuration automatically.

After flushing the DNS cache, CLIENT01 successfully resolved jimeneztech.local through DC01 while retaining Internet connectivity through the NAT adapter.

Windows Update was retried and successfully connected to Microsoft. Current September 2026 security and platform updates were downloaded and installed. A restart was completed, and CLIENT01 returned to normal operation.

This ticket demonstrated Windows Update troubleshooting, network-layer isolation, routing analysis, DNS troubleshooting, multi-adapter configuration, patch management, and post-remediation verification.

## Environment
- Client: CLIENT01
- Host Name: DESKTOP-9NPE7VN
- Domain: jimeneztech.local
- Client Operating System: Windows 11 Enterprise Evaluation
- Domain Controller / DNS Server: DC01
- DC01 IP Address: 192.168.56.101
- CLIENT01 Domain Network IP: 192.168.56.150
- Virtualization Platform: VirtualBox
- Network Interfaces:
  - Ethernet - VirtualBox NAT
  - Ethernet 2 - VirtualBox Host-Only
- Administrative Tools:
  - Windows PowerShell
  - Windows Update
  - Network Connections
  - ipconfig
  - nslookup
  - Test-NetConnection

## Tasks Completed
- Inventoried installed Windows updates using PowerShell
- Reviewed KB identifiers and update installation dates
- Checked Windows Update status
- Identified an outdated Last Checked timestamp
- Initiated a fresh Windows Update scan
- Reproduced a Windows Update connection failure
- Tested CLIENT01-to-DC01 connectivity
- Tested external IP connectivity
- Tested public DNS resolution
- Distinguished DNS functionality from Internet routing
- Reviewed CLIENT01 IP configuration
- Identified a missing default gateway on the active Host-Only adapter
- Reviewed the VirtualBox network configuration
- Identified the NAT adapter as disabled inside Windows
- Enabled the NAT adapter
- Restored Internet connectivity
- Detected a secondary DNS-selection problem
- Identified the NAT-provided DNS server being used instead of DC01
- Configured the NAT adapter to use DC01 for DNS
- Flushed the DNS resolver cache
- Restored Active Directory domain-name resolution
- Preserved Internet connectivity through NAT
- Retried Windows Update
- Successfully downloaded current Windows security updates
- Installed Microsoft Defender and Windows Security updates
- Completed the required system restart
- Verified CLIENT01 returned to normal operation
- Documented root cause and remediation

## Installed Update Baseline
PowerShell was used to review recently installed updates.

Command:

Get-HotFix |
Sort-Object InstalledOn -Descending |
Select-Object -First 10 HotFixID, Description, InstalledOn

The results included:

KB5100998
Description: Update
Installed: 7/22/2026

KB5101650
Description: Security Update
Installed: 7/21/2026

KB5120102
Description: Security Update
Installed: 7/21/2026

KB5054156
Description: Update
Installed: 7/21/2026

KB5095189
Description: Update
Installed: 7/21/2026

The results established an installed-update baseline before further troubleshooting.

## Get-HotFix Limitation
Get-HotFix is useful for identifying many installed Windows updates and hotfixes.

However, it should not automatically be treated as a complete inventory of every update installed through modern Windows servicing mechanisms.

For this reason, the PowerShell results were correlated with the Windows Update interface.

This demonstrated an important troubleshooting principle:

Do not rely on a single diagnostic source when additional evidence is available.

## Initial Windows Update Status
Windows Update initially displayed:

You're up to date

However, the page also showed:

Last checked:
7/22/2026, 4:09 PM

An available update was also displayed:

2026-07 Update
KB5121767
Build 26200.8894

Because the Last Checked timestamp was older than expected, a fresh update scan was performed rather than assuming the workstation was currently patched.

## Windows Update Failure
After initiating a fresh update check, Windows Update reported:

Error encountered

Windows also displayed a message indicating that it could not connect to the update service.

The Last Checked timestamp remained:

7/22/2026, 4:09 PM

This indicated that the new update scan had not successfully communicated with the update service.

Rather than immediately resetting Windows Update components, network connectivity was investigated first.

## Connectivity Troubleshooting
Three tests were used to isolate the problem.

### Internal Network Test

Command:

Test-NetConnection 192.168.56.101

Result:

Source Address:
192.168.56.150

Interface:
Ethernet 2

PingSucceeded:
True

This confirmed that CLIENT01 could communicate with DC01 across the Host-Only network.

### External Connectivity Test

Command:

Test-NetConnection 8.8.8.8

Initial Result:

PingSucceeded:
False

This demonstrated that CLIENT01 could not reach an external IPv4 destination.

Because this test used an IP address directly, the failure could not automatically be attributed to DNS name resolution.

### Public DNS Resolution Test

Command:

nslookup www.microsoft.com

DNS Server:

192.168.56.101

The public Microsoft hostname successfully resolved to public addresses.

This demonstrated that DC01 was capable of providing DNS resolution even though CLIENT01 could not directly reach external destinations.

## Diagnostic Conclusion
The initial tests produced the following pattern:

Internal connectivity:
Working

DNS resolution:
Working

External IP connectivity:
Failing

This strongly indicated an Internet routing problem rather than a basic DNS failure.

The next troubleshooting step was therefore to inspect CLIENT01's network configuration.

## IP Configuration Investigation
Command:

ipconfig /all

Ethernet 2 reported:

IPv4 Address:
192.168.56.150

Subnet Mask:
255.255.255.0

DHCP Enabled:
Yes

DHCP Server:
192.168.56.101

DNS Server:
192.168.56.101

Connection-Specific DNS Suffix:
jimeneztech.local

Default Gateway:
None

The absence of a default gateway explained why CLIENT01 could communicate with devices on its local 192.168.56.0/24 network but could not route ordinary traffic to external networks.

## Default Gateway
A default gateway provides a host with a path to destinations that are outside its directly connected local network.

CLIENT01 could communicate directly with:

192.168.56.101

because DC01 was located on the same subnet.

However, destinations such as:

8.8.8.8

were outside the local subnet.

Without an appropriate route or default gateway, CLIENT01 did not have a path for that traffic.

## VirtualBox Network Investigation
VirtualBox configuration was reviewed before changing the VM's network configuration.

The NAT network adapter was already enabled at the VirtualBox virtual-hardware level.

However, Windows Network Connections revealed:

Ethernet:
Disabled

Ethernet 2:
Enabled

Ethernet represented the VirtualBox NAT interface.

Ethernet 2 represented the Host-Only/domain interface.

This established that the NAT hardware existed but its Windows network interface had been administratively disabled.

## Root Cause
The primary Windows Update connectivity failure was caused by the NAT network adapter being disabled inside CLIENT01.

As a result, CLIENT01 only had its Host-Only network path.

The Host-Only adapter provided:

- Active Directory communication
- DHCP communication
- DNS communication with DC01
- Local subnet connectivity

It did not provide CLIENT01 with a default Internet route.

Therefore, Windows Update could not communicate with Microsoft's update services.

## Primary Remediation
The Ethernet NAT adapter was enabled through:

Control Panel
→ Network and Internet
→ Network Connections
→ Ethernet
→ Enable

Ethernet 2 remained enabled.

This produced a dual-interface configuration:

Ethernet:
VirtualBox NAT / Internet connectivity

Ethernet 2:
Host-Only / domain connectivity

## Internet Connectivity Verification
After enabling the NAT interface, external connectivity was tested again.

Command:

Test-NetConnection 8.8.8.8

Result:

InterfaceAlias:
Ethernet

SourceAddress:
10.0.2.20

PingSucceeded:
True

PingReplyDetails RTT:
5 ms

This confirmed that enabling the NAT adapter successfully restored external IPv4 connectivity.

## Secondary DNS Problem
After Internet connectivity was restored, Active Directory DNS resolution was tested.

Command:

nslookup jimeneztech.local

The lookup attempted to use:

10.0.2.15

as the DNS server.

The DNS requests timed out.

This demonstrated a secondary issue introduced by the dual-interface configuration.

The NAT adapter had obtained DNS configuration that Windows attempted to use instead of the internal Active Directory DNS server.

That external/NAT-side DNS service did not provide authoritative resolution for the private jimeneztech.local Active Directory namespace.

## DNS Remediation
The NAT adapter's IPv4 configuration was modified.

The adapter continued to:

Obtain an IP address automatically

However, its preferred DNS server was configured as:

192.168.56.101

This preserved the NAT adapter's Internet routing while directing DNS queries to the Active Directory DNS server.

The DNS resolver cache was then cleared.

Command:

ipconfig /flushdns

## Domain DNS Verification
Command:

nslookup jimeneztech.local

Post-remediation result:

DNS Server:
192.168.56.101

The jimeneztech.local domain successfully resolved.

Returned addresses included:

192.168.56.101
10.0.2.15

along with an IPv6 address.

This confirmed that CLIENT01 was again querying DC01 for DNS.

## Final Network Architecture
After remediation, CLIENT01 effectively used:

Ethernet / NAT:
Internet connectivity and external routing

Ethernet 2 / Host-Only:
Internal lab and domain connectivity

DC01 / 192.168.56.101:
DNS resolution for CLIENT01

This allowed CLIENT01 to retain both Internet access and Active Directory DNS functionality.

## Windows Update Remediation Verification
Windows Update was retried after network and DNS remediation.

The previous connection error disappeared.

CLIENT01 successfully contacted Microsoft's update infrastructure and retrieved current September 2026 updates.

The Windows Update interface displayed:

Restart required

Updates shown included:

Security Intelligence Update for Microsoft Defender Antivirus
KB2267602
Status: Completed

2026-09 Security Update
KB5124008
Build 26200.9445
Status: Pending restart

2026-09 .NET Framework Security Update
KB5126052
Status: Pending restart

Windows Malicious Software Removal Tool x64
KB890830
Status: Completed

Update for Windows Security platform
KB5007651
Status: Completed

This confirmed that the original Windows Update connectivity problem had been resolved.

## Restart and Final Verification
Because security updates were pending restart, CLIENT01 was restarted to complete the servicing process.

After restart, CLIENT01 returned to normal operation and Windows Update continued functioning normally.

The restart completed the remediation workflow.

## KB Numbers
KB stands for:

Knowledge Base

Microsoft uses KB identifiers to identify specific updates, fixes, support documentation, and servicing packages.

Examples encountered during this ticket included:

KB5124008
KB5126052
KB2267602
KB890830
KB5007651

KB numbers provide administrators with a consistent identifier when researching, deploying, verifying, or troubleshooting updates.

## Patch Management
Patch management is the process of identifying, evaluating, deploying, and verifying software and operating-system updates.

A basic endpoint patching workflow may include:

1. Determine current update status
2. Identify missing updates
3. Verify connectivity to update infrastructure
4. Resolve update errors
5. Install required patches
6. Restart when necessary
7. Verify successful servicing

Patch management is important for both IT operations and cybersecurity because updates frequently address reliability problems and security vulnerabilities.

## Multi-Homed Systems
CLIENT01 operated with two network interfaces during the final configuration.

A system with multiple active network interfaces may be described as multi-homed.

Multi-homed systems require careful configuration because different interfaces can introduce:

- Multiple routes
- Multiple DNS servers
- Different interface metrics
- Different network scopes
- Name-resolution conflicts

This ticket demonstrated why restoring one network function can unintentionally affect another.

## Troubleshooting Methodology
The ticket followed this process:

1. Inventory installed updates
2. Review Windows Update status
3. Identify an outdated Last Checked timestamp
4. Initiate a fresh update scan
5. Reproduce the Windows Update connection error
6. Test connectivity to DC01
7. Test connectivity to an external IP
8. Test public DNS resolution
9. Compare the results
10. Determine that internal connectivity and DNS were functioning
11. Identify external routing as the likely failure point
12. Inspect CLIENT01 IP configuration
13. Identify the missing default gateway
14. Inspect the virtual network configuration
15. Determine that VirtualBox NAT existed
16. Inspect Windows network adapters
17. Identify the NAT interface as disabled
18. Enable the NAT interface
19. Verify external Internet connectivity
20. Test Active Directory DNS resolution
21. Detect a secondary DNS-selection problem
22. Configure the NAT adapter to use DC01 for DNS
23. Flush the DNS cache
24. Verify jimeneztech.local resolution
25. Retry Windows Update
26. Confirm current updates could be retrieved
27. Complete the required restart
28. Verify normal workstation operation
29. Document the root cause and resolution

## Verification
Final verification confirmed:

- CLIENT01 could communicate with DC01
- CLIENT01 could resolve public DNS names through DC01
- Initial external IPv4 connectivity failed
- Ethernet 2 had no default gateway
- The VirtualBox NAT adapter existed
- The NAT interface was disabled inside Windows
- Enabling Ethernet restored Internet connectivity
- 8.8.8.8 became reachable through Ethernet
- NAT provided CLIENT01 with external routing
- The dual-NIC configuration initially caused incorrect DNS selection
- jimeneztech.local initially failed when queried through 10.0.2.15
- The NAT adapter was configured to use 192.168.56.101 for DNS
- jimeneztech.local successfully resolved through 192.168.56.101
- Windows Update successfully contacted Microsoft
- September 2026 security updates were retrieved
- Multiple updates completed successfully
- Security updates requiring a restart reached Pending restart
- CLIENT01 was restarted
- CLIENT01 returned to normal operation after restart

## Evidence
01_CLIENT01_Installed_Updates_Baseline.png
- Shows Get-HotFix output
- Shows recently installed KB identifiers
- Shows update descriptions
- Shows installation dates
- Establishes the initial patch baseline

02_CLIENT01_Windows_Update_Status.png
- Shows Windows Update initially reporting "You're up to date"
- Shows the old Last Checked timestamp
- Shows KB5121767 available
- Establishes the initial Windows Update state

03_CLIENT01_Windows_Update_Connection_Error.png
- Shows "Error encountered"
- Shows Windows could not connect to the update service
- Shows the unsuccessful fresh update check
- Establishes the incident being investigated

04_CLIENT01_Windows_Update_Connectivity_Diagnosis.png
- Shows successful connectivity to 192.168.56.101
- Shows failed connectivity to 8.8.8.8
- Shows successful public DNS resolution
- Demonstrates the distinction between internal connectivity, DNS, and external routing

05_CLIENT01_Missing_Default_Gateway.png
- Shows CLIENT01 ipconfig /all output
- Shows IPv4 address 192.168.56.150
- Shows DHCP server 192.168.56.101
- Shows DNS server 192.168.56.101
- Shows no default gateway on Ethernet 2
- Supports the external-routing diagnosis

06_CLIENT01_Network_Adapter_Remediation.png
- Shows Ethernet enabled
- Shows Ethernet 2 enabled
- Documents restoration of the NAT interface
- Shows the resulting dual-interface configuration

07_CLIENT01_Windows_Update_Post_Remediation.png
- Shows Windows Update successfully communicating after remediation
- Shows current September 2026 updates
- Shows completed Microsoft Defender and Windows Security updates
- Shows security updates pending restart
- Shows "Restart required"
- Demonstrates successful remediation

## Commands Used
Get-HotFix |
Sort-Object InstalledOn -Descending |
Select-Object -First 10 HotFixID, Description, InstalledOn

Test-NetConnection 192.168.56.101

Test-NetConnection 8.8.8.8

nslookup www.microsoft.com

ipconfig /all

nslookup jimeneztech.local

ipconfig /flushdns

## Skills Demonstrated
- Windows 11 Administration
- Windows Update Troubleshooting
- Patch Management
- Security Update Management
- PowerShell
- Get-HotFix
- Test-NetConnection
- ipconfig
- nslookup
- TCP/IP Troubleshooting
- IPv4 Troubleshooting
- Default Gateway Analysis
- Routing Troubleshooting
- DNS Troubleshooting
- Active Directory DNS
- Multi-Homed Workstation Troubleshooting
- VirtualBox Networking
- NAT Networking
- Host-Only Networking
- Network Adapter Management
- DHCP Configuration Analysis
- Root Cause Analysis
- Layered Troubleshooting
- Post-Remediation Verification
- Help Desk Troubleshooting
- Technical Documentation

## Cheat Sheet Concepts Introduced
Windows Update
- Windows servicing system used to obtain operating-system, security, driver, Defender, and other Microsoft updates.

KB
- Knowledge Base identifier used by Microsoft to identify updates, fixes, and support information.

Get-HotFix
- PowerShell cmdlet used to retrieve information about many installed Windows updates and hotfixes.

Patch Management
- Process of identifying, deploying, and verifying software and operating-system updates.

Security Update
- Update intended to address security vulnerabilities or security-related issues.

Cumulative Update
- Update package that incorporates multiple fixes and previously released servicing changes.

Pending Restart
- Indicates that an update has installed or staged changes that require Windows to restart before servicing is fully completed.

Default Gateway
- Router or network device used to reach destinations outside the host's directly connected subnet.

Local Subnet
- Network range that a device can reach directly without routing through a gateway.

NAT
- Network Address Translation.
- In this lab, VirtualBox NAT provides the virtual machine with a route to external networks through the host.

Host-Only Network
- VirtualBox network type that provides communication between selected virtual systems and the host without inherently providing ordinary Internet routing.

Multi-Homed System
- Computer with multiple active network interfaces.

DNS Server
- Server responsible for translating names into IP addresses and providing other DNS records.

Active Directory DNS
- DNS infrastructure used by Active Directory clients to locate domain controllers and domain services.

Test-NetConnection
- PowerShell cmdlet used to test network connectivity and retrieve connection information.

nslookup
- Command-line utility used to query DNS servers and troubleshoot name resolution.

ipconfig /all
- Command used to display detailed TCP/IP configuration including IP addresses, gateways, DHCP servers, and DNS servers.

ipconfig /flushdns
- Clears the Windows DNS resolver cache.

Root Cause
- Underlying condition responsible for producing an incident or symptom.

Key Troubleshooting Principle:

Internal connectivity ≠ Internet connectivity.

A workstation can successfully communicate with local servers while having no route to external networks.

Key DNS Principle:

Successful DNS resolution ≠ successful network routing.

A DNS server may resolve a hostname even when the client cannot reach the resulting destination.

Key Routing Principle:

A host normally requires an appropriate route/default gateway to communicate with destinations outside its local subnet.

Key Active Directory Principle:

Domain-joined workstations should use DNS capable of resolving the organization's Active Directory namespace.

Key Multi-NIC Principle:

Adding or enabling another network adapter can fix routing while simultaneously introducing DNS or interface-selection problems.

Always verify both connectivity and name resolution after changing network interfaces.

Key Troubleshooting Workflow:

Symptom
→ reproduce
→ establish baseline
→ isolate layer
→ identify root cause
→ remediate
→ test for side effects
→ verify original problem
→ document

## Outcome
CLIENT01's Windows Update status was successfully investigated and an actual update connectivity problem was discovered.

The workstation initially appeared to be up to date, but its Last Checked timestamp was from July 22, 2026.

A fresh update scan failed because Windows could not connect to the update service.

Network testing showed that CLIENT01 could communicate with DC01 and resolve public DNS names but could not reach an external IPv4 destination.

Inspection of the network configuration revealed that Ethernet 2 had no default gateway.

Further investigation showed that CLIENT01's VirtualBox NAT adapter existed but was disabled inside Windows.

The NAT adapter was enabled, restoring external connectivity through a 10.0.2.x address.

A secondary DNS problem was then discovered when CLIENT01 attempted to resolve jimeneztech.local using the NAT-provided DNS server at 10.0.2.15.

The NAT adapter was configured to use DC01 at 192.168.56.101 as its preferred DNS server.

After flushing the DNS cache, CLIENT01 successfully resolved jimeneztech.local through DC01 while retaining Internet access through NAT.

Windows Update was retried and successfully contacted Microsoft's update infrastructure.

Current September 2026 security, .NET, Defender, malicious-software-removal, and Windows Security platform updates were retrieved. Updates requiring a reboot reached Pending restart status, and CLIENT01 was restarted to complete servicing.

Following the restart, CLIENT01 operated normally.

The incident was resolved without sacrificing Active Directory DNS functionality, demonstrating a complete troubleshooting workflow involving Windows Update, routing, DNS, dual network adapters, patch management, remediation, side-effect detection, and final verification.