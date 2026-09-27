# Ticket #006 – Configure a Reverse Lookup Zone

## Category

DNS Administration

---

## Priority

Medium

---

## Request

The DNS server displayed a warning indicating that associated PTR records could not be created because no Reverse Lookup Zone existed.

Configure a Reverse Lookup Zone to support reverse DNS resolution and allow future PTR records to be created.

---

## Analysis

The DNS server already contained a functioning Forward Lookup Zone that resolved hostnames to IPv4 addresses.

However, no Reverse Lookup Zone existed for the **10.0.2.0/24** network.

Without a Reverse Lookup Zone, Windows has no location to store PTR (Pointer) Records, preventing automatic reverse DNS registration.

A Reverse Lookup Zone was required to provide IP address-to-hostname resolution.

---

## Resolution

Created a new **Primary Reverse Lookup Zone** using the following configuration:

- Zone Type: Primary Zone
- Active Directory Replication Scope:
  - To all DNS servers running on domain controllers in this domain
- Reverse Lookup Zone Type:
  - IPv4
- Network ID:
  - 10.0.2
- Dynamic Updates:
  - Allow only secure dynamic updates

The Reverse Lookup Zone was successfully created.

---

## Verification

Verified that the Reverse Lookup Zone:

**2.0.10.in-addr.arpa**

appeared under **Reverse Lookup Zones** in DNS Manager.

The DNS server is now capable of storing PTR (Pointer) Records for devices on the **10.0.2.0/24** network.

---

## Evidence Collected

### Evidence 1

DNS Manager displaying the newly created Reverse Lookup Zone.

Screenshot:

- Mission6_Ticket006_ReverseLookupZone.png

---

### Evidence 2

Reverse Lookup Zone properties showing:

- Primary Zone
- Active Directory Integrated
- Secure Dynamic Updates

Screenshot:

- Mission6_Ticket006_ZoneProperties.png

*(If you didn't capture the Properties window, that's okay. We can substitute another screenshot showing the completed Reverse Lookup Zone.)*

---

## Skills Demonstrated

- Windows Server 2025 Administration
- DNS Administration
- Reverse Lookup Zones
- Active Directory Integrated DNS
- DNS Replication
- Secure Dynamic Updates
- DNS Infrastructure Configuration

---

## Lesson Learned

Forward Lookup Zones resolve hostnames into IPv4 addresses using A Records.

Reverse Lookup Zones store PTR Records, allowing IPv4 addresses to resolve back to hostnames.

Both Forward and Reverse Lookup Zones work together to provide complete DNS name resolution and improve troubleshooting capabilities.

Secure Dynamic Updates allow authenticated domain computers to automatically maintain DNS records while protecting against unauthorized modifications.

---

## Reflection

This ticket helped me understand why Windows attempted to create a PTR record when I created an A Record earlier in the lab.

I learned that the warning occurred because no Reverse Lookup Zone existed for the subnet.

Creating the Reverse Lookup Zone completed the DNS infrastructure by allowing reverse DNS records to be stored, improving troubleshooting, logging, and administrative visibility across the network.