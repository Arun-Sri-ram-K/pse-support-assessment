# Marina Bay Hospitality – API and ASP Submission Investigation

## Import evidence
- Import ID: feabef0d-49d9-47a0-a1ea-a19c81a59153
- File: mbh-morning-234.xlsx
- Seller: Marina Bay Hospitality LLC
- Created: 2026-11-13T15:16:58.274015Z
- Completed: 2026-11-13T15:17:00.732773Z
- Rows: 12
- Invoices: 6

## Observed impact in this test batch
- 3 invoices Accepted by ASP with message IDs.
- 3 invoices remain Submitted without ASP message IDs.
- No portal validation rejections or failed invoices in this batch.

## Confirmed cause of pending submissions
API logs record HTTP 429 for:
- MBH-1116-G4YD-234-02
- MBH-1116-G4YD-234-05
- MBH-1116-G4YD-234-06

The ASP returned rate-limit responses during submission. The portal left these invoices in Submitted status without message IDs.

## API restart and 502 investigation
- Docker inspection reported RestartCount=2.
- Previous process ExitCode=0 and OOMKilled=false.
- API was healthy at the time of later inspection.
- Captured API and web logs did not establish actual HTTP 502 responses.
- The reason for the API restarts remains undetermined.

## Assessment
P2 provisional. This batch demonstrates partial submission and ASP throttling, not a complete outage. The synthetic load-test batch must not be treated as proof of the original customer-reported pilot incident.

## Recommended escalation
Escalate to L2/Engineering to:
1. Investigate the API restart events and the reported 502s using production timestamps and customer-specific request IDs.
2. Review ASP rate-limit handling, including Retry-After and backoff behaviour.
3. Determine a safe recovery/reconciliation process for invoices still in Submitted status.
4. Confirm the original Marina Bay pilot batch's invoice IDs and ASP outcomes.

Do not blindly resend or retry Submitted invoices. Confirm their ASP state and obtain the supported recovery procedure first.

## Evidence
- marina-bay-import-234.json
- API response records in ../load-api.log
- Source rows: marina-bay-rows-234.json (if captured)
