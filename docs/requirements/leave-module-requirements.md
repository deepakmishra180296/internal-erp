# Leave Module: Client Requirement Brief

## Purpose
We are building an internal ERP for our organisation. This brief covers only the **Leave Module**, as a prototype. Please act as a Business Analyst: read this, ask me clarifying questions, then produce a full spec sheet.

## Users
- **Employee**: applies for and tracks their own leave.
- **Lead / Manager**: approves leave for the people who report to them.
- **HR**: manages policies, quotas, holidays, and can approve leave.

## Organisation Rules (already decided)
- Leave year is the **calendar year** (January to December).
- Every employee belongs to **one pod**. Leave can be viewed and reported by pod.
- Every employee has a **reporting person** who approves their leave. This includes Leads, whose leave is approved by their own reporting person.
- Public holidays are the **same for all employees** (no location differences).
- Leave balance **cannot go negative**. Advance leave is not allowed.

## What the module should do

### 1. Apply Leave
- Employee picks leave type, start date, end date, full or half day, and gives a reason.
- Can attach a supporting document (e.g. medical certificate) where the leave type needs one.
- Shows available balance before submitting.
- Blocks the request if balance is insufficient or dates overlap with existing leave.
- Request goes to the employee's reporting person for approval.

### 2. Cancel Leave
- Employee can cancel a pending request at any time.
- Cancelling an approved leave is allowed before it starts, and the balance is restored.
- Cancelling after it has started needs HR or Lead approval.

### 3. View Leave Status
- Employee sees all requests with status: Pending, Approved, Rejected, Cancelled.
- Shows approver comments and a simple history of actions.

### 4. Leave Quota Management
- Each leave type has a yearly quota per employee.
- Supports prorated quota for new joiners and carry-forward rules.
- HR can adjust an individual's balance with a reason.
- Employee can always see their current balance per leave type.

### 5. Floating Leaves
- A fixed number of floating holidays per year, chosen by the employee from a list HR publishes.
- Employee picks the date(s) within a set window; unused ones lapse at year end (or carry over, as HR decides).

### 6. Fixed Public Holidays
- HR maintains the yearly holiday calendar.
- Holidays do not count against leave balance.
- All employees can view the calendar.

### 7. Leave and Leave Type Management (HR only)
- HR can create, edit, and deactivate leave types (e.g. Casual, Sick, Earned, Maternity, Paternity, Unpaid).
- Per type, HR sets: yearly quota, carry-forward, half-day allowed, document needed, max consecutive days, advance notice needed.
- Not visible to regular employees or Leads.

### 8. Leave Approvals (HR or Leads only)
- Approvers see a list of pending requests with employee, dates, leave type, balance, and any attachment.
- Approve or reject with a comment; bulk approval for simple cases.
- If an approver does not respond in a set time, a reminder is sent and the request escalates to HR.

### 9. Dashboard
- **Employee**: balance by type, upcoming leaves, pending requests, next holiday.
- **Lead**: pending approvals and upcoming leaves of the people reporting to them.
- **HR**: who is off today, leave usage by pod and leave type, unused leave and low balance alerts.

### 10. Notifications
- Email and in-app notifications when a request is submitted, approved, rejected, or cancelled.
- Reminders to approvers for pending requests.

### 11. Reports and Export
- HR can view leave reports by employee, pod, leave type, and date range.
- Export to Excel and PDF for payroll use.

### 12. Audit History
- Every leave action is recorded: who did what, when, and why (applied, approved, cancelled, balance adjusted, policy changed).
- Visible to HR.

## Key Workflows
1. **Apply and approve**: Employee applies → system checks balance and rules → reporting person notified → approves or rejects → employee notified → balance updated.
2. **Cancel**: Employee requests cancel → (if pending) cancelled instantly → (if approved) rules checked, approver informed → balance restored.
3. **Escalation**: No action from approver within set days → reminder → escalates to HR.
4. **Year start (1 January)**: HR runs yearly reset → quotas refilled, carry-forward applied, new holiday calendar published.
5. **HR adjustment**: HR corrects a balance or applies leave on behalf of an employee, with a reason recorded.

## Out of Scope (for now)
Team leave calendar, conflict warnings, comp-off, approver delegation, sandwich rule, work-from-home requests, payroll integration, attendance tracking, and other ERP modules.

## What I Need From You
1. Ask me questions to fill any gaps.
2. Produce a spec sheet with user stories, screens, rules, workflows, and edge cases.
3. Keep language simple so business and technical teams both understand it.
