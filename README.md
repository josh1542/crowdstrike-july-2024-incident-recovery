# July 2024 CrowdStrike Incident Recovery

This repository documents my work during the July 2024 CrowdStrike Channel File 291 incident.

It includes a sanitised incident case study and a Windows endpoint recovery runbook based on the recovery process used during the outage.

## Project Overview

On Friday, 19th July 2024, a faulty CrowdStrike Rapid Response Content update caused Windows devices worldwide to crash and enter Blue Screen of Death (BSOD) restart loops.

At the financial services organisation where I worked, around 150 Windows workstations were affected, along with loan laptops.

I was part of a three-person internal IT team responding to the outage. We split the affected devices between us and changed priorities as business requirements shifted.

For the devices assigned to me, I handled the recovery from the initial checks through remediation, validation and return to the employee. I also kept recovery records updated, communicated with affected employees and escalated devices that did not recover as expected.

All affected endpoints were returned to service during a 30-hour recovery period.

## Repository Contents

### [Incident Recovery Case Study](case-study.md)

Covers:

- What happened and how the business was affected
- My role during the incident
- How affected devices were identified and prioritised
- How the recovery approach changed during troubleshooting
- Communication and recovery tracking
- Validation and service restoration
- Results and lessons from the incident

### [Windows Endpoint Recovery Runbook](runbook.md)

Documents the recovery process, including:

- Incident and symptom checks
- Safe Mode and BitLocker handling
- Channel File 291 identification and removal
- Command-line remediation
- Endpoint validation
- Troubleshooting and escalation
- Recovery tracking
- Post-recovery monitoring

## My Contribution

During the incident, I:

- Took ownership of assigned laptops and workstations
- Confirmed whether devices matched the CrowdStrike incident
- Carried out hands-on remediation
- Helped adjust recovery priorities as business needs changed
- Tested a targeted Channel File 291 remediation before it was used more widely
- Kept device ownership, progress and validation records up to date
- Communicated with affected employees and the response team
- Escalated devices that did not recover as expected
- Helped coordinate the return of affected work-from-home laptops
- Documented the confirmed recovery process in the internal knowledge base

## Key Outcomes

- All affected Windows endpoints were returned to service during the 30-hour recovery period.
- The most time-sensitive devices were restored first.
- Targeted removal of the affected Channel File 291 content provided a quicker and less disruptive recovery path than uninstalling the Falcon sensor entirely.
- Shared tracking gave the team clear visibility of device ownership and recovery status.
- Devices were returned only after startup, authentication, connectivity, endpoint security and required applications had been checked.
- No recurrence of the original CrowdStrike failure was identified during follow-up monitoring.
- The confirmed recovery process was retained in the organisation's internal knowledge base.

## Sanitisation and Confidentiality

This repository has been sanitised to remove employee identities, the organisation's name and location, internal hostnames, confidential system names, credentials, recovery keys and other commercially sensitive information.

Internal tools and operational details have also been generalised where appropriate.

## Important Notice

This repository documents one specific incident and the recovery process used at the time.

The runbook is not intended as a general procedure for unrelated Windows startup failures or future CrowdStrike incidents. Before applying remediation in production, review current vendor guidance, obtain the appropriate internal approval and validate the procedure for the environment.
