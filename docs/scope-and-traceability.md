# Scope and traceability

**Status:** Derived from [IMS Scope Document v1.5](../IMS-Scope-Document.md), which is dated 17 November 2026 and marked **Draft for approval**. This document does not approve the scope on behalf of Operations/EHS.

## Product intent

The source proposes one incident management system for NHIT/NHIPPL operations to replace paper, messaging-group capture, and manual report assembly. Its goals include timely point-of-occurrence capture, auditable evidence, bounded approval SLAs, offline operation, statutory encroachment notice tracking, and generation of current NHIT reports.

## Scope map

| Area | Source requirement / status | Trace |
|---|---|---|
| Incident families | Accidents (road-user and work-zone), Encroachment (temporary and permanent), Violence, and Public Unrest are in scope. Natural Disaster is not a full module. | Sections 2, 4, 5, 7, 8 |
| Channels | Android-native offline-first capture for RPO; PWA for office roles. iOS is excluded. | Sections 2, 3, 10 |
| Accident | RPO triage expanded by CRO into an internal descriptive report; road-user flow uses RPO -> CRO -> Corridor, with PM as observer, auto-close, and restricted reopen. No statutory MoRTH form in scope. | Sections 2, 4, 5 |
| Work zone | Link to an active work permit; live-road and work-at-height checklists; Maintenance Manager and Safety Officer checks; incident suspends permit and raises SWI. | Sections 2, 4, 6 |
| Encroachment | Temporary removal is a fast, same-shift flow. Permanent cases use dated notice, removal, and cost-recovery milestones; issuing parties act offline and Corridor logs evidence. | Sections 2, 7 |
| Violence / unrest | Common master chain with Security or Management co-approver, respectively. | Sections 4, 8 |
| Reporting | Seventeen NHIT operational reports and ten linked registers are named in the scope; exact template-locked workbook output and matching PDF are required. | Sections 2, 11 |
| Cross-cutting | Server-side scope enforcement, offline sync, audit, evidence, notifications, configurable master data/SLA, India hosting, retention, and performance targets. | Section 12 |
| Delivery | Five phases, approximately 29-36 weeks; P0 includes report-template registry/cell mapping; P1 is the road-user accident MVP. | Section 13 |

## Approval and lifecycle requirements

The source baseline says RPO entry within 1 hour, CRO completion within 5 days, Corridor review within 48 hours, accident auto-close 3 days after Corridor approval, and HO/Admin-only reopen. Non-accident modules proceed to Project Manager and HO approval. The source does not resolve whether SLA days are calendar or working days (Q6).

## Explicit exclusions and deferrals

- Full Natural Disaster workflows, checklists, and reports are deferred until its SOP is supplied. A minimal record is recommended in Q1 but remains an open decision.
- MoRTH Road Accident Recording Form Sections A-E and 18 aggregate tables are deferred; the in-scope accident record is descriptive/internal.
- PIU, PD, and SP do not log into IMS; their notices and letters are issued offline and recorded by Corridor.
- Police prosecution/court follow-up, clinical PCR contents, insurance settlement/vendor recovery accounting, toll/revenue systems, civil rectification design/BOQ, telematics/CCTV analytics, iOS, payroll, procurement, and depreciation are outside this scope or belong elsewhere.

## Requirement traceability to the source

| Source location | Requirement area | Foundation document |
|---|---|---|
| Sections 1-3 | Objectives, channels, roles, visibility | [Architecture](architecture.md), [Users and roles](roles-and-access.md) |
| Sections 4-5 | Approval stages, accident data, report, reopen | [Workflows](workflows.md), [API contract](api-contract.md) |
| Section 6 | Permit lifecycle, work-zone checklists, SWI | [Workflows](workflows.md), [API contract](api-contract.md) |
| Section 7 | Temporary/permanent encroachment lifecycle | [Workflows](workflows.md), [API contract](api-contract.md) |
| Sections 8-9 | Violence/unrest, automation boundaries | [Workflows](workflows.md), [Architecture](architecture.md) |
| Sections 10-12 | Design options, reports, cross-cutting constraints | [Architecture](architecture.md), [API contract](api-contract.md) |
| Sections 13-15 | Phases, risks, open decisions | This document and the relevant foundation documents |
| Section 16 | Sign-off | Approval remains outstanding in the source |

## Open decisions and recommendations (not approved here)

The scope document's Section 15 contains Q1-Q11. Key items for design review:

- Q1: minimal Natural Disaster capture, with the full module deferred.
- Q2: whether Project Manager approves work-zone accidents. Section 4.2 says view-only for A2; Q2 recommends approval for A2. This is a material source conflict.
- Q3: add SPV/Vendor Member Involved as an RPO field to determine routing; recommendation is yes.
- Q4-Q5: notice periods and whether legal/appeal cases pause or live outside IMS.
- Q6: confirm SLA values and calendar versus working-day semantics.
- Q7: verify the current 17 reports and supply blank NHAI workbooks.
- Q8-Q10: operational scale, go-live integrations, and any external statutory MoRTH return.
- Q11: whether Safety Officer needs an Android handset; baseline is PWA-only, while R12 flags delayed desk entry as a risk.

Additional detailed-design questions are listed after Q11 in the source (language support, data ownership, template mapping/versioning, back-dating, fatality notifications, retention, corrective actions, and HO approval threshold).

## Known discrepancies and dependencies

1. **Report count:** the scope lists 17 report formats, while the supplied report matrix/archive is reported to contain 16. Do not silently drop or invent a report; reconcile the named report list against the archive and identify the missing/extra entry before committing the reporting baseline.
2. **Blank NHAI templates:** these are an explicit P0 dependency in Section 11.2 and Risk R11. The source permits provisional development against current report files, but export styling cannot be finalized until the blank templates are supplied per report.
3. **Work-zone approval:** the A2 baseline and Q2 recommendation conflict; keep permission policy configurable until a decision is recorded.
4. **Referenced materials:** the source refers to `.opencode/plan/incident-management-scope-v0.2.md`, `Reports/`, and `Formats/`. These are not root entries in the current repository tree; the reports ZIP may bundle related files, but its contents need inventory.

## Source files

The root [scope document](../IMS-Scope-Document.md) is the readable source of truth for this planning set. Other tracked references include the accident mobile-interface workbook, road-user process-flow PDF, permit SOP and forms, report/format ZIP, MoRTH reporting PDF, a PDF scope copy, and two images. Binary reference contents and the archive inventory should be reviewed before detailed field/report mappings are treated as verified.
