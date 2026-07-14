# Prabha Dairy — Custom TDL Add-ons

Two standalone TDL files. Neither modifies `dairy.tcp` — both load
alongside it, the same way `test_tdl_diagnostic.tdl` already proved works.

- `Prabha_ReceiptFormat.tdl` — hierarchical Receipt voucher list + printable detail view
- `Prabha_BatchTraceability.tdl` — output batch → source batch traceability for manufacturing

## 1. Deploy in a SANDBOX first

Copy the company data folder to a test location. Do not point this at
the live `Data=` path until you've verified both files load cleanly and
behave as expected.

## 2. Load both files (two options)

**Option A — via Tally.ini (permanent):**
Add a second line under `[TALLY]` in `Tally.ini`, right after the
existing `TDL=D:\TALLY.ERP9\dairy.tcp` line:

```
TDL=D:\TALLY.ERP9\dairy.tcp
TDL=D:\TALLY.ERP9\Prabha_ReceiptFormat.tdl
TDL=D:\TALLY.ERP9\Prabha_BatchTraceability.tdl
```

**Option B — via F12 config (no ini editing, easy to toggle off):**
Gateway of Tally → `F12: Configure` → `Product & Features` →
`TDL & Add-On` → add both file paths under "List of TDL files to
preload". Restart Tally.

## 3. Confirm they loaded

Gateway of Tally → `F1: Help` → `TDL & Add-On` should list both new
objects. You should also see two new menu items on the Gateway of
Tally screen: **"Receipt Register (Custom)"** and **"Batch
Traceability (Manufacturing)"**.

## 4. Test each feature

**Receipt format:** Open "Receipt Register (Custom)", pick any Receipt
voucher, confirm the hierarchy (company header → identity → party →
instrument → ledger table → words → narration → signature) renders
correctly, then hit `P: Print` to confirm the printed layout matches.

**Batch traceability:** Open "Batch Traceability (Manufacturing)",
select Paneer (or whichever output item), and check one real
production entry. Read the `TESTING NOTES` block at the bottom of
`Prabha_BatchTraceability.tdl` — there are two orientation checks
(`IsDeemedPositive` Yes/No, and the voucher-type filter) that are
written using the standard Tally convention but should be verified
against your actual data, since a black-box `.tcp` (dairy.tcp) may
have customized how manufacturing entries are recorded.

## 5. Known dependency

The batch traceability report can only show batches that were
actually selected during data entry. If milk is currently consumed
without picking a batch, turn on "Maintain Batch-wise Details" for
those stock items and require batch selection at consumption — the
report will then start working for entries made after that change.
It cannot retroactively reconstruct batch data for past unbatched
entries.

## 6. If something collides with dairy.tcp

Every object in both files is prefixed `Prabha` specifically to avoid
name clashes. If Tally still throws a duplicate-definition error on
load, check `F1 → TDL & Add-On` for the conflicting object name and
rename the `Prabha` prefix throughout that file to something else
(e.g. `PDT2`).
