# Mission 12 - Ticket 064: Troubleshoot Stored Network Credentials

## Ticket Summary

Investigated and resolved a simulated stale network credential issue on CLIENT01.

A deliberately incorrect Windows credential was stored for a simulated network resource named:

LAB-FILESERVER

The stored credential referenced:

JIMENEZTECH\olduser

while the currently logged-in Windows identity was:

JIMENEZTECH\Administrator

Windows Credential Manager information was investigated using the built-in cmdkey utility.

The stale resource-specific credential was identified and removed. Final verification confirmed that the LAB-FILESERVER credential was no longer stored.

---

## Environment

- Client: CLIENT01
- Operating System: Windows 11 Enterprise Evaluation
- Domain: jimeneztech.local
- Current User: JIMENEZTECH\Administrator
- Simulated Network Target: LAB-FILESERVER
- Simulated Stale User: JIMENEZTECH\olduser
- Credential Type: Domain Password
- Administrative Tools:
  - PowerShell
  - cmdkey

---

## Ticket Request

A user reports that Windows continues attempting to access a network resource using incorrect credentials.

Investigate:

- Current Windows identity
- Stored Windows credentials
- Resource-specific credentials
- Whether an outdated username is associated with the network target

Remove the stale credential and verify that Windows no longer stores the incorrect entry.

---

## Initial Credential Baseline

Stored credentials were inspected using:

cmdkey /list

The lab target was specifically searched:

cmdkey /list |
Select-String "LAB-FILESERVER"

No LAB-FILESERVER entry was initially present.

This established a clean baseline before the simulated incident.

---

## Simulated Incident

An incorrect resource-specific credential was deliberately stored:

cmdkey /add:LAB-FILESERVER /user:JIMENEZTECH\olduser /pass:WrongPassword123!

Windows returned:

CMDKEY: Credential added successfully.

The credential was then identified using:

cmdkey /list |
Select-String -Pattern "LAB-FILESERVER","olduser" -Context 1,1

Results showed:

Target:
Domain:target=LAB-FILESERVER

Type:
Domain Password

User:
JIMENEZTECH\olduser

The stored password itself was not displayed by cmdkey /list.

---

## Identity Investigation

The currently logged-in Windows identity was checked using:

whoami

Result:

jimeneztech\administrator

This differed from the username associated with the stored LAB-FILESERVER credential:

JIMENEZTECH\olduser

This demonstrated the simulated credential conflict.

---

## Root Cause

Windows contained a stored resource-specific credential for:

LAB-FILESERVER

associated with:

JIMENEZTECH\olduser

The current Windows identity was:

JIMENEZTECH\Administrator

In a real environment, an outdated resource-specific credential can cause Windows or an application to attempt authentication using credentials different from those the user expects.

The simulated root cause was therefore a stale stored Windows credential.

---

## Authentication vs Authorization

This ticket focused primarily on authentication.

Authentication answers:

"Who are you, and can you prove your identity?"

Authorization answers:

"Now that your identity is known, what resources are you permitted to access?"

A user may have correct permissions to a resource but still experience access problems if Windows attempts authentication using incorrect stored credentials.

This differs from a share-permission problem, where authentication may succeed but authorization is denied.

---

## Resolution

The stale credential was removed using:

cmdkey /delete:LAB-FILESERVER

Windows returned:

CMDKEY: Credential deleted successfully.

The credential list was then searched again:

cmdkey /list |
Select-String "LAB-FILESERVER"

No matching entry was returned.

---

## Final Verification

The final credential state and current identity were checked using:

$StoredCredential = cmdkey /list |
Select-String "LAB-FILESERVER"

[PSCustomObject]@{
    CurrentIdentity         = whoami
    StaleCredentialPresent = [bool]$StoredCredential
}

Results:

CurrentIdentity:
jimeneztech\administrator

StaleCredentialPresent:
False

This confirmed that the simulated stale network credential had been removed successfully.

---

## Evidence

### 01_CLIENT01_Stale_Network_Credential_Identified.png

Shows:

- LAB-FILESERVER credential target
- Credential type: Domain Password
- Stored user: JIMENEZTECH\olduser

This establishes the simulated stale credential condition.

### 02_CLIENT01_Stale_Network_Credential_Removed.png

Shows:

- cmdkey reporting successful credential deletion
- Current identity as jimeneztech\administrator
- StaleCredentialPresent = False

This confirms successful remediation.

---

## Commands Used

cmdkey /list

cmdkey /list |
Select-String "LAB-FILESERVER"

cmdkey /add:LAB-FILESERVER /user:JIMENEZTECH\olduser /pass:WrongPassword123!

cmdkey /list |
Select-String -Pattern "LAB-FILESERVER","olduser" -Context 1,1

whoami

cmdkey /delete:LAB-FILESERVER

$StoredCredential = cmdkey /list |
Select-String "LAB-FILESERVER"

[PSCustomObject]@{
    CurrentIdentity         = whoami
    StaleCredentialPresent = [bool]$StoredCredential
}

---

## Skills Demonstrated

- Windows Credential Manager troubleshooting
- Stored network credentials
- Windows authentication troubleshooting
- Domain credentials
- cmdkey
- PowerShell
- Current-user identity verification
- Resource-specific credentials
- Credential persistence
- Authentication vs authorization
- Credential cleanup
- Root cause analysis
- Help desk troubleshooting
- Technical documentation

---

## Cheat Sheet Concepts Introduced

### Windows Credential Manager

Windows Credential Manager provides storage for credentials associated with applications, websites, and network resources.

Stored credentials can sometimes cause authentication problems when they become outdated or reference the wrong account.

---

### cmdkey

cmdkey is a built-in Windows command-line utility for managing stored credentials.

List credentials:

cmdkey /list

Add a credential:

cmdkey /add:<target> /user:<username> /pass:<password>

Delete a credential:

cmdkey /delete:<target>

---

### Stored Credential

A credential saved by Windows for later authentication to a particular resource or service.

It may contain information associated with:

- A target
- Username
- Credential type
- Authentication resource

A stale credential is one that is no longer appropriate for the resource.

---

### Resource-Specific Credential

Windows can store credentials associated with a particular target rather than relying solely on the user's current Windows logon identity.

This means:

Current Windows User

and

Stored Credential for Resource

can represent different accounts.

---

### Credential Persistence

Stored credentials can remain available across sessions until they are updated or removed.

This persistence is useful when credentials are correct but can create confusing authentication problems when credentials become outdated.

---

### Authentication

Authentication establishes identity.

Example question:

Who is attempting to access this resource?

Common authentication factors include:

- Username
- Password
- Security token
- Certificate
- Other credentials

---

### Authorization

Authorization determines what an authenticated identity is permitted to do.

Examples:

- Read a folder
- Modify a file
- Access a network share
- Perform an administrative action

Authentication and authorization should be investigated separately.

---

### whoami

Displays the security identity of the current Windows session.

Example:

whoami

Result in this ticket:

jimeneztech\administrator

This can be compared against stored credentials when troubleshooting identity-related problems.

---

## Key Troubleshooting Principle

Do not assume Windows is authenticating to a resource with the account the user expects.

When investigating repeated credential or network authentication problems:

Identify current Windows user
→ inspect stored credentials
→ identify resource-specific entries
→ compare stored username with expected identity
→ determine whether the credential is still required
→ remove or update confirmed stale credentials
→ retry authentication
→ verify the stale entry is gone

This helps distinguish credential problems from:

- DNS problems
- Network connectivity failures
- Firewall problems
- SMB service failures
- Share permissions
- NTFS permissions

---

## Security Note

cmdkey /list does not display stored credential passwords in plaintext.

Troubleshooting should avoid exposing or documenting real user passwords.

The password used during this ticket was a deliberately simulated lab value rather than a real credential.

---

## Important Scope Note

LAB-FILESERVER was a simulated credential target.

No actual SMB authentication attempt against LAB-FILESERVER occurred.

Therefore, this ticket demonstrates:

Stored credential identification and remediation

rather than claiming:

A real network server rejected the credential.

---

## Outcome

A simulated stale Windows network credential was successfully investigated and removed from CLIENT01.

The stored credential referenced:

Target:
LAB-FILESERVER

User:
JIMENEZTECH\olduser

while the active Windows identity was:

JIMENEZTECH\Administrator

The stale credential was removed using cmdkey.

Final verification showed:

CurrentIdentity = jimeneztech\administrator

StaleCredentialPresent = False

The ticket demonstrated Windows Credential Manager troubleshooting, stored credential analysis, identity verification, authentication-versus-authorization reasoning, credential removal, and post-remediation verification.