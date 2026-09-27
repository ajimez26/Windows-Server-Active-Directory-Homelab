# Mission 11 - Ticket 030: Move Domain Workstation to Organizational Unit (OU)

## Category

Active Directory Administration

## Priority

Medium

---

## Request

Move the domain-joined Windows 11 workstation into the existing **Workstations** Organizational Unit (OU) to prepare the computer for workstation-specific Group Policy deployment.

---

## Analysis

The Windows 11 client computer was initially located in the default **Computers** container within Active Directory.

While computers can function correctly from this location, the default **Computers** container does not provide the same level of administrative organization or flexibility as a dedicated Organizational Unit (OU). Placing workstations into a dedicated OU allows administrators to apply targeted Group Policy Objects (GPOs) without affecting servers, domain controllers, or other computer objects.

The organization already maintained a **Workstations** OU as part of its Active Directory structure, making it the appropriate location for domain-joined client computers.

---

## Resolution

Opened **Active Directory Users and Computers** on the Domain Controller.

Located the Windows 11 client computer within the **Computers** container.

Moved the workstation object (**DESKTOP-9NPE7VN**) into the existing **Workstations** Organizational Unit.

Verified that the workstation appeared within the Workstations OU after the move was completed successfully.

No user accounts or security groups were modified during this task.

---

## Verification

Confirmed that:

- The workstation object was successfully removed from the default Computers container.
- The workstation appeared inside the Workstations Organizational Unit.
- The computer remained joined to the Active Directory domain.
- The workstation is now prepared to receive workstation-specific Group Policy Objects.

---

## Evidence Collected

### Screenshot

**01_Workstation_Moved_To_OU.png**

Displays:

- The Workstations Organizational Unit.
- DESKTOP-9NPE7VN located inside the Workstations OU.

---

## Skills Demonstrated

- Active Directory Users and Computers
- Organizational Unit (OU) Administration
- Active Directory Object Management
- Windows Domain Administration
- Active Directory Organization
- Group Policy Preparation

---

## Lesson Learned

Organizational Units provide a structured method for organizing Active Directory objects and allow administrators to target Group Policy Objects to specific groups of computers or users. Moving workstations into dedicated OUs improves administrative efficiency and reduces the likelihood of unintentionally applying policies to inappropriate systems.

---

## Reflection

This ticket prepared the Windows 11 workstation for centralized management through Group Policy. Rather than relying on the default Active Directory Computers container, the workstation was placed into a dedicated Organizational Unit that supports targeted policy deployment and better reflects enterprise Active Directory design. Organizing computer objects into purpose-specific OUs improves scalability, simplifies administration, and establishes a foundation for future workstation security policies.