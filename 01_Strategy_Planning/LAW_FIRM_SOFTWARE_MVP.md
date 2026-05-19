# Law Firm Software MVP Blueprint

This plan turns the feature list into a buildable MVP for a small, high-volume neighborhood law firm. The first release should focus on reducing daily administrative drag: intake, client lookup, case status, document organization, tasks, billing, and payment tracking.

## Product Goal

Build a secure, mobile-friendly practice management system that lets a small firm:

- Capture new clients quickly from a phone or desktop.
- Find any client, matter, deadline, or document in seconds.
- Track next actions so court dates, filings, and payments do not get missed.
- Generate basic invoices and record payment plans.
- Preserve portability through exports, backups, and clean data ownership.

## MVP Scope

### Release 1: Daily Operations

1. Client intake and CRM
   - Mobile-first intake form.
   - Draft autosave for incomplete intakes.
   - Client profile with contact details, notes, contact history, emergency contacts, and family links.
   - Search by name, phone, email, address, case number, and aliases.
   - Referral source and marketing source tracking.

2. Case management
   - Case dashboard with status, practice area, assigned staff, deadlines, and next action.
   - Task assignment with reminders.
   - Case timeline for calls, filings, court dates, payments, notes, and document uploads.
   - Case-type checklist templates for criminal, eviction, family, personal injury, and civil rights matters.

3. Document management
   - Secure file upload from phone or desktop.
   - Auto-naming by client, case, date, and document type.
   - Tags and document categories.
   - Template library for letters, engagement agreements, pleadings, and fee documents.
   - Basic version history.

4. Billing and payments
   - Flat-fee matters.
   - Hourly time entries with timer support.
   - Invoice generation with PDF export.
   - Payment plan schedule and installment tracking.
   - Manual payment recording for cash, card, Cash App, Venmo, Zelle, check, and money order.
   - Sliding-scale, pro bono, and hardship flags.

5. Security baseline
   - Role-based access for attorney, paralegal, admin, and billing user.
   - Required 2FA for staff.
   - Encryption in transit and at rest.
   - Audit log for client, case, document, invoice, payment, and export access.
   - Automated database and file backups.
   - Client file export by matter.

### Release 2: Communication and Scale

- SMS reminders for appointments, court dates, documents needed, and payment due dates.
- Calendar sync with Google and Outlook.
- Client portal for case status, uploads, invoices, and payments.
- Spanish and English interface support.
- Quick translation workflow for notes and messages.
- Batch workflows for similar cases.
- Court location, travel-time estimate, and virtual meeting link generation.

### Release 3: Advanced Practice Tools

- IOLTA trust accounting workflows.
- State bar compliance exports.
- Financial reporting by practice area, attorney, and referral source.
- Discovery document summarization.
- Drafting assistant for routine letters.
- Conflict check assistant.
- Mobile app with push notifications.

## Non-Goals for MVP

These should not block the first release:

- Native iOS or Android apps.
- Full trust accounting ledger.
- Automated legal advice.
- Deep accounting-system replacement.
- Real-time document collaboration.
- Court e-filing integrations.
- Automated dangerous-area scoring without a clear, lawful, and reviewable policy.

## Recommended Stack

The fastest practical stack for this workspace:

- Frontend: Next.js with responsive web UI.
- Backend: Next.js API routes or FastAPI if a Python service is preferred.
- Database: PostgreSQL.
- Auth: Supabase Auth or Auth0.
- File storage: S3-compatible object storage with server-side encryption.
- Search: PostgreSQL full-text search for MVP, Meilisearch or OpenSearch later.
- SMS: Twilio.
- Email: Postmark, SendGrid, or AWS SES.
- PDFs: Server-side HTML-to-PDF generation.
- E-signature: Dropbox Sign, DocuSign, or an open-source/manual signature workflow for MVP.

## Core Data Model

### Users and Access

- User: name, email, phone, role, status, 2FA status.
- Role: attorney, paralegal, admin, billing, read-only.
- Permission: resource, action, role.
- AuditEvent: user, action, resource type, resource id, timestamp, IP/device metadata.

### Client CRM

- Client: legal name, preferred name, date of birth, contact details, address, language, hardship flag, pro bono flag.
- ClientContact: phone, email, address, preferred contact method, consent flags.
- ClientRelationship: client id, related client id, relationship type.
- Intake: client id, source, practice area, answers, draft status, submitted timestamp.
- ContactLog: client id, case id, channel, summary, staff member, timestamp.

### Matters

- Case: client id, case number, practice area, status, assigned attorney, assigned staff, opened date, closed date.
- CaseEvent: case id, event type, title, notes, occurred at, created by.
- Deadline: case id, title, due date, jurisdiction/court, reminder settings, completion status.
- Task: case id, assignee, title, due date, priority, status.
- ChecklistTemplate: practice area, checklist items.
- ChecklistItem: case id, title, status, due date, assigned user.

### Documents

- Document: case id, client id, file name, storage key, document type, tags, version, uploaded by.
- DocumentVersion: document id, storage key, version number, uploaded by, timestamp.
- Template: practice area, document type, template file, active status.
- SignatureRequest: document id, provider, signer, status, requested timestamp.

### Billing

- FeeAgreement: case id, type, flat fee amount, hourly rates, payment plan terms.
- TimeEntry: case id, user id, description, started at, ended at, duration, rate, billable status.
- Invoice: case id, client id, invoice number, status, total, due date, pdf storage key.
- PaymentPlan: invoice id or case id, total amount, installment amount, frequency, start date, status.
- Payment: client id, case id, invoice id, amount, method, received date, reference, notes.

## Offline and Reliability Strategy

Offline support should be limited and deliberate in the MVP:

- Intake drafts should save locally in the browser first, then sync when connected.
- Staff should see a clear sync status for unsent drafts.
- File uploads should resume or retry after connection loss.
- Critical staff workflows should show last-saved timestamps.
- The system should run automated nightly backups and keep tested restore procedures.

Full offline case management can wait until intake, drafts, and upload retry are stable.

## Security Requirements

- Enforce least-privilege role access.
- Require 2FA for all staff accounts.
- Log access to client profiles, cases, documents, exports, invoices, and payments.
- Encrypt database disks and object storage.
- Use signed URLs for document downloads.
- Set short-lived sessions for admin accounts.
- Provide matter-level export for client portability.
- Do not expose confidential matter data to AI tools unless the firm has approved vendor terms, retention controls, and client confidentiality review.

## Practical Neighborhood-Firm Features

- Spanish and English intake from day one if the firm serves Spanish-speaking clients.
- SMS consent capture during intake.
- Payment method flexibility, including manual recording for community-standard payment methods.
- Hardship and sliding-scale flags that are visible in billing but not used to stigmatize clients.
- Address notes should be factual and policy-controlled. Avoid informal labels; use operational notes such as "requires two-person visit", "meet at court", or "remote meeting preferred" when justified.
- Virtual meeting creation should be one click from the case dashboard.

## Delivery Plan

### Sprint 0: Foundation

- Confirm practice areas, user roles, current tools, and compliance constraints.
- Choose hosting, auth provider, storage, and SMS provider.
- Define matter statuses and checklist templates.
- Create database schema and audit log baseline.

### Sprint 1: Intake and Client Search

- Build mobile intake form.
- Add autosave drafts.
- Create client profile.
- Add search across client name, phone, email, address, and case number.
- Capture referral source and SMS consent.

### Sprint 2: Cases, Tasks, and Timeline

- Create case dashboard.
- Add deadlines, tasks, reminders, and status tracking.
- Add case timeline.
- Add checklist templates per practice area.

### Sprint 3: Documents

- Add secure uploads.
- Add document tags, categories, and auto-naming.
- Add template library.
- Add basic version history.

### Sprint 4: Billing

- Add flat-fee and hourly matter settings.
- Add time entries and timer.
- Generate invoices and PDFs.
- Add payment plans and manual payment recording.

### Sprint 5: Security Hardening and Pilot

- Enforce RBAC.
- Turn on 2FA.
- Verify audit logs.
- Test backups and exports.
- Pilot with real staff on a limited set of matters.

## MVP Acceptance Criteria

The MVP is ready for pilot when staff can:

- Complete intake on a phone and recover an incomplete draft.
- Create a client and case from that intake.
- Find a client or matter by name, phone, address, or case number.
- See the next action, next deadline, and responsible staff member for every open case.
- Upload and retrieve documents from a client matter.
- Generate an invoice PDF and record payments or installments.
- Export a complete client matter file.
- Review an audit trail for access and key changes.

## Open Decisions

- Primary practice areas for launch.
- Number of staff users and roles.
- Whether to buy and configure Clio/MyCase first or build custom software.
- Required state bar trust-accounting rules.
- Preferred payment processor and whether Cash App, Venmo, and Zelle are recorded manually or integrated.
- Whether Spanish support is mandatory for MVP or Release 2.
- Whether client portal access is required before pilot.

