# Al Noor Fuel – BR-CO-15 Investigation

## Impact
November import contained 6 invoices:
- 3 Accepted
- 3 Rejected by ASP
- All 3 rejected invoices returned BR-CO-15.

## Evidence
Import ID: 99713aa0-5cba-478d-812f-4715e1831dfa

Invoice discrepancies:
| Invoice | Net AED | VAT AED | Expected gross AED | Portal gross AED |
|---|---:|---:|---:|---:|
| ANF-2611-0042-G4YD | 14307.08 | 715.35 | 15022.43 | 15022.42 |
| ANF-2611-0044-G4YD | 11254.84 | 562.75 | 11817.59 | 11817.57 |
| ANF-2611-0046-G4YD | 5343.80 | 267.20 | 5611.00 | 5610.99 |

Source workbook line net amounts match the generated invoice payloads.
Per-line VAT calculations using ROUND_HALF_UP match the portal's VAT values.

## Root cause
In services/api/internal/pint/pint.go, computeTotals() calculates:
- Net and VAT by summing line amounts and per-line VAT.
- Gross independently using quantity * unit price * (1 + VAT rate).

The independent gross calculation and separate rounding produce an inconsistent invoice total.

The ASP validates that inclusive total equals exclusive total plus VAT and rejects the mismatch under BR-CO-15.

## Recommended fix
Calculate inclusive total consistently from rounded net plus rounded VAT.
Prefer fixed-point/decimal monetary arithmetic over float64.
Add regression tests covering all three rejected invoices.

## Customer-safe action
Do not ask the customer to alter correct source line values to compensate for a portal calculation issue.
Keep the three rejected invoices on hold pending engineering correction and a safe resubmission plan.
The three accepted invoices should not be resent.
