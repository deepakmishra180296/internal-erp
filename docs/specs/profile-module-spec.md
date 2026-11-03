# Spec: Profile Module (Internal ERP)

Status: REVIEWED by the developer. Their answers to Open Questions 1 to 14 are applied. Criteria marked **(default, OQ n)** relate to Open Question n. Questions 1 to 14 are confirmed. Questions 15 to 18 are new, raised by those answers, and still need a yes or no. See Open Questions at the end.
Source: docs/specs/profile-module-discovery.md (client-reviewed, 18 questions resolved)
Size: EPIC. Risk: MODERATE-HIGH (bank details, UAN, personal data).

## Overview
The Profile Module is the first page every employee sees after signing in. It gives each employee a public Home page and private detail sections. HR controls organisation data (employees, clients, PODs, reporting lines) and document verification. Every change can be traced to who made it and when. Later modules, starting with Leave, will rely on the POD and "reporting to" data captured here.

## Terms Used in This Spec
- **HR**: any active employee whose role is HR. Every other role, including Lead, has employee access in this module.
- **Client**: an organisation the company works for. **Internal** is a built-in client that always exists.
- **POD**: a team that belongs to exactly one client. **Bench** is a built-in POD under Internal that always exists.
- **Real POD**: any POD other than Bench.
- **Role**: a job role HR keeps in a list, such as HR or Lead. **HR** is a built-in role that always exists. Every employee has exactly one role.
- **Assigned HR**: the HR person looking after an employee. It defaults to the HR person who created the employee, and HR can change it.
- **Private sections**: Personal Details (private contact and emergency contact parts), Educational Details, Professional Details, Bank Details. Only the owner and HR can open them.
- **Organisation fields**: org email, role, PODs (and so clients), reporting to, assigned HR, active or inactive status. Only HR sets them.
- **Masked number**: UAN or bank account number shown with only the last 4 digits visible, for example `XXXXXXXX1234`.
- **Document status**: Pending, Verified, or Rejected (with a reason).
- **Inactive**: an employee HR has "deleted". The record and data are kept. They are hidden from search, the org chart and POD member lists.

## Rules That Apply to Every Slice
These hold across the whole module. Each slice repeats the rule as an AC where that slice is the first to need it.
- Permission checks apply to every request, not only to what the screen shows. A request sent directly, without the screen, gets the same refusal.
- A save is all or nothing. If any part of a save is refused, nothing from that save is stored and no history entry is written.
- If two people edit the same section at once, the later save is refused with a message to reload. No change is silently lost.
- Personal phone, personal email, full address and emergency contact never appear on any Home page.

---

## Slice 1: Walking Skeleton (HR Creates an Employee, the Employee Sees Their Home)
The thinnest end-to-end path. HR adds a person and that person signs in and sees their own Home.

- [ ] HR can create an employee by entering first name, last name, org email and choosing a role from the roles list
- [ ] A new employee appears in the HR Employees list with name, org email, role and status Active
- [ ] A new employee is allocated to client Internal and POD Bench without HR choosing anything
- [ ] A new employee's assigned HR is the HR person who created them (OQ 13)
- [ ] Shows an error naming the missing field when first name, last name, org email or role is empty
- [ ] Shows an error when the org email is not a valid email address
- [ ] Shows an error when the org email is already used by another employee, active or inactive, ignoring letter case
- [ ] A signed-in employee lands on their own Home showing their name, role, org email, clients and PODs
- [ ] A signed-in user whose org email does not belong to an active employee sees "No access" and no profile data (default, OQ 5)
- [ ] The employee's own Home offers no way to change name, role, org email, client, POD or reporting to
- [ ] A person whose role is not HR sees "Access denied" when opening Organisation Details, including by typing its address
- [ ] A request from a non-HR person to create or change an employee is refused and nothing changes

## Slice 2: HR Sets Up Clients and PODs
HR builds the client and POD lists that employees are assigned to.

- [ ] Client Internal and POD Bench (under Internal) exist from the first use of the system without HR creating them
- [ ] HR can add a client by entering a name
- [ ] HR can add a POD by entering a name and choosing exactly one client
- [ ] Shows an error when HR saves a POD without choosing a client
- [ ] Shows an error when a client name is empty or already used by another client, ignoring letter case (default, OQ 6)
- [ ] Shows an error when a POD name is already used by another POD under the same client, ignoring letter case (default, OQ 6)
- [ ] HR can rename a client, and the new name shows wherever the client appears
- [ ] HR can rename a POD, and the new name shows wherever the POD appears
- [ ] Shows an error when HR tries to rename Internal or Bench
- [ ] The Clients list shows each client with its PODs and the number of active employees in each POD
- [ ] A request from a non-HR person to add or change a client or POD is refused and nothing changes

## Slice 2A: HR Sets Up Roles
HR builds the list of roles employees can be given (OQ 3). The role decides whether someone has HR access. This slice is numbered 2A so the other slice numbers stay stable. The planner may renumber it.

- [ ] Role HR exists from the first use of the system without anyone creating it
- [ ] HR can add a role by entering a name
- [ ] Shows an error when a role name is empty or already used by another role, ignoring letter case
- [ ] HR can rename a role, and the new name shows wherever the role appears
- [ ] Shows an error when HR tries to rename or delete the role HR, and a request to do so is refused
- [ ] HR is asked to confirm before a role is deleted
- [ ] Deleting a role that still has employees, active or inactive, is refused with a message showing how many employees have it
- [ ] Deleting a role with no employees removes it, and it is no longer offered when choosing a role
- [ ] The Roles list shows each role with the number of active employees who have it
- [ ] A request from a non-HR person to add, rename or delete a role is refused and nothing changes

## Slice 3: HR Deletes Clients and PODs
Deleting is safe: nothing that still has people or PODs attached can be removed.

- [ ] HR is asked to confirm before a client or POD is deleted
- [ ] Deleting a POD that still has employees is refused with a message showing how many employees are assigned to it
- [ ] Deleting a POD with no employees removes it, and it is no longer offered when assigning employees
- [ ] Deleting a client that still has PODs is refused with a message showing how many PODs it has (default, OQ 2)
- [ ] Deleting a client with no PODs removes it
- [ ] Internal and Bench offer no delete option, and a delete request for either is refused
- [ ] If an employee is assigned to a POD after HR opened its delete confirmation, the delete is refused with the current employee count
- [ ] History entries that mention a deleted client or POD still show its name

## Slice 4: HR Assigns Employees to PODs
An employee can be in several PODs, one per client. Clients follow from PODs.

- [ ] HR can choose one or more PODs when creating an employee, and the employee's Home shows each POD with its client name
- [ ] HR can add an existing employee to a POD
- [ ] HR can remove an employee from a POD
- [ ] Adding an employee to their first real POD removes Internal and Bench from them
- [ ] Removing an employee's last real POD puts them back in Internal and Bench
- [ ] Shows an error when HR tries to remove Bench from an employee who has no other POD
- [ ] Shows an error when HR adds an employee to a second POD under a client they already have a POD in (default, OQ 1)
- [ ] Shows an error when HR assigns an employee to a POD that was deleted after the screen was opened, and the employee's PODs do not change
- [ ] The employee's Home lists each of their clients once

## Slice 5: Reporting To
Each employee has one "reporting to" person, used later by the Leave module.

- [ ] HR can set one active employee as an employee's reporting person
- [ ] Home shows "Reporting to" with the reporting person's name
- [ ] Home shows "Reporting to: not assigned" when the employee has no reporting person
- [ ] Shows an error when HR picks the employee as their own reporting person
- [ ] Shows an error when the choice would create a loop, either direct (A to B, B to A) or longer (A to B, B to C, C to A)
- [ ] Inactive employees are not offered as a reporting person, and a request to pick one is refused

## Slice 6: HR Updates and Deactivates Employees
HR changes organisation fields and "deletes" employees by marking them inactive.

- [ ] HR can change an employee's role
- [ ] A person whose role is changed to HR can open Organisation Details on their next page load
- [ ] A person whose role is changed away from HR is refused Organisation Details on their next request
- [ ] HR can change an employee's org email, and the same format and uniqueness errors as creation apply
- [ ] HR can "delete" an employee after confirming, which marks them Inactive and keeps all their data
- [ ] HR can still open an inactive employee's record from the HR Employees list, where it shows status Inactive
- [ ] When a reporting person is marked inactive, their direct reports show "Reporting to: not assigned"
- [ ] After marking a reporting person inactive, HR is shown the names of the direct reports who need a new reporting person
- [ ] Shows an error when HR tries to deactivate, or remove the HR role from, the last active HR person (default, OQ 4)
- [ ] HR can change an employee's assigned HR to another active HR person (OQ 13)
- [ ] Shows an error when HR picks someone who is inactive or not in the HR role as an employee's assigned HR, and a request to do so is refused
- [ ] When an assigned HR person is deactivated or loses the HR role, HR is shown the names of the employees who need a new assigned HR (default, OQ 15)

## Slice 7: Personal Details, Public Profile Fields
The employee fills in the parts of their profile that colleagues see on Home.

- [ ] An employee can open their own Personal Details from their Home
- [ ] An employee can save date of birth, location, summary, skills, activities, bio and contact number
- [ ] After saving, Home shows the new date of birth, location, contact number, summary, skills and activities
- [ ] Bio does not appear on Home
- [ ] An employee can upload a JPG or PNG photo up to 5 MB, and it then shows on Home
- [ ] Shows an error when the photo is larger than 5 MB, and the existing photo stays unchanged
- [ ] Shows an error when the photo is not a JPG or PNG, or the file is empty, and the existing photo stays unchanged
- [ ] Shows an error when the date of birth is in the future (default, OQ 7)
- [ ] The contact number line is hidden on Home when the employee has not given one
- [ ] HR can edit these fields for any employee, and that employee's Home shows the change

## Slice 8: Personal Details, Private Contact and Access Protection
Sensitive contact details are captured but never leave the private section.

- [ ] An employee can save personal phone, personal email and full address
- [ ] An employee can save an emergency contact with name, relationship and phone number
- [ ] Shows an error naming the missing field when an emergency contact is saved without its name, relationship or phone number
- [ ] Shows an error when the personal email is not a valid email address
- [ ] Shows an error when a phone number is not 10 digits, with or without a leading +91 (default, OQ 7)
- [ ] No Home page shows personal phone, personal email, full address or emergency contact, whoever is viewing
- [ ] An employee who opens another employee's Personal Details by address sees "Access denied" and none of the data
- [ ] A direct request for another employee's private details from a non-HR person is refused
- [ ] HR can view and edit the private contact details of any employee

## Slice 9: Edit History and Safe Concurrent Edits
Every change can be traced, and two people editing at once cannot overwrite each other.

- [ ] An employee can open the edit history of their own Personal Details
- [ ] Each history entry shows the field, old value, new value, editor's name, and date and time
- [ ] An edit made by HR shows the HR person's name marked as HR in the editor column
- [ ] A photo change shows in history as "Photo updated" with the editor and time
- [ ] Changes to organisation fields (PODs, role, org email, reporting to, assigned HR, status) appear in the employee's Organisation Details history
- [ ] HR can open the history of any section of any employee, including Organisation Details
- [ ] An employee cannot open the Organisation Details history, even their own
- [ ] No one, including HR, can edit or delete a history entry
- [ ] Saving a section with no changed values creates no history entry
- [ ] When the employee and HR edit the same section at once, the later save is refused with a message to reload, and nothing from it is stored

## Slice 10: Search for Colleagues
Any employee can find a colleague in a few seconds.

- [ ] Every employee can search from their Home
- [ ] Search matches any part of a first or last name, ignoring letter case
- [ ] Search matches an org email
- [ ] Results are sorted by name A to Z
- [ ] Results show 20 per page, with a way to move to the next page
- [ ] Each of the skill, POD, role and location filters narrows the results on its own
- [ ] Using several filters together shows only employees who match all of them
- [ ] The POD filter finds an employee who is in that POD even when they are also in other PODs
- [ ] Inactive employees never appear in results
- [ ] Shows "No employees found" when nothing matches

## Slice 11: Colleague Home
A colleague sees the public Home only.

- [ ] Choosing a search result opens that colleague's Home with Employee Details, Contact Details, Talent Card and Organisational Chart
- [ ] A colleague's Home offers no links to private sections and no edit options to a non-HR viewer
- [ ] HR viewing any employee's Home can open that employee's private sections and Organisation Details
- [ ] Opening a link to an inactive employee's Home shows "Invalid link" and no profile data
- [ ] Opening a link to an employee who does not exist shows "Invalid link"

## Slice 12: Organisational Chart
Shows where a person sits: Organisation, then Client, then POD, then Reporting to, then the employee and team members.

- [ ] Home shows a chart of Organisation, then each of the employee's clients, then the employee's POD under each client
- [ ] The chart shows the active members of each of the employee's PODs
- [ ] An employee in several PODs sees every POD, each under its own client
- [ ] A new employee with no assignment sees Internal, then Bench, with the other Bench members
- [ ] The chart shows the reporting person, or "not assigned" when there is none
- [ ] Tapping any person in the chart opens that person's Home
- [ ] Inactive employees do not appear in the chart
- [ ] A colleague's Home shows the colleague's clients and PODs, not the viewer's (default, OQ 11)
- [ ] A change HR makes to PODs or reporting to shows in the chart the next time it is opened

## Slice 13: Bank Details with Masking
Highly sensitive. Only the owner and HR see it, and the account number stays masked unless revealed.

- [ ] An employee can save bank name, name as per bank, account number, IFSC code and branch location
- [ ] Shows an error naming the missing field when any bank field is empty
- [ ] Shows an error when the IFSC code is not 11 characters made of 4 letters, then 0, then 6 letters or digits (default, OQ 7)
- [ ] Shows an error when the account number is not 9 to 18 digits (default, OQ 7)
- [ ] After saving, the account number shows only its last 4 digits
- [ ] The full account number is not included in what the page receives until the viewer chooses Reveal
- [ ] The employee and HR can choose Reveal to see the full account number
- [ ] Each reveal is recorded in the audit trail with who revealed it, which field and when, without the number itself
- [ ] Bank Details history shows old and new account numbers masked
- [ ] An employee who opens another employee's Bank Details by address sees "Access denied" and none of the data
- [ ] When the employee and HR edit Bank Details at once, the later save is refused with a message to reload, and nothing from it is stored

## Slice 14: In-App Notifications and Bank Change Alerts
The first use of notifications: HR hears about bank changes.

- [ ] Every employee has a Notifications page listing their notifications, newest first
- [ ] The Notifications link shows the number of unread notifications
- [ ] Opening a notification marks it as read and opens the section it refers to
- [ ] When an employee changes their bank details, the employee's assigned HR gets a notification naming the employee and the time (OQ 13)
- [ ] When an employee has no assigned HR, every active HR person gets the bank change notification instead (default, OQ 15)
- [ ] No notification is sent when HR changes an employee's bank details, and history still records the change
- [ ] A bank change notification does not show the account number, masked or full
- [ ] An employee sees only their own notifications

## Slice 15: Document Upload
Employees attach supporting PDFs to Bank Details. Later slices reuse the same upload for education and employment.

- [ ] An employee can upload a PDF up to 5 MB to Bank Details, and it shows with status Pending
- [ ] An employee can upload any number of documents, and none is refused because of the combined size
- [ ] Shows an error when a file is larger than 5 MB, and nothing is saved
- [ ] Shows an error when a file is not a PDF, including a file named ".pdf" whose content is not a PDF, and nothing is saved
- [ ] Shows an error when a file is empty, and nothing is saved
- [ ] The employee and HR can open an uploaded document to view it
- [ ] An employee who requests another employee's document by address sees "Access denied" and no file
- [ ] HR can upload a PDF to any employee's Bank Details
- [ ] The employee and HR can remove an uploaded document, and the removal is recorded in the section's history (OQ 10)
- [ ] A request from anyone other than the owner or HR to remove a document is refused and nothing changes

## Slice 16: Document Verification
HR reviews documents. The employee always knows each document's status.

- [ ] HR can see a list of all Pending documents across employees, oldest first
- [ ] HR can mark a Pending document as Verified
- [ ] HR can reject a document by entering a reason
- [ ] Shows an error when HR rejects a document without a reason
- [ ] The employee sees each document's status, and the reason for any rejection
- [ ] The employee gets a notification when HR verifies or rejects one of their documents
- [ ] An employee can upload a replacement for a rejected document, and the new file starts as Pending
- [ ] The rejected file stays visible in the document's history after it is replaced
- [ ] When an employee changes any bank detail, all Verified bank documents return to Pending, and the employee sees that the change caused it (OQ 8)
- [ ] When HR changes a bank detail, the document statuses stay as they were (default, OQ 16)
- [ ] A request from a non-HR person to change a document's status is refused and the status does not change
- [ ] A document uploaded or replaced by an HR person, for themselves or for another employee, is saved as Verified and does not appear in the Pending list (default, OQ 16)
- [ ] When two HR people act on the same document at once, the later action is refused with a message to reload

## Slice 17: Educational Details
- [ ] An employee can add an education entry with degree, institution and year
- [ ] An employee can edit an existing education entry
- [ ] Shows an error naming the missing field when degree, institution or year is empty
- [ ] Shows an error when the year is not a 4-digit year, or is in the future (default, OQ 7)
- [ ] An employee can attach PDFs to an education entry, using the same file rules and statuses as bank documents
- [ ] When an employee edits an education entry, its Verified documents return to Pending, and the employee sees that the edit caused it (OQ 8)
- [ ] When HR edits an education entry, its documents keep their status (default, OQ 16)
- [ ] An employee can remove an education entry, and the removal is recorded in history (default, OQ 10)
- [ ] Educational Details history shows each change with field, old value, new value, editor and time
- [ ] HR can add and edit education entries for any employee
- [ ] An employee who opens another employee's Educational Details by address sees "Access denied"

## Slice 18: Professional Details and UAN
- [ ] An employee can add an employment entry with employer, role, location, start date and end date, where an empty end date means current
- [ ] An employee can edit an existing employment entry
- [ ] Shows an error when the end date is before the start date
- [ ] An employee can add reference details (name, designation, phone, email) to an employment entry (default, OQ 7)
- [ ] An employee can attach PDFs to an employment entry, using the same file rules and statuses as bank documents
- [ ] When an employee edits an employment entry or its references, that entry's Verified documents return to Pending (OQ 8)
- [ ] When HR edits an employment entry or its references, its documents keep their status (default, OQ 16)
- [ ] An employee can remove an employment entry, and the removal is recorded in history (OQ 10)
- [ ] Removing an education or employment entry also removes its documents, and history records this (default, OQ 18)
- [ ] HR can add, edit and remove employment entries for any employee
- [ ] An employee can save their UAN
- [ ] Shows an error when the UAN is not exactly 12 digits (default, OQ 7)
- [ ] After saving, the UAN shows only its last 4 digits, and the employee and HR can choose Reveal to see it in full
- [ ] Each UAN reveal is recorded in the audit trail with who, which field and when, without the number itself
- [ ] Professional Details history shows old and new UAN values masked
- [ ] An employee who opens another employee's Professional Details by address sees "Access denied"

## Slice 19: Profile Completeness Indicator
A fixed checklist nudges employees to finish their profile.

Checklist (12 items): photo, date of birth, location, summary, at least one skill, personal phone, personal email, full address, emergency contact, at least one education entry, UAN, bank details.

- [ ] An employee's own Home shows a completeness percentage
- [ ] The indicator lists each checklist item that is still missing
- [ ] The percentage is the number of completed items out of 12, rounded down to a whole number (default, OQ 14)
- [ ] An item counts as complete once it is saved, whatever the status of its documents (default, OQ 14)
- [ ] The percentage and the missing list update right after the employee saves a missing item
- [ ] Leaving the optional contact number empty does not lower the percentage
- [ ] A profile with all 12 items shows 100% and no missing items
- [ ] HR sees the indicator on any employee's Home
- [ ] A non-HR colleague viewing someone's Home does not see the indicator

---

## Key Data (Indicative Only)
Shapes to show intent. Field names and storage are decided at technical planning.

```
Employee:     { firstName, lastName, orgEmail (unique), roleId, status: Active|Inactive,
                pods: [podId] (at least one), reportingTo: employeeId | none,
                assignedHrId: employeeId | none }
Role:         { name }                       HR is built in
Client:       { name }                       Internal is built in
Pod:          { name, clientId (exactly one) }   Bench is built in, under Internal
Document:     { ownerId, section, entryId?, file, status: Pending|Verified|Rejected,
                rejectionReason?, replaces? }
HistoryEntry: { employeeId, section, field, oldValue, newValue, editorId, editorIsHr, at }
                (masked fields store masked values; entries are never changed or removed)
RevealEntry:  { employeeId, field: UAN|AccountNumber, revealedBy, at }  (no number stored)
Notification: { recipientId, kind, aboutEmployeeId, section, at, isRead }
```

## Out of Scope
- Sign-in, passwords and creating login accounts. Employees sign in with their org email. This belongs to the Authentication module.
- What changing an employee's org email does to their sign-in. This belongs to the Authentication module.
- Separation and exit handling, including reactivating an inactive employee and full ex-employee handling.
- Leave, payroll, attendance, performance and other ERP modules.
- Email and SMS notifications. Notifications are in-app only.
- Approval workflows for profile edits. Edits save immediately.
- Bulk import of employees.
- Extra Lead-only features. A Lead has employee access here.
- Browsing the whole organisation as one chart. The chart shows one employee's context.
- Bank account or UAN verification against outside systems. Only the format is checked.
- Virus scanning of uploaded files (not raised in discovery, flag at technical planning).

## Technical Context
- Greenfield: the project holds documents only. There is no codebase, design brief (`.claude/DESIGN.md`) or module boundaries file yet.
- Patterns to follow: none yet. Decided at technical planning.
- Key dependencies:
  - Authentication module (future) provides the signed-in user's org email. Slice 1 assumes a signed-in identity is available.
  - The Leave module (`docs/specs/leave-module-spec.md`) reads POD and reporting-to data from here. Its spec assumes one POD per employee, but this module allows several (one per client). Leave will need to decide which POD, or all of them, applies to leave reporting.
- Risk level: MODERATE-HIGH. Security, audit, data integrity and concurrency criteria are included in Slices 1, 3, 6, 8, 9, 13, 15 and 16.

## Open Questions
Questions 1 to 14 are answered by the developer's review. Questions 15 to 18 are new, raised by those answers. Each has a proposed default that the criteria above already use. The developer has not yet confirmed 15 to 18.

1. **RESOLVED. BR27, one POD per client.** An employee can be in at most one POD per client. Affects Slice 4.
<!-- -------- bee-comment -------- -->
> **@developer**: yes for now lets keep it this way.
> **@bee**: Confirmed. No change to Slice 4.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
2. **RESOLVED. BR28, client with PODs cannot be deleted.** HR must delete the client's PODs first. Affects Slice 3.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Confirmed. No change to Slice 3.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
3. **RESOLVED. Where roles come from.** HR adds, renames and deletes roles, alongside employees, clients and PODs.
<!-- -------- bee-comment -------- -->
> **@developer**: Along with employee, client and POD add option to add roles as well.
> **@bee**: Added a new Slice 2A, "HR Sets Up Roles", with 10 criteria. I made HR a built-in role that cannot be renamed or deleted, like Internal and Bench. Slice 1 now picks the role from the list. I removed "managing the list of roles" from Out of Scope and added a Role entry to Key Data. How the very first HR person gets created is new Question 17.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
4. **RESOLVED. Last HR person.** The system refuses to deactivate, or remove the HR role from, the last active HR person. Affects Slice 6.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Confirmed. No change.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
5. **RESOLVED. Signed-in person with no active employee record.** They see "No access" and no profile data. Affects Slice 1.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Confirmed. No change.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
6. **RESOLVED. Name uniqueness.** Client names are unique. POD names are unique within their client, ignoring letter case. Affects Slice 2.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Confirmed. No change.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
7. **RESOLVED. Format rules.** IFSC is 4 letters, then 0, then 6 letters or digits. Account number is 9 to 18 digits. UAN is 12 digits. Phone numbers are 10 digits with an optional +91. Date of birth and education year cannot be in the future. A reference has name, designation, phone and email. Affects Slices 7, 8, 13, 17 and 18.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Confirmed. No change.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
8. **RESOLVED. What returns a document to Pending.** When an employee edits a detail, the Verified documents it supports return to Pending: all bank documents for a bank change, or only that entry's documents for an education or employment edit. Edits by HR do not reset documents.
<!-- -------- bee-comment -------- -->
> **@developer**: yes but HR person does not need to verify their own edit docuemnt or docuemnt they edit for other employee
> **@bee**: Confirmed for employee edits. For HR edits, I made the document statuses stay as they were, so HR does not have to review its own edits. That is in Slices 16, 17 and 18. I read this as a guess at your meaning, so I listed it as Question 16 to confirm.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
9. **RESOLVED. HR verifying their own documents.** HR does not need to verify its own documents, or documents it has edited, for itself or for another employee. See Question 16 for how this works.
<!-- -------- bee-comment -------- -->
> **@developer**: HR person do not need to verify their own document or even verify document tht they have edited be it for self or for another employee.
> **@bee**: Removed the old rule that HR cannot verify its own documents. Now a document uploaded or replaced by an HR person is saved as Verified and skips the Pending list (Slice 16). This also removes the problem of an organisation with only one HR person.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
10. **RESOLVED. Removing entries and documents.** Education and employment entries can be removed, and so can documents. Each removal is recorded in history. Affects Slices 15, 17 and 18.
<!-- -------- bee-comment -------- -->
> **@developer**: it can be removed as well.
> **@bee**: Read as "documents can be removed too". Replaced "documents cannot be deleted" in Slice 15 with removal by the owner or HR, recorded in history. I also noticed Slice 18 had no way to remove an employment entry, so I added one, plus HR add/edit/remove for employment entries.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
11. **RESOLVED. Org chart scope on a colleague's Home.** It shows the colleague's clients, PODs, reporting person and POD members, not the viewer's. Affects Slice 12.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Confirmed. No change.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
12. **RESOLVED. Who can see the reveal audit trail.** Reveals show in the section's history to the employee and HR. Affects Slices 13 and 18.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Confirmed. No change.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
13. **RESOLVED. Who gets bank change notifications.** Each employee has an assigned HR, which defaults to the HR person who added them and can be changed. That HR person gets the notification. Affects Slices 1, 6 and 14.
<!-- -------- bee-comment -------- -->
> **@developer**: each employee has a reporting manger and an HR assigned to him as well, defaults to the HR who add the employee in the org. Can be updated.
> **@bee**: Added "assigned HR" as a new organisation field. Slice 1 sets it to the creating HR person, Slice 6 lets HR change it, and Slice 14 sends bank change notifications only to that person. It also appears in Organisation Details history and in Key Data. What happens when the assigned HR person leaves, and whether it shows on Home, is Question 15.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
14. **RESOLVED. Completeness details.** Rounded down to a whole number. An item counts once it is saved, whatever its verification status. "Bank details" counts only when all five bank fields are saved. "Emergency contact" counts only when name, relationship and phone are all saved. Affects Slice 19.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Confirmed. No change.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
15. **OPEN. Assigned HR: leaving HR and visibility.** Default: when the assigned HR person is deactivated or loses the HR role, HR is shown the employees who need a new one, and until then bank change notifications go to every active HR person. The assigned HR is shown only in Organisation Details, not on Home. Affects Slices 6 and 14. @bee yes
16. **OPEN. HR uploads and edits need no review.** Default: a document uploaded or replaced by HR is saved as Verified straight away, and an edit by HR leaves document statuses as they were. Say if you meant something different, for example that such documents stay with no status. Affects Slices 16, 17 and 18. @bee yes
17. **OPEN. The first HR person.** HR creates employees, but someone must exist as HR first. Default: the first HR person is set up when the system is installed, outside this module. Affects Slices 1 and 2A. @bee yes which can be created in backend by developer.
18. **OPEN. Documents of a removed entry.** Default: removing an education or employment entry also removes its documents, and history records this. Affects Slices 17 and 18. @bee yes

[x] Reviewed
