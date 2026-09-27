# Mission 12 - Ticket 036: Create and Configure IPv4 DHCP Scope

## Category

Windows Server / DHCP Administration

## Priority

High

---

## Request

Create and configure an IPv4 DHCP scope for the Jimenez Technologies workstation network so that domain clients can automatically receive valid network configurations from the Windows DHCP Server.

---

## Analysis

The DHCP Server role was previously installed and authorized on DC01.

The internal domain network uses the following subnet:

* **Network:** `192.168.56.0/24`
* **Subnet Mask:** `255.255.255.0`
* **DC01:** `192.168.56.101`
* **DNS Server:** `192.168.56.101`
* **Domain:** `jimeneztech.local`

A dedicated DHCP address pool was required for workstation clients while keeping infrastructure and reserved addresses separate from dynamically assigned addresses.

During configuration, an existing VirtualBox DHCP service was also identified on the same `192.168.56.0/24` network. Allowing both DHCP services to operate simultaneously could result in clients receiving network configurations from the unintended DHCP server.

---

## Resolution

Created a new IPv4 DHCP scope named:

**JimenezTech Workstations**

The scope was configured with:

```text
Network:             192.168.56.0/24
Start IP Address:    192.168.56.120
End IP Address:      192.168.56.199
Subnet Mask:         255.255.255.0
Prefix Length:       /24
Lease Duration:      8 days
```

An exclusion range was configured:

```text
192.168.56.120 - 192.168.56.129
```

This resulted in an effective dynamic DHCP pool of:

```text
192.168.56.130 - 192.168.56.199
```

The excluded addresses can remain available for future infrastructure devices or other systems requiring manually assigned addresses.

---

## DHCP Scope Options

The following DHCP scope options were configured:

```text
006 DNS Servers       192.168.56.101
015 DNS Domain Name   jimeneztech.local
```

A Router (`003`) option was intentionally not configured because the internal VirtualBox host-only network does not currently use a default gateway.

This allows domain clients receiving DHCP leases to use DC01 for DNS resolution and the `jimeneztech.local` Active Directory DNS namespace.

---

## DHCP Conflict Prevention

Before activating the Windows DHCP scope, the VirtualBox host-only network configuration was reviewed.

VirtualBox had its own DHCP service enabled with:

```text
DHCP Server:         192.168.56.100
Subnet Mask:         255.255.255.0
Lower Address Bound: 192.168.56.101
Upper Address Bound: 192.168.56.254
```

Because this DHCP server operated on the same subnet as the new Windows DHCP Server, it could compete with DC01 when clients requested network configurations.

The VirtualBox DHCP service was therefore disabled while leaving the **VirtualBox Host-Only Ethernet Adapter** itself enabled.

This preserved network connectivity between the virtual machines while ensuring that Windows Server would be the intended DHCP authority for the lab network.

---

## Scope Activation

After verifying the address pool, exclusion range, DNS server, and DNS domain configuration, the **JimenezTech Workstations** scope was activated.

The Windows DHCP Server is now configured to provide dynamic IPv4 addressing to eligible clients on the `192.168.56.0/24` workstation network.

---

## Verification

The DHCP Management console was used to verify the completed scope configuration.

The Address Pool displayed:

```text
Address range for distribution:
192.168.56.120 - 192.168.56.199

IP addresses excluded from distribution:
192.168.56.120 - 192.168.56.129
```

The Scope Options displayed:

```text
006 DNS Servers       192.168.56.101
015 DNS Domain Name   jimeneztech.local
```

The scope was then activated successfully.

---

## Evidence Collected

### Screenshot 1

**01_DHCP_Address_Pool_and_Exclusion.png**

Displays:

* JimenezTech Workstations DHCP scope.
* Configured distribution range.
* Configured exclusion range.
* DHCP Management console.

### Screenshot 2

**02_DHCP_Scope_Options.png**

Displays:

* `006 DNS Servers` configured as `192.168.56.101`.
* `015 DNS Domain Name` configured as `jimeneztech.local`.
* Scope-level DHCP configuration.

---

## Skills Demonstrated

* Windows DHCP Server Administration
* IPv4 DHCP Scope Creation
* IPv4 Subnet Configuration
* DHCP Address Pool Management
* DHCP Exclusion Configuration
* DHCP Lease Configuration
* DHCP Scope Options
* Active Directory DNS Integration
* DHCP Conflict Prevention
* VirtualBox Network Administration
* Windows Server Network Infrastructure
* Infrastructure Troubleshooting
* Technical Documentation

---

## Troubleshooting / Preventive Action

An existing VirtualBox DHCP service was discovered on the same subnet as the newly deployed Windows DHCP Server.

Running two independent DHCP servers on the same broadcast domain could cause clients to receive leases from the wrong server and result in inconsistent IP addresses, DNS servers, or other network settings.

Rather than removing the VirtualBox host-only network, only its DHCP service was disabled.

This allowed the existing virtual network to remain operational while transferring DHCP responsibility to DC01.

---

## Lesson Learned

A DHCP scope involves more than selecting a range of IP addresses.

Address pools should be planned around infrastructure requirements, exclusions should prevent inappropriate dynamic assignments, and DHCP options must provide clients with the correct supporting network services.

It is also important to identify other DHCP servers operating on the subnet before activating a new DHCP scope. Multiple uncoordinated DHCP servers can respond to the same client requests and create unpredictable network configurations.

---

## Reflection

This ticket demonstrated the planning and deployment of an IPv4 DHCP scope in a Windows Server and Active Directory lab environment.

In addition to configuring the address pool, exclusion range, lease duration, DNS server, and DNS domain, the existing virtual network infrastructure was reviewed for potential DHCP conflicts.

Disabling the competing VirtualBox DHCP service before activating the Windows DHCP scope ensured that DC01 could become the intended DHCP provider for the workstation network while preserving the existing host-only network connectivity.
