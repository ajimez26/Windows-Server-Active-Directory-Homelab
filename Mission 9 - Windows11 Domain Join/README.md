# Mission 9 — Windows 11 Domain Join

## Objective

Deploy a Windows 11 Enterprise workstation and join it to the `jimeneztech.local` Active Directory domain, establishing centralized authentication and management between the client workstation and domain controller.

---

## Environment

- Hypervisor: Oracle VirtualBox
- Server OS: Windows Server 2025
- Client OS: Windows 11 Enterprise
- Active Directory Domain: `jimeneztech.local`
- Domain Controller / DNS Server: `192.168.56.101`

---

## Tasks Completed

- Deployed and configured a Windows 11 Enterprise virtual machine.
- Configured the workstation's virtual network adapters.
- Established internal network communication between the Windows 11 client and domain controller.
- Configured the client to use the domain controller for DNS.
- Verified network and DNS connectivity before attempting the domain join.
- Joined the Windows 11 workstation to the `jimeneztech.local` domain.
- Verified the workstation's computer object in Active Directory.
- Signed into the workstation using domain credentials.
- Verified communication with centralized domain resources and services.

---

## Network Architecture

The workstation used separate virtual network adapters for Internet and internal domain communication:

```text
Windows 11 CLIENT01
        |
        ├── NAT Adapter
        |       └── Internet Connectivity
        |
        └── Host-Only Adapter
                └── Internal Domain Network
                         |
                         └── DC01
                             192.168.56.101
                             AD DS / DNS
