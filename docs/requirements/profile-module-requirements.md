# Profile Module: Client Requirement Brief

## Purpose
We are building an internal ERP for our organisation. This brief covers the **Profile Module**, which is the landing page for every employee after login. Please act as a Business Analyst: read this, ask me clarifying questions, then produce a full spec sheet.

## Users
- **Employee**: views and maintains their own profile, and searches for colleagues.
- **HR**: can view and edit every employee's profile, verifies documents, and manages employees, clients, and PODs.
- **Other employees**: can view only the public Home page of a colleague.

## Decisions Already Made
- **Organisation-level details** (POD, organisation email ID, client, role, reporting to) are set **only by HR**. Employees cannot edit them.
- Changes to personal, educational, professional, and bank details need **no approval**. They take effect on save.
- HR can **view and edit** all private sections of any employee.
- Every edit is recorded in history, including **who** made it (employee or HR).
- All photo and PDF uploads are limited to **5 MB per file**. There is no limit on the number of documents.
- An employee belongs to **one POD** but can work for **more than one client**.
- **Ex-employees**: data is kept but marked as ex-employee and hidden from search. This will be handled in a future Separation module and is out of scope here.

## Home (Landing Page)
Shown to the employee on login, and to any employee who searches for them. It shows:

1. **Employee Details**: photo, name, role, current POD, date of birth (visible to all, e.g. for birthday wishes).
2. **Contact Details**: organisation email ID, contact number (optional), location.
3. **Talent Card**: skills, summary, activities besides work.
4. **Organisational Chart**:
   - Current POD members.
   - Reporting to.
   - Organisation hierarchy: Organisation → Client → POD → Reporting to → Employee, along with team members. If an employee works for several clients, all of them are shown.
   - Every person shown in the chart can be tapped to open that person's Home page.
5. **Search**: any employee can search for another employee and see that person's Home page.
   - Filters: skill, POD, role, location.
   - Ex-employees do not appear.
6. **Profile completeness indicator**: shows the employee how complete their profile is and what is missing. Visible to the employee and HR only.

Home never shows personal phone, personal email, full address, or emergency contact.

## Sub Modules

### 1. Home
As described above. Visible to everyone in the organisation.

### 2. Personal Details (employee and HR only)
- Editable: photo, date of birth, location, summary, skills, activities, bio.
- Private personal contact details: personal phone, personal email, full address.
- **Emergency contact**: name, relationship, phone number.
- Photo, location, summary, skills, activities, and date of birth appear on Home automatically. Private details never do.
- Edit history is available.

### 3. Educational Details (employee and HR only)
- Add and edit education entries (e.g. degree, institution, year).
- Upload supporting PDFs (up to 5 MB each), each with a verification status.
- Edit history is available.

### 4. Professional Details (employee and HR only)
- Add and edit employment history: employer, role, location, tenure.
- For each employer: reference details and supporting PDFs, each with a verification status.
- UAN number is captured and masked on screen.
- Edit history is available.

### 5. Bank Details (employee and HR only)
- Bank name, name as per bank, account number (masked on screen), IFSC code, branch location.
- Upload supporting PDFs (up to 5 MB each), each with a verification status.
- HR is notified whenever bank details are changed.
- Edit history is available.

### 6. Organisation Details (HR only)
HR can **add, update, delete, and view** each of the following:
- **Employees**: create a new employee record, including organisation email ID, role, POD, client(s), and reporting to.
- **Clients**: create and manage the list of clients.
- **PODs**: create and manage the list of PODs.

HR can also search for any employee and change their organisation-level details. These values feed the Home page and organisational chart. Edit history is available. Not visible to employees or Leads.

## Common Rules
- **Document verification**: every uploaded document is Pending, Verified, or Rejected (with a reason). Only HR can change the status. The employee can see it.
- **Edits reset verification**: if an employee edits a verified document or the detail it supports, the status goes back to Pending.
- **Masking**: UAN and account number show only the last few digits by default. The employee and HR can choose to reveal the full number.
- **Notifications**: HR is notified of bank detail changes. The employee is notified when HR verifies or rejects a document.

## Key Workflows
1. **New employee setup**: HR adds the employee in Organisation Details → sets org email, role, POD, client(s), reporting to → employee logs in and sees their Home.
2. **Client or POD setup**: HR adds or updates a client or POD → it becomes available when assigning employees.
3. **First login**: Employee lands on own Home → sees completeness indicator → fills missing details.
4. **Update details**: Employee opens a sub module → edits and saves → change recorded in history → Home updates where applicable.
5. **Bank change**: Employee updates bank details → HR notified → documents go to Pending → HR reviews.
6. **Document verification**: Employee uploads a PDF → Pending → HR reviews → Verified or Rejected → employee notified.
7. **Search and browse**: Employee searches by name or filter, or taps a person in the org chart → sees that person's Home only.
8. **HR edit**: HR edits a private section or organisation details → history shows HR as the editor.

## Questions Still Open (please ask me)
- When HR "deletes" an employee, is the record removed or marked inactive? (Ex-employee handling is a later module.)
- What should happen when a client or POD that still has employees is deleted?
- Does each POD belong to one client, or can a POD serve several clients?

## Out of Scope (for now)
Employee separation and exit, leave, payroll, attendance, performance, and other ERP modules. POD and reporting person details here will later be used by the Leave module.

## What I Need From You
1. Ask me questions to fill any gaps, starting with the ones above.
2. Produce a spec sheet with user stories, screens, rules, workflows, and edge cases.
3. Keep language simple so business and technical teams both understand it.
