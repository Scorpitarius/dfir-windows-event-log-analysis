
# DFIR-002 | Windows Authentication Event Log Investigation

## Investigation Overview

**Case ID:** DFIR-002  
**Examination Date:** October 31, 2026  
**Classification:** Controlled Laboratory Exercise  
**Status:** Completed

This investigation examined Windows Security event logs to identify and analyze authentication activity within a controlled forensic laboratory.

The examination identified one failed interactive authentication attempt and eleven successful service logons during a 15-minute period.

The investigation focused on interpreting Windows Security events, reconstructing an authentication timeline, preserving event evidence, and verifying exported evidence integrity.

## Investigation Objectives

- Identify successful and failed authentication events.
- Examine Windows Security Event IDs and logon types.
- Determine the recorded authentication failure reason.
- Construct a chronological authentication timeline.
- Examine surrounding service authentication activity.
- Preserve relevant events in CSV and native EVTX formats.
- Verify exported evidence integrity using SHA-256.

## Laboratory Environment

| Component | Configuration |
|---|---|
| Operating System | Windows 10 |
| Virtualization | VMware Workstation Pro |
| Network | Isolated VMnet2 host-only network |
| Tools | Event Viewer, PowerShell, Get-WinEvent, wevtutil, Get-FileHash |
| Hash Algorithm | SHA-256 |
| Time Zone | Pacific Daylight Time (UTC-7) |

## Examination Scope

**Date:** October 31, 2026  
**Local Time:** 08:50:00–09:05:00 PDT  
**UTC Time:** 15:50:00–16:05:00 UTC

The investigation examined Windows Security authentication activity, focusing on Event IDs 4624, 4625, 4634, 4740, and 4776.

## Examination Methodology

### 1. Audit Policy Verification

Verified the Windows logon auditing configuration using:

```powershell
auditpol /get /subcategory:"Logon"
```

The system was configured to record successful and failed logon events.

### 2. Authentication Event Examination

Used Windows Event Viewer and PowerShell to examine relevant Security events.

Identified twelve authentication records within the examination window:

- Eleven successful Type 5 service logons.
- One failed Type 2 interactive logon.

### 3. Failed Authentication Analysis

Examined Event ID 4625, Record ID 14839.

| Field | Value |
|---|---|
| Event ID | 4625 |
| Record ID | 14839 |
| Time | 08:55:39 PDT |
| Logon Type | 2 — Interactive |
| Status | 0xC000006D |
| Sub Status | 0xC000006A |
| Source IP Address | Not recorded |

The status codes were consistent with an incorrect-password authentication failure.

The recorded domain and workstation information was consistent with a local-account authentication attempt.

### 4. Authentication Timeline Reconstruction

Queried the Security log for the 15-minute examination window and exported the results to CSV.

Structured XML event data was examined to identify logon types and reconstruct the authentication sequence.

### 5. Service Logon Analysis

Examined two successful service logons recorded at approximately 08:56:09 PDT.

| Field | Record 14846 | Record 14848 |
|---|---|---|
| Event ID | 4624 | 4624 |
| Logon Type | 5 | 5 |
| Logon Process | Advapi | Advapi |
| Process | services.exe | services.exe |

The events were consistent with Windows service-related authentication.

No causal relationship between these events and the preceding failed interactive logon was established.

### 6. Additional Security Event Examination

Queried the selected examination window for:

- Event ID 4740 — Account lockout
- Event ID 4776 — NTLM credential validation

No matching records were returned.

### 7. Native Event Log Preservation

Exported the relevant Security events using `wevtutil` into native EVTX format.

An initial export used an incorrect UTC examination window. The time-zone configuration was verified, the examination window was corrected, and the export was repeated.

The corrected file, `authentication-events-corrected.evtx`, contained twelve records.

### 8. Evidence Integrity Verification

Calculated the SHA-256 hash of the corrected EVTX export using `Get-FileHash`.

Recorded the hash in `evidence-manifest.csv`.

Compared the Event Record IDs in the CSV and EVTX exports. No differences were identified between the sets of Record IDs.

## Investigation Findings

| Finding | Result |
|---|---|
| Failed interactive authentication | One Event ID 4625, Type 2 |
| Successful service authentication | Eleven Event ID 4624, Type 5 |
| Incorrect-password indication | Status 0xC000006D / Sub Status 0xC000006A |
| Account lockout events | No Event ID 4740 returned |
| NTLM validation events | No Event ID 4776 returned |
| Evidence export | Twelve records preserved in corrected EVTX |
| Integrity verification | EVTX SHA-256 matched recorded manifest |

## Limitations

- The investigation was conducted in a controlled virtual laboratory.
- Analysis was limited to a selected 15-minute window and specified Security Event IDs.
- The absence of particular event records does not conclusively establish that the corresponding activity did not occur.
- The identity or intent of the individual or process responsible for the failed logon was not established.
- The individual Windows services responsible for the observed service logons were not identified.
- Evidence was collected from a live Windows system without forensic disk imaging or write-blocking.
- The exported EVTX hash does not independently establish the integrity of the source Security log.

## Conclusion

The investigation identified one failed interactive logon consistent with an incorrect password and eleven successful service logons within the examination window.

The evidence did not establish repeated password guessing, unauthorized access, or a causal relationship between the failed authentication and subsequent service activity.

The examination demonstrated Windows Security event analysis, structured event interpretation, timeline reconstruction, time-zone normalization, native EVTX preservation, and SHA-256 evidence integrity verification.

## Supporting Documentation

The complete investigation report and supporting evidence artifacts will be added after review.
