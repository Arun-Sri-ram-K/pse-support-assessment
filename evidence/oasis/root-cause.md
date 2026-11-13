# Oasis Builders – Duplicate Submission Investigation

## Impact
Customer reported a November workbook upload stuck in Processing, repeated upload attempts, and document-number errors.

Two November imports are present in the portal:
- Import 1: b31d7bdb-f34d-4b6a-87a9-7856107a6928
- Import 2: 35a2b35d-ef8f-4af6-9874-22d26f3c68aa

Each import contains 600 rows and 8 invoices. Both are Completed, with all 8 invoices Accepted.

## ASP verification
Seller Peppol ID: 0235:1003182746

Direct ASP query evidence: oasis-asp-documents.json

The ASP returned 16 accepted document records:
- 8 invoices with G4UT suffix, received around 15:13:19 UTC.
- 8 corresponding invoices with G4YD suffix, received around 15:15:27 UTC.

The underlying invoice numbers, buyers and amounts match between the two imports. Each corresponding submission has a different ASP message ID.

## Finding
The two observed imports resulted in duplicate submissions of the same 8 business invoices. This is confirmed by the ASP records, not merely by the portal status.

The inbox reports a third upload attempt that returned an error. No third November import appears in the imports query. Its outcome is therefore not established by the current evidence.

ASP acceptance confirms receipt by the ASP. It does not establish whether the buyers received, processed, or paid both copies.

## Customer-safe action
- Do not upload or retry the workbook again.
- Preserve both import IDs and all 16 ASP message IDs.
- Escalate to L2/Engineering and coordinate with the ASP on duplicate-document handling or withdrawal options.
- Confirm with the customer which invoice copies should remain authoritative.
- Do not claim that duplicate documents have been withdrawn or corrected until verified.
- Continue customer updates while the escalation is in progress.

## Evidence files
- oasis-import-1.json
- oasis-import-2.json
- oasis-asp-documents.json
