# July 2024 CrowdStrike Incident Recovery Case Study

> **Sanitisation and confidentiality:** This public case study omits or generalises employee identities, the employer's name and location, hostnames, internal platforms, access details, BitLocker recovery information and other commercially sensitive material.

> **Important notice:** This is a retrospective of one specific incident, not a universal recovery procedure. Any production remediation must follow current vendor guidance, internal approval requirements and appropriate technical validation.

## Related Documents

- [Repository Overview](README.md)
- [Windows Endpoint Recovery Runbook](runbook.md)

## Executive Summary

During the July 2024 CrowdStrike outage, around 150 Windows workstations at the financial services organisation where I worked failed to start correctly, along with loan laptops.

I was part of a three-person internal IT team responding to the incident. I took ownership of assigned devices from the initial checks through remediation and validation, helped adjust recovery priorities as business requirements changed, kept recovery records up to date and communicated with affected employees.

Our first recovery approach was to start affected devices in Safe Mode and attempt to remove the CrowdStrike Falcon sensor. While testing one of the affected machines, I found that removing the affected Channel File 291 content was enough to restore normal Windows startup without removing the sensor entirely.

I tested the approach before sharing it with the rest of the team. Once confirmed, it became the standard recovery method for the remaining affected devices.

The team returned all affected endpoints to service during a 30-hour recovery period, with the most time-sensitive systems handled first.

## Incident Background

On Friday, 19th July 2024, at 2:09 pm AEST (04:09 UTC), CrowdStrike released a Rapid Response Content update for the Windows Falcon sensor.

The faulty content caused affected Windows systems to crash. CrowdStrike reverted the update at 3:27 pm AEST (05:27 UTC), 78 minutes after release.

Windows hosts running Falcon sensor version 7.11 or later that were online and received the content during that period were potentially affected. CrowdStrike confirmed that macOS and Linux systems were unaffected and that the event was caused by a defective content update rather than malicious activity.

The incident was traced to **Channel File 291**, part of the Rapid Response Content used by Falcon on Windows.

On affected systems, the relevant content was stored under:

`C:\Windows\System32\drivers\CrowdStrike\`

The files followed this pattern:

`C-00000291*.sys`

Although the files used the `.sys` extension and were stored in the Windows drivers directory, they contained Falcon configuration content rather than operating as standalone kernel drivers.

CrowdStrike later reported that the faulty content caused an out-of-bounds memory read in the Falcon sensor's Content Interpreter. Microsoft reported that affected endpoints could display `0x50` or `0x7E` blue-screen errors and enter continuous restart loops.

## Business Impact

Affected employees could not reach a stable Windows session, which meant they could not access the applications and information they needed for work.

The outage affected devices used for time-sensitive operational work, customer-facing work, compliance and reporting, financial processing and other corporate and technical functions.

Support demand increased quickly while the business still needed to serve clients and maintain time-sensitive financial operations. Around 150 Windows workstations were affected, along with loan laptops. With only three of us working on the recovery, we had to coordinate the response and decide which devices needed to come back first.

## My Role

I was responsible for the devices assigned to me and worked with the rest of the IT team throughout the recovery.

My work included:

- Confirming which devices were affected and who they belonged to
- Carrying out hands-on remediation on laptops and workstations
- Helping adjust recovery priorities as business requirements changed
- Keeping ownership, progress and validation records up to date
- Communicating with affected employees
- Checking that recovered devices were stable and usable before returning them
- Escalating devices that did not recover as expected
- Helping coordinate the return of affected work-from-home laptops
- Documenting the confirmed recovery process in the internal knowledge base

We split ownership of the affected devices so several machines could be worked on at the same time without losing track of who was responsible for each one.

## Initial Assessment

The first reports described blue-screen crashes followed by devices failing to start normally.

One of the stop codes we saw was:

`PAGE_FAULT_IN_NONPAGED_AREA`

Some devices repeatedly restarted, while others entered the Windows recovery environment instead of reaching the normal sign-in screen.

When the same behaviour appeared across different parts of the business within a short period, it became clear that we were not dealing with a single faulty machine.

During triage, I compared the stop codes and startup behaviour across several affected devices. `PAGE_FAULT_IN_NONPAGED_AREA` pointed to a memory-access problem, but the stop code alone was not enough to identify the cause.

The affected machines had the CrowdStrike Falcon sensor in common. We compared the timing and symptoms we were seeing locally with information being published by CrowdStrike and Microsoft, which helped confirm that our devices were part of the wider Channel File 291 incident.

Because the devices could not reach a stable Windows session, our normal remote-support and diagnostic tools were unavailable. We relied on direct examination of the machines, employee reports, comparisons between affected endpoints and current vendor information.

Once the pattern was confirmed, we treated the problem as one coordinated endpoint incident rather than a collection of unrelated workstation faults.

## Prioritisation

We could not simply recover devices in the order they were reported.

Priority was based on:

- How urgent the employee's work was
- The effect of continued downtime
- Whether the device was needed for client service, reporting or decision-making
- Whether the device was physically available for hands-on work
- Changes in business priorities during the response

Devices supporting time-sensitive operational work were handled first. We then worked through customer-facing, compliance, reporting and leadership requirements before continuing across the remaining affected devices.

Onsite workstations could enter the recovery queue immediately. Work-from-home laptops had to be physically returned before remediation could begin, so the order changed as devices became available.

## Recovery Approach

### Initial Approach

Our early approach was to start affected devices in Safe Mode and attempt to remove the Falcon sensor.

Safe Mode gave us a way to work on affected machines when normal Windows startup was not usable, but removing the sensor entirely was more disruptive and slower than necessary for an incident involving this many devices.

### Targeted Channel File Remediation

While working on one of the affected machines, I found that removing the affected Channel File 291 content allowed Windows to start normally while leaving the Falcon sensor installed.

I tested the result and shared the method with the rest of the response team. Once confirmed, we used it as the standard remediation path for the remaining affected endpoints.

At a high level, the process was:

1. Access Safe Mode through the approved Windows recovery options
2. Unlock the BitLocker-protected volume with an authorised recovery key where required
3. Locate the CrowdStrike directory and confirm the matching Channel File 291 content
4. Remove the affected content
5. Restart Windows normally
6. Complete technical and role-specific checks
7. Update the device record and return the endpoint only after validation

Where BitLocker recovery was triggered, we used the authorised recovery key for that specific device. We did not disable or decrypt BitLocker as part of the recovery.

On some machines, File Explorer became unstable while we were removing the affected content. Using an elevated Command Prompt gave us a more reliable way to complete the remediation.

The exact commands, checks and escalation steps are documented in the accompanying Windows Endpoint Recovery Runbook.

## Communication and Recovery Tracking

We used a shared recovery record to track each endpoint, the employee it belonged to, which technician owned it and where it was in the recovery process.

That gave the team a clear view of devices waiting for attention, in progress, ready for validation, completed or requiring escalation. It also made it easier to move work between technicians and change priorities when needed.

I also kept technical leadership and the rest of the response team updated on recovery progress, priority changes and unresolved cases.

Affected employees were told what had happened, what we needed them to do and when they could expect another update.

Employees with affected company laptops outside the office were asked to return them because the startup failure prevented normal remote remediation. As laptops came back, we recorded them, assigned a priority and added them to the recovery queue.

Once a device had passed validation, we told the employee it was ready to use again.

## Challenges

The main challenges were:

- **No normal remote access:** affected devices could not start normally, so technicians needed physical access
- **High device volume:** around 150 workstations, along with loan laptops, had to be recovered by a three-person team
- **Different recovery conditions:** recovery screens, BitLocker prompts and File Explorer stability varied between devices
- **Work-from-home laptops:** some devices could not be worked on until they were physically returned
- **Changing priorities:** the recovery order had to change as new reports and business requirements emerged
- **Finding a repeatable method:** the recovery process had to work quickly across many devices without unnecessarily removing the installed security sensor

## Validation and Service Restoration

Seeing the Windows sign-in screen was not enough for us to call a device recovered.

Before returning an endpoint, I checked that it:

- Started normally without returning to a Blue Screen of Death or restart loop
- Reached a stable Windows session
- Allowed the employee to authenticate and load their profile
- Had working network and internet connectivity
- Could access required email, shared files and collaboration platforms
- Could access the applications needed for the employee's role
- Had expected endpoint security services operating
- No longer showed the original crash or restart behaviour

The application checks depended on the employee. A device used for operational monitoring needed different validation from one used mainly for general office work.

If a device still had startup, authentication, security-service or application problems, it stayed open and was escalated.

For us, recovery meant that remediation, validation and the device record were complete — not simply that Windows could boot again.

## Post-Recovery Monitoring

I returned onsite at around 7:00 am the following morning so I could monitor the environment and respond quickly if more failures were reported.

We did not identify any further failures associated with Channel File 291 during the monitoring period, and the recovered endpoints remained stable.

## Results

By the end of the 30-hour recovery period, the team had returned all affected endpoints to service.

My direct contribution covered the laptops and workstations assigned to me, including remediation, employee communication, validation, record updates and escalation where needed.

The response resulted in:

- The most time-sensitive users being restored first
- A repeatable Channel File 291 recovery method that kept the Falcon sensor installed
- Clear ownership and recovery status across the affected devices
- Devices being returned only after technical and role-specific checks
- No recurrence of the original CrowdStrike failure during follow-up monitoring
- The confirmed recovery process being retained in the organisation's internal knowledge base

## What I Took Away From the Incident

A few lessons stood out to me:

- **A fix has to work at scale.** A technically valid method is not always the right one when it needs to be repeated across a large number of devices.
- **Recovery order matters.** Restoring the most time-sensitive users first had more value than following ticket order.
- **You need a plan for machines that cannot boot normally.** Physical access, authorised BitLocker recovery information and command-line recovery options mattered once remote tools were unavailable.
- **Tracking becomes part of the technical response.** Clear ownership and status information helped prevent duplicate work and missed devices.
- **A successful boot is not the end of recovery.** Authentication, connectivity, endpoint security and the applications the employee actually needs still have to work.
- **Communication matters.** Employees needed clear instructions, while the response team and technical leadership needed an accurate view of progress and outstanding issues.
- **Monitoring and documentation finish the job.** We still needed to confirm stability after recovery and retain what we had learned for future use.

These lessons were used when putting together the accompanying Windows Endpoint Recovery Runbook.

## References

- [CrowdStrike — Technical Details: Falcon Content Update for Windows Hosts](https://www.crowdstrike.com/en-us/blog/falcon-update-for-windows-hosts-technical-details/)
- [CrowdStrike — Preliminary Post Incident Review: Falcon Content Update](https://www.crowdstrike.com/en-us/blog/falcon-content-update-preliminary-post-incident-report/)
- [CrowdStrike — Channel File 291 Incident Root Cause Analysis](https://www.crowdstrike.com/en-us/blog/channel-file-291-rca-available/)
- [Microsoft Support — KB5042421: CrowdStrike issue impacting Windows endpoints](https://support.microsoft.com/en-au/topic/kb5042421-crowdstrike-issue-impacting-windows-endpoints-causing-an-0x50-or-0x7e-error-message-on-a-blue-screen-b1c700e0-7317-4e95-aeee-5d67dd35b92f)
