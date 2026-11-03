# Spec: Leave Module (Internal ERP Prototype)

Status: Defaults 1, 6 and 13 confirmed by the developer. Other defaults are open for review comments.
Source: docs/specs/leave-module-discovery.md (client-reviewed, no open questions)

## Overview
One place where employees apply for leave, see their balance, and cancel leave. Reporting persons approve or reject it. HR manages leave types, holidays, balances and the yearly reset, and uses reports and the audit history. Every action is recorded. The prototype is a success when the major workflows work without defects and at least 2 pods run the full cycle of apply, approve and cancel.

## Terms Used in This Spec
- **Reporting person**: the one person who approves an employee's leave. Every employee has one, except the head of the organisation.
- **Working day**: Monday to Friday, not a public holiday. Only working days count as leave days.
- **Leave year**: the calendar year, January to December.
- **Balance types**: Floating, Maternity, Paternity. They have a yearly number of days and a balance that can never go below 0.
- **No-balance types**: Personal, Unpaid. They have no quota and no balance check. The employee sees the days they have taken this year.
- **Available balance**: yearly days, minus approved days, minus days in pending requests, plus or minus HR adjustments, for one leave year.
- **Statuses**: Pending, Approved, Rejected, Cancelled, Cancellation requested.

Day-one leave types:

| Type | Yearly days | Who can take it | Special rule |
|---|---|---|---|
| Personal | No quota | Everyone | Covers sickness. A document can be attached but is not required |
| Floating | 4 | Everyone | Only on dates HR publishes. Full day only |
| Maternity | Set by HR | Female employees | One continuous block per leave year |
| Paternity | Set by HR | Male employees | One continuous block per leave year |
| Unpaid | No quota | Everyone | None |

Public holidays are a calendar that HR maintains. Employees cannot apply for them as a leave type.

---

## Slice 1: Walking Skeleton (Apply for and Approve Personal Leave)
The thinnest path from start to finish. HR loads people, an employee applies for Personal leave, and the reporting person approves or rejects it.

- [ ] HR can upload an employee list where each row has name, company email, gender, pod, reporting person's email, and whether the person is HR
- [ ] A person on the employee list can sign in with the company sign-in and sees their own leave page
- [ ] A person not on the employee list sees a "no access" message after company sign-in and cannot see any leave data
- [ ] An employee can submit a Personal leave request with a start date, an end date and a reason, and then sees it as Pending in their request list
- [ ] The day count of a request leaves out Saturdays and Sundays (Fri to Mon counts as 2 days)
- [ ] The reporting person sees the request in their pending list with the employee's name, leave type, dates and day count
- [ ] The reporting person can approve a request with an optional comment, and the employee then sees it as Approved
- [ ] The reporting person can reject a request with a comment, and the employee then sees it as Rejected along with the comment
- [ ] Shows an error when the reporting person tries to reject without a comment
- [ ] Approving a request adds its days to the employee's "Personal days taken this year". Rejecting does not.
- [ ] Shows an error, and the request does not change, when anyone other than the employee's reporting person tries to approve or reject it (HR's special approval is added in Slice 11)
- [ ] An employee sees only their own requests, not other employees' requests
- [ ] Each submit, approve and reject action is saved in the audit history with who did it, when, the action, and the comment or reason

## Slice 2: Organisation Data Upload Rules
Makes the upload from Slice 1 safe to run again and again as pods and reporting lines change.

- [ ] An upload creates any pod named in the file that does not already exist
- [ ] An upload rejects a row whose reporting person is not on the file or among existing employees, shows the row number and reason, and still loads the valid rows
- [ ] An upload rejects a row where the employee is their own reporting person
- [ ] An upload rejects a row that is missing a name, company email, gender or pod, and names the missing field
- [ ] Uploading again updates existing employees, matched by company email, without creating duplicates
- [ ] When an upload changes an employee's pod, leave days before the change stay under the old pod and leave days from the change onwards go under the new pod
- [ ] HR can mark an employee as left in the upload, and a left employee can no longer sign in
- [ ] An employee uploaded without a reporting person sees a message that they cannot apply for leave
- [ ] Shows an error when someone who is not HR tries to open or use the upload
- [ ] Each upload is saved in the audit history with who ran it, when, and how many rows were loaded and rejected

## Slice 3: Holiday Calendar, Working Days and Half Days
- [ ] HR can add a public holiday with a date and a name
- [ ] All employees can view the public holiday calendar for the current year and the next year
- [ ] Shows an error when HR adds a holiday on a date that is already a public holiday
- [ ] Shows an error when someone who is not HR tries to add a holiday
- [ ] The day count of a request leaves out public holidays that fall inside the date range
- [ ] The employee sees the day count before submitting
- [ ] Shows an error, and saves nothing, when every day in the requested range is a weekend or a public holiday (this includes a single day on a public holiday)
- [ ] An employee can request a half day for a single date, choosing first half or second half, and it counts as 0.5 day
- [ ] Shows an error when a half-day request does not say first half or second half
- [ ] Shows an error when a half-day request has an end date that differs from its start date
- [ ] When HR adds a holiday on a date covered by approved leave, that request's day count drops by that day and those days go back to the employee's balance or days-taken total
- [ ] Each holiday added, and each change it causes to a request, is saved in the audit history

## Slice 4: Balances and Balance Checks
- [ ] The employee sees their available balance for each balance type, and their days taken this year for each no-balance type
- [ ] The employee sees the available balance for the selected leave type before submitting
- [ ] A Pending request holds its days, so the available balance goes down when a request is submitted
- [ ] Shows an error, and saves nothing, when a request for a balance type asks for more days than are available
- [ ] Personal and Unpaid requests are never blocked because of balance
- [ ] Rejecting a Pending request releases its held days back to the available balance
- [ ] Only female employees can request Maternity leave and only male employees can request Paternity leave. Anyone else does not see the option, and a direct attempt shows an error
- [ ] Shows an error when an employee requests Maternity or Paternity while they already have a Pending or Approved request of the same type in that leave year
- [ ] For a request that spans two leave years, each day is counted against its own year's balance, and the error names the year that is short
- [ ] Shows an error to the approver, and the request stays Pending, when approving it would make the balance go below 0 (for example after an HR deduction)

## Slice 5: Leave Type Management (HR)
- [ ] HR sees every leave type with its settings: yearly days (or "no quota"), whether half days are allowed, and whether a document is required, optional or not accepted
- [ ] HR can create a new leave type with a name and these settings
- [ ] Shows an error when HR creates a leave type with a name that already exists
- [ ] Shows an error when HR enters yearly days below 0 or not in steps of 0.5
- [ ] HR can change the yearly days of a leave type (for example set Maternity days)
- [ ] Changing yearly days in the middle of a year does not change any employee's balance for the current year
- [ ] A change to yearly days applies from the next yearly reset
- [ ] Shows an error when an employee requests a half day of a leave type that does not allow half days
- [ ] Shows an error when someone who is not HR tries to view or change leave type settings
- [ ] Each leave type change is saved in the audit history with the old value, the new value, and who made the change

## Slice 6: Request Date and Overlap Rules
- [ ] Shows an error when the reason is empty
- [ ] Shows an error when the end date is before the start date
- [ ] Shows an error that names the conflicting request's dates when the new request overlaps one of the employee's Pending or Approved requests
- [ ] Rejected and Cancelled requests do not block a new request for the same dates
- [ ] A first-half and a second-half request on the same date are both allowed
- [ ] Shows an error when a half-day request is for the same half, or the same date, as an existing full-day request
- [ ] An employee can apply for past dates within the current calendar month
- [ ] Shows an error saying that HR must apply on the employee's behalf when the start date is in an earlier month
- [ ] When two overlapping requests from the same employee are submitted at the same moment, exactly one is saved and the other shows the overlap error

## Slice 7: Notifications (In-app, Outlook Email, Teams)
- [ ] Each user has an in-app notification list with a count of unread items
- [ ] A user can mark a notification as read, and the unread count goes down
- [ ] The reporting person gets a notification when a request is submitted to them
- [ ] The employee gets a notification when their request is approved or rejected, including the approver's comment
- [ ] The employee gets a notification when a holiday added after approval changes their request's day count
- [ ] Each notification is also sent by Outlook email and by Microsoft Teams
- [ ] When the email or Teams message cannot be sent, the leave action still completes and the in-app notification still appears
- [ ] Notifications never include an attached document or what it contains

## Slice 8: Cancellation
- [ ] An employee can cancel their own Pending request at any time. It becomes Cancelled and its held days are released.
- [ ] An employee can cancel their own Approved request before its start date. It becomes Cancelled and all its days are restored.
- [ ] For Approved leave that has already started, the employee can ask to cancel the remaining days with a reason, and the request becomes "Cancellation requested"
- [ ] When the reporting person accepts a cancellation request, days from today onwards are restored, earlier days stay counted, and the request shows its new end date
- [ ] When the reporting person declines a cancellation request, the leave goes back to Approved with all its days still counted
- [ ] Shows an error when the employee tries to cancel Approved leave whose days are all in the past
- [ ] Shows an error when the employee tries to cancel a request that is Rejected or already Cancelled
- [ ] When an approve and a cancel happen on the same request at the same moment, the request ends with one final status and the balance matches that status
- [ ] The reporting person is notified (in-app, email, Teams) when a request of theirs is cancelled or a cancellation is requested. The employee is notified of the decision.
- [ ] Each cancellation, cancellation request and decision is saved in the audit history

## Slice 9: Request Details, History and Documents
- [ ] The employee can open a request and see its full status history: each change with who made it, when, and the comment
- [ ] The reporting person can open a request of someone who reports to them and see the same history
- [ ] The employee can attach one document in PDF, JPG or PNG format, up to 5 MB, when submitting
- [ ] Shows an error when the attached file is not PDF, JPG or PNG
- [ ] Shows an error when the attached file is larger than 5 MB
- [ ] Shows an error, and saves nothing, when the leave type requires a document and none is attached
- [ ] The reporting person can open the document from the pending request
- [ ] HR can open the document
- [ ] The employee who uploaded the document can open it
- [ ] Shows an error when anyone else (other employees, other reporting persons) tries to open the document

## Slice 10: Reminders and Escalation
- [ ] The reporting person gets one reminder when a request is still Pending after 1 full working day
- [ ] No more than one reminder is sent for each request
- [ ] No reminder is sent once the request has been approved, rejected or cancelled
- [ ] A request still Pending on the working day before its start date is escalated: it shows as "Escalated" in HR's list and HR is notified
- [ ] A request submitted when its start date is tomorrow or earlier (including backdated requests) is escalated as soon as it is submitted
- [ ] After escalation, the reporting person can still approve or reject the request
- [ ] HR can approve or reject an escalated request with a reason, and the reason appears in the request history
- [ ] Shows an error when HR approves or rejects an escalated request without a reason
- [ ] When the reporting person and HR act on the same request, only the first action counts, and the second person sees a message saying who already acted and what they decided
- [ ] Reminders and escalations are sent in-app, by email and by Teams, and saved in the audit history

## Slice 11: Reporting-Line Changes and HR Special Approval
- [ ] HR can change an employee's reporting person from the HR screen
- [ ] When an employee's reporting person changes, by screen or by upload, their Pending requests move to the new reporting person, who is notified
- [ ] Shows an error when the previous reporting person tries to act on a request that has moved
- [ ] HR sees a "needs attention" list of Pending requests whose reporting person has been marked as left
- [ ] HR can approve or reject any Pending request with a reason, and the reason is saved in the request history and the audit history
- [ ] Shows an error when HR approves or rejects a non-escalated request without a reason
- [ ] Shows an error when anyone, HR included, tries to approve or reject their own request
- [ ] Shows an error when HR sets an employee as their own reporting person, or sets a left employee as a reporting person
- [ ] Each reassignment is saved in the audit history with the old reporting person, the new one, and who made the change

## Slice 12: Bulk Approval
- [ ] The reporting person can select several Pending requests and approve them in one action
- [ ] Requests that need a document cannot be selected and are labelled "review individually"
- [ ] Requests that ask for more days than the available balance cannot be selected
- [ ] If a selected request was cancelled, or its balance became too low, after it was selected, it is skipped with the reason and the others are still approved
- [ ] After a bulk approval the approver sees a summary: how many were approved, and which were skipped and why
- [ ] Each request approved in bulk gets its own status history entry, audit entry and notification to the employee

## Slice 13: HR Balance Adjustment and Leave on Behalf
- [ ] HR can add or take away days (in steps of 0.5) from an employee's balance for a balance type and a leave year, with a reason
- [ ] Shows an error when HR submits an adjustment without a reason
- [ ] Shows an error when a deduction would take the balance below 0
- [ ] Balance adjustment is not offered for Personal or Unpaid leave
- [ ] The employee sees the adjusted balance and the adjustment in their balance history, with its reason
- [ ] When two HR adjustments to the same balance happen at the same moment, both are applied and the final balance reflects both (or the second is refused if it would go below 0)
- [ ] HR can apply leave on behalf of an employee with a reason, including dates in earlier months
- [ ] Leave applied by HR on someone's behalf is Approved immediately, and the employee and their reporting person are notified
- [ ] Leave applied by HR on someone's behalf still follows the balance, overlap, public holiday and half-day rules
- [ ] Each adjustment and each leave applied on behalf is saved in the audit history, naming HR as the person who acted and the employee as the person affected

## Slice 14: Floating Leave
- [ ] HR can publish floating holiday dates for a leave year
- [ ] Shows an error when HR publishes a floating date that falls on a weekend or a public holiday
- [ ] All employees can see the published floating dates alongside the public holiday calendar
- [ ] An employee can request Floating leave on a published floating date, and the request goes to their reporting person like any other request
- [ ] Shows an error when Floating leave is requested for a date that is not a published floating date
- [ ] Shows an error when Floating leave is requested as a half day
- [ ] Shows an error when a request would take the employee past 4 Floating days in a leave year (counting both Pending and Approved)
- [ ] Publishing floating dates is saved in the audit history

## Slice 15: Yearly Reset
- [ ] HR can start the yearly reset for a named leave year and sees a summary when it finishes (employees processed, balances set)
- [ ] Shows a warning before the reset when that year has no public holidays in the calendar
- [ ] After the reset, each active employee's Floating, Maternity and Paternity balances for that year equal the yearly days set for those types, minus days already Pending or Approved in that year
- [ ] Unused balance from the previous year is not carried into the new year
- [ ] Personal and Unpaid days taken start at 0 for the new year, and the previous year's totals can still be seen
- [ ] Running the reset again for a year that has already been reset shows "already completed" and changes no balance
- [ ] If the reset stops partway, running it again finishes the remaining employees and does not apply the reset twice to employees already done
- [ ] If two HR users start the reset for the same year at the same moment, only one runs and the other sees a message
- [ ] Before the reset, a request with dates in the next year is checked against that year's yearly days, minus that year's Pending and Approved days
- [ ] The reset is saved in the audit history with who started it, when, and its outcome

## Slice 16: Dashboards
- [ ] The employee dashboard shows balance or days taken for each leave type, upcoming Approved leave, Pending requests, and the next public holiday
- [ ] The reporting person dashboard shows their pending approvals and the Approved leave of their reports for the next 30 days
- [ ] A user with nobody reporting to them does not see the reporting person dashboard
- [ ] The HR dashboard shows who is off today, with name, pod, leave type, and whether it is a full day, first half or second half
- [ ] The HR dashboard shows "Nobody is on leave today" when no one is on Approved leave today
- [ ] The HR dashboard shows Approved leave days used this year, grouped by pod and by leave type
- [ ] Pod usage counts each leave day under the pod the employee belonged to on that day
- [ ] Pending, Rejected and Cancelled requests are not counted as used or as off today
- [ ] Shows an error when someone who is not HR tries to open the HR dashboard

## Slice 17: Reports and Export
- [ ] HR can view a leave report filtered by employee, pod, leave type and date range
- [ ] Each report row shows employee, pod at the time of the leave, leave type, dates, day count and status
- [ ] The report lists Approved and Rejected requests, and each row shows which one it is
- [ ] The report does not list Pending or Cancelled requests
- [ ] When leave runs partly outside the chosen date range, only the days inside the range are counted
- [ ] HR can export the filtered report to Excel, and it has the same rows and columns as on screen
- [ ] HR can export the filtered report to PDF, and it has the same rows and columns as on screen
- [ ] When no leave matches the filters, the report and both exports show "No leave found"
- [ ] Shows an error when the end of the date range is before the start
- [ ] Shows an error when someone who is not HR tries to open or export reports

## Slice 18: Audit History View (HR)
- [ ] HR sees the audit history newest first, with when, who acted, the action, the employee affected, the reason or comment, and the values before and after
- [ ] HR can filter the audit history by employee affected, person who acted, action type and date range
- [ ] The audit history covers these actions: upload, submit, approve, reject, cancel, cancellation requested or decided, reminder, escalation, reassignment, HR special approval, balance adjustment, leave on behalf, leave type change, holiday added, floating dates published, and yearly reset
- [ ] Audit entries cannot be edited or deleted by anyone, and attempts to do so are refused
- [ ] Entries from earlier leave years stay visible after the yearly reset
- [ ] Shows an error when someone who is not HR tries to open the audit history

---

## Slice Order and Release Notes
- Slices 1 to 8 give the pilot its full cycle of apply, approve and cancel. This roughly matches discovery Phase 1 plus cancellation.
- Slices 9 to 12 finish the request lifecycle (Phase 2).
- Slices 13 to 15 cover HR tools, floating leave and the yearly reset (Phase 3).
- Slices 16 to 18 cover dashboards, reports and the audit history view (Phase 4). Phase 3 and Phase 4 can swap places. Slice 15 (yearly reset) must be live before 1 January of the first year in production.
- From Slice 1 onwards, every slice saves its actions in the audit history. Slice 18 only adds the HR screen for viewing it.

## Indicative Shapes (contracts, not implementation)
```
Employee:     { id, name, companyEmail, gender, podId, reportingPersonId?, isHR, status: active|left }
PodMembership:{ employeeId, podId, from, to? }
LeaveType:    { id, name, yearlyDays?, halfDayAllowed, document: required|optional|not_accepted }
LeaveRequest: { id, employeeId, leaveTypeId, start, end, halfDay?: first|second, reason,
                dayCount, status, approverId, escalated, documentId?, history[] }
Balance:      { employeeId, leaveTypeId, year, yearlyDays, approved, pending, adjustments, available }
AuditEntry:   { at, actorId, action, subjectEmployeeId?, requestId?, reason?, before?, after? }
```

## Spec Defaults Not Stated in the Discovery Doc (confirm or correct)
Defaults 1, 6 and 13 are confirmed by the developer.

1. **Holiday added after approval** (confirmed): that day stops counting, the day goes back to the employee, and the request stays Approved (with 0 days if every day is now a holiday).
2. **Removing a published public holiday or floating date**: not supported in the prototype. HR fixes mistakes with a balance adjustment.
3. **Cancelling after the leave has started**: only days from today onwards can be cancelled. Days already passed stay counted. If every day has passed, only HR can correct it, with an adjustment.
4. **Pending requests hold days** from the balance, so two pending requests cannot both be approved past the balance.
5. **Escalation**: a request whose start date is tomorrow or earlier, including backdated requests, escalates as soon as it is submitted.
6. **Leave applied by HR on someone's behalf** (confirmed) is Approved immediately and does not go to the reporting person.
7. **Maternity and Paternity**: one request per leave year, for up to the set number of days. A block that crosses 31 December uses each year's days (the general rule).
8. **Document visibility**: the employee who uploaded a document can also open it, along with the approver and HR.
9. **Rejecting needs a comment. Approving does not.**
<!-- -------- bee-comment -------- -->
> **@developer**: for approval it should be optional.
> **@bee**: No change needed. The default already makes the approval comment optional, and Slice 1 says "approve a request with an optional comment". I only confirmed the wording.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
10. **Lead dashboard window**: upcoming leave of reports for the next 30 days.
11. **Reports list Approved and Rejected leave.** Pending and Cancelled requests are not listed.
<!-- -------- bee-comment -------- -->
> **@developer**: include rejected leaves as well.
> **@bee**: Changed this default and the Slice 17 criterion to list Approved and Rejected requests. The report already has a status column, so the two can be told apart. Pending and Cancelled stay out because you only asked for Rejected. Rejected days are still not counted as used leave (Slice 16), so please say if you want them totalled separately.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
12. **Next-year requests before the reset** are checked against next year's yearly days. The reset then sets balances to yearly days minus what is already booked.
13. **Low-balance alerts and "unused leave" on the HR dashboard** (confirmed) (from the original brief) are left out because no threshold was decided.

## Out of Scope
- Team leave calendar and conflict warnings
- Comp-off
- Approver delegation (a request waits for escalation if the approver is away)
- Sandwich rule (weekends or holidays between leave days counted as leave)
- Work-from-home requests
- Payroll integration and a payroll-specific export layout (a separate payroll module will handle these)
- Attendance tracking
- Other ERP modules
- Multi-level approval, per-location holidays, per-group or per-grade policies
- Carry-forward of unused leave and prorated quotas for new joiners
- Maximum consecutive days and advance-notice rules
- Deactivating a leave type, including one with pending requests or balances
- Leave for the head of the organisation
- Separate Sick leave type (Personal leave covers it)
- Half days at the start or end of a multi-day range
- Payout or carry-over of balance when an employee leaves
- Low-balance alerts and unused-leave alerts (see default 13)

## Technical Context
- Greenfield: no code exists in the project yet. The tech stack is not decided, and this spec does not assume one.
- Integrations: company sign-in (provider details not yet known), Outlook email, Microsoft Teams. How the module connects to the wider ERP (for example a shared employee list) is not yet known. For the prototype, the employee list comes from HR's manual upload.
- No design brief (.claude/DESIGN.md) or module boundaries (.claude/BOUNDARIES.md) exist. Screens are described by behaviour only.
- Scale: 200 to 300 employees, pods of 6 to 8, and the number of pods changes over time.
- Risk level: MODERATE. Balance accuracy (never below 0, never counted twice), an audit history that cannot be changed, restricted access to medical documents, and safe handling of actions at the same moment (approve vs cancel, two approvers, overlapping submissions, concurrent HR adjustments, yearly reset run twice or after a partial failure).

[X] Reviewed
