# Scope and traceability

**Status:** Derived from [IMS Scope Document v1.5](../IMS-Scope-Document.md), which is dated 17 November 2026 and marked **Draft for approval**. This document does not approve the scope on behalf of Operations/EHS.

## Product intent

The source proposes one incident management system for NHIT/NHIPPL operations to replace paper, messaging-group capture, and manual report assembly. Its goals include timely point-of-occurrence capture, auditable evidence, bounded approval SLAs, offline operation, statutory encroachment notice tracking, and generation of current NHIT reports.

## Scope map

| Area | Source requirement / status | Trace |
|---|---|---|
| Incident families | Accidents (road-user and work-zone), Encroachment (temporary and permanent), Violence, and Public Unrest are in scope. Natural Disaster is not a full module. | Sections 2, 4, 5, 7, 8 |
| Channels | Android-native offline-first capture for RPO; PWA for office roles. iOS is excluded. | Sections 2, 3, 10 |
| Accident | Source baseline: RPO triage expanded by CRO into an internal descriptive report; A1 includes Corridor review, PM observer, auto-close, and restricted reopen. User-directed design adds a CRO/Corridor Manager toggle and HO final approval for every workflow; no statutory MoRTH form in scope. | Sections 2, 4, 5 plus later user decisions |
| Work zone | Link to an active work permit; live-road and work-at-height checklists; Maintenance Manager and Safety Officer checks; incident suspends permit and raises SWI. | Sections 2, 4, 6 |
| Encroachment | Temporary removal is a fast, same-shift flow. Permanent cases use dated notice, removal, and cost-recovery milestones; issuing parties act offline and Corridor logs evidence. | Sections 2, 7 |
| Violence / unrest | Common master chain with Security or Management co-approver, respectively. | Sections 4, 8 |
| Reporting | Seventeen NHIT operational reports and ten linked registers are named in the scope; exact template-locked workbook output and matching PDF are required. | Sections 2, 11 |
| Cross-cutting | Server-side scope enforcement, offline sync, audit, evidence, notifications, configurable master data/SLA, India hosting, retention, and performance targets. | Section 12 |
| Delivery | Five phases, approximately 29-36 weeks; P0 includes report-template registry/cell mapping; P1 is the road-user accident MVP. | Section 13 |

## Approval and lifecycle requirements

The source baseline says RPO entry within 1 hour, CRO completion within 5 days, Corridor review within 48 hours, accident auto-close 3 days after Corridor approval, and HO/Admin-only reopen. The user has since directed that every workflow end with a final approval by a configurable HO-level approver list; this supersedes the draft source's earlier A1/non-accident approval distinction for the target design. The CRO stage is configurable: CRO completes it when enabled, and Corridor Manager completes it when disabled and routes directly to HO without a separate Corridor review. The source's 48-hour Corridor target therefore does not add a Corridor review to the disabled-toggle path; its applicability and clock boundary on the enabled path remain open. The source does not resolve whether SLA days are calendar or working days (Q6); the auto-close timer's start point under the new HO gate also needs confirmation.

## Additional user-directed requirements (beyond source v1.5)

These are direct project decisions and are not present in the unchanged root scope document:

- **CRO availability toggle:** configuration selects whether CRO completes the CRO stage or the Corridor Manager performs those completion duties. When disabled, the Corridor Manager routes directly to HO without a separate Corridor review. The toggle does not select or remove the final HO approver.
- **HO final-approver roster:** every in-scope workflow terminates at the configured HO-level final-approver list. The CRO-disabled fallback routes directly from Corridor Manager completion to HO, with no separate Corridor review. For the enabled path, exact stage ordering before HO, roster selection semantics (one listed approver, all, quorum, or sequence), configuration scope/effective-time behavior, and self-review controls remain open.
- **Segregation of duties:** whether the Corridor Manager completing the CRO stage may also be selected as the HO approver is unresolved; do not treat same-identity final approval as permitted or prohibited until policy is decided.

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
5. **Source/user approval change:** source v1.5 describes HO/Admin wording and an A1 flow without an HO gate; the later user-directed decision makes the configured HO list the final approver for every workflow. Keep the root source unchanged, apply the newer decision in this documentation, and do not infer that Admin is an HO final approver.

## Source files

The root [scope document](../IMS-Scope-Document.md) is the readable source of truth for the original v1.5 baseline; later user-directed requirements are recorded separately in this planning set and do not rewrite that source. Other tracked references include the accident mobile-interface workbook, road-user process-flow PDF, permit SOP and forms, report/format ZIP, MoRTH reporting PDF, a PDF scope copy, and two images. Binary reference contents and the archive inventory should be reviewed before detailed field/report mappings are treated as verified.

## User and operational journey

1. **Capture:** RPO creates the occurrence record at the scene on Android, with GPS, device timestamp, and camera evidence. Offline drafts remain queued until synchronization.
2. **Complete:** the CRO toggle routes the completion stage to CRO when enabled or to Corridor Manager when disabled; either route completes the descriptive accident report and required department data.
3. **Pre-HO route:** with CRO enabled, the configured route before HO remains open and may include source-defined Corridor/other review stages. With CRO disabled, Corridor Manager completion goes directly to HO with no separate Corridor review. Required checks/co-approvals remain in scope and need mapping.
4. **Final approval:** every in-scope workflow is routed to the configured HO final-approver list. The PM's role and other enabled-toggle pre-HO stages remain subject to source conflicts and open decisions.
5. **Close/reopen:** the source's A1 auto-close rule is three days after Corridor approval, but its trigger must be reconciled with the new HO-final stage. The source restricts reopening to HO/Admin.
6. **Follow supporting work:** a work-zone record links to a permit and safety checks; a permanent encroachment record accrues dated notice milestones. Notifications, audit, and reports are derived from the same authorized record.

Detailed actors, transition tables, timers, and exceptions are in [Workflows](workflows.md).

## Measurable source outcomes

These are source targets, not verified implementation results:

| Source objective/target | Trace / interpretation |
|---|---|
| >=95% incidents created in-app; RPO entry within 1 hour | Section 1 O1; app adoption and capture-time metrics |
| 100% complete GPS/time/photo evidence; all post-submit edits audited | Section 1 O2; integrity and audit coverage |
| 100% traverse required approval levels; SLA breaches visible/escalated | Section 1 O3; workflow and escalation coverage |
| All field forms work offline; zero data loss on sync | Section 1 O4 and Section 12; sync acceptance criteria |
| All 17 formats generated from system data using prescribed templates | Section 1 O5 and Sections 11.1-11.2; count mismatch remains open |
| Notice 1-3, PD notice, SP letter, removal/cost recovery dated and aged | Section 1 O6 and Section 7; notice intervals are open (Q4) |
| Call response, arrival, and clearance computed per incident | Section 1 O7 and Section 12 |
| Cold start <=3 s; lists <=2 s; API p95 <=500 ms; monthly report pack <=30 s | Section 12; test conditions and traffic volumes are open |

## Report and register catalogue

These are the 17 report names enumerated by source Section 11.1. Frequencies and summary content follow the source; archive membership still needs reconciliation.

| # | Report | Frequency | Primary use |
|---|---|---|---|
| 1 | Highway Incident Report (HIR) | Per incident | Investigation, HSMU analysis, appendices/photos |
| 2 | Accident Report (RPO/RPV) | Per accident | Response, casualty, damage, analysis, dual sign-off |
| 3 | Incident Log (RPO) | Per shift | Patrol and incident record with shift totals |
| 4 | Daily accident and incident report | Daily | Fatal/major/minor/non-injury totals |
| 5 | Daily unusual occurrence accident report | Daily | Concession Agreement Article 16.6 occurrence details |
| 6 | Monthly Break Down Report | Monthly | Breakdown occurrence and response |
| 7 | Monthly Dead Animal Removal Report | Monthly | Dead-animal locations/types/removal |
| 8 | Monthly Encroachment Removal data | Monthly | Removals by type, shift, action, and RPO |
| 9 | Monthly unusual occurrence accident data | Monthly | Consolidated daily unusual-occurrence data |
| 10 | Monthly utilisation of ambulance | Monthly | Deployment, calls, response, hospital trips |
| 11 | Monthly vehicle responsible accident data | Monthly | Accident counts by responsible vehicle class |
| 12 | Monthly Vehicle Running Status | Monthly | Vehicle kilometres, fuel, and expiry/service status |
| 13 | Monthly Assets Damage Report | Monthly | Damage, theft, missing assets and quantities |
| 14 | MPR (HQ Format) | Monthly | Ambulance, crane, encroachment, incident, response |
| 15 | OMMPR Encroachment List | Monthly | Permanent encroachment register |
| 16 | Year Accident Details | Monthly cumulative | Monthly severity totals and annual total |
| 17 | Consolidate Report | Monthly | Combined monthly pack |

Section 11.4 names ten linked registers: Daily Vehicle Inspection Report; RP Vehicle Equipment Checklist; Crane Vehicle Equipment Checklist; Log Book of Vehicles; First Aid Register; Ambulance Medicine Checklist/Consumption Register; Ambulance Patient Care Form (PCR serial only); Bio-Medical Waste Register; Incident Log (RPO); Accident Report (RPV). The registers are linked/generated from incident and vehicle data; the scope does not say all paper use is immediately retired.

## Traceability to tracked source artifacts

| Tracked source | How it is used by the scope | Verification status |
|---|---|---|
| [Scope v1.5 (Markdown)](../IMS-Scope-Document.md) | Canonical requirements, decisions, risks, exclusions, report list | Readable; this foundation set traces to it |
| [Scope PDF](../IMS-Scope-Document.pdf) | Parallel scope artifact | Binary; equivalence to Markdown not independently verified |
| [Accident Module mobile interface workbook](../Accident%20Module-%20Mobile%20Interface.xlsx) | Field/mobile interface reference | Binary; use the 21-field description in scope Section 5.2 provisionally |
| [Road User Accident Module Process Flow](../Road%20User%20Accident%20Module%20Process%20Flow.pdf) | A1 process authority per scope Sections 4.1 and 14 | Diagram preview available; detailed text should be checked against the source before final build |
| [SOP for Permit to Work](../SOP%20for%20Permit%20to%20Work.pdf) | Permit process basis for Section 6 | Binary; source scope summarizes the lifecycle |
| [WAH permit EHS/FRM/019](../WAH%20permit-EHS-FRM-019.pdf) | Work-at-height checklist basis | Binary; scope Section 6.2 summarizes checklist categories |
| [Work Permit Live Road Activities](../Work%20Permit%20Live%20Road%20Activities.pdf) | Live-road checklist basis | Binary; scope Section 6.2 lists 32 items by category |
| [Reports and formats archive](../Incident%20Management%20Reports%20%26%20Format.zip) | Report/register samples used by scope Sections 11.1 and 11.4 | Archive not enumerated here; report count requires reconciliation |
| [MoRTH accident-reporting formats](../morth-accident-reporting-formats-22-dec-2017.pdf) | Reference to statutory formats | Binary; scope explicitly defers MoRTH Sections A-E and aggregate tables |
| [Create Accident mock-up](../image.jpg) and [second image](../image.png) | Visual source artifacts | JPG preview shows a capture form; PNG preview was not readable in prior inspection |

The source also references `.opencode/plan/incident-management-scope-v0.2.md`, `Reports/`, `Formats/`, blank NHAI/NHIT workbooks, and a named Create Accident mock-up. These paths are not separate entries in the current root tree; the archive may contain some report/register materials, but its contents must be inventoried.

## P0 prerequisites and report baseline

Before report implementation is called production-ready:

1. Reconcile all 17 report names in Section 11.1 with the 16 entries reported in the supplied report matrix/archive. Produce an approved one-to-one mapping; do not infer the missing/extra item.
2. Obtain a blank NHAI/NHIT workbook for each report. Section 11.2 says the workbook must be populated without restyling; until real templates are available, cell mapping and visual fidelity remain provisional.
3. Confirm which reports remain current (Q7), which templates and versions are authoritative, and whether historical exports must regenerate from the version effective at the time.

## Decision register

| Decision | Source status | Required before |
|---|---|---|
| CRO stage availability toggle | Direct user requirement; toggle's scope/default and in-flight behavior open | Workflow configuration and task routing |
| HO final-approver list | Direct user requirement for every workflow; enabled-toggle route before HO and list-selection semantics open | Authorization matrix and workflow implementation |
| Same-person completion/HO approval | Open segregation-of-duties decision when Corridor Manager completes the CRO stage and may also appear on HO roster | Access policy and approval enforcement |
| Natural Disaster minimal record | Q1 recommends yes; not approved | Data model/report baseline |
| Project Manager approves A2? | Section 4.2 says view-only; Q2 recommends approval | Authorization matrix and workflow implementation |
| SPV/Vendor field 22 | Q3 recommends yes | RPO field contract and routing |
| Notice time periods | Q4 open | Notice aging/escalation configuration |
| Legal appeal case behavior | Q5 open | Permanent encroachment state model |
| SLA values and calendar/work days | Q6 open | Scheduler and acceptance tests |
| 17 current reports and blank workbooks | Q7 open; blank templates are P0 dependency | Report template registry/export sign-off |
| Actual scale | Q8 open | Capacity and performance tests |
| Go-live integrations | Q9 open | Integration architecture |
| External MoRTH reporting | Q10 open | Reporting boundary |
| Safety Officer handset | Q11 open; baseline is PWA-only | Work-zone operations and evidence |

Recommendations in this table are not decisions. See [Users, roles, and access](roles-and-access.md) for dynamic role administration decisions and [API contract](api-contract.md) for API proposals.
