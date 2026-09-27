# Mission 11 - Ticket 031: Validate Group Policy Inheritance

## Category

Group Policy Administration

## Priority

Medium

---

## Request

Verify that the existing Workstation Security Baseline Group Policy Object (GPO) is inherited by the Workstations Organizational Unit (OU) and confirm that workstation computers are within the intended scope of policy application.

---

## Analysis

The organization maintains a centralized Workstation Security Baseline GPO to enforce standardized security settings across domain-joined client computers.

Rather than creating duplicate GPOs for each Organizational Unit, Active Directory allows policies linked to parent containers to be inherited by child OUs. Verifying inheritance ensures that workstation security settings are applied consistently while reducing administrative overhead.

---

## Resolution

Opened the Group Policy Management console and selected the Workstations Organizational Unit.

Reviewed the **Group Policy Inheritance** tab to identify all inherited policies affecting the Workstations OU.

Confirmed that the Workstation Security Baseline GPO was inherited from its parent Organizational Unit and that additional domain-level policies were also present.

No configuration changes were required because policy inheritance was functioning as expected.

---

## Verification

Confirmed that:

- The Workstations OU successfully inherited the Workstation Security Baseline GPO.
- Default Domain Policy was inherited from the domain.
- All inherited Group Policy Objects were enabled.
- The workstation OU was correctly positioned to receive centralized security policies.

---

## Evidence Collected

### Screenshot

**02_Group_Policy_Inheritance.png**

Displays:

- Workstations Organizational Unit selected.
- Group Policy Inheritance tab.
- Inherited Group Policy Objects and precedence order.

---

## Skills Demonstrated

- Group Policy Management
- Active Directory Organizational Units
- Group Policy Inheritance
- GPO Scope Validation
- Windows Server Administration
- Group Policy Troubleshooting

---

## Lesson Learned

Group Policy inheritance allows administrators to apply security settings at higher levels of the Active Directory hierarchy while automatically extending those policies to child Organizational Units. Understanding inheritance helps prevent unnecessary duplicate GPOs and simplifies policy management across the domain.

---

## Reflection

This ticket reinforced the importance of validating Group Policy inheritance before making configuration changes. By confirming that the Workstations Organizational Unit correctly inherited the organization's security baseline, the Active Directory environment remained centrally managed without requiring additional GPO links or duplicate policy configurations.