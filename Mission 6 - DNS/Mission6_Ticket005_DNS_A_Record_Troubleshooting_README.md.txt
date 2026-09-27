# Ticket #005 – Troubleshoot Incorrect DNS A Record

## Category

DNS Troubleshooting

---

## Priority

Medium

---

## Request

A user reported that they could no longer access:

**fileserver.jimeneztech.local**

The user stated the resource had been accessible previously but was no longer reachable by hostname.

Initial troubleshooting suggested the DNS A Record may have been modified incorrectly.

---

## Analysis

The first step was to verify the current DNS configuration before making any changes.

The DNS Manager was used to inspect the existing A Record for **fileserver.jimeneztech.local**.

The configuration was then verified using:

```cmd
nslookup fileserver.jimeneztech.local
```

The DNS query returned an incorrect IPv4 address, confirming that the hostname was resolving to the wrong destination.

---

## Resolution

Opened the properties of the existing **Host (A) Record** for:

**fileserver**

The incorrect IPv4 address:

```text
10.0.2.99
```

was corrected to:

```text
10.0.2.20
```

The updated DNS record was saved successfully.

---

## Verification

Verified the corrected configuration using:

```cmd
nslookup fileserver.jimeneztech.local
```

The command successfully resolved:

```text
fileserver.jimeneztech.local
        ↓
10.0.2.20
```

confirming that the DNS record had been restored to the correct IPv4 address.

---

## Evidence Collected

### Evidence 1

DNS Manager displaying the corrected Host (A) Record for:

**fileserver**

Screenshot:

- Mission6_Ticket005_DNS_Record_Corrected.png

---

### Evidence 2

Command Prompt showing successful verification using:

```cmd
nslookup fileserver.jimeneztech.local
```

Screenshot:

- Mission6_Ticket005_nslookup_Verification.png

---

## Skills Demonstrated

- Windows Server 2025 Administration
- DNS Administration
- DNS Troubleshooting
- DNS Record Verification
- Host (A) Record Management
- DNS Manager
- Command Prompt
- nslookup
- Problem Analysis
- Configuration Validation

---

## Lesson Learned

Successful DNS resolution does not always indicate that the configuration is correct.

A DNS server may function normally while returning incorrect information if an A Record contains an invalid IPv4 address.

Verifying DNS responses using **nslookup** before making configuration changes helps identify whether the issue is caused by incorrect DNS data rather than a failure of the DNS service itself.

---

## Reflection

This ticket reinforced the importance of verifying the current configuration before making changes.

I learned that a functioning DNS service can still produce connectivity issues if a hostname is mapped to an incorrect IPv4 address.

By inspecting the DNS record first and confirming the issue with **nslookup**, I was able to identify the root cause, correct the configuration, and verify that users would once again resolve the hostname to the proper destination.