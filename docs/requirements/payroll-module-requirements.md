# Payroll Module: Client Requirement Brief

## Purpose
We are building an internal ERP for our organisation in **India**. This brief covers the **Payroll Module**, which lets employees see their pay documents and raise reimbursement claims, and lets HR run payroll, taxation, and increments. Please act as a Business Analyst: read this, ask me clarifying questions, then produce a full spec sheet.

## Users
- **Employee**: sees only their own pay documents, dashboard, and claims.
- **HR**: manages payroll, tax, increments, claim types, and claims for all employees.
- **Other employees and Leads**: have no access to anyone else's payroll information.

## How This Fits With Other Modules
- **Profile module**: provides employee records, bank details, UAN, and organisation details (client, POD, role).
- **Leave module**: unpaid leave may affect salary for the month.
- Both are built separately. Payroll should reuse their data rather than ask for it again.

## Decisions Already Made
- Payroll data is **strictly private**. An employee can never see another employee's data.
- Payroll is run **monthly** and published on the **1st of every month**.
- Salary figures are **masked on screen** by default, with a click to reveal.
- Once payroll is published, it is **locked**. Any change needs a formal correction, with the original kept in history.
- Claims are approved by **HR only**, and reimbursed **separately** from payroll.
- Notifications are **in-app only** (no email for now).
- All uploads (e.g. claim documents) are limited to **5 MB per file**.
- Documents from before the system started are **not uploaded**. They show as not available.
- Tax and statutory rules follow **Indian** requirements.

## Sub Modules

### 1. Home (Employee)
- Employee sees documents for each month where available:
  - Payslip
  - Tax statement
  - Annual salary statement
  - Increment letter
- Documents are organised by month and year, so older ones are easy to find.
- Employee can select **one or many documents** and download them together.
- Missing documents are clearly shown as not available.

### 2. Claim (Employee)
- Employee raises a claim by choosing a claim type, expense date, amount, description, and supporting documents.
- Supporting documents are mandatory.
- Claim types and their limits are set by HR (see Payroll Management). The employee is stopped if a claim goes over the limit.
- Employee sees every claim raised so far, with status: Draft, Submitted, Approved, Rejected, Paid.
- Each claim shows HR comments and a history of actions.
- Employee can withdraw a claim that is not yet approved.
- Rejected claims show the reason, and the employee can correct and resubmit.

### 3. Dashboard (Employee)
- Shows only the logged-in employee's own details.
- Suggested content: latest net pay, annual salary, tax paid so far this year, recent payslips, claim summary (pending, approved, paid), and latest increment.
- Salary figures are masked until the employee reveals them.

### 4. Payroll Management (HR only)

**Salary structure**
- Set up each employee's salary with components: basic, allowances, bonus, PF, professional tax, income tax (TDS), and other earnings and deductions.
- HR can add, update, and view components.

**Payroll generation**
- Run payroll monthly for all employees, review it, then publish it on the 1st.
- Payslips are generated automatically and appear on the employee's Home once published.
- Handle one-off items: bonus, arrears, deductions.
- Adjust pay for unpaid leave from the Leave module.
- After publishing, payroll is locked. HR makes changes only through a formal correction that issues a revised payslip.

**Taxation**
- Maintain tax details per employee under Indian tax rules.
- Generate tax statements for each employee.
- Show tax paid to date and projected tax for the year.

**Increments**
- Record each increment: effective date, old and new salary, reason.
- Keep full increment history per employee.
- Generate an increment letter the employee can download from Home.

**Claim types and claims**
- HR can add, update, and view claim types (e.g. travel, medical, internet) and set limits per type and per year.
- HR sees all claims, approves or rejects with comments, and marks them as Paid once paid separately.

**Reports**
- Payroll reports such as monthly cost, tax summary, and claims summary.
- Filter by month, POD, client, or employee.
- Export to Excel and PDF.

**Records**
- View any employee's full payroll history: salary, payslips, tax, increments, and claims.
- Every action is recorded: who did what, when, and why.

## Key Workflows
1. **Monthly payroll**: HR sets up inputs → runs payroll → reviews and corrects → publishes on the 1st → payroll locks → payslips appear → employees notified in-app.
2. **Download documents**: Employee opens Home → picks month(s) and document(s) → downloads.
3. **Raise a claim**: Employee picks type, adds details and documents → system checks limit → submits → HR notified → HR approves or rejects → employee notified → HR pays separately and marks Paid.
4. **Claim type setup**: HR adds or updates a claim type and its limits → available to employees from then on.
5. **Increment**: HR records increment → salary updated from the effective date → increment letter generated → employee notified.
6. **Tax statement**: HR generates tax statements → they appear on employee Home.
7. **Correction**: HR spots an error after publishing → raises a formal correction → revised payslip issued, original kept in history.
8. **New joiner**: HR sets up salary structure → first payslip is prorated for days worked.

## Questions Still Open (please ask me)
- Indian tax has more than one regime. Is there one regime for everyone, or can employees choose? - Employee can choose.
- Is the payroll published on the 1st for the previous month? Which days count when unpaid leave is deducted? - Yes 
- What are the exact formulas and rules for PF, professional tax, and TDS? - Currently keep the rules simple for the prototype exact rules and calculation can be added later.
- Are there salary components beyond those listed, such as HRA, gratuity, or ESI? - Out of scope currently, for now keep the rule simple.

## Out of Scope (for now)
- Employee tax-saving and investment declarations.
- Password-protected payslip downloads.
- Full-and-final settlement (later, with the Separation module).
- A finance approval step before payroll is published.
- Connections to external systems (bank transfer files, statutory filings).
- Email notifications, attendance tracking, performance-linked pay, loans and advances.

## What I Need From You
1. Ask me questions to fill any gaps, starting with the ones above.
2. Produce a spec sheet with user stories, screens, rules, workflows, and edge cases.
3. Keep language simple so business and technical teams both understand it.
