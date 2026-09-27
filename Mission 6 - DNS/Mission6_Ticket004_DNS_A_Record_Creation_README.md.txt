# Ticket #004 – Create DNS A Record

## Category

DNS Administration

---

## Priority

Medium

---

## Request

The Infrastructure team deployed a new file server with the following information:

**Hostname:**
fileserver.jimeneztech.local

**IPv4 Address:**
10.0.2.20

Create the appropriate DNS record so users can access the server by hostname instead of memorizing the IP address.

---

## Analysis

A Forward Lookup Zone was required because users know the hostname and need DNS to resolve it to an IPv4 address.

An **A (Host) Record** was selected because it maps a hostname to an IPv4 address.

---

## Resolution

Created a new **Host (A) Record** in the **jimeneztech.local** Forward Lookup Zone.

Configuration:

- Hostname: fileserver
- IPv4 Address: 10.0.2.20

During creation, Windows displayed a warning indicating that an associated **PTR record** could not be created because no Reverse Lookup Zone existed for the subnet.

The warning was expected and did not prevent the successful creation of the A Record.

---

## Verification

Verified successful DNS name resolution using:

```cmd
nslookup fileserver.jimeneztech.local
```

The command successfully resolved:

```text
fileserver.jimeneztech.local
        ↓
10.0.2.20
```

confirming that the DNS record was functioning correctly.

---

## Evidence Collected

**Evidence 1**

DNS Manager showing the newly created **fileserver** Host (A) Record inside the **jimeneztech.local** Forward Lookup Zone.

Screenshot:

- Mission6_Ticket004_A_Record.png

**Evidence 2**

Command Prompt showing successful DNS resolution using:

```cmd
nslookup fileserver.jimeneztech.local
```

Screenshot:

- Mission6_Ticket004_nslookup_Verification.png

---

## Skills Demonstrated

- Windows Server 2025 Administration
- DNS Manager
- Forward Lookup Zones
- DNS Host (A) Record Creation
- DNS Verification
- Command Prompt
- nslookup
- DNS Troubleshooting Fundamentals

---

## Lesson Learned

Forward Lookup Zones resolve hostnames into IP addresses.

Host (A) Records are used to map hostnames to IPv4 addresses so users can access resources by name instead of numerical IP addresses.

A warning about a missing PTR record does not necessarily indicate that the A Record creation failed. Verification with **nslookup** confirmed the record was functioning correctly.

---

## Reflection

This ticket helped reinforce how DNS translates hostnames into IPv4 addresses using A Records.

I also learned that Windows attempts to create a corresponding PTR record during A Record creation. Although the PTR record could not be created because a Reverse Lookup Zone had not yet been configured, the A Record was successfully created and verified.

Verifying changes using **nslookup** reinforced the importance of confirming configuration changes instead of assuming they were successful.