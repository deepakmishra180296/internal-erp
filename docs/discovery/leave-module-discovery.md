# Discovery: Leave Module (Internal ERP Prototype)

Status: Client answers incorporated. Based on the client brief (leave-module-requirements.md), the client's review comments, and a follow-up interview. No open questions remain.

## Why
The organisation is building an internal ERP and wants to start with leave management as a prototype. The current leave application has many defects and limited flexibility, so the organisation is building its own.
<!-- -------- bee-comment -------- -->
> **@developer**: Current application has lot of issues hence devloping our own.
> **@bee**: Replaced the assumption about how leave is tracked today with the stated reason: the current application has many defects and little flexibility.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->

## Who
- **Employee**: applies for leave, cancels it, and tracks balance and status.
- **Lead / Manager**: approves leave for the people who report to them. A Lead's own leave goes to the Lead's reporting person.
- **HR**: manages leave types, quotas, holidays and the yearly reset. HR can also approve leave (in special cases, with a reason), adjust balances, apply leave on someone's behalf, and view reports and audit history. HR's own leave is approved by HR's reporting person.

Scale: about 200 to 300 employees. Each pod has 6 to 8 employees. The number of pods changes over time, so pods must be easy to add and change.
<!-- -------- bee-comment -------- -->
> **@developer**: the application is build for approx 200 - 300 employees. And POD numbers keep changing but each POD is 6-8 employees.
> **@bee**: Added the headcount and pod size. Noted that pods change over time, so the module must not assume a fixed list of pods.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->

## Success Criteria
- An employee can apply for leave, see it approved or rejected, and see the balance update without asking anyone.
- A leave balance is never negative and never double-counted, even when two actions happen at the same time.
- Approvers can clear their pending requests from one list, and no request is left unanswered past the escalation time.
- HR can answer "who is off today?" and "how much leave did pod X use this year?" without a spreadsheet.
- HR can export leave reports to Excel and PDF. A payroll-specific layout is handled later (see Out of Scope).
- Every leave action can be traced: who did it, when, and why.
- Prototype success: the major workflows are covered and work without bugs or defects. A pilot of at least 2 pods runs the full cycle of apply, approve and cancel.
<!-- -------- bee-comment -------- -->
> **@developer**: Cover major workflows with bugs or defects. / yes atleat 2 PODS
> **@bee**: Read "with bugs or defects" as "without bugs or defects" (please correct me if that is wrong). Set the prototype success measure to major workflows covered and working, with a pilot of at least 2 pods.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->

## Problem Statement
The current leave application has many defects and limited flexibility. Employees do not always know their balance, approvers lack the information to decide, and HR cannot easily see usage. The Leave Module gives one place to request, approve and track leave under clear rules, with a full history of every action.

## Decisions Made

**From the brief**
- Leave year is the calendar year (January to December).
- Each employee belongs to exactly one pod. Leave can be viewed and reported by pod.
- Each employee has one reporting person who approves their leave. Only one approver per employee, no second-level approval.
- Public holidays are the same for everyone (no location differences).
- Balance cannot go negative. Advance leave is not allowed.
- A new request is blocked if the balance is too low (for leave types that have a balance) or the dates overlap existing leave.
- Pending requests can be cancelled any time. Approved leave can be cancelled before it starts and the balance is restored.
- Leave types are managed by HR only.
- Email and in-app notifications on submitted, approved, rejected and cancelled.
- Reports by employee, pod, leave type and date range, exported to Excel and PDF.
- Audit history is visible to HR only, kept for the life of the system, and never edited or deleted.
- Rules are one global set per leave type (no per-grade or per-group policies).

**From the client's review**
- Weekends and public holidays inside a date range are not counted as leave days.
<!-- -------- bee-comment -------- -->
> **@developer**: yes weekends are not counted same as public holidays. (also: H3 "Yes correct")
> **@bee**: Confirmed as a decision. Work week is Monday to Friday.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- Half-day is 0.5 day. A half-day request must say whether it is the first half or the second half of the day.
<!-- -------- bee-comment -------- -->
> **@developer**: half day can be for first or the second half and needs to be mentioned. (H4: yes correct)
> **@bee**: Replaced my proposal ("single day only") with your rule: half-day must name first or second half. It is still open whether a half-day can be the start or end of a multi-day range (see Remaining Open Questions).
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- Backdated leave: an employee can apply for past dates within the same month. Beyond that, only HR can apply on their behalf.
<!-- -------- bee-comment -------- -->
> **@developer**: yes limited to same month or beyond that only HR can apply.
> **@bee**: Replaced my "7 days back" proposal with "same calendar month".
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- There is no limit on the number of consecutive days and no advance-notice rule. The approver decides and can reject.
<!-- -------- bee-comment -------- -->
> **@developer**: as of now there is not limit on number of days, if not acceptable , approver might reject it.
> **@bee**: Removed max consecutive days and advance notice from the rules for now. They drop out of the leave type settings and out of Phase 2.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- Every employee reports to someone, including HR. The head of the organisation does not apply for leave in the system.
<!-- -------- bee-comment -------- -->
> **@developer**: yes every employee reports to someone even the HR and he would approve it. For head of the organisation they need not apply it alltogether so can be ignored as of now.
> **@bee**: Recorded: HR's leave goes to HR's reporting person. The head of the organisation is out of scope.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- Cancelling approved leave after it has started is decided by the Lead (the employee's reporting person).
<!-- -------- bee-comment -------- -->
> **@developer**: the lead decides it.
> **@bee**: Replaced "HR or Lead" with "the Lead decides". HR's role in this case is not stated, so I left it out.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- If the reporting person has left or is away, HR can reassign the employee or approve the request.
<!-- -------- bee-comment -------- -->
> **@developer**: yes HR can reassign or even approve in that case.
> **@bee**: Recorded as written.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- Only the employee's reporting person can approve their leave. HR can approve only in special cases, with a reason recorded. Nobody approves their own request.
<!-- -------- bee-comment -------- -->
> **@developer**: No only the reporting person of that employee can approve or the HR in special cases with a reason.
> **@bee**: Recorded. HR approval requires a reason, which goes into the audit history.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- Escalation: if a request is not approved by one day before the leave starts, it escalates to HR.
<!-- -------- bee-comment -------- -->
> **@developer**: escalation if not approved just a day prior to the leave.
> **@bee**: Set the escalation point to one day before the leave start. Reminder timing before that was not given (see Remaining Open Questions).
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- Bulk approval is allowed when the balance is sufficient.
<!-- -------- bee-comment -------- -->
> **@developer**: yes if the balance is sufficent it can be approved in bulk.
> **@bee**: Set "simple case" to: balance is sufficient. I did not add the "no document needed" condition from my proposal, since you did not mention it; a request that needs a document still needs the approver to see it, so this is worth a quick look.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- Leave types on day one:
  - **Personal leave**: no yearly quota. Taken as needed, the approver decides.
  - **Floating leave**: 4 per year, taken only on dates HR publishes (alongside public holidays).
  - **Public holidays**: from the HR calendar.
  - **Maternity** (female employees only) and **Paternity** (male employees only): number of days set by HR, the same for every employee. Weekends are not counted.
  - **Unpaid leave**: no quota, no balance.
<!-- -------- bee-comment -------- -->
> **@developer**: There are persoanl leave that can be taken unlimited time based on approver, there is floating leave of 4, there is public holiday based on HR list, There is paternity and materinity leave as well whose number of days can be set by HR. / Maternity only for females and paternity only for Males... no weekends do not count. / Unpaid leave: yes correct
> **@bee**: Replaced the Casual, Sick and Earned examples with the types you listed. Added the gender rule for Maternity and Paternity. Unpaid follows my proposal (no quota, no balance), which you confirmed. Whether Sick leave exists is a follow-up question.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- No carry-forward for any leave type. Everything resets on 1 January. A new joiner gets the same entitlements as a regular employee (no proration). Nothing is carried or paid out when someone leaves.
<!-- -------- bee-comment -------- -->
> **@developer**: Carry-forward: no. / New joiner: same as regular employee. / No carry forward, it resets on 1 Jan (floating and on leaving).
> **@bee**: Removed carry-forward and prorating from the scope and from Phase 3. For "employee leaves mid-year" I read your answer as "no balance is carried or shown after leaving".
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- Attachments: PDF, JPG or PNG up to 5 MB, visible only to the approver and HR.
- Notifications go by Outlook email and Microsoft Teams, plus in-app.
<!-- -------- bee-comment -------- -->
> **@developer**: Outlok mail and Teams
> **@bee**: Added Teams as a notification channel. The brief said email and in-app only, so this is an addition. Which events go to Teams is a follow-up question.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- People sign in with the company sign-in.
- If an employee's reporting person changes while a request is pending, the request goes to the new reporting person, and they are notified.
- A request must not be allowed on a public holiday, because it is already a day off. Each employee has their own balance and cannot use anyone else's.
- When a quota or policy changes mid-year, existing balances only change through a recorded HR adjustment.
- Leave that spans two calendar years uses each day's own year balance.
- If two people act on the same request (for example Lead and HR after escalation), only the first action counts and the second sees a message.
- The yearly reset must be safe to run twice or after a partial failure.

## Hypotheses (now confirmed)
- H1: One approver per employee. Confirmed.
- H2: One global rule set per leave type. Confirmed.
- H3: Weekends and holidays not counted. Confirmed.
- H4: Half-day is 0.5 day, balances can hold halves. Confirmed.
- H5: A pilot of at least 2 pods. Confirmed.
- H6: Excel and PDF exports, no payroll connection. Confirmed (see Out of Scope for the payroll layout).
- H7: Safe against simultaneous actions. Confirmed.

## Out of Scope
- Team leave calendar and conflict warnings
- Comp-off
- Approver delegation (so a request waits for escalation if the approver is away)
- Sandwich rule (weekends or holidays between leave days being counted as leave)
- Work-from-home requests
- Payroll integration and a payroll-specific export layout (to be addressed in a separate payroll module)
- Attendance tracking
- Other ERP modules
- Multi-level approval, per-location holidays, per-group policies
- Carry-forward and prorated quotas
- Max consecutive days and advance-notice rules
- Deactivating a leave type that has pending requests or balances
- Leave for the head of the organisation
<!-- -------- bee-comment -------- -->
> **@developer**: lets address payroll module differently currently out of scope. / HR deactivates a leave type...: out of scope
> **@bee**: Moved the payroll-specific export layout and leave type deactivation to Out of Scope. General Excel and PDF reports stay in scope because you confirmed "just the export" under H6.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->

## Milestone Map

Risk level: moderate. Balances, audit and sensitive medical documents need care. Rules for simultaneous actions and balance accuracy are needed from Phase 1.

### Phase 1: Walking skeleton (apply, approve, balance)
- HR has loaded employees, pods and reporting persons by upload, and set up the leave types (Personal, Floating, Maternity, Paternity, Unpaid) and the holiday calendar.
- Employee applies for leave (type, dates, full day, or a single-day first/second half, reason) and sees their balance before submitting (days taken for Personal leave).
- Request is blocked for low balance, overlapping dates, or a public holiday.
- Backdated requests allowed within the same month only.
- Reporting person sees pending requests and approves or rejects with a comment.
- Employee sees the status and the updated balance.
- Employee can cancel a pending request.
- Every action is recorded in the audit history.
- Basic in-app notification for submit, approve and reject.

### Phase 2: Complete the request lifecycle
- Cancel approved leave before it starts (balance restored), and after start with the Lead's decision.
- Document attachment for leave types that need one (for example Maternity; Personal leave can carry a document such as a medical certificate).
- Status history and approver comments view.
- Outlook email and Teams notifications for all events.
- One approver reminder after 1 working day, then escalation to HR one day before the leave.
- HR can reassign a reporting person and approve with a reason.
- Bulk approval when the balance is sufficient and no document is needed.

### Phase 3: Floating leave, HR tools and yearly reset
- Floating holidays: HR publishes the dates, employee picks up to 4 per year.
- HR balance adjustment with a reason, and HR applying leave on an employee's behalf (including backdated leave).
- Yearly reset on 1 January (quotas refilled, new holiday calendar published, no carry-forward).

### Phase 4: Dashboards and reports
- Employee, Lead and HR dashboards (including who is off today).
- Reports by employee, pod, leave type and date range.
- Excel and PDF export of reports.
- Audit history view and filters for HR.

Phases 3 and 4 can be reordered if the client prefers.

## Module Structure
Not applicable. The brief describes a module of an existing ERP effort. Where the Leave Module sits among other modules is a technical decision for later.

## Follow-up Decisions
Answered by the client after the first review.
- No separate Sick leave type. It is covered by Personal leave.
- Personal leave shows days taken only. It has no balance and no balance check.
- A half-day is a single-day request only. It cannot be the start or end of a multi-day range.
- The approver gets one reminder after 1 working day if the request is still pending. Escalation to HR still happens one day before the leave starts.
- Teams receives all notification events (submit, approve, reject, cancel, reminder, escalation), alongside Outlook email and in-app.
- Bulk approval only covers requests with sufficient balance and no document needed. Requests that need a document are reviewed one by one.
- Maternity and Paternity each have one fixed number of days set by HR, taken as a single continuous block.
- First employee, pod and reporting-person data is loaded by HR through a manual upload.
- When an employee changes pod, only future reports use the new pod. Past leave stays under the pod at the time.
- Prototype success means the major workflows work without bugs or defects.
<!-- -------- bee-comment -------- -->
> **@developer**: Asked to settle the open questions here before the spec.
> **@bee**: Answered all 10 open questions in a short interview and recorded them above. Phases and earlier decisions were updated to match.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->

## Remaining Open Questions
None.

## Edge Cases to Cover in the Detailed Spec
- Two requests with overlapping dates submitted at nearly the same time. Only one should succeed.
- Approver approves while the employee cancels at the same moment. The result must be one clear final status and a correct balance.
- Two approvers (for example, Lead and HR after escalation) act on the same request. Only the first action counts, and the second sees a message.
- Employee applies for a day on the public holiday list. Blocked.
- A holiday is added to the calendar after leave is approved on that date.
- HR changes a quota or policy mid-year. Existing balances must not change without a recorded adjustment.
- Leave that spans two calendar years: each day uses its own year's balance.
- Reporting person changes while a request is pending. The request goes to the new reporting person with a notification.
- The reporting person is away or has left. HR can reassign or approve with a reason.
- Yearly reset is run twice, or run partly and fails. It must not apply twice.
<!-- -------- bee-comment -------- -->
> **@developer**: Notes on edge cases: balance-sharing "each employee has its own quota"; holiday applications "cannot apply on a public holiday"; pending reporter change "assigned to the new reporter with notification"; others "yes correct".
> **@bee**: Removed the "two requests using the same balance" case (balances are per employee). Changed the holiday case to "blocked on a public holiday". Kept the "holiday added after approval" case, since the calendar can still change later. Dropped the "deactivate a leave type" case as out of scope. "Carry-forward twice" became "applies twice", since there is no carry-forward.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->

## Revised Assessment
Size: EPIC
Greenfield: yes (new module; integration points with the wider ERP, such as the employee list and company sign-in, are not yet known)
Risk: Moderate (balance accuracy, audit trail, sensitive medical documents)

[x] Reviewed
