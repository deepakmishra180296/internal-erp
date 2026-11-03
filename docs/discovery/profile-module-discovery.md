# Discovery: Profile Module

Status: REVIEWED. Client answers are folded in. Items marked **[CONFIRMED]** were confirmed by the client. Items marked **[BRIEF]** were decided in the client's brief. Items marked **[PROPOSED]** are still suggestions and need a yes or no (see Open Questions 16 to 18).

Source: `docs/requirements/profile-module-requirements.md`

## Why
The organisation is building an internal ERP. The Profile Module is the first thing every employee sees after login. Today there is no single place that answers "who is this person, where do they sit in the organisation, and are their records complete and verified?". HR needs one trusted place for employee records (including sensitive ones: bank, UAN, personal contacts). Employees need to find colleagues and see who works with whom. Later modules (starting with Leave) will rely on the POD and "reporting to" data captured here, so it must be right from the start.

## Who
- **Employee**: views and maintains their own profile; searches for colleagues.
- **HR**: views and edits every employee's profile; verifies documents; manages employees, clients and PODs.
- **Other employees (viewers)**: see only the public Home page of a colleague.
- **Roles are set by the employee's role [CONFIRMED]**: whether someone is HR, a Lead or something else comes from the role HR gives them. Anyone whose role is HR gets HR access. In this module a Lead has the same access as any other employee (the brief says Organisation Details are not visible to Leads).
<!-- -------- bee-comment -------- -->
> **@developer**: the role of the employee decides if he is HR, Lead or some other role
> **@bee**: Replaced "Lead is undefined" with the rule that the employee's role decides HR, Lead or other. Because the brief hides Organisation Details from Leads, I wrote that a Lead has no extra access in this module. Tell me if Leads should be able to do anything more here (see Open Question 18).
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->

## Success Criteria
- Every employee lands on a Home page after login that shows who they are and where they fit in the organisation.
- HR can set up a new employee, client or POD without developer help.
- Any employee can find a colleague in a few seconds and open their Home page.
- Sensitive details (personal phone, personal email, full address, emergency contact, bank, UAN) are never visible on Home or to non-HR colleagues.
- Every change to a profile can be traced to who made it and when.
- HR can see which documents are waiting for review and act on them.

## Problem Statement
Employee information is not held in one trusted place, so HR cannot reliably verify it and colleagues cannot easily find or understand each other's roles, teams and clients. The Profile Module gives each employee a public Home page and private detail sections, gives HR control of organisation-level data and document verification, and records every change.

## Hypotheses
- H1 [BRIEF]: Changes to personal, educational, professional and bank details need no approval. Verification of documents is enough control.
- H2 [CHANGED by client]: An employee can work for several clients. In each client the employee is in one POD of that client, so an employee can be in several PODs. (The brief said one POD per employee; this replaces that.)
- H3 [CONFIRMED]: Each POD belongs to exactly one client. An employee can be in several PODs, each under a different client.
<!-- -------- bee-comment -------- -->
> **@developer**: a POD belong to one client only but an employee can work for two different PODs of two different clients.
> **@bee**: Changed H3 so a POD belongs to exactly one client. This also changes H2: an employee can now be in several PODs, one per client, where the brief said one POD. I updated Home, the org chart, search, BR23 and Slices 1, 2 and 5 to match. The Leave module will need to know this, because it assumes one POD per employee.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- H4 [CONFIRMED]: HR "delete" of an employee means mark inactive, never erase (brief says ex-employee data is kept).
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Marked H4 as confirmed. No wording change.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- H5 [CONFIRMED]: A client or POD that still has employees cannot be deleted until HR reassigns them.
<!-- -------- bee-comment -------- -->
> **@developer**: Yes that works better.
> **@bee**: Marked H5 as confirmed. No wording change.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- H6 [CONFIRMED]: A completeness score based on a fixed checklist is enough to nudge employees; it does not need weighting.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Marked H6 as confirmed. No wording change.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- H7 [CONFIRMED]: Every employee has a default client called **Internal** and a default POD called **Bench**, and is allocated to them until HR assigns something else. Bench belongs to the Internal client.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Marked H7 as confirmed. I added that Bench belongs to Internal, since every POD now has exactly one client.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->

## Users, Roles and Permissions

| Area | Employee (own) | Employee (viewing colleague) | HR |
|---|---|---|---|
| Home | view; edit via Personal Details | view only | view and edit via Personal details and Organisational detials|
| Personal, Educational, Professional, Bank | view and edit | no access | view and edit |
| Organisation Details | no access | no access | add, update, delete, view |
| Verify/reject documents | no | no | yes |
| Completeness indicator | yes | no | yes |
| Edit history | own sections | no | all |

## Screens
1. **Home** (own and colleague view): Employee Details, Contact Details, Talent Card, Organisational Chart, Search, and (own/HR only) Completeness indicator.
2. **Search results**: list of matching employees with filters (skill, POD, role, location).
3. **Personal Details**: photo, date of birth, location, summary, skills, activities, bio; personal phone, personal email, full address; emergency contact.
4. **Educational Details**: entries plus document uploads with status.
5. **Professional Details**: employment history, references, documents with status, UAN (masked).
6. **Bank Details**: bank name, name as per bank, account number (masked), IFSC, branch location, documents with status.
7. **Edit History** (per section).
8. **Organisation Details (HR only)**: Employees, Clients, PODs lists and forms; HR employee search.
9. **Notifications**.

## Business Rules
- **BR1 [BRIEF]** Organisation-level fields (POD, org email, client(s), role, reporting to) are set only by HR.
- **BR2 [BRIEF]** Personal, educational, professional and bank edits save immediately with no approval.
- **BR3 [BRIEF]** Every edit is recorded with who made it (employee or HR).
- **BR4 [BRIEF]** Each uploaded photo or PDF is limited to 5 MB. There is no limit on how many documents an employee uploads, and the 5 MB is per file, not combined.
- **BR5 [BRIEF]** Documents are Pending, Verified, or Rejected (with a reason). Only HR changes status.
- **BR6 [BRIEF]** Editing a verified document, or the detail it supports, resets status to Pending.
- **BR7 [BRIEF]** UAN and account number show only the last few digits by default; employee and HR may reveal the full number.
- **BR8 [BRIEF]** HR is notified of bank detail changes; the employee is notified when HR verifies or rejects a document.
- **BR9 [BRIEF]** Home never shows personal phone, personal email, full address or emergency contact.
- **BR10 [BRIEF]** Ex-employees are hidden from search (handled by the future Separation module).
- **BR11 [CONFIRMED]** Masked numbers show the last 4 digits.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Marked BR11 as confirmed.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- **BR12 [CONFIRMED]** Each reveal of a masked number is written to the audit trail (who, which field, when) but the number itself is not stored in the history.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Marked BR12 as confirmed.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- **BR13 [CONFIRMED]** Edit history shows, per change: field, old value, new value, editor, date and time. Masked fields (UAN, account number) show masked old/new values. History is kept indefinitely and cannot be edited or deleted.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Marked BR13 as confirmed.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- **BR14 [CONFIRMED]** Notifications appear in-app. Email is out of scope for the first release.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Marked BR14 as confirmed.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- **BR15 [CONFIRMED]** HR is notified of bank changes made by an employee. If HR itself made the change, no notification is sent (history still records it).
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Marked BR15 as confirmed.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- **BR16 [CONFIRMED]** A rejected document can be replaced by uploading a new file; the new file starts as Pending. The rejected file stays visible in history.
<!-- -------- bee-comment -------- -->
> **@developer**: Yes
> **@bee**: Marked BR16 as confirmed.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- **BR17 [CONFIRMED]** Documents must be PDF. Photos must be JPG or PNG. Both limited to 5 MB.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Marked BR17 as confirmed.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- **BR18 [CONFIRMED]** "Reporting to" must be an active employee and cannot create a loop (A reports to B, B reports to A, or longer cycles). An employee cannot report to themselves.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Marked BR18 as confirmed.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- **BR19 [CONFIRMED]** If a reporting person is marked inactive, HR must reassign their direct reports; until then those employees show "Reporting to: not assigned".
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Marked BR19 as confirmed.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- **BR20 [CONFIRMED]** Organisation email must be unique across employees.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Marked BR20 as confirmed.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- **BR21 [CONFIRMED]** Completeness is the percentage of a fixed checklist completed: photo, date of birth, location, summary, at least one skill, personal phone, personal email, full address, emergency contact, at least one education entry, UAN, bank details. Contact number is optional and not counted. The indicator lists each missing item.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Marked BR21 as confirmed.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- **BR22 [CONFIRMED]** Search matches partial names (case-insensitive, anywhere in first or last name) and organisation email. Results are sorted by name A to Z, filters combine with AND, and 20 results are shown per page. The POD filter matches any of an employee's PODs.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Marked BR22 as confirmed. I added that the POD filter matches any of an employee's PODs, because an employee can now have several.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- **BR23 [CONFIRMED]** A new employee is automatically allocated to client **Internal** and POD **Bench**. HR can change this at any time. An employee always has at least one POD, and their clients are the clients of those PODs. When HR gives an employee a real POD, Internal and Bench are removed automatically; Bench returns only if the employee has no POD left.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Marked BR23 as confirmed. I reworded it so clients follow from PODs, since a POD belongs to one client. Whether Internal and Bench are removed once HR assigns a real POD is a new question (Open Question 16).
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- **BR24 [CONFIRMED]** The Internal client and Bench POD always exist and cannot be deleted or renamed away.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Marked BR24 as confirmed.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- **BR25 [CONFIRMED]** Every POD belongs to exactly one client, chosen when the POD is created.
- **BR26 [CONFIRMED]** Access depends on the employee's role. A person whose role is HR has HR access. Every other role, including Lead, has employee access in this module.
- **BR27 [PROPOSED]** An employee can be in at most one POD per client.
- **BR28 [PROPOSED]** A client that still has PODs cannot be deleted until its PODs are deleted or moved.
- **BR29 [CONFIRMED]** Each employee has exactly one "reporting to" person, regardless of how many PODs they are in.

## Key Workflows
1. **New employee setup**: HR adds employee, sets org email, role, reporting to, and PODs (defaults to Internal and Bench). Employee signs in with their org email and sees Home.
2. **Client or POD setup**: HR adds or updates a client, then adds PODs under it; they become selectable for assignments.
3. **First login**: employee lands on Home, sees completeness indicator, fills missing details.
4. **Update details**: open section, edit, save, history recorded, Home updates where applicable.
5. **Bank change**: employee saves, HR notified, linked documents go to Pending, HR reviews.
6. **Document verification**: upload, Pending, HR verifies or rejects with reason, employee notified.
7. **Search and browse**: search or tap in org chart, see colleague's Home only.
8. **HR edit**: HR edits any section; history shows HR as editor.
9. **HR removes an employee [CONFIRMED]**: HR chooses Delete, confirms, employee is marked inactive and disappears from search and org chart; data is kept.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Marked workflow 9 as confirmed.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
10. **HR deletes client/POD [CONFIRMED]**: blocked with a message listing the number of employees still assigned.
<!-- -------- bee-comment -------- -->
> **@developer**: Yes
> **@bee**: Marked workflow 10 as confirmed.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->

## Edge Cases
- New employee with no specific assignment: Home shows client Internal and POD Bench, and the org chart shows Internal, then Bench, with the other Bench members. [CONFIRMED]
- Employee in several PODs: Home and the org chart show every POD under its client.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Confirmed, and widened it from "several clients" to "several PODs", since each POD sits under one client.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- Upload over 5 MB, wrong file type, or empty file: rejected with a clear message; nothing saved.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Confirmed. No wording change.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- Two people (employee and HR) edit the same section at once: the later save is refused with a message to reload, so no change is silently lost. [CONFIRMED]
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Marked as confirmed.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- Employee edits a verified document's supporting detail: status returns to Pending and employee sees why.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Confirmed. No wording change.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- Employee has no contact number: field is simply hidden on Home.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Confirmed. No wording change.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- Search with no matches: "No employees found". Search never returns ex-employees or inactive employees.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Confirmed. No wording change.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- Invalid IFSC or UAN format: save refused with a message. [CONFIRMED]
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Marked as confirmed.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- Employee tries to open another person's private section by address: access denied.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Confirmed. No wording change.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- Viewer opens a colleague who has become inactive (stale link): shows "Invalid link".
<!-- -------- bee-comment -------- -->
> **@developer**: Show invalid link
> **@bee**: Changed the message from "This employee is no longer available" to "Invalid link". Slice 4 now has a matching criterion.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->

## Out of Scope
- Separation/exit module, including full ex-employee handling.
- Leave, payroll, attendance, performance and other ERP modules.
- Email or SMS notifications (in-app only for now).
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Confirmed. No wording change.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
- Approval workflows for profile edits.
- Bulk import of employees.
- Sign-in, passwords and creating login accounts. Employees log in with their org email, and this will be covered in the Authentication module.
- Extra Lead-only features. A Lead has employee access here.

## Milestone Map (Proposed Vertical Slices)

Risk: moderate to high. The module holds bank details, UAN and personal data, so security, audit and data integrity criteria are included.

### Slice 1: Walking skeleton, HR creates an employee and the employee sees Home
- [ ] HR can create an employee with name, org email and role
- [ ] New employee is allocated to client Internal and POD Bench by default
- [ ] Shows error when org email already exists
- [ ] Employee signs in with org email and lands on own Home showing name, role, PODs, clients, org email
- [ ] HR can choose other PODs for the employee when creating them, and each POD brings its client
- [ ] Employee cannot edit organisation fields
- [ ] Non-HR user cannot open Organisation Details

### Slice 2: HR manages clients and PODs
- [ ] HR can add, update, view clients
- [ ] HR can add, update, view PODs, and each POD must be created under exactly one client
- [ ] HR can assign an employee to one or more PODs, each under a different client
- [ ] Deleting a client or POD with employees is refused with the count of employees
- [ ] Deleting an empty POD succeeds
- [ ] Deleting a client that still has PODs is refused
- [ ] Deleting an empty client with no PODs succeeds
- [ ] Internal client and Bench POD cannot be deleted
- [ ] Assigning an employee a real POD removes Internal and Bench from them
- [ ] Removing an employee's last real POD puts them back in Internal and Bench
- [ ] HR deleting an employee marks them inactive rather than erasing the record

### Slice 3: Personal Details with edit history
- [ ] Employee can edit photo, date of birth, location, summary, skills, activities, bio
- [ ] Changes appear on Home immediately after save
- [ ] Personal phone, personal email, address and emergency contact never appear on Home
- [ ] Photo over 5 MB or not JPG/PNG is rejected with a message
- [ ] History shows field, old value, new value, editor and time
- [ ] HR edit shows HR as editor

### Slice 4: Search and colleague Home
- [ ] Employee can search by partial name
- [ ] Filters for skill, POD, role, location work alone and together
- [ ] POD filter finds employees in that POD even if they are also in other PODs
- [ ] Results sorted A to Z, 20 per page
- [ ] Inactive employees never appear
- [ ] Colleague view shows Home only, with no private sections
- [ ] Shows "No employees found" when nothing matches
- [ ] Opening a link to an inactive employee shows "Invalid link"

### Slice 5: Organisational chart
- [ ] Chart shows organisation, clients, PODs under each client, reporting to, and members of each POD
- [ ] Tapping a person opens their Home
- [ ] Employee in several PODs shows every POD under its client
- [ ] HR cannot set reporting to that creates a loop
- [ ] Inactive reporting person shows "not assigned" until HR reassigns

### Slice 6: Bank Details with masking, documents and verification
- [ ] Employee can save bank details; account number shows last 4 digits only
- [ ] Employee can reveal the full number and the reveal is logged
- [ ] Invalid IFSC is refused with a message
- [ ] Employee can upload a PDF up to 5 MB; status starts Pending
- [ ] Employee can upload any number of documents, each up to 5 MB
- [ ] HR can verify, or reject with a required reason
- [ ] Employee is notified in-app on verify or reject
- [ ] Employee can upload a replacement for a rejected document
- [ ] Editing bank details resets verified documents to Pending
- [ ] HR is notified when an employee changes bank details, and not when HR makes the change

### Slice 7: Educational and Professional Details
- [ ] Employee can add and edit education entries with PDFs and status
- [ ] Employee can add and edit employment history with reference details and PDFs
- [ ] UAN is captured and masked with reveal
- [ ] Invalid UAN is refused with a message
- [ ] Editing a verified item resets it to Pending
- [ ] History available for both sections

### Slice 8: Completeness indicator
- [ ] Employee and HR see a percentage and a list of missing items
- [ ] Colleagues do not see the indicator
- [ ] Percentage updates after saving a missing item
- [ ] Optional contact number does not lower the score

## Module Structure
Not applicable (brief does not indicate a new codebase layout; to be decided at technical planning).

## Open Questions
All 18 questions are resolved. Only BR27 and BR28 are still marked [PROPOSED] in the rules above.

1. RESOLVED: HR deleting an employee marks them inactive.
<!-- -------- bee-comment -------- -->
> **@developer**: mark inactive
> **@bee**: Resolved. Reflected in H4, workflow 9 and Slice 2.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
2. RESOLVED: A client or POD with employees cannot be deleted until they are reassigned.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Resolved. Reflected in H5, workflow 10 and Slice 2.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
3. RESOLVED: Each POD belongs to one client. An employee can be in several PODs under different clients.
<!-- -------- bee-comment -------- -->
> **@developer**: Employee can be part of multiple POD that can be part of different clients but each POD belong to only one client.
> **@bee**: Resolved. See H2 and H3 and the comment there. This replaces the brief's "one POD per employee".
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
4. RESOLVED: Employees log in with their org email. How accounts and sign-in work will be covered in the Authentication module.
<!-- -------- bee-comment -------- -->
> **@developer**: yes as of now employee log in using org email id will cover in details in authentication module.
> **@bee**: Resolved. Moved sign-in to Out of Scope and noted the org email login in Slice 1.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
5. RESOLVED: The employee's role decides whether they are HR, Lead or another role. See "Who" and BR26.
<!-- -------- bee-comment -------- -->
> **@developer**: the role of the employee decides if he is HR, Lead or some other role
> **@bee**: Resolved. See the comment under "Who".
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
6. RESOLVED: Completeness checklist as in BR21.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Resolved.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
7. RESOLVED: Edit history detail and retention as in BR13.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Resolved.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
8. RESOLVED: Reveal audit as in BR12.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Resolved.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
9. RESOLVED: Notifications are in-app only.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Resolved.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
10. RESOLVED: Rejected documents can be re-uploaded.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Resolved.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
11. RESOLVED: HR is not notified of its own bank changes.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Resolved.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
12. RESOLVED: Photos are JPG or PNG, documents are PDF.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Resolved.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
13. RESOLVED: Search behaviour as in BR22.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Resolved.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
14. RESOLVED: Employees default to client Internal and POD Bench.
15. RESOLVED: No limit on how many documents. Each document is 5 MB or less, not combined.
<!-- -------- bee-comment -------- -->
> **@developer**: no limit on number of document and each document should be %mb or less not combined.
> **@bee**: Resolved. I read "%mb" as "5 MB". Written into BR4 and Slice 6.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
16. RESOLVED: Once HR gives an employee a real POD, Internal and Bench are removed automatically. Bench returns only if the employee has no POD left.
<!-- -------- bee-comment -------- -->
> **@developer**: yes
> **@bee**: Resolved. Added to BR23 as a second sentence and as a Slice 2 criterion.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
17. RESOLVED: "Reporting to" is one person per employee, as in the brief.
<!-- -------- bee-comment -------- -->
> **@developer**: one per employee
> **@bee**: Resolved. Added as BR29 so the Leave module can rely on it.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->
18. RESOLVED: Leads have no access in this module beyond a normal employee.
<!-- -------- bee-comment -------- -->
> **@developer**: no
> **@bee**: Resolved. This matches BR26, so no change was needed there.
> - [ ] mark as resolved
<!-- -------- /bee-comment -------- -->

## Revised Assessment
Size: EPIC
Greenfield: yes (ERP Project directory contains docs only)
Risk: MODERATE-HIGH (personal and banking data)

[x] Reviewed
