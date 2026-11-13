# Ghaf Tree Café – Invoice Date Format Rejection

## Import evidence
- Import ID: 723c89fc-d516-430b-b391-ba28517b79cc
- Seller: Ghaf Tree Café LLC
- File: ghaf-cafe-nov-2026.xlsx
- Rows: 8
- Invoices: 4
- Import status: Completed
- Results: 4 Rejected, 0 Accepted

A second test import of the same workbook also resulted in 4 rejected invoices.

## Confirmed root cause
All four invoices failed portal validation with AE-BR-04.

Both Invoice issue date and Payment due date must use YYYY-MM-DD format.

| Invoice | Issue date | Payment due date |
|---|---|---|
| GTC-118-G4YD | 13/11/2026 | 13/12/2026 |
| GTC-119-G4YD | 13/11/2026 | 13/12/2026 |
| GTC-120-G4YD | 14/11/2026 | 14/12/2026 |
| GTC-121-G4YD | 15/11/2026 | 15/12/2026 |

The validation errors explicitly identify both date fields. No invoice reached the ASP.

## Customer guidance
Ask the customer to correct both date columns:
- 13/11/2026 -> 2026-11-13
- 13/12/2026 -> 2026-12-13
- 14/11/2026 -> 2026-11-14
- 14/12/2026 -> 2026-12-14
- 15/11/2026 -> 2026-11-15
- 15/12/2026 -> 2026-12-15

Use ISO date text or valid Excel date cells, then upload the corrected workbook.

## Priority
P3 – single-customer file-format issue.

## Evidence
- ../ghaf-import-details.json
- ../ghaf-invoice-errors.txt
