# Windows Endpoint Recovery Runbook — CrowdStrike Channel File 291

> **Sanitisation and confidentiality:** This public runbook omits employee identities, the employer's name and location, hostnames, internal platforms, access details, BitLocker recovery information and other commercially sensitive information.

> **Important notice:** This runbook documents the recovery process used for the CrowdStrike Channel File 291 incident in July 2024. Do not use it for unrelated Windows startup failures or future CrowdStrike incidents. Before applying remediation in production, review current vendor guidance, obtain internal approval and validate the procedure for your environment.

## Related Documents

- [Repository Overview](README.md)
- [Incident Recovery Case Study](case-study.md)

## 1. Purpose

This runbook documents the manual process used to recover Windows endpoints that failed to start after receiving the faulty CrowdStrike Channel File 291 content released on 19th July 2024.

It covers incident confirmation, Safe Mode and BitLocker considerations, targeted file removal, validation, escalation, tracking and post-recovery monitoring.

## 2. Scope

Use this procedure only when:

- The device is a Windows workstation or laptop running the CrowdStrike Falcon sensor
- The symptoms are consistent with the July 2024 Channel File 291 incident
- The incident has been confirmed against current vendor guidance and approved internal guidance
- The technician has approval to perform endpoint remediation
- The device can be accessed through Safe Mode using an approved Windows recovery method

This runbook does not cover unrelated Windows startup failures, macOS or Linux devices, general Falcon sensor removal or reinstallation, server recovery, BitLocker decryption or security-control bypass, hardware repair or data recovery.

## 3. Known Symptoms

Affected endpoints may show:

- A Blue Screen of Death during startup
- Stop code `PAGE_FAULT_IN_NONPAGED_AREA`
- Blue-screen errors associated with `0x50` or `0x7E`
- Repeated automatic restarts
- Entry into the Windows recovery environment
- Failure to reach a stable Windows sign-in screen
- Loss of normal remote-support and diagnostic access

Not every affected device will show the same recovery screen or stop code. Confirm the wider incident context before applying remediation.

## 4. Required Access and Information

Before starting, make sure you have:

- Physical access to the workstation or laptop
- Approval to perform the remediation
- The device asset identifier, assigned employee and operational priority
- Administrator access where required
- An authorised BitLocker recovery key if requested
- A shared recovery record for ownership and status tracking
- Current CrowdStrike and Microsoft guidance for the incident

Do not store passwords, BitLocker recovery keys or other credentials in the shared recovery record.

## 5. Safety and Change Controls

Before modifying the endpoint:

1. Confirm the symptoms, device identity and assigned employee.
2. Record the device as assigned and in progress.
3. Verify the Windows system drive, CrowdStrike directory and exact filename pattern.
4. Use only the approved BitLocker recovery key for the correct device if prompted.
5. Do not uninstall the Falcon sensor, disable or decrypt BitLocker, or make unrelated system changes unless a separate approved recovery path requires it.
6. Stop and escalate if the device does not follow the confirmed recovery path.

The `del` command permanently deletes matching files. Verify the full path and filename pattern before running it.

## 6. Recovery Decision

- **Device starts normally:** Do not delete files automatically. Validate the endpoint and confirm the incident context against current vendor guidance.
- **Device enters Safe Mode:** Continue with the targeted recovery procedure.
- **BitLocker prompt appears:** Use the authorised recovery key matching the displayed recovery key identifier.
- **Safe Mode cannot be reached:** Stop and escalate.
- **Symptoms or affected product cannot be confirmed:** Treat the issue as a separate incident.

## 7. Manual Recovery Procedure

The commands below assume Windows is installed on `C:`. Verify the actual system drive and replace `C:` where necessary.

### 7.1 Record and Assign the Endpoint

Record the asset identifier, assigned employee, location, operational priority, assigned technician, reported symptoms and current status.

Set the status to **In progress** once hands-on recovery begins.

### 7.2 Access Safe Mode

1. Connect the endpoint to power.
2. Access the approved Windows recovery options.
3. Select the available Safe Mode option.
4. Enter the authorised BitLocker recovery key if prompted.
5. Confirm that the endpoint reaches a stable Safe Mode session.

Safe Mode entry varies by Windows version and device configuration. During this incident, technicians used the startup and recovery options available on each endpoint rather than relying on one key sequence.

A BitLocker recovery key unlocks the encrypted volume for authorised access. It does not decrypt the drive or disable BitLocker protection.

### 7.3 Open an Elevated Command Prompt

Open Command Prompt with administrator privileges where required, then confirm the Windows system drive:

```cmd
echo %SystemDrive%
```

Confirm that the expected Windows directory exists:

```cmd
dir "C:\Windows"
```

Do not proceed until the correct system drive has been verified.

### 7.4 Confirm the Affected Channel File Content

Locate files matching the Channel File 291 pattern:

```cmd
dir "C:\Windows\System32\drivers\CrowdStrike\C-00000291*.sys"
```

- If one or more matching files are displayed, continue to removal.
- If the directory or matching files cannot be found, verify the system drive, CrowdStrike installation path and incident diagnosis.
- Do not delete unrelated `.sys` files or broaden the wildcard pattern.

### 7.5 Remove the Affected Content

After confirming the exact matches, run:

```cmd
del "C:\Windows\System32\drivers\CrowdStrike\C-00000291*.sys"
```

Verify that no matching files remain:

```cmd
dir "C:\Windows\System32\drivers\CrowdStrike\C-00000291*.sys"
```

The expected result is that no matching files remain.

If deletion fails or access is denied, do not make unapproved ownership or permission changes. Follow the troubleshooting and escalation section instead.

### 7.6 Restart Windows Normally

1. Close Command Prompt.
2. Restart the endpoint.
3. Allow Windows to start in normal mode and observe the startup process.

Do not mark the endpoint as restored just because the Windows sign-in screen appears.

## 8. Endpoint Validation

Complete validation before returning the device to its employee.

### 8.1 Startup and Access

Confirm that:

- Windows starts normally without another blue screen, restart loop or recovery screen
- The device remains stable after sign-in
- User authentication and the Windows profile load correctly
- Local network and internet connectivity work
- VPN works where required
- Email, shared files and collaboration platforms are accessible

### 8.2 Security

Confirm that:

- The CrowdStrike Falcon sensor remains installed
- Expected endpoint security services are operating
- The endpoint appears healthy in the approved security or management console where access is available
- No new endpoint security alerts require investigation

### 8.3 Role-Specific Applications

Test the applications and services the employee needs for their role rather than applying the same generic checks to every endpoint.

Examples include operational monitoring, customer support, compliance and reporting, financial processing, collaboration and technical documentation systems.

Where practical, ask the employee to confirm sign-in and required application access, report any further crashes or unusual behaviour, and record the validation result.

## 9. Troubleshooting and Escalation

### BitLocker Recovery Key Is Unavailable or Rejected

Confirm the device identity and displayed recovery key identifier, then retrieve the key only from the organisation's authorised source. Do not bypass BitLocker. Escalate if the correct key cannot be obtained or accepted.

### Safe Mode Is Unavailable

Confirm that the approved Windows recovery options have been attempted. Do not repeatedly force shutdowns without an authorised recovery plan. Escalate for an alternative vendor-supported recovery method.

### CrowdStrike Directory or Matching File Is Not Found

Verify the Windows system drive, Falcon installation, path and filename pattern. Confirm that the symptoms belong to the Channel File 291 incident. Do not delete other channel files or unrelated drivers.

### File Explorer Is Unstable

Use an elevated Command Prompt and the verified commands in this runbook rather than relying on graphical file deletion.

### Access Is Denied During Deletion

Confirm the recovery mode, administrator access, path and matching file. Do not make unapproved access-control or ownership changes. Escalate if the file cannot be removed through the confirmed process.

### The Endpoint Still Crashes After Remediation

Confirm that the matching Channel File 291 content was removed, record the current stop code and startup behaviour, check current vendor guidance and escalate for further investigation.

### Windows Starts but Business Access Is Not Restored

Identify whether the remaining problem affects authentication, networking, endpoint security or a role-specific application. Record the failed check and route it to the appropriate support owner. Do not mark the endpoint as restored until required checks pass or the remaining issue is formally tracked separately.

### Unrelated Hardware or Software Fault

Record and manage the issue separately. Do not attribute unrelated failures to Channel File 291 without evidence.

## 10. Work-from-Home Devices

Affected work-from-home laptops may need to be physically returned because the startup failure prevents remote remediation.

Confirm the employee, asset identifier and symptoms. Provide the approved return instructions and record the device as **Awaiting return**. When the device is received, add it to the active recovery queue, prioritise it based on operational need and physical availability, and complete the same remediation and validation used for onsite endpoints.

Do not ask employees to send BitLocker recovery keys through unapproved communication channels.

## 11. Status Tracking and Communication

Maintain one shared record covering:

- Asset, employee and location
- Operational priority and assigned technician
- Reported symptoms and BitLocker prompt status
- Remediation, validation and escalation status
- Relevant timestamps and concise technical notes

Recommended status values are **Reported**, **Awaiting return**, **Assigned**, **In progress**, **Awaiting validation**, **Restored** and **Escalated**.

Keep employees informed when their device requires action, changes status materially or passes validation. Keep the response team and technical leadership updated on recovery totals, priority changes, devices awaiting action and unresolved cases.

Do not store credentials or recovery keys in the record, and do not promise a completion time unless it has been confirmed.

## 12. Completion Criteria

Mark an endpoint **Restored** only after:

- The targeted remediation is complete and Windows is stable
- Authentication, network access and required services work
- Endpoint security has been checked
- Role-specific applications have been validated
- The recovery record is complete and the employee has been informed
- Any remaining issue has been resolved or formally separated and escalated

The wider recovery is complete when every reported endpoint is restored, accounted for or actively escalated, the tracking record is current, recurring failures are not being observed and technical leadership has received the final status.

## 13. Post-Recovery Monitoring

After immediate recovery:

- Monitor for renewed blue-screen crashes, restart loops or recurring support reports
- Confirm that restored endpoints remain visible and healthy in approved management consoles
- Follow up on escalated or unavailable devices
- Separate unrelated issues from the original incident and document confirmed findings or improvements in the internal knowledge base

Continue monitoring until restored endpoints are confirmed stable during normal employee use.

## 14. References

- [CrowdStrike — Technical Details: Falcon Content Update for Windows Hosts](https://www.crowdstrike.com/en-us/blog/falcon-update-for-windows-hosts-technical-details/)
- [CrowdStrike — Falcon Content Update Preliminary Post Incident Report](https://www.crowdstrike.com/en-us/blog/falcon-content-update-preliminary-post-incident-report/)
- [CrowdStrike — Channel File 291 Incident Root Cause Analysis](https://www.crowdstrike.com/en-us/blog/channel-file-291-rca-available/)
- [Microsoft Support — KB5042421: CrowdStrike issue impacting Windows endpoints](https://support.microsoft.com/en-au/topic/kb5042421-crowdstrike-issue-impacting-windows-endpoints-causing-an-0x50-or-0x7e-error-message-on-a-blue-screen-b1c700e0-7317-4e95-aeee-5d67dd35b92f)
- [Microsoft Learn — `del` command](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/del)
- [Microsoft Learn — BitLocker recovery process](https://learn.microsoft.com/en-us/windows/security/operating-system-security/data-protection/bitlocker/recovery-process)
