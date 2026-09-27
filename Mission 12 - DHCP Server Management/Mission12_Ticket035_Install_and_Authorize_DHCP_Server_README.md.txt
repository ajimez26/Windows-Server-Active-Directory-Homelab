# Mission 12 - Ticket 035: Install and Authorize DHCP Server

## Category

Windows Server / DHCP Administration

## Priority

High

---

## Request

Deploy the DHCP Server role on DC01 and authorize the server in Active Directory so that it can provide DHCP services to domain clients.

---

## Analysis

Before installing the DHCP Server role, the existing network configuration on DC01 was reviewed.

During installation validation, Server Manager reported that no static IP addresses were detected on the server.

Further investigation using `ipconfig /all` showed that the domain-facing network adapter, **Ethernet 2**, was receiving its `192.168.56.101` address through DHCP rather than using a manually assigned static address.

Because infrastructure servers such as Domain Controllers, DNS servers, and DHCP servers require predictable network addressing, the adapter was converted to a static IPv4 configuration before continuing with the DHCP deployment.

---

## Resolution

Configured the domain-facing network adapter on DC01 with the following static network configuration:

* **IPv4 Address:** `192.168.56.101`
* **Subnet Mask:** `255.255.255.0`
* **Default Gateway:** None
* **Preferred DNS Server:** `192.168.56.101`
* **DHCP Enabled:** No

After refreshing Server Manager, the static-IP validation warning cleared.

The **DHCP Server** role and associated management tools were then installed through Server Manager.

Following installation, the DHCP Post-Install Configuration Wizard was used to:

* Create the **DHCP Administrators** security group.
* Create the **DHCP Users** security group.
* Authorize the DHCP Server in Active Directory Domain Services using `JIMENEZTECH\Administrator`.

The DHCP Server service was then restarted so the newly created security groups would become effective.

---

## Verification

DHCP post-installation configuration reported:

* **Creating security groups — Done**
* **Authorizing DHCP server — Done**

The DHCP Server service was restarted using PowerShell:

```powershell
Restart-Service DHCPServer
```

Service status was then verified with:

```powershell
Get-Service DHCPServer
```

The resulting status confirmed:

```text
Status    Name         DisplayName
------    ----         -----------
Running   DHCPServer   DHCP Server
```

This confirmed that the DHCP Server role was installed, authorized, and operational.

---

## Evidence Collected

### Screenshot 1

**01_DHCP_PostInstall_Authorization.png**

Displays:

* Successful creation of DHCP security groups.
* Successful Active Directory authorization of the DHCP server.
* Completed DHCP post-install configuration.

### Screenshot 2

**02_DHCP_Service_Running.png**

Displays:

* PowerShell verification of the DHCP Server service.
* `DHCPServer` service in the **Running** state.

---

## Troubleshooting

During the initial DHCP installation attempt, Server Manager generated the following validation warning:

> No static IP addresses were found on this computer.

Investigation revealed that Ethernet 2 was configured as a DHCP client despite currently holding the expected `192.168.56.101` address.

The adapter was changed from automatic addressing to a manually configured static address. `ipconfig /all` was then used to verify:

```text
DHCP Enabled: No
IPv4 Address: 192.168.56.101
Subnet Mask: 255.255.255.0
DNS Servers: 192.168.56.101
```

Server Manager was refreshed and subsequently recognized the static network configuration, allowing the DHCP Server installation to proceed without the previous validation warning.

---

## Skills Demonstrated

* Windows Server Administration
* DHCP Server Deployment
* Active Directory DHCP Authorization
* Static IPv4 Configuration
* Windows Network Adapter Configuration
* DHCP Service Management
* PowerShell Service Administration
* Server Manager
* Infrastructure Troubleshooting
* Network Configuration Validation

---

## Commands Used

```cmd
ipconfig /all
```

```powershell
Restart-Service DHCPServer
Get-Service DHCPServer
```

---

## Lesson Learned

A server can display a consistent IP address while still obtaining that address dynamically from another DHCP server. Verifying whether an address is actually static is therefore important before deploying infrastructure services.

DHCP servers should use predictable static addressing so clients and other network services can reliably communicate with them.

Active Directory authorization also provides protection against unauthorized Windows DHCP servers operating within an AD environment.

---

## Reflection

This ticket demonstrated the complete deployment process for a Windows DHCP Server, including prerequisite validation, role installation, Active Directory authorization, service management, and operational verification.

The initial static-IP warning also provided an opportunity to troubleshoot the server's existing network configuration rather than bypassing the warning. Correcting the underlying configuration before continuing resulted in a cleaner and more reliable DHCP deployment.
