# Mission 11 - Ticket 034: Generate and Review Group Policy Results Report

## Category

Group Policy Administration

## Priority

Medium

---

## Request

Generate a detailed Group Policy Results report for the domain-joined Windows 11 workstation and review the report to verify effective policy settings and identify the Group Policy Object responsible for enforcing them.

---

## Analysis

Previous tickets verified Group Policy inheritance, successful client-side processing, and effective policy settings using `gpresult /r` and Resultant Set of Policy (RSoP).

For more detailed administrative review, Windows provides the ability to generate an HTML Group Policy Results report using `gpresult`.

Unlike the abbreviated command-line output, the HTML report provides a structured view of computer configuration, policy settings, policy status, and the GPO responsible for each effective setting.

This information can be useful for auditing, documentation, and troubleshooting Group Policy deployments.

---

## Resolution

Opened an elevated Command Prompt on the Windows 11 workstation and generated an HTML Group Policy Results report.

The report was generated using:

```cmd
gpresult /h C:\GPReport.html /f
```

Verified that the report was successfully created and opened the resulting HTML file in Microsoft Edge.

Reviewed the **Computer Configuration** results and expanded the Administrative Templates section to inspect effective workstation policies.

The report displayed multiple enabled policies and identified **Workstation Security Baseline** in the **Winning GPO** column as the Group Policy Object responsible for enforcing the settings.

---

## Verification

Confirmed that:

* The HTML Group Policy Results report generated successfully.
* The report contained detailed Computer Configuration information.
* Administrative Template policies were visible within the report.
* Configured workstation policies displayed an Enabled state.
* The report identified the Winning GPO responsible for effective settings.
* Workstation Security Baseline was responsible for multiple enforced workstation policies.

Examples of verified settings included:

* Configure Automatic Updates
* Desktop Wallpaper
* Enable screen saver
* Password protect the screen saver
* Prevent changing screen saver
* Prohibit access to Control Panel and PC settings
* Screen saver timeout

---

## Evidence Collected

### Screenshot

**06_Group_Policy_Results_Report.png**

Displays:

* HTML Group Policy Results report on CLIENT01.
* Computer Configuration Administrative Template settings.
* Enabled policy states.
* Workstation Security Baseline identified in the Winning GPO column.

---

## Skills Demonstrated

* Group Policy Administration
* `gpresult` Reporting
* Group Policy Auditing
* Effective Policy Analysis
* Administrative Template Validation
* GPO Troubleshooting
* Windows Command Line Administration
* Windows Workstation Management
* Technical Documentation

---

## Command Used

```cmd
gpresult /h C:\GPReport.html /f
```

### Command Breakdown

* `/h` — Generates the Group Policy Results report in HTML format.
* `C:\GPReport.html` — Specifies the output location and filename.
* `/f` — Forces an existing report with the same filename to be overwritten.

---

## Lesson Learned

Group Policy validation can be performed at multiple levels.

`gpresult /r` provides a quick command-line view of applied GPOs, while `rsop.msc` provides a graphical view of effective policies. Generating an HTML report with `gpresult /h` provides a more detailed and portable record of the workstation's Group Policy configuration.

The **Winning GPO** information is particularly useful when multiple GPOs contain settings that could affect the same configuration because it helps identify which policy ultimately controls the effective setting.

---

## Reflection

This ticket demonstrated how Group Policy reporting can be used for more than basic troubleshooting. Generating and reviewing an HTML Group Policy Results report provided detailed evidence of the workstation's effective configuration and identified the GPO responsible for enforcing individual policy settings.

Combined with Group Policy inheritance, `gpresult /r`, RSoP, and secure-channel validation performed throughout Mission 11, this exercise established a structured workflow for verifying and troubleshooting Group Policy deployment in an Active Directory environment.
