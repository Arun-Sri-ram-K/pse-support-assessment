# Submission

- **Name:** Arun Sriram K
- **Date submitted:** 13 November 2026
- **Repository link:** Add repository URL after submission
- **Total elapsed time from 6:52 PM to 10:05 PM:** 3 hours 13 minutes, including breaks; active work time was not separately tracked

> Customer replies below are drafts using the exercise clock: Monday 16 November 2026, Asia/Dubai. Lab execution timestamps are separate and are from 13 November 2026 UTC.

## Initial triage and acknowledgements

### Incident 1 — Oasis Builders

**Acknowledgement time:** 09:15
**Priority:** P2
**Reason:** Multiple upload attempts, uncertain invoice submission state and month-end business impact.
**SLA:** First response within 1 hour; next update by 11:15; resolution target 8 hours.

**Customer reply:**

Hello Rashid,

We understand that the November workbook is stuck in Processing and that repeated uploads have resulted in duplicate-number warnings. We are checking the import records and ASP submission references to identify exactly which invoices were accepted and which remain pending.

Please avoid uploading the workbook again or retrying invoices until reconciliation is complete. I will share an update by 11:15.

Regards,
Product Support

### Incident 2 — API restarts / Marina Bay

**Acknowledgement time:** 09:55
**Sent to:** Internal support / on-call team
**Initial priority:** P1 provisional
**Reason:** Critical production restart alert and reports of intermittent 502 responses. P1 applies if production invoice submissions are completely blocked.
**SLA:** First response within 15–30 minutes; next update by 10:25; resolution target 4 hours if P1 is confirmed.

**Acknowledgement:**

The API restart alert and reports of intermittent 502 responses have been received. I am checking API health, restart information, invoice statuses and ASP submission responses. If a production-wide submission blockage is confirmed, Engineering must be paged immediately.

### Incident 3 — Al Noor Fuel

**Acknowledgement time:** 10:25
**Priority:** P2
**Reason:** A subset of invoices is rejected by the ASP, while others are accepted. The issue also affects an ongoing audit.
**SLA:** First response within 1 hour; next update by 12:25; resolution target 8 hours.

**Customer reply:**

Hello Fatima,

We are reviewing the rejected invoices and comparing their submitted totals with the accepted invoices. We will identify the exact validation differences and share the required correction steps. Please hold off on re-uploading the rejected invoices until we complete the review.

I will provide an update by 12:25.

Regards,
Product Support

### Incident 4 — Sahara Medical

**Acknowledgement time:** 11:40
**Priority:** P2
**Reason:** A large batch has mixed Accepted and Submitted statuses, and the customer cannot confirm delivery to the buyer.
**SLA:** First response within 1 hour; next update by 13:40; resolution target 8 hours.

**Customer reply:**

Hello Daniel,

We have received your report regarding the 40-invoice batch. We are checking the invoice-level statuses and ASP responses to identify which invoices have been accepted and which are still pending. Please do not resend the batch until reconciliation is complete, as this could create duplicate submissions.

I will share an update by 13:40.

Regards,
Product Support

### Incident 5 — Ghaf Tree Café

**Acknowledgement time:** 12:20
**Priority:** P3
**Reason:** The issue affects one customer and is caused by a documented date-format validation requirement.
**SLA:** First response within 4 business hours; next update by 16:20; resolution target 3 days.

**Customer reply:**

Hello Hessa,

We checked the rejection reason. The portal requires invoice issue dates and payment due dates in YYYY-MM-DD format. Please update the dates in both columns (for example, 13/11/2026 to 2026-11-13) and upload the corrected workbook.

We will be happy to verify the corrected upload.

Regards,
Product Support

---

## Investigation 1 — Oasis Builders

### Investigation log

| Time (exercise clock) | What I checked / did | What I found |
|---|---|---|
| 09:20 | Reviewed the import lifecycle and duplicate-number rules. | A Completed import does not guarantee ASP acceptance. Invoice-level statuses and ASP message IDs must be checked. |
| 09:35 | Inspected the November test imports. | Two separate test imports were found, each containing 8 invoices. |
| 09:45 | Checked invoice-level statuses. | All 8 invoices in each test import were Accepted. |
| 10:00 | Queried ASP documents using the seller participant ID. | 16 accepted ASP document records were returned across the two test runs. |
| 10:10 | Compared invoice numbers and identifiers. | The two runs used different test suffixes, G4UT and G4YD, and generated separate invoice and ASP message IDs. |

### Root cause and findings

- **What happened:** Two test uploads of the same underlying eight-invoice business sequence resulted in 16 accepted ASP documents.
- **Where:** Import processing, invoice submission and ASP mock records.
- **Why it matters:** Repeated uploads can result in multiple accepted documents. However, the evidence does not establish that the same exact Peppol invoice identifiers were submitted twice.
- **Evidence:**
  - [First import details](evidence/oasis-import-1.json)
  - [Second import details](evidence/oasis-import-2.json)
  - [ASP document records](evidence/oasis-asp-documents.json)
  - [Oasis investigation note](evidence/oasis/root-cause.md)
- **Confidence and open questions:** High confidence that both test runs were accepted. The third upload attempt mentioned by the customer was not identified in the available test records. ASP acceptance does not establish whether the buyer processed or paid duplicate documents.

### Escalation note

**Escalation to:** L2 Support / Engineering, with ASP coordination if required.

**Priority:** P2.

**Issue:** Customer reports repeated uploads and uncertainty about duplicate submissions. Two available test runs each generated eight accepted ASP documents.

**Evidence:** Import IDs:
- `b31d7bdb-f34d-4b6a-87a9-7856107a6928`
- `35a2b35d-ef8f-4af6-9874-22d26f3c68aa`

**Action requested:** Reconcile the customer's actual production import IDs and ASP message IDs, confirm whether any duplicate business documents exist, and advise on available corrective or withdrawal options. Do not assume withdrawal is possible.

**Customer update:** We have identified two accepted test submissions containing the same underlying invoice sequence. We are reconciling the original production records and ASP references before advising on any corrective action. Please do not upload the workbook again until this review is complete.

### Workaround offered

Do not upload or retry the workbook again until invoice-level reconciliation is complete. Preserve existing import IDs and ASP references.

### Prevention ideas

- Add duplicate-upload warnings before processing.
- Provide invoice-level submission status and ASP message IDs.
- Make import reconciliation easier for customers and support teams.
- Provide clear guidance against repeated uploads while processing is ongoing.

---

## Investigation 2 — API restarts / Marina Bay

### Investigation log

| Time (exercise clock) | What I checked / did | What I found |
|---|---|---|
| 09:55 | Reviewed the critical API restart alert and reported 502 errors. | Treated as provisional P1 pending confirmation of production-wide submission impact. |
| 10:05 | Checked Docker service status and API health. | API was running and healthy during the lab inspection. |
| 10:15 | Inspected API container restart metadata. | RestartCount was 2, ExitCode was 0 and OOMKilled was false. The exact restart cause was not established. |
| 10:25 | Reviewed API and web logs for errors and 502 responses. | No confirmed 502 response or panic/fatal error was identified in the captured logs. |
| 10:35 | Inspected a six-invoice Marina Bay test import. | Three invoices were Accepted and three remained Submitted without ASP message IDs. |
| 10:45 | Correlated the three pending invoices with API submission logs. | All three received HTTP 429 responses from the ASP mock. |

### Root cause and findings

- **What happened:** The available test batch experienced partial submission. Three invoices were accepted and three received ASP rate-limit responses (HTTP 429), leaving them in Submitted status without message IDs.
- **Where:** API-to-ASP submission path and ASP mock rate limiting.
- **Why it happens:** The ASP mock returned HTTP 429 for the three pending invoices. The API container had restarted twice, but the available evidence does not establish why those restarts occurred.
- **Evidence:**
  - [Marina Bay import details](evidence/marina-bay-import-234.json)
  - [API logs from load test](evidence/load-api.log)
  - [Marina Bay investigation note](evidence/marina-bay/root-cause.md)
- **Confidence and open questions:** High confidence in the three HTTP 429 responses and partial acceptance in this test batch. The actual cause of the API restarts and the customer's original production 502 reports remain unconfirmed. The lab batch is not proof of the original production incident.

### Escalation note

**Escalation to:** L2 Support and Engineering.

**Priority:** P2 provisional based on partial acceptance in the available test batch. Raise to P1 immediately if production evidence confirms that no invoices are reaching the ASP.

**Evidence:** Test import ID `feabef0d-49d9-47a0-a1ea-a19c81a59153`.

Pending invoices:
- `MBH-1116-G4YD-234-02`
- `MBH-1116-G4YD-234-05`
- `MBH-1116-G4YD-234-06`

**Action requested:** Investigate API restart causes, production 502 reports, ASP throttling, retry/backoff handling and safe recovery of pending invoices. Confirm the original production import and invoice IDs before taking recovery action.

**Customer update:** Our investigation of the available test batch shows that some invoices were accepted while others encountered ASP rate limiting. We are checking the original production records and coordinating the safe recovery of pending invoices. Please do not resend the batch until reconciliation is complete.

### Workaround offered

Avoid resending the batch. Preserve the existing invoice IDs and message references while L2/Engineering confirms the safe recovery process.

### Prevention ideas

- Implement controlled retry and backoff for ASP HTTP 429 responses.
- Improve monitoring for API restarts and HTTP 502 responses.
- Alert on invoices remaining Submitted without ASP message IDs.
- Provide better visibility into pending and accepted invoice counts.

---

## Investigation 3 — Al Noor Fuel

### Investigation log

| Time (exercise clock) | What I checked / did | What I found |
|---|---|---|
| 10:25 | Reviewed the import and invoice-level statuses. | Six invoices were processed: three Accepted and three Rejected. |
| 10:40 | Inspected the ASP rejection responses. | All three rejected invoices failed BR-CO-15. |
| 10:55 | Compared source workbook values and generated invoice payloads. | Source line amounts matched the generated payloads. |
| 11:10 | Inspected VAT and total calculation logic in the API code. | VAT was rounded per line, but gross total was calculated independently. |
| 11:25 | Compared the ASP expected totals with the submitted totals. | Differences of AED 0.01, AED 0.02 and AED 0.01 were identified. |

### Root cause

- **What happened:** Three invoices were rejected by the ASP because the submitted inclusive total did not equal the sum of the net total and VAT total.
- **Where:** `services/api/internal/pint/pint.go`, particularly the VAT and `computeTotals` logic around lines 167–185.
- **Why it happens:** The code calculates rounded VAT amounts separately but calculates the gross amount independently using quantity × unit price × (1 + VAT rate). Rounding at different stages can produce a one- or two-fils discrepancy.
- **Evidence:**
  - [Import details](evidence/al-noor-import-details.json)
  - [Accepted invoice 0041](evidence/al-noor/accepted-0041.json)
  - [Rejected invoice 0042](evidence/al-noor/rejected-0042.json)
  - [Rejected invoice 0044](evidence/al-noor/rejected-0044.json)
  - [Rejected invoice 0046](evidence/al-noor/rejected-0046.json)
  - [Root cause analysis](evidence/al-noor/root-cause.md)
- **Confidence and open questions:** High confidence. The ASP rejection values match the submitted calculation discrepancy, and the code inspection identifies the inconsistent calculation approach. A code fix and regression tests are still required; no code change has been made.

### Rejection details

| Invoice | ASP expected total | Submitted total | Difference |
|---|---:|---:|---:|
| ANF-2611-0042-G4YD | AED 15,022.43 | AED 15,022.42 | AED 0.01 |
| ANF-2611-0044-G4YD | AED 11,817.59 | AED 11,817.57 | AED 0.02 |
| ANF-2611-0046-G4YD | AED 5,611.00 | AED 5,610.99 | AED 0.01 |

### Escalation note

**Escalation to:** L3 Engineering, with L2 Support coordination.

**Priority:** P2.

**Issue:** ASP rejects three Al Noor invoices with BR-CO-15 because invoice total including VAT does not equal total net plus VAT.

**Evidence:** Import ID `99713aa0-5cba-478d-812f-4715e1831dfa`; see the rejection evidence above.

**Action requested:** Review the total calculation in `services/api/internal/pint/pint.go`. Use consistent fixed-point or decimal arithmetic and derive the inclusive total from the net and VAT totals. Add regression tests for the three failing cases and verify that accepted invoice calculations remain unchanged.

**Customer update:** We identified a discrepancy in the invoice total calculation that causes the ASP to reject the affected invoices. We have escalated the calculation issue to Engineering for review. We will confirm the correction and safe resubmission steps after validation.

### Workaround offered

Do not manually alter invoice totals without finance approval. Hold the rejected invoices until Engineering confirms a corrected calculation and Support verifies the regenerated payloads.

### Prevention ideas

- Use consistent decimal arithmetic for financial calculations.
- Derive the inclusive total from the rounded net and VAT totals.
- Add regression tests for BR-CO-15 and other VAT total rules.
- Compare generated totals against ASP validation rules before submission.

---

## Investigation 4 — Sahara Medical

### Investigation log

| Time (exercise clock) | What I checked / did | What I found |
|---|---|---|
| 11:40 | Reviewed the 40-invoice batch and customer report. | Customer received HTTP 201 for the import, but buyer receipt was incomplete. |
| 11:55 | Inspected the two available November test imports. | Each contained 40 invoices and completed import processing. |
| 12:10 | Checked invoice-level statuses. | Each test run had 8 Accepted and 32 Submitted invoices. |
| 12:20 | Compared API submission responses. | The 8 accepted invoices received HTTP 200; the 32 pending invoices received HTTP 429. |
| 12:35 | Inspected an accepted invoice and a pending invoice. | Accepted invoice had an ASP message ID; pending invoice had no ASP response or message ID. |

### Root cause and findings

- **What happened:** The available test batches show partial ASP acceptance. In each run, 8 invoices were accepted and 32 remained Submitted following ASP HTTP 429 responses.
- **Where:** API-to-ASP submission path and ASP rate limiting.
- **Why it happens:** The ASP mock rate-limited submissions. A Submitted status without an ASP message ID does not confirm acceptance or buyer delivery.
- **Evidence:**
  - [Sahara import details](evidence/sahara/import-details.json)
  - [Pending invoice details](evidence/sahara/invoice-009-submitted.json)
  - [Accepted invoice details](evidence/sahara/invoice-001-accepted.json)
  - [G4YD response log](evidence/sahara/g4yd-invoice-responses.log)
  - [G4UT response log](evidence/sahara/g4ut-invoice-responses.log)
  - [Invoice response summary](evidence/sahara/invoice-responses.log)
- **Confidence and open questions:** High confidence in the HTTP 429 responses and mixed statuses in the test batches. The original production batch and buyer-side receipt records still need reconciliation.

### Escalation note

**Escalation to:** L2 Support and Engineering.

**Priority:** P2.

**Issue:** 32 invoices in each available test run remain Submitted after ASP rate limiting.

**Action requested:** Reconcile the original production invoice IDs against ASP document records. Confirm whether pending invoices were subsequently accepted and establish a safe retry or recovery process. Do not resend the full connector batch.

**Customer update:** We confirmed that the available test batches contain both accepted and pending invoices. The pending invoices encountered ASP rate limiting and do not have ASP message IDs in the captured evidence. We are coordinating reconciliation and safe recovery. Please continue to hold off on resending the batch.

### Workaround offered

Do not resend the entire batch. Preserve the existing invoice IDs and wait for L2/Engineering to confirm the recovery process. The current Submitted status does not support treating the invoices as accepted.

### Prevention ideas

- Implement controlled retry and backoff for HTTP 429 responses.
- Alert on prolonged Submitted statuses.
- Expose ASP message IDs and submission responses in the portal.
- Provide a reconciliation report for large batch uploads.

---

## Investigation 5 — Ghaf Tree Café

### Investigation log

| Time (exercise clock) | What I checked / did | What I found |
|---|---|---|
| 12:20 | Reviewed the import details. | Four invoices were rejected during portal validation. |
| 12:35 | Inspected invoice validation errors. | All four invoices had AE-BR-04 date-format errors. |
| 12:45 | Checked the date fields. | Both issue date and payment due date used DD/MM/YYYY rather than YYYY-MM-DD. |
| 13:00 | Compared the source dates with the expected format. | All eight date fields could be converted to ISO format without changing their calendar dates. |

### Root cause

- **What happened:** All four invoices were rejected before submission to the ASP.
- **Where:** Portal invoice validation, AE-BR-04.
- **Why it happens:** The workbook uses DD/MM/YYYY date values. The portal requires YYYY-MM-DD or valid Excel date cells.
- **Evidence:**
  - [Import details](evidence/ghaf-import-details.json)
  - [Invoice validation errors](evidence/ghaf-invoice-errors.txt)
  - [Root cause analysis](evidence/ghaf/root-cause.md)
- **Confidence and open questions:** High confidence. All four invoice rejections show the documented validation rule. No invoice reached the ASP.

### Customer resolution reply

Hello Hessa,

We identified the issue in both the Invoice issue date and Payment due date columns. The portal requires dates in YYYY-MM-DD format.

Please use these corrections:

| Invoice | Issue date | Due date |
|---|---|---|
| GTC-118 | 2026-11-13 | 2026-12-13 |
| GTC-119 | 2026-11-13 | 2026-12-13 |
| GTC-120 | 2026-11-14 | 2026-12-14 |
| GTC-121 | 2026-11-15 | 2026-12-15 |

Once both date columns are corrected, please upload the workbook again. These invoices were rejected by portal validation and were not submitted to the ASP.

Regards,
Product Support

### Workaround offered

Correct both date columns using ISO format or valid Excel date cells, then upload the corrected workbook.

### Prevention ideas

- Add date-format guidance beside the upload template.
- Validate date columns before starting import processing.
- Show the affected row and field in validation errors.
- Provide a downloadable validation report.

---

## Overall assessment

### Prioritisation

I initially treated the API restart alert as provisional P1 because it involved a critical production alert and reports of intermittent 502 errors. After investigating the available test evidence, I found partial ASP acceptance and HTTP 429 responses rather than confirmed total submission failure. Therefore, P2 was appropriate for the evidence available, with instructions to raise it immediately if production-wide blockage is confirmed.

Oasis Builders, Al Noor Fuel and Sahara Medical were handled as P2 because they involved multiple invoices, submission uncertainty or partial rejection. Ghaf Tree Café was P3 because it was an isolated, documented date-format validation issue.

I prioritised business impact, invoice delivery risk, customer urgency and the applicable SLA rather than simply following the order of arrival.

### What I would do differently

- Capture original production import IDs and invoice IDs before testing.
- Preserve all relevant API and ASP logs at the beginning of an investigation.
- Build a clear invoice-level reconciliation report.
- Avoid repeated test uploads when investigating duplicate-submission reports.
- Confirm production impact separately from synthetic lab results.
- Validate financial calculations with regression tests before deployment.

### What I learned / what I would need to learn

This exercise helped me understand the end-to-end e-invoicing workflow, including workbook import, invoice validation, PINT AE generation, ASP submission and buyer-side delivery.

I also gained practical experience investigating API responses, container health, Docker logs, invoice statuses, ASP rate limiting and financial validation errors.

My next learning areas would be Peppol/PINT AE specifications, decimal arithmetic and VAT validation, distributed-system retry mechanisms, observability with Grafana and Loki, and safe production incident recovery.

### Time log (real time)

Enter your actual working time. Do not use the simulated exercise timestamps in this table.

| Date | From–to | What I worked on |
|---|---|---|
| 13 November 2026 | Not separately tracked | Environment setup, Docker lab and baseline testing |
| 13 November 2026 | Not separately tracked | Load testing and investigation of all five cases |
| 13 November 2026 | Not separately tracked | Evidence collection and submission documentation |

### AI usage disclosure

I used ChatGPT to help structure the investigation, explain technical concepts, interpret selected logs and code, and prepare customer communication and documentation drafts.

I verified the technical findings against the local lab environment, API responses, database records, Docker logs, ASP mock responses and application source code. The conclusions distinguish observed evidence from assumptions and unresolved questions. No customer communication was sent and no real external system was contacted.

---
