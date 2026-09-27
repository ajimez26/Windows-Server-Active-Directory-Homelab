# Ticket #007 – Create and Verify a PTR Record

## Category

DNS Administration

---

## Priority

Medium

---

## Request

The Reverse Lookup Zone has been successfully configured.

Create a PTR (Pointer) Record for the file server to enable reverse DNS lookups from an IPv4 address to its corresponding hostname.

---

## Analysis

A Reverse Lookup Zone had already been created for the **10.0.2.0/24** network.

The next step was to create a PTR Record that maps the IPv4 address of the file server back to its Fully Qualified Domain Name (FQDN).

This would allow administrators and troubleshooting tools to resolve IP addresses into hostnames.

---

## Resolution

Created a new PTR Record using the following information:

- Host IP Number:
  - 20

- Host Name:
  - fileserver.jimeneztech.local

The PTR Record was successfully created inside the Reverse Lookup Zone.

---

## Verification

Verified successful reverse DNS resolution using:

```cmd
nslookup 10.0.2.20
```

The DNS server successfully resolved:

```text
10.0.2.20
        ↓
fileserver.jimeneztech.local
```

confirming that reverse DNS was functioning correctly.

---

## Evidence Collected

### Evidence 1

DNS Manager displaying the PTR Record inside:

**2.0.10.in-addr.arpa**

Screenshot:

- Mission6_Ticket007_PTR_Record.png

---

### Evidence 2

Command Prompt displaying successful reverse DNS resolution.

Command used:

```cmd
nslookup 10.0.2.20
```

Screenshot:

- Mission6_Ticket007_Reverse_nslookup_Verification.png

---

## Skills Demonstrated

- Windows Server 2025 Administration
- DNS Administration
- Reverse Lookup Zones
- PTR Record Creation
- Reverse DNS Resolution
- Command Prompt
- nslookup
- DNS Verification
- Troubleshooting

---

## Lesson Learned

PTR Records provide reverse DNS resolution by mapping IPv4 addresses back to hostnames.

Unlike A Records, which resolve hostnames into IPv4 addresses, PTR Records enable administrators to identify systems when only an IP address is known.

Reverse DNS is commonly used during troubleshooting, network administration, security investigations, and log analysis.

---

## Reflection

This ticket completed my understanding of DNS by demonstrating both forward and reverse name resolution.

I learned how PTR Records complement A Records and why Reverse Lookup Zones are required to support reverse DNS queries.

Verifying the PTR Record using **nslookup** reinforced the importance of confirming configuration changes after implementation.