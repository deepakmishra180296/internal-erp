# Discovery: Payroll Module

Status: Draft. Built from the client brief (docs/requirements/payroll-module-requirements.md) and the client's answers to its four open questions. Items marked PROPOSED were not answered by the client and are working assumptions to confirm. They are listed again under Open Questions.

## Why
The organisation is building an internal ERP for India. Today employees have no single place to see payslips, tax statements and increment letters, or to raise reimbursement claims. HR has no single place to run monthly payroll, tax and increments. The Profile module (employee records) and the Leave module (unpaid leave) are being built separately, so payroll can reuse that data instead of asking for it again.

## Who
- **Employee**: sees only their own documents, dashboard and claims. A Lead is an employee here and has no extra access.
- **HR**: manages salary, payroll, tax, increments, claim types and claims for all employees. In the Profile module, HR is one built-in role, so there is a single HR role. PROPOSED: every HR user has the same full payroll access, including HR users' own payroll being run by another HR user where possible.
- **Nobody else** can see anyone's payroll.

## Success Criteria
- An HR user can run, review and publish one month of payroll for all employees, and every employee finds their own payslip on Home afterwards.
- An employee can never see another person's payroll data, by any screen or by typing an address.
- Published payroll never changes silently. Every fix is a visible correction with the original kept.
- An employee can raise a claim with documents, and see it through to Paid.
- Every payroll action can be traced to who, when and why.
- The prototype is a success when the main workflows run without defects. PROPOSED: at least 2 pods go through a full monthly cycle (same bar as the Leave module).

## Problem Statement
Employees cannot easily find their pay documents or claim expenses, and HR has no controlled way to run payroll with a clear history. Salary is highly sensitive, so privacy, locking after publish, and a full audit trail matter as much as the calculations. The prototype must prove the full cycle end to end with simple rules, and leave room for exact Indian rules later.

## Decisions Already Made (from the brief and the client's answers)
- Payroll is monthly. Payroll published on the 1st covers the previous month (client answered Yes).
- Employees can choose their tax regime (client answer). The old and new Indian regimes both exist. CONFIRMED: the employee picks once per financial year (or on joining). HR can view it and change it only with a logged reason. The change applies from the next payroll run.
- PF, professional tax and TDS are kept simple for the prototype. Exact rules and formulas come later (client answer). CONFIRMED: flat rates as described under Proposed Simple Rules.
- CONFIRMED: salary is entered as annual CTC and split automatically into monthly components.
- CONFIRMED: unpaid leave is deducted on a working-day basis.
- HRA, gratuity, ESI and similar components are out of scope for now (client answer).
- Published payroll is locked. Changes need a formal correction that issues a revised payslip and keeps the original.
- Claims are approved by HR only and paid separately from payroll. HR marks them Paid.
- Notifications are in-app only.
- Uploads are limited to 5 MB per file.
- Documents from before the system started are not uploaded and show as "not available".
- Indian tax and statutory rules apply.

## Hypotheses
- H1: A salary structure of basic, allowances, bonus, PF, professional tax, income tax (TDS) and other earnings and deductions is enough for the prototype. HRA, gratuity and ESI are not needed.
- H2: Simple rules (see Proposed Simple Rules) are good enough to prove the workflow. Exact tax and PF rules can be swapped in later without changing screens.
- H3: Unpaid leave days from the Leave module can be read directly, so HR does not retype them.
- H4: One HR role with full access is enough. No separate payroll approver is needed because a finance approval step is out of scope.
- H5: HR enters annual CTC and the system splits it into monthly components using set percentages (to be defined, see open question 8). Monthly figures are CTC divided by 12.
- H6: Locking after publish plus formal corrections is a better control than allowing edits with a log.
- H7: Employees will accept masked salary figures that reveal on click.

## Proposed Simple Rules (PROPOSED, to confirm)
These keep the prototype simple and make the rules testable. Each is a single setting HR or a developer can change later.
- **Pay period**: payroll published on the 1st covers the whole previous calendar month.
- **Gross pay**: basic + allowances + bonus + other earnings for the month.
- **PF**: a flat percentage of basic (12 percent suggested), taken from the employee. No employer share shown and no wage ceiling.
- **Professional tax**: a flat monthly amount set by HR (a single value, no state slabs).
- **TDS**: for each regime, a simple flat percentage of projected taxable annual income, spread over 12 months. Two regimes are available (old and new), each with its own percentage. No slabs, rebates or declarations.
- **Net pay**: gross pay minus PF, professional tax, TDS and other deductions, plus or minus one-off items.
- **Rounding**: every amount is rounded to the nearest whole rupee. PROPOSED.
- **Currency**: Indian rupees only.

## Unpaid Leave
CONFIRMED by the user: the deduction is based on working days. Details below the first bullet are still PROPOSED.
- Deduction per unpaid day = monthly gross pay divided by the number of working days in that month. Working days follow the Leave module's calendar (weekends and public holidays are not working days). Payroll needs the Leave module to expose this working-day count; to be confirmed.
- Count only approved Unpaid leave days that fall inside the pay period. Half-days count as 0.5.
- Weekends and public holidays inside an unpaid leave request follow the Leave module's own day count. Payroll does not recount them.
- Pending, rejected and cancelled leave never reduces pay.
- If unpaid leave is approved after payroll is published, the fix goes through a formal correction, not a silent change.
What the Leave module gives payroll (from docs/specs/leave-module-spec.md): Unpaid is a no-balance leave type, requests have dates, half-days and statuses, and public holidays reduce a request's day count. Payroll needs the approved Unpaid days per employee per month; this must be confirmed as something the Leave module will expose.

## New Joiner Proration (PROPOSED)
- First payslip pays for days from the joining date to the end of the pay period, divided by the calendar days in that month (joining date comes from Profile).
- Same proration formula applies to a salary change from an increment that takes effect mid-month: days before the effective date use the old salary, days from the effective date use the new salary.
- A person who joins after the pay period ends gets no payslip for that period.

## Claim Limits (PROPOSED)
- HR sets a limit per claim type, per year.
- The limit is measured as the total of the employee's claims of that type in the year, counting Submitted, Approved and Paid claims. Draft, Rejected and withdrawn claims do not count.
- "Year" means the financial year (April to March), in line with Indian tax. Needs confirmation, since Leave uses its own leave year.
- Also assumed: HR can set an optional per-claim maximum, left blank by default. A claim that would push the yearly total over the limit is blocked with an error showing the remaining amount.

## Correction Flow (PROPOSED)
1. HR opens a published payslip and starts a correction, entering a reason (required).
2. HR changes the needed amounts in a separate correction draft. The original stays unchanged and visible.
3. HR confirms the correction. A revised payslip is issued, marked as revised with a version number.
4. The employee is notified in-app and sees the revised payslip. The original remains in history for HR. PROPOSED: the employee sees the latest version on Home and can still open earlier versions.
5. The audit trail records who, when, why, and the before and after values.
- Any difference in pay (extra or short) is carried as a one-off item in the next month's payroll. No money transfer is handled in the system.

## Out of Scope
- Employee tax-saving and investment declarations.
- Password-protected payslip downloads.
- Full-and-final settlement (later, with the Separation module).
- A finance approval step before publishing.
- External connections: bank transfer files, statutory filings.
- Email notifications.
- Attendance tracking, performance-linked pay, loans and advances.
- HRA, gratuity, ESI and other components beyond the list above.
- Exact Indian rules for PF, professional tax and TDS (slabs, wage ceilings, rebates, surcharge, state differences). Prototype uses the simple rules above.
- Uploading documents from before the system started.
- Payment of claims inside the system (HR pays outside and marks Paid).
- Multiple currencies.

## Milestone Map
Each phase is a vertical slice a user can see and try. Depth is high for Phase 1 and rougher for later phases.

### Phase 1: Walking skeleton (HR publishes a payslip, the employee sees it)
- HR sets up an employee's salary (monthly components, tax regime).
- HR runs payroll for the previous month, reviews it, and publishes it.
- Simple PF, professional tax and TDS rules apply.
- Published payroll is locked.
- Employee opens Home, sees their own payslip by month and year, downloads it, and sees "not available" for missing documents.
- An employee cannot see anyone else's payslip, and non-HR users cannot open payroll management.
- Every action is recorded in the audit trail.

### Phase 2: Real-life payroll inputs
- Unpaid leave deduction from the Leave module.
- New joiner proration.
- One-off items: bonus, arrears, deductions.
- Formal correction with a revised payslip.
- In-app notification when payroll is published.

### Phase 3: Claims
- HR sets up claim types with yearly limits.
- Employee raises, saves as Draft, submits, withdraws and resubmits claims with mandatory documents up to 5 MB each.
- HR approves or rejects with comments, and marks claims Paid.
- Employee sees status, HR comments and action history, and is notified in-app.

### Phase 4: Dashboard and documents
- Employee dashboard with masked salary figures and reveal on click.
- Multi-select download of documents from Home.
- Tax statement and annual salary statement documents.

### Phase 5: Increments and tax views
- HR records increments with effective date, old and new salary, and reason; full history; increment letter on Home.
- Tax paid to date and projected tax for the year; employee's choice of tax regime.

### Phase 6: Reports and records
- Monthly cost, tax summary and claims summary reports, filtered by month, POD, client or employee, exported to Excel and PDF.
- HR views any employee's full payroll history.
- Searchable audit trail.

## Key Screens
- **Employee**: Home (documents by month and year, select and download), My Claims (list, new claim, claim detail with history), Dashboard (masked figures).
- **HR**: Salary Structure (per employee), Payroll Run (generate, review, publish, corrections), Taxation, Increments, Claim Types, All Claims, Reports, Employee Payroll Records, Audit Trail.

## Key Rules and Edge Cases (to turn into acceptance criteria in the spec)
Privacy and access
- Employee requests for another employee's payslip, claim or document are refused, including by typed address.
- Non-HR users get "Access denied" on every payroll management screen.
- Documents are served only to the owner or HR.
- Salary figures are masked by default on every screen and in lists, and reveal on a click. Masking is on screen only, it is not a security control.

Locking and integrity
- A published month cannot be run again or edited. A second publish attempt shows "already published" and changes nothing.
- Two HR users publishing at the same moment produce one set of payslips, not two.
- If generation fails halfway, no payslip is visible to employees and HR can safely run again.
- Publish is all or nothing for the month.
- Salary changes by an increment do not alter published months.

Payroll content
- An employee with no salary structure is flagged in review and blocks publish until HR fixes or excludes them. PROPOSED.
- Net pay can never go below zero. If deductions exceed gross, HR is warned in review. PROPOSED.
- Inactive employees (from Profile) are left out of new payroll runs. Employees made inactive mid-period: PROPOSED, still paid up to their last working day, and final settlement is out of scope.

Claims
- Documents are mandatory, 5 MB per file. A file over 5 MB shows an error and nothing is saved.
- Withdraw is allowed while Draft or Submitted. It is not allowed once Approved.
- A rejected claim shows the reason and can be corrected and resubmitted. The history keeps every version.
- Only Approved claims can be marked Paid. Two HR users acting on the same claim at the same moment end with one final status.
- Changing a claim type's limit does not change claims already submitted.
- Expense date cannot be in the future. PROPOSED. Older-claim cutoff is an open question.

Documents
- Missing documents show as "not available". Multi-select download of several documents works even when some are not available (those are skipped and named).
- Tax statement and annual salary statement are issued by HR, not automatically for every month.

Audit trail
- Records who, what, when and why for: salary change, payroll run, publish, correction, increment, tax regime change, claim type change, claim approve, reject, pay, and document generation.
- Audit entries cannot be edited or deleted by anyone.

## Module Structure
Not greenfield for the overall ERP. Payroll is a new module beside Profile and Leave.
- Reads from Profile: employees, joining date, bank details, UAN, client, POD, role, active status.
- Reads from Leave: approved Unpaid leave days per month.
- Payroll owns: Salary Structure, Payroll Run, Payslip (with versions and corrections), Tax Details, Increment, Claim Type, Claim, Document, Audit Entry.

## Open Questions
These could not be asked live because the interview tool was not available in this run. Each has a proposed answer above, so the build can start. Please confirm or change.
1. ANSWERED: unpaid leave uses working days. STILL OPEN: confirm half-days count as 0.5, and that the Leave module will give payroll approved Unpaid days and the working-day count per month.
2. PARTLY ANSWERED: flat rates chosen (PF 12 percent of basic, one flat professional tax amount, flat TDS percentage per regime). STILL OPEN: the professional tax amount and the TDS percentages for old and new regime.
3. ANSWERED: employee picks once per financial year, HR can change with a logged reason, effective from the next payroll run. STILL OPEN: can an employee change it again mid-year in special cases?
4. New joiner proration: confirm days worked divided by calendar days in the month.
5. HR users: confirm one HR role with equal access, and whether HR may run their own payroll.
6. Claim limit: confirm per type per financial year, which statuses count, and whether there is also a per-claim maximum.
7. Correction flow: confirm the steps above, and whether the employee sees both versions.
8. ANSWERED: annual CTC is entered and split automatically. STILL OPEN: the split percentages (for example basic as a share of CTC, allowances, bonus), whether HR can adjust the split per employee, and whether CTC includes PF.
9. Claim age: is there a cutoff for how old an expense can be?
10. When payroll is published on the 1st, can HR publish early or late, or only on the 1st? What if the 1st is a holiday or weekend?
11. Who gets the in-app notification when a claim is submitted: all HR users or only the assigned HR from Profile?
12. Tax statement and annual salary statement: when are they generated, and for which period (financial year)?
13. Document formats: payslips and letters as PDF only?
14. Does the first prototype need real employee data, or sample data only? (Affects how strictly privacy controls must be proved before release.)

## Revised Assessment
Size: EPIC
Risk: HIGH (salary data privacy, locking after publish, audit trail, data integrity of calculations)
Greenfield: no (new module beside Profile and Leave)

[ ] Reviewed
