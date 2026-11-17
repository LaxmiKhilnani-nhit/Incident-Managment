# Proposed logical architecture

**Status:** Conceptual proposal. The repository currently contains planning/source material, not application code. No cloud provider, framework, database, API gateway, identity provider, or authentication protocol is selected here.

## Constraints from the source scope

The following are stated in [IMS Scope Document v1.5](../IMS-Scope-Document.md); the document itself remains a draft:

- An Android-native, offline-first field app is for Route Patrolling Officers (RPOs). It is the only role assigned a handset in the stated baseline.
- Office workflows use a Progressive Web App (PWA) for CRO, Corridor, Project Manager, Maintenance Manager, Safety Officer, HO, and Admin.
- The design is one incident core with module configuration, a shared API/design system, and one multi-corridor deployment hosted in India.
- Corridor, project, and stretch access is enforced server-side. The RPO loses record-view access after submission; audit history is append-only.
- Field evidence is camera-captured, watermarked with time/location, and queued for offline upload. Offline forms must survive app restarts and synchronize without data loss.
- Report exports must preserve supplied NHAI/NHIT workbook formatting; the PDF is generated from the same populated workbook.

These constraints do not choose a product or implementation technology.

## Proposed component boundaries

This is a way to organize responsibilities, not a commitment to separate deployables or services.

1. **Field client** - RPO draft capture, validation, GPS and camera metadata, encrypted local draft storage, retryable synchronization, and a visible sync state.
2. **Office client** - role- and scope-aware queues, CRO completion, Corridor review, work-permit checks, notice logging, dashboards, and report requests.
3. **Application/API boundary** - validates requests, derives the acting user's effective assignments, enforces server-side permissions and scope, and records state-changing actions.
4. **Incident core** - shared identity, location, evidence references, lifecycle, audit, notifications, and common descriptive fields. Module-specific fields and rules are configuration/data, not permission bypasses.
5. **Workflow and SLA capability** - evaluates module/state transitions, deadlines, escalation, approval authority, returns, reopen, and scheduled auto-close.
6. **Supporting capabilities** - master data; permit-to-work and SWI; encroachment milestones; evidence/document handling; report template registry and exports; integrations.
7. **External systems/parties** - GIS, messaging, SMTP, SSO, FIR reference, VMS/ATMS, CCTV, weather, or ERP integrations are prioritized in the source but remain subject to availability and go-live decisions. PIU, PD, and SP have no IMS user access in the source model; their notices are recorded by the Corridor.

## Conceptual data boundaries

The following nouns are design aids only. Names, storage shape, and identifiers are not approved.

- **User**: a person who may authenticate; organization membership and status are separate from role definitions.
- **Role template / permission**: reusable capability set; a user's effective access comes from active assignments and scope (see [Users, roles, and access](roles-and-access.md)).
- **Incident**: common identity, family/subtype, location, chronology, evidence references, current workflow state, and version.
- **Module details**: accident report, violence/unrest details, or temporary/permanent encroachment-specific data.
- **Workflow action**: actor, action, previous/next state, timestamp, reason, and applicable SLA.
- **Evidence/document**: metadata, capture provenance, and storage reference; binary storage technology is undecided.
- **Permit / safety check / SWI**: work authorization, verifications, linked work-zone incidents, suspension, and close-out.
- **Notice milestone**: offline-issued document reference, printed issue date, allowed period, recorded date, evidence, and ageing status.
- **Report template/export**: template version/effective date, mapping, generated workbook, corresponding PDF, and provenance.
- **Audit event**: append-only field/action history with actor, time, old/new values, and reason.

## Conceptual request and data flow

1. An assigned RPO creates a local draft; the client validates fields and captures GPS/time/evidence metadata.
2. On synchronization, the API resolves the user's current assignment and scope, rejects unauthorized or malformed changes, and safely accepts retries.
3. Submission locks the RPO's edit/view permissions as specified; workflow rules create the CRO task and deadline.
4. CRO completion and subsequent reviews are recorded as explicit actions, with audit events and notifications.
5. Workflow rules update queues, deadlines, escalation, and (for accidents) scheduled auto-close. A closed incident can only be reopened by HO/Admin.
6. Reporting reads authorized incident data and writes into the active prescribed template version; template version and export history remain traceable.

## Required qualities and open design work

The source specifies offline-first behavior, encrypted local storage, retry/resume, server-side scope checks, immutable audit, 8+ year retention for accident/encroachment records, India hosting, and performance targets. Detailed threat model, key management, backup/restore, retention exceptions, observability, disaster recovery, and deployment topology still need design.

Do not implement the proposal until the source's open questions and the user/role model are reviewed. In particular, work-zone Project Manager authority conflicts with the baseline accident observer rule; see [Scope and traceability](scope-and-traceability.md).
