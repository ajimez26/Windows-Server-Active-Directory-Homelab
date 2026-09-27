# Mission 12 - Ticket 060: Troubleshoot Mapped Network Drive

## Ticket Summary

Investigated and resolved a network share access issue affecting CLIENT01.

CLIENT01 could successfully resolve the domain controller through DNS and establish TCP connectivity to the SMB service on port 445, but attempts to access the DepartmentShares SMB share returned "Access is denied."

Server-side investigation determined that NTFS permissions allowed administrative access, but the SMB share permissions only granted access to two individual domain users.

Domain Admins was granted Full share-level access.

CLIENT01 was then able to access and enumerate the DepartmentShares directory successfully.

After restoring access, the share was mapped persistently to drive S: and verified using PowerShell.

---

## Environment

- Domain: jimeneztech.local
- Domain Controller / File Server: WIN-J9AV7RB7VVJ.jimeneztech.local
- Domain Controller Internal IP: 192.168.56.101
- Client: CLIENT01
- CLIENT01 Internal IP: 192.168.56.150
- Share Name: DepartmentShares
- Server Path: C:\DepartmentShares
- UNC Path:
  \\WIN-J9AV7RB7VVJ.jimeneztech.local\DepartmentShares
- Mapped Drive: S:
- Protocol: SMB
- SMB Port: TCP 445
- Administrative Tools: PowerShell / SMB cmdlets

---

## Ticket Request

A user reports that a network share is unavailable from CLIENT01.

Troubleshoot the issue by determining whether the failure is related to:

- DNS resolution
- Network connectivity
- SMB connectivity
- Existing SMB mappings
- Authentication
- Share permissions
- NTFS permissions

Restore access and configure a persistent mapped network drive.

---

## Initial Connectivity Investigation

CLIENT01 first tested DNS resolution for the file server.

Command:

nslookup WIN-J9AV7RB7VVJ.jimeneztech.local

DNS successfully resolved the server.

The internal IPv4 address included:

192.168.56.101

This demonstrated that CLIENT01 could locate the domain controller/file server through DNS.

---

## SMB Connectivity Test

SMB connectivity was tested using:

Test-NetConnection WIN-J9AV7RB7VVJ.jimeneztech.local -Port 445

Result:

TcpTestSucceeded : True

CLIENT01 used:

SourceAddress:
192.168.56.150

RemoteAddress:
192.168.56.101

RemotePort:
445

This confirmed that basic network connectivity to the SMB service was functioning.

The problem therefore existed above the basic network-transport layer.

---

## Share Access Failure

The DepartmentShares UNC path was tested:

Test-Path "\\WIN-J9AV7RB7VVJ.jimeneztech.local\DepartmentShares"

Result:

Access is denied.

Test-Path returned:

False

At this point:

DNS resolution              = Working
TCP 445 connectivity        = Working
SMB server reachable        = Working
Share access                = Failed

This narrowed the investigation toward authentication and authorization.

---

## Authentication Investigation

The current CLIENT01 security context was identified using:

whoami

Result:

jimeneztech\administrator

Group membership was inspected using:

whoami /groups

The account's security token included administrative domain memberships, including Domain Admins.

Existing SMB mappings were then inspected:

Get-SmbMapping

No existing SMB mappings were present.

The legacy network connection utility was also checked:

net use

Result:

There are no entries in the list.

This helped rule out an existing mapped drive or obvious stale SMB mapping as the source of the failure.

---

## Server-Side Share Permission Investigation

On the file server, SMB share permissions were inspected:

Get-SmbShareAccess -Name "DepartmentShares"

The share allowed:

JIMENEZTECH\drodriguez     Full
JIMENEZTECH\slopez         Full

No share-level permission existed for Domain Admins.

This became a key finding in the investigation.

---

## NTFS Permission Investigation

NTFS permissions were inspected using:

(Get-Acl "C:\DepartmentShares").Access |
Format-Table IdentityReference, FileSystemRights, AccessControlType, IsInherited -AutoSize

The ACL included:

BUILTIN\Administrators     FullControl

This demonstrated that administrative NTFS access existed on the underlying folder.

However, SMB access requires the requesting account to pass both the applicable share-level and NTFS authorization checks.

The restrictive share-level configuration prevented remote access even though the account had sufficient NTFS rights.

---

## Root Cause

The DepartmentShares SMB share did not grant share-level access to Domain Admins.

The domain Administrator account had sufficient NTFS permissions through its administrative group memberships, but the SMB share permissions were more restrictive.

As a result, CLIENT01 could:

- Resolve the server
- Reach the server
- Connect to TCP port 445

but could not access the share.

This produced:

Access is denied.

---

## Resolution

Domain Admins was granted Full access at the SMB share layer.

Command:

Grant-SmbShareAccess `
    -Name "DepartmentShares" `
    -AccountName "JIMENEZTECH\Domain Admins" `
    -AccessRight Full `
    -Force

The resulting permissions were verified using:

Get-SmbShareAccess -Name "DepartmentShares"

This preserved the existing user permissions while adding administrative access for troubleshooting and management.

---

## Access Verification

CLIENT01 retested the UNC path:

Test-Path "\\WIN-J9AV7RB7VVJ.jimeneztech.local\DepartmentShares"

Result:

True

The directory contents were then enumerated:

Get-ChildItem "\\WIN-J9AV7RB7VVJ.jimeneztech.local\DepartmentShares"

CLIENT01 successfully displayed departmental directories including:

- Finance
- HR
- IT
- Marketing
- Public
- Sales

This confirmed that SMB share access had been restored.

---

## Mapped Network Drive Configuration

After restoring access, the DepartmentShares share was mapped to:

S:

The drive letter was first checked to ensure that it was available.

Command:

Get-PSDrive -Name S -ErrorAction SilentlyContinue

The persistent SMB mapping was then created:

New-SmbMapping `
    -LocalPath "S:" `
    -RemotePath "\\WIN-J9AV7RB7VVJ.jimeneztech.local\DepartmentShares" `
    -Persistent $true

---

## Mapped Drive Verification

The mapping was inspected using:

Get-SmbMapping

Result:

Status      : OK
Local Path  : S:
Remote Path : \\WIN-J9AV7RB7VVJ.jimeneztech.local\DepartmentShares

The mapped drive was then tested:

Test-Path "S:\"

Result:

True

This confirmed that the workstation could successfully access the DepartmentShares resource through the mapped drive.

---

## Evidence

### 01_CLIENT01_DepartmentShare_Access_Denied.png

Shows:

- Successful DNS resolution
- Successful TCP 445 connectivity
- Access denied when testing the DepartmentShares UNC path

This captures the original failure state.

### 02_CLIENT01_DepartmentShare_Access_Restored.png

Shows:

- Test-Path returning True
- Successful enumeration of the DepartmentShares directory
- Finance, HR, IT, Marketing, Public, and Sales folders

This demonstrates successful remediation.

### 03_CLIENT01_DepartmentShare_Mapped_Drive_Verified.png

Shows:

- Get-SmbMapping
- S: mapped to DepartmentShares
- Mapping status OK
- Test-Path S:\ returning True

This verifies the final mapped-drive configuration.

---

## Commands Used

Get-SmbShare |
Select-Object Name, Path, Description

nslookup WIN-J9AV7RB7VVJ.jimeneztech.local

Test-NetConnection WIN-J9AV7RB7VVJ.jimeneztech.local -Port 445

Test-Path "\\WIN-J9AV7RB7VVJ.jimeneztech.local\DepartmentShares"

whoami

whoami /groups

Get-SmbMapping

net use

Get-SmbShareAccess -Name "DepartmentShares"

(Get-Acl "C:\DepartmentShares").Access |
Format-Table IdentityReference, FileSystemRights, AccessControlType, IsInherited -AutoSize

Grant-SmbShareAccess `
    -Name "DepartmentShares" `
    -AccountName "JIMENEZTECH\Domain Admins" `
    -AccessRight Full `
    -Force

Get-ChildItem "\\WIN-J9AV7RB7VVJ.jimeneztech.local\DepartmentShares"

Get-PSDrive -Name S -ErrorAction SilentlyContinue

New-SmbMapping `
    -LocalPath "S:" `
    -RemotePath "\\WIN-J9AV7RB7VVJ.jimeneztech.local\DepartmentShares" `
    -Persistent $true

Get-SmbMapping

Test-Path "S:\"

---

## Skills Demonstrated

- SMB troubleshooting
- Network share troubleshooting
- Mapped network drives
- UNC paths
- DNS troubleshooting
- TCP connectivity testing
- TCP port 445
- PowerShell
- SMB share permissions
- NTFS permissions
- Windows authorization
- Active Directory group membership
- Security token inspection
- Persistent drive mappings
- Root cause analysis
- Layered troubleshooting
- Help desk troubleshooting
- Technical documentation

---

## Cheat Sheet Concepts Introduced

### SMB

Server Message Block is a Windows networking protocol commonly used for:

- File sharing
- Folder sharing
- Printer sharing
- Network resource access

Modern Windows SMB file sharing commonly uses:

TCP 445

---

### UNC Path

UNC stands for Universal Naming Convention.

It identifies a network resource using a format such as:

\\ServerName\ShareName

Example:

\\WIN-J9AV7RB7VVJ.jimeneztech.local\DepartmentShares

UNC paths allow network resources to be accessed without requiring a drive letter.

---

### Mapped Network Drive

A mapped network drive assigns a drive letter to a remote network resource.

Example:

S:

can represent:

\\WIN-J9AV7RB7VVJ.jimeneztech.local\DepartmentShares

This allows users to access the network resource similarly to a local drive.

---

### TCP 445

TCP port 445 is used by modern SMB communications.

Testing it with:

Test-NetConnection SERVER -Port 445

helps determine whether the workstation can establish TCP connectivity to the SMB service.

A successful TCP 445 test does NOT prove that the user has permission to access a particular share.

---

### Share Permissions

Permissions configured on an SMB share.

They determine which users and groups are authorized through the SMB share layer.

PowerShell command:

Get-SmbShareAccess

---

### NTFS Permissions

Filesystem permissions stored on files and folders on an NTFS volume.

They can control rights such as:

- Read
- Write
- Modify
- Full Control

PowerShell ACL information can be inspected using:

Get-Acl

---

### Share Permissions vs NTFS Permissions

Remote SMB access can be affected by both:

Share Permissions
+
NTFS Permissions

A user must have sufficient effective authorization through the relevant permission layers.

A permissive NTFS ACL does not override a restrictive SMB share permission.

Likewise, permissive share access does not override restrictive NTFS permissions.

---

### Get-SmbMapping

Displays SMB mappings associated with the current user/session context.

Useful for troubleshooting:

- Mapped drives
- Remote share paths
- Mapping status

---

### net use

Traditional Windows command used to inspect, create, and remove network connections.

Running:

net use

can help identify existing network connections during troubleshooting.

---

### whoami

Displays the identity associated with the current security context.

Example:

whoami

Additional group membership information can be viewed with:

whoami /groups

This helps determine which security groups are represented in the current user's access token.

---

## Key Troubleshooting Principle

An "Access is denied" error does not automatically mean the network is down.

Troubleshoot network shares layer by layer:

User cannot access share
→ Resolve server hostname
→ Test network connectivity
→ Test TCP 445
→ Test UNC path
→ Identify current user
→ Check existing SMB mappings/connections
→ Inspect share permissions
→ Inspect NTFS permissions
→ Identify restrictive authorization layer
→ Apply targeted remediation
→ Retest UNC path
→ Verify mapped drive

This prevents unnecessary changes to DNS, networking, firewalls, or permissions that are already functioning correctly.

---

## Outcome

CLIENT01 was unable to access the DepartmentShares network share despite successful DNS resolution and TCP 445 connectivity.

Investigation determined that the domain Administrator account had sufficient NTFS administrative permissions but was not authorized by the DepartmentShares SMB share permissions.

Domain Admins was granted Full share-level access.

After remediation:

Test-Path UNC path = True

Directory enumeration = Successful

The DepartmentShares resource was then mapped persistently to:

S:

Get-SmbMapping reported:

Status = OK

and:

Test-Path S:\ = True

The incident demonstrated end-to-end troubleshooting of DNS, SMB connectivity, authentication context, share permissions, NTFS permissions, UNC paths, and persistent mapped network drives.