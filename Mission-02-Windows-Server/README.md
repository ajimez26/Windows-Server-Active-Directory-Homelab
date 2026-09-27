# Mission 2 — Windows Server Foundation

## Objective

Deploy and configure the Windows Server system that would become the foundation of the Active Directory homelab environment.

## Environment

- Windows Server 2025 Standard Evaluation
- Oracle VirtualBox
- Virtualized network adapters
- NAT networking
- Host-Only networking

## Tasks Completed

- Installed Windows Server 2025 in Oracle VirtualBox.
- Configured virtual hardware resources for the server.
- Completed the initial Windows Server setup and administrative configuration.
- Used Server Manager to review and manage the server environment.
- Configured network settings required for the internal lab environment.
- Configured a static IPv4 address for the server's internal network interface.
- Configured the server to use its internal IP address for DNS after DNS services were established.
- Prepared the server for the installation of Active Directory Domain Services and other infrastructure roles.
- Verified network configuration and connectivity before progressing to domain services.

## Network Configuration

The server was configured with separate virtual network interfaces for different purposes.

```text
NAT Adapter
    ↓
Internet Connectivity

Host-Only Adapter
    ↓
Internal Lab Communication
