## Problem 1 – Frontend Developer Challenge: “Add New Notice” (Description not saved)

### Context / Constraints
While setting up the project locally, I could not find the `seed_db/` directory referenced in the repository README (e.g. `seed_db/tables.sql`, `seed_db/seed-db.sql`).  
Because of that, I was not able to fully initialize the database and complete the authentication flow via the backend.

To continue with the frontend task (Problem 1) despite missing seed data, I approached the issue **code-first**:
- I enabled access to the `/app/notices/add` page for local testing (temporarily bypassing auth/permission gating) so I could reproduce and verify the form behavior.
- I validated the form submission payload in the browser DevTools to confirm whether the “Description” value is actually included on submit.

### Root Cause
The “Description” textarea was not being properly bound/submitted due to an inconsistent field mapping in the React Hook Form setup.

Specifically:
- The textarea was registered under one field name, while validation/error rendering (and/or default values/schema) referenced a different field name.
- As a result, the UI could show the field as “required” even when text was entered, and the submitted payload did not reliably contain the intended description value.

### Fix Implemented
I made the form field naming **consistent across the entire flow**:
- `defaultValues` uses `description`
- the textarea input uses `register('description')`
- validation/error UI reads from `errors.description`
- submit handler receives `description` in the `NoticeFormProps` payload

After the fix, clicking **Save** submits a payload that includes:
- `title`
- `description`
- `status`
- `recipientType` / `recipientRole` / `firstField` (if applicable)

### How I Verified (without relying on backend persistence)
1. Opened `/app/notices/add`
2. Entered values in **Title** and **Description**
3. Clicked **Save**
4. Verified via DevTools (console/network) that the submitted form data contains:
   - `description: "<typed value>"`

This confirms that the description field is now properly bound and submitted from the frontend side.

<img width="1876" height="731" alt="Screenshot 2026-03-05 222850" src="https://github.com/user-attachments/assets/db522504-7295-4f70-989f-196eb61f2a93" />
 


