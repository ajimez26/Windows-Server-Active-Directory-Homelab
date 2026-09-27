# Mission 12 - Ticket 065: Troubleshoot Windows Device and Driver Issue

## Ticket Summary

Investigated and resolved a simulated Windows Plug and Play device issue on CLIENT01.

Windows device inventory was first inspected for existing device problems. The High Definition Audio Device was selected as a safe virtual device for the troubleshooting exercise.

The audio device initially reported:

Status: OK
Problem Code: 0

The device was deliberately disabled to simulate a user reporting that audio hardware was unavailable.

After the device was disabled, Windows reported:

Problem Code: 22

The issue was diagnosed as an administratively disabled device rather than a missing, unsigned, or corrupted driver.

The device was re-enabled and final verification confirmed:

Status: OK
Problem Code: 0
Driver Signed: True

---

## Environment

- Client: CLIENT01
- Operating System: Windows 11 Enterprise Evaluation
- Domain: jimeneztech.local
- Virtualization Platform: VirtualBox
- Device: High Definition Audio Device
- Device Class: MEDIA
- Driver Provider: Microsoft
- Driver Version: 10.0.26100.9278
- Driver Signed: True
- Administrative Tool: Windows PowerShell

---

## Ticket Request

A user reports that an audio device is unavailable on CLIENT01.

Investigate:

- Windows Plug and Play device inventory
- Device status
- Device class
- Device Instance ID
- Windows device problem code
- Installed driver information
- Driver signature status

Determine whether the problem is caused by the device configuration or its driver and restore the device to a healthy state.

---

## Initial Device Investigation

Windows Plug and Play devices reporting a status other than OK were inspected:

Get-PnpDevice |
Where-Object Status -ne "OK" |
Select-Object Status, Class, FriendlyName, InstanceId |
Format-Table -AutoSize

Two devices reported Unknown:

- Microsoft Device Association Root Enumerator
- Generic Monitor

Because CLIENT01 is a VirtualBox virtual machine and these devices were not causing an observed user-facing failure, they were not automatically treated as confirmed hardware problems.

A known healthy device was selected for the controlled troubleshooting exercise instead.

---

## Device Inventory

Healthy Windows devices were grouped by device class:

Get-PnpDevice |
Where-Object Status -eq "OK" |
Group-Object Class |
Sort-Object Count -Descending |
Select-Object Count, Name |
Format-Table -AutoSize

Device classes included:

- System
- Net
- Volume
- SoftwareDevice
- USB
- Mouse
- PrintQueue
- Processor
- AudioEndpoint
- Keyboard
- DiskDrive
- Monitor
- MEDIA
- Display
- Battery

This demonstrated how Windows Plug and Play devices can be inventoried and organized by device class.

---

## Audio Device Selection

Safe audio-related devices were inspected:

Get-PnpDevice |
Where-Object {
    $_.Status -eq "OK" -and
    $_.Class -in @("AudioEndpoint","Media","SoftwareDevice")
} |
Select-Object Status, Class, FriendlyName, InstanceId |
Format-Table -AutoSize

The following device was selected:

High Definition Audio Device

Class:

MEDIA

Status:

OK

---

## Device Object

The device was stored as a PowerShell object:

$AudioDevice = Get-PnpDevice |
Where-Object {
    $_.Class -eq "MEDIA" -and
    $_.FriendlyName -eq "High Definition Audio Device"
}

The object provided access to information including:

- Status
- Class
- FriendlyName
- InstanceId

The Instance ID allowed subsequent commands to target the exact device rather than another audio or system device.

---

## Healthy Problem Code Baseline

The Windows Plug and Play problem code was inspected:

Get-PnpDeviceProperty `
    -InstanceId $AudioDevice.InstanceId `
    -KeyName "DEVPKEY_Device_ProblemCode"

Initial result:

Problem Code:
0

Problem Code 0 indicated that Windows did not currently report a configuration problem for the device.

This established the healthy baseline.

---

## Driver Investigation

Driver information was inspected using:

Get-CimInstance Win32_PnPSignedDriver |
Where-Object DeviceID -eq $AudioDevice.InstanceId |
Select-Object DeviceName, DriverProviderName, DriverVersion,
    DriverDate, IsSigned, InfName

This allowed the device configuration and installed driver information to be investigated separately.

---

## Simulated Incident

The High Definition Audio Device was deliberately disabled:

Disable-PnpDevice `
    -InstanceId $AudioDevice.InstanceId `
    -Confirm:$false

The device was then queried:

Get-PnpDevice -InstanceId $AudioDevice.InstanceId |
Select-Object Status, Class, FriendlyName, InstanceId

The Plug and Play problem code was checked again:

Get-PnpDeviceProperty `
    -InstanceId $AudioDevice.InstanceId `
    -KeyName "DEVPKEY_Device_ProblemCode"

Result:

Problem Code:
22

---

## Problem Code 22

Windows Device Manager Problem Code 22 indicates that the device has been disabled.

This provided a specific diagnostic explanation for the simulated audio failure.

Rather than immediately reinstalling or replacing the driver, the problem code indicated that the device configuration should be investigated first.

---

## Root Cause

The High Definition Audio Device had been administratively disabled.

Windows identified the condition using:

DEVPKEY_Device_ProblemCode = 22

The issue was therefore not diagnosed as:

- Missing hardware
- Missing driver
- Unsigned driver
- Driver corruption
- Physical hardware failure

The root cause was the disabled Plug and Play device state.

---

## Resolution

The audio device was re-enabled:

Enable-PnpDevice `
    -InstanceId $AudioDevice.InstanceId `
    -Confirm:$false

The device status was then checked:

Get-PnpDevice -InstanceId $AudioDevice.InstanceId |
Select-Object Status, Class, FriendlyName

Result:

Status:
OK

Class:
MEDIA

FriendlyName:
High Definition Audio Device

---

## Problem Code Verification

The problem code was checked again:

Get-PnpDeviceProperty `
    -InstanceId $AudioDevice.InstanceId `
    -KeyName "DEVPKEY_Device_ProblemCode"

Result:

Problem Code:
0

The device therefore transitioned from:

Healthy
Problem Code 0

to:

Disabled
Problem Code 22

and finally back to:

Healthy
Problem Code 0

---

## Driver Verification

The installed driver was inspected after remediation:

Get-CimInstance Win32_PnPSignedDriver |
Where-Object DeviceID -eq $AudioDevice.InstanceId |
Select-Object DeviceName, DriverProviderName, DriverVersion, IsSigned

Results:

Device Name:
High Definition Audio Device

Driver Provider:
Microsoft

Driver Version:
10.0.26100.9278

IsSigned:
True

This confirmed that a signed driver remained installed after the device was restored.

---

## Evidence

### 01_CLIENT01_Audio_Device_Disabled.png

Shows:

DEVPKEY_Device_ProblemCode = 22

This establishes the simulated disabled-device condition.

### 02_CLIENT01_Audio_Device_Restored.png

Shows:

- Problem Code 22 before remediation
- Enable-PnpDevice
- Device Status = OK
- Problem Code = 0
- Driver Provider = Microsoft
- Driver Version = 10.0.26100.9278
- IsSigned = True

This confirms successful device recovery and driver verification.

---

## Commands Used

Get-PnpDevice

Get-PnpDeviceProperty

Get-CimInstance Win32_PnPSignedDriver

Group-Object

Sort-Object

Select-Object

Disable-PnpDevice

Enable-PnpDevice

$AudioDevice = Get-PnpDevice |
Where-Object {
    $_.Class -eq "MEDIA" -and
    $_.FriendlyName -eq "High Definition Audio Device"
}

Get-PnpDeviceProperty `
    -InstanceId $AudioDevice.InstanceId `
    -KeyName "DEVPKEY_Device_ProblemCode"

---

## Skills Demonstrated

- Windows Device Manager troubleshooting
- Plug and Play troubleshooting
- PowerShell device administration
- Get-PnpDevice
- Get-PnpDeviceProperty
- Device Instance IDs
- Windows device problem codes
- Device Manager Code 22
- Driver investigation
- Win32_PnPSignedDriver
- Driver version verification
- Driver signature verification
- Device disable/enable operations
- Hardware vs software troubleshooting
- Root cause analysis
- Help desk troubleshooting
- Technical documentation

---

## Cheat Sheet Concepts Introduced

### Plug and Play (PnP)

Plug and Play is the Windows technology used to detect, configure, and manage hardware devices.

Windows maintains device instances representing detected hardware and virtual devices.

PowerShell can inspect them using:

Get-PnpDevice

---

### Device Instance ID

A Device Instance ID uniquely identifies a particular device instance to Windows.

It allows administrative commands to target the exact device.

Example:

$AudioDevice.InstanceId

Using an Instance ID is safer than targeting devices only by a generic friendly name.

---

### Device Class

Windows organizes devices into classes based on their function.

Examples include:

MEDIA
Net
Display
USB
DiskDrive
Keyboard
Mouse
Monitor
AudioEndpoint

Device classes help technicians narrow troubleshooting to a particular hardware category.

---

### Device Problem Code

Windows maintains problem codes that describe certain device configuration problems.

PowerShell can retrieve the value using:

Get-PnpDeviceProperty `
    -InstanceId <InstanceID> `
    -KeyName "DEVPKEY_Device_ProblemCode"

A value of:

0

indicates no reported device problem.

---

### Device Manager Code 22

Problem Code 22 indicates that the device has been disabled.

In this ticket:

Healthy:
Code 0

Disabled:
Code 22

Restored:
Code 0

This allowed the failure to be diagnosed without unnecessarily reinstalling the driver.

---

### Win32_PnPSignedDriver

A CIM/WMI class containing information about Plug and Play drivers.

Useful properties include:

- DeviceName
- DriverProviderName
- DriverVersion
- DriverDate
- IsSigned
- InfName

PowerShell example:

Get-CimInstance Win32_PnPSignedDriver

---

### Signed Driver

A digitally signed driver contains a cryptographic signature that allows Windows to verify information about the software publisher and detect certain forms of modification.

In this ticket:

IsSigned = True

---

### Disable-PnpDevice

Administratively disables a Plug and Play device.

Example:

Disable-PnpDevice -InstanceId <ID>

This should be used carefully because disabling critical devices such as networking, storage, display, keyboard, or mouse can disrupt access to a computer.

---

### Enable-PnpDevice

Re-enables a disabled Plug and Play device.

Example:

Enable-PnpDevice -InstanceId <ID>

---

## Key Troubleshooting Principle

Do not immediately reinstall a driver simply because a hardware device is unavailable.

First determine what Windows is reporting.

Useful workflow:

User reports device failure
→ identify device
→ inspect PnP status
→ identify exact Instance ID
→ inspect device problem code
→ inspect installed driver
→ determine whether device is disabled, driver-related, or potentially hardware-related
→ perform targeted remediation
→ verify device status
→ verify problem code
→ verify driver state

In this ticket:

Audio unavailable
→ device investigated
→ Code 22 discovered
→ device identified as disabled
→ device re-enabled
→ Status returned to OK
→ Problem Code returned to 0
→ signed driver verified

---

## Important Scope Note

This was a controlled device-disable simulation using a VirtualBox audio device.

It did not demonstrate:

- Physical sound-card failure
- Corrupted driver files
- Missing driver
- Actual speaker failure

The documented root cause is specifically:

Administratively disabled Plug and Play device.

---

## Outcome

A simulated Windows audio-device failure was successfully diagnosed and resolved on CLIENT01.

The High Definition Audio Device initially reported:

Problem Code = 0

After being deliberately disabled:

Problem Code = 22

Windows Problem Code 22 identified the device as disabled.

The device was re-enabled and final verification showed:

Status = OK
Problem Code = 0
Driver Provider = Microsoft
Driver Version = 10.0.26100.9278
IsSigned = True

The ticket demonstrated Windows Plug and Play troubleshooting, device problem-code interpretation, driver inspection, safe device administration, and targeted remediation.