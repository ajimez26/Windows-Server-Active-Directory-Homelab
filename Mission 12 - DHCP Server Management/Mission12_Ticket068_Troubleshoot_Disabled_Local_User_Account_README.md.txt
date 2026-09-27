# Mission 12 - Ticket 068: Troubleshoot Disabled Local User Account

## Ticket Summary

Investigated and resolved a simulated Windows local-user sign-in problem on CLIENT01.

A temporary local account named:

HelpDeskLab068

was created for the troubleshooting exercise.

During account creation, Windows initially rejected multiple passwords with:

InvalidPasswordException

The rejected passwords contained significant portions of the account or display name. A password unrelated to the username and full name was then accepted.

After establishing a healthy account baseline, the account was deliberately disabled to simulate a user reporting that they could no longer sign in.

Investigation determined:

Enabled = False
Disabled = True
Lockout = False
LocalAccount = True

This distinguished an administratively disabled account from an account lockout.

The account was re-enabled and final verification confirmed:

Enabled = True
Disabled = False
LockedOut = False

The temporary lab account was removed after the exercise.

---

## Environment

- Client: CLIENT01
- Operating System: Windows 11 Enterprise Evaluation
- Domain: jimeneztech.local
- Account Type: Local Windows User
- Lab Account: HelpDeskLab068
- Full Name: Help Desk Lab User
- Administrative Tool: Windows PowerShell
- Account Management Module: Microsoft.PowerShell.LocalAccounts

---

## Ticket Request

A user reports that they can no longer sign into their Windows account.

Investigate the account before resetting the password.

Determine whether the problem is related to:

- Disabled account
- Account lockout
- Password expiration
- Account expiration
- Incorrect account context

Restore access after identifying the actual account-state problem.

---

## Local vs Domain Account Context

Before troubleshooting the account, CLIENT01's account context was considered.

A domain-joined workstation can contain both:

Local accounts:

CLIENT01\username

and domain accounts:

JIMENEZTECH\username

It is important to determine which account database is being investigated before making account changes.

This ticket intentionally used a temporary local account so existing Active Directory accounts were not affected.

---

## Local Account Baseline

Existing local users were inspected using:

Get-LocalUser |
Select-Object Name, Enabled, LastLogon, PasswordRequired |
Format-Table -AutoSize

The workstation/domain context was also verified using Windows system information and the current security identity.

---

## Temporary Account Creation

A temporary local account was created for the troubleshooting exercise.

Initial password attempts resulted in:

InvalidPasswordException

The passwords contained portions of the account name or full name.

A new password unrelated to the username and display name was converted to a SecureString and successfully supplied to New-LocalUser.

The account was created using:

New-LocalUser -Name "HelpDeskLab068" -Password $Password -FullName "Help Desk Lab User" -Description "Temporary account for Ticket 068"

Windows returned:

Name:
HelpDeskLab068

Enabled:
True

Description:
Temporary account for Ticket 068

---

## Password Policy Troubleshooting Finding

The initial account-creation failure demonstrated that password validation can involve more than password length and character variety.

A password may appear complex but still fail applicable Windows password policy requirements.

The troubleshooting process included:

- Confirming the command syntax
- Confirming that $Password contained a SecureString
- Trying a password unrelated to the account identity
- Reattempting account creation
- Confirming successful creation

This demonstrated the importance of investigating the actual exception rather than assuming a PowerShell command failed because of syntax.

---

## Account Baseline

The new account was inspected:

Get-LocalUser -Name "HelpDeskLab068" |
Select-Object Name, Enabled, PasswordRequired, PasswordExpires, LastLogon

The account initially reported:

Enabled:
True

LastLogon:
Blank

The blank LastLogon value was expected because the temporary account had not yet logged into CLIENT01.

---

## SID Verification

The account SID was retrieved:

Get-LocalUser -Name "HelpDeskLab068" |
Select-Object Name, SID

Result:

HelpDeskLab068
S-1-5-21-3052394217-381613384-3437527660-1005

The SID uniquely identified the Windows security principal.

---

## Simulated Incident

The local user was deliberately disabled:

Disable-LocalUser -Name "HelpDeskLab068"

The account was then inspected:

Get-LocalUser -Name "HelpDeskLab068" |
Select-Object Name, Enabled, LastLogon, PasswordExpires, SID

Results included:

Name:
HelpDeskLab068

Enabled:
False

This established the simulated sign-in problem.

---

## Disabled vs Locked-Out Investigation

The account was queried through Win32_UserAccount:

$user = Get-CimInstance Win32_UserAccount -Filter "LocalAccount=True AND Name='HelpDeskLab068'"

$user |
Select-Object Name, Disabled, Lockout, LocalAccount, SID

Results:

Name:
HelpDeskLab068

Disabled:
True

Lockout:
False

LocalAccount:
True

This established that the account was administratively disabled rather than temporarily locked out.

---

## Root Cause

The local Windows account:

HelpDeskLab068

was administratively disabled.

Evidence showed:

Enabled = False
Disabled = True
Lockout = False

The inability to use the account was therefore not diagnosed as:

- Account lockout
- Password expiration
- Incorrect password
- Domain connectivity problem
- Active Directory problem

The root cause was the account's disabled state.

---

## Disabled Account vs Account Lockout

A disabled account and a locked-out account are different conditions.

### Disabled Account

An administrator or management process has prevented the account from being used.

The account generally remains disabled until it is explicitly re-enabled.

### Locked-Out Account

A lockout is typically triggered by authentication failures according to an account lockout policy.

Depending on policy, the account may:

- Automatically unlock after a configured duration
- Require administrative intervention

The appropriate remediation depends on which state is actually present.

---

## Resolution

After confirming that the lab account was intended to remain active, it was re-enabled:

Enable-LocalUser -Name "HelpDeskLab068"

The account was then queried again.

---

## Final Verification

Two Windows interfaces were used to verify the account state:

- Get-LocalUser
- Win32_UserAccount through Get-CimInstance

A final PowerShell object summarized the results:

$LocalUser = Get-LocalUser -Name "HelpDeskLab068"

$CimUser = Get-CimInstance Win32_UserAccount -Filter "LocalAccount=True AND Name='HelpDeskLab068'"

[PSCustomObject]@{
    Account   = $LocalUser.Name
    Enabled   = $LocalUser.Enabled
    Disabled  = $CimUser.Disabled
    LockedOut = $CimUser.Lockout
    SID       = $LocalUser.SID
}

Results:

Account:
HelpDeskLab068

Enabled:
True

Disabled:
False

LockedOut:
False

SID:
S-1-5-21-3052394217-381613384-3437527660-1005

This confirmed successful remediation.

---

## Evidence

### 01_CLIENT01_Local_User_Account_Disabled.png

Shows:

- Account = HelpDeskLab068
- Enabled = False
- Password expiration information
- SID ending in -1005

This establishes the disabled-account condition.

### 02_CLIENT01_Local_User_Account_Restored.png

Shows:

- Account = HelpDeskLab068
- Enabled = True
- Disabled = False
- LockedOut = False
- Same SID ending in -1005

This confirms that the same security principal was successfully restored.

---

## Commands Used

Get-LocalUser

ConvertTo-SecureString

New-LocalUser

Disable-LocalUser

Enable-LocalUser

Get-CimInstance Win32_UserAccount

Select-Object

whoami

Remove-LocalUser

---

## Skills Demonstrated

- Windows local-user administration
- Local account troubleshooting
- Sign-in troubleshooting
- Account-state investigation
- Disabled account identification
- Account lockout differentiation
- Password-policy troubleshooting
- PowerShell LocalAccounts module
- Get-LocalUser
- New-LocalUser
- Disable-LocalUser
- Enable-LocalUser
- Win32_UserAccount
- SID identification
- SecureString handling
- Root cause analysis
- Help desk troubleshooting
- Technical documentation

---

## Cheat Sheet Concepts Introduced

### Local Account

A Windows account whose identity information is stored on the individual computer.

Typical identity format:

COMPUTERNAME\username

This differs from a domain account managed through Active Directory.

---

### Domain Account

An account centrally managed by Active Directory.

Typical identity format:

DOMAIN\username

A domain-joined workstation can contain both local and domain accounts.

---

### SID

SID stands for:

Security Identifier

Windows uses SIDs to uniquely identify security principals such as:

- Users
- Groups
- Computers

The SID is more fundamental to Windows security than the human-readable username.

---

### Get-LocalUser

PowerShell cmdlet used to inspect local Windows accounts.

Example:

Get-LocalUser -Name "username"

Useful properties include:

- Enabled
- LastLogon
- PasswordExpires
- SID

---

### Disable-LocalUser

Disables a local Windows account.

Example:

Disable-LocalUser -Name "username"

A disabled account cannot normally be used for interactive authentication.

---

### Enable-LocalUser

Re-enables a disabled local Windows account.

Example:

Enable-LocalUser -Name "username"

The account should only be re-enabled after confirming that it is supposed to remain active.

---

### Account Lockout

A security state commonly triggered by repeated authentication failures according to configured lockout policy.

A lockout should be distinguished from an administratively disabled account.

---

### Win32_UserAccount

A CIM/WMI class containing Windows account information.

Useful properties include:

- Name
- Disabled
- Lockout
- LocalAccount
- SID

Example:

Get-CimInstance Win32_UserAccount

---

### SecureString

PowerShell uses SecureString objects for some password parameters rather than ordinary plaintext strings.

Example:

ConvertTo-SecureString

The password object can then be supplied to account-management cmdlets such as New-LocalUser.

---

## Key Troubleshooting Principle

Do not automatically reset a user's password simply because they report:

"I can't sign in."

First determine the actual account state.

A useful workflow is:

Identify account type
→ confirm correct username
→ inspect Enabled/Disabled state
→ inspect lockout state
→ inspect expiration information
→ evaluate password-related evidence
→ determine root cause
→ perform the smallest appropriate remediation
→ verify account state

In this ticket:

User cannot access account
→ local account identified
→ Enabled = False
→ Disabled = True
→ Lockout = False
→ disabled account diagnosed
→ account re-enabled
→ Enabled = True
→ Disabled = False
→ LockedOut = False

No unnecessary password reset was performed as part of the disabled-account remediation.

---

## Cleanup

After final verification, the temporary lab account was removed:

Remove-LocalUser -Name "HelpDeskLab068"

This returned CLIENT01 to its previous account configuration.

---

## Outcome

A simulated Windows local-account sign-in issue was successfully diagnosed and resolved.

The account initially showed:

Enabled = False
Disabled = True
Lockout = False

This established that the account was disabled rather than locked out.

After remediation:

Enabled = True
Disabled = False
LockedOut = False

The account SID remained consistent throughout the troubleshooting process.

The ticket demonstrated local-account administration, account-state diagnosis, disabled-versus-lockout differentiation, SID verification, password-policy troubleshooting, targeted remediation, and post-remediation verification.