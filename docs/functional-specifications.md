# Functional and non-functional specifications

**Status:** Technical specification baseline for design review; not an approved implementation contract. It is derived from [IMS Scope Document v1.5](../IMS-Scope-Document.md), which is marked draft, and adds later confirmed user decisions. No application implementation is present in this branch.

## 1. Authority, status, and interpretation

### 1.1 Requirement status labels

| Label | Meaning |
|---|---|
| **[SRC-DRAFT]** | Stated in the source scope. It remains subject to source approval and source conflicts. |
| **[USER]** | Confirmed by the user after the source scope was written; this specification preserves that decision. |
| **[OPEN]** | A source question/conflict or a design decision that has not been resolved. Do not encode a single behavior as approved. |
| **[PROPOSAL]** | A technical contract suggestion to make review or later testing concrete; not a user-approved requirement. |

Use **MUST** for a [SRC-DRAFT] or [USER] requirement within its stated status; do not read MUST as evidence that the draft source has been formally signed off. A requirement tagged [OPEN] must remain configurable, blocked from implementation, or explicitly marked as unresolved until a decision is recorded.

### 1.2 Confirmed workflow decisions

1. **[USER] CRO availability is a toggle.** When enabled, CRO performs the existing CRO stage. When disabled, the acting Corridor Manager performs that stage in CRO's place.
2. **[USER] The toggle changes only the CRO-stage actor.** It does not remove, add, bypass, or reorder downstream workflow stages. The CRO-disabled path continues through the same downstream route as the CRO-enabled path.
3. **[USER] Every in-scope workflow has a final decision at the configured HO-level final-approver list.** Where the existing workflow includes PM before HO, preserve that order. The PM remains an observer, not an approver, in A1. The source conflict for A2 is unresolved.
4. **[OPEN]** HO roster selection rules (one listed approver, all, quorum, or sequence), configuration authority/effective-time behavior, and whether a CRO-stage completion actor may also perform a later review or final approval are not decided here.

### 1.3 Technical neutrality

This specification does not select a programming language, framework, database, cloud/hosting vendor, identity provider, authentication protocol, storage product, messaging provider, or deployment topology. API paths and state names from other docs remain conceptual unless marked as source terminology.

## 2. Scope and actors

### 2.1 In-scope features

- Road-user accidents (A1) and work-zone/OHS accidents (A2).
- Work permits, permit verification, work-zone incident linkage, and Stop Work Instruction (SWI).
- Temporary and permanent encroachment.
- Violence and Public Unrest.
- Operational reports and linked registers.
- Dynamic user, role-template, scope-assignment, and workflow-setting administration.
- Offline capture/synchronization, audit history, and evidence handling.

Natural Disaster is not a full module until its SOP is supplied. A minimal downtime record is an open recommendation, not an in-scope requirement. Statutory MoRTH forms and aggregate tables are excluded by the source scope.

### 2.2 Actors

The table gives source responsibilities and does not create an authorization policy. User assignments and resource scope must be evaluated server-side.

| Actor | Source responsibility |
|---|---|
| RPO | Creates incident and field evidence on Android; own project only; loses record-view access after submission. |
| CRO | Completes the CRO stage and descriptive accident information when the CRO toggle is enabled. |
| Corridor Incharge / Operations Manager Corridor | Reviews the existing downstream stage; may comment/edit/approve where source assigns it; logs offline encroachment notices. When the CRO toggle is disabled, completes the CRO stage in CRO's place. |
| Project Manager (PM) | Observer on A1; approves non-accident modules in source chain; authorizes work permits. A2 incident authority is conflicted. |
| Maintenance Manager | Physically verifies permit/work-zone conditions and records checks through PWA after site visit. |
| Safety Officer / EHS | Verifies safety/SIMOPS, records checklist results, and handles SWI. PWA-only in the baseline. |
| HO final approver | Member of the configured HO-level list; final decision for each in-scope workflow under the later user decision. Roster-selection policy remains open. |
| Admin | Manages users, masters, workflow/report templates, and SLAs in the source; only HO/Admin may reopen under source rules. Admin is not implicitly an HO approver. |
| HSMU | Prepares the Highway Incident Report investigation, cause analysis, summary, and appendices. |
| Security / Management co-approver | Security co-approval for Violence; Management co-approval for Public Unrest. Exact co-approval order/permissions need confirmation. |
| PIU / PD / SP | Issue statutory notices/letters offline; they do not have IMS user accounts. Corridor records the references and evidence. |

## 3. Shared incident contract

### FS-COM-001 — Server-authoritative workflow [SRC-DRAFT]

The service MUST derive permitted actions from the authenticated actor, active role assignments, organizational scope, module, current state, and workflow policy. The client MUST NOT choose an arbitrary next state or self-assert its effective role/scope.

**Acceptance criteria**

- **AC-COM-001a:** A caller without an active assignment for a record's corridor/project/stretch cannot read or mutate it through either a direct lookup or a list/filter query.
- **AC-COM-001b:** A request with an action invalid for the current state is rejected without a partial state change.
- **AC-COM-001c:** The system records an allowed transition and its audit event atomically or returns an explicit failure; it never reports success for a rejected transition.

### FS-COM-002 — CRO-stage actor toggle [USER]

For workflows which already contain a CRO stage, the configured toggle MUST assign that stage to CRO when enabled and to the acting Corridor Manager when disabled. It MUST NOT change any other stage, actor, approval, validation, or stage ordering.

**Acceptance criteria**

- **AC-COM-002a:** With the toggle enabled, a newly assigned CRO-stage task is available to an eligible CRO.
- **AC-COM-002b:** With the toggle disabled, the same CRO-stage work is available to an eligible Corridor Manager and the audit records the actual actor as Corridor Manager.
- **AC-COM-002c:** For the same module and record data, both toggle outcomes produce identical downstream stage sequences; only the CRO-stage task owner differs.
- **AC-COM-002d:** A toggle change does not silently rewrite an in-flight task. Effective-time and in-flight behavior remain [OPEN] until specified.

### FS-COM-003 — Final HO approval [USER]

Every in-scope workflow MUST end at the configured HO-level final-approver list. The final HO decision MUST occur after the workflow's existing downstream stages. Preserve PM-before-HO order where that stage exists; do not treat A1's PM observer notification as approval.

**Acceptance criteria**

- **AC-COM-003a:** A workflow cannot reach its completed/closed terminal outcome before an eligible member of the configured HO list records the final decision.
- **AC-COM-003b:** A user not eligible under the active HO list cannot perform the final decision, even if they hold another review role.
- **AC-COM-003c:** CRO-off routing does not bypass Corridor, PM, co-approval, Maintenance, or Safety stages required by that workflow.
- **AC-COM-003d:** Roster selection (single/all/quorum/sequence), list editing authority, and same-person review/approval are not inferred by these criteria; those remain [OPEN].

### FS-COM-004 — Common transition, exception, and notification evidence [SRC-DRAFT]

For each transition, retain the actor, action, previous/resulting state, event time, reason/comment where applicable, changed values, and related evidence. Notify the next responsible actor and expose pending/overdue items; exact escalation recipient and cadence are configurable/open where the source is silent.

**Acceptance criteria**

- **AC-COM-004a:** A return, cancellation, reopen, or denied transition does not erase the previous state or audit history.
- **AC-COM-004b:** The workflow queue displays the current stage, assigned role/actor, and stage age for authorized users.
- **AC-COM-004c:** A transition creates a notification/work item for the next actor when one is defined; delivery failure is visible and does not silently advance the workflow.

## 4. Road-user accident (A1)

**Trace:** Source Sections 2, 3, 4.1, 5.1-5.4, 9, 12, 14-15; later [USER] CRO toggle and HO final-approver decisions. A1's PM is observer-only in the source.

### FS-A1-001 — Capture and submit RPO triage [SRC-DRAFT]

**Actors / preconditions:** Assigned RPO; active project and chainage masters; Android field client.

**Trigger:** RPO creates a draft at incident location and submits it.

**Main flow:** Save a draft with incomplete information; capture device/GPS/evidence metadata; validate on submit; lock the submitted record; revoke RPO view access; create the CRO-stage task according to FS-COM-002; notify PM as read-only observer.

**Fields and validation:** The source defines 21 fields: occurrence date/time; chainage; direction; road type; GPS location/accuracy/time; accident category; crash type; dependent nature; severity; hit-and-run; vehicle count; total persons; killed/grievous/minor counts; calculated uninjured; accident photos; safety-arrangement photos; description; draft/submit action. Occurrence time cannot be future; an entry older than 48 hours requires CRO approval. Chainage must be within project bounds. GPS accuracy target is <=50 m; a location more than 1 km from chainage warns and requires a reason. Casualty counts cannot exceed total persons; uninjured is calculated and nonnegative. Severity must agree with casualty counts. Accident photos: 2-10, live camera, watermarked with date/time/GPS/chainage, <=2 MB each. Safety photos: 1-10. Description: 20-1000 characters and not only numeric/special characters. Draft may be incomplete; submit may not.

**Alternate/error paths:** Offline draft remains queued. Invalid fields block submission and identify errors. Stale/late occurrence requires the source-defined CRO approval rather than silent acceptance. Crash type filters valid nature values. Single-vehicle hit-and-run is automatically Not Applicable. Duplicate detection within 100 m is source automation; its resolution path is [OPEN].

**Authorization:** RPO creates/edits own-project drafts and cannot view the submitted record. Server enforces project scope. CRO can access assigned completion stage; PM receives read-only access. Later reviewers cannot use UI visibility as authorization.

**Timers / notification / evidence:** RPO entry within 1 hour; CRO completion within 5 days from incident creation. Corridor review within 48 hours after CRO submission. PM is notified on submission. Clock pause, business-day semantics, and late/duplicate handling beyond stated rules are [OPEN]. Preserve occurrence, device-capture, and server-receipt times separately.

**Conceptual transitions:** `DRAFT -> SUBMITTED/PENDING_CRO -> PENDING_CORRIDOR -> PENDING_PROJECT_MANAGER (observer) -> PENDING_HO -> final decision`. State labels follow source terms where available; the HO state is a [USER] addition.

**Acceptance criteria**

- **AC-A1-001a:** Draft save succeeds when optional/incomplete fields remain; submit fails closed on every required validation failure.
- **AC-A1-001b:** Submitted RPO record is not readable by its submitting RPO after submission; the PM can read it but cannot edit, return, or approve it.
- **AC-A1-001c:** Casualty arithmetic and severity inconsistencies are rejected or flagged according to source rules; no fatality count is silently overwritten.
- **AC-A1-001d:** Out-of-bound chainage, poor GPS accuracy, >1 km mismatch, older-than-48-hour entry, and invalid photo count/size each produce the source-required block/warning/approval path.
- **AC-A1-001e:** Offline submit replay does not create a second incident; see FS-OFF requirements for retry contract.

### FS-A1-002 — CRO descriptive report completion [SRC-DRAFT] [USER]

The task is assigned to CRO when enabled and Corridor Manager when disabled. The completing actor MUST fill the existing CRO report sections and submit to Corridor; the fallback changes only the actor.

**Required report data:** System report number; received/reported timestamps; corridor/project/plaza; police station/district/FIR and investigating officer; precise location/lane/urban-rural/work-zone; vehicle rows; injured/fatality rows; ambulance/crane response; traffic clearance/diversion; NHIPPL and private property damage; cause/contributory causes; eyewitnesses, weather/light/surface, remedial action; required sign-offs.

Part 1 RPO fields are read-only to CRO. Correction requires return to the permitted supervisor/correction path and an audited amendment with reason. Before CRO submission, report identity, location, vehicles, injured persons, and cause analysis are required; Part 2 vehicle/casualty counts reconcile with Part 1; severity changes require justification and both values remain retained.

**Acceptance criteria**

- **AC-A1-002a:** Toggle-enabled and toggle-disabled tasks contain the same CRO-stage fields and validation.
- **AC-A1-002b:** An incomplete report or unreconciled count cannot be submitted to Corridor.
- **AC-A1-002c:** A severity correction requires a reason and preserves original and corrected values in audit.
- **AC-A1-002d:** The accepted completion actor is stored as the actual CRO or Corridor Manager, not inferred from the configured toggle.

### FS-A1-003 — Corridor review, PM observation, and HO final approval [SRC-DRAFT] [USER]

After the CRO stage, Corridor reviews/comments/edits and approves within the existing stage; PM is notified and remains read-only observer; the workflow then routes to the configured HO final-approver list. HO is the final approver under the later user decision, without converting PM into an approver.

**Acceptance criteria**

- **AC-A1-003a:** Both completion actors reach the same Corridor review stage, then the PM observer stage, then HO.
- **AC-A1-003b:** PM observation cannot satisfy a Corridor or HO approval action.
- **AC-A1-003c:** A1 cannot complete without the configured HO final decision.
- **AC-A1-003d:** Whether the same Corridor Manager can both complete the CRO stage and perform the later Corridor review is [OPEN]; do not enforce allow or deny until decided.

### FS-A1-004 — Auto-close, reopen, and exception handling [SRC-DRAFT] [USER] [OPEN]

The source specifies automatic closure three days after Corridor approval and restricts reopening to HO/Admin; reopened records restart at the CRO stage and source clocks repeat. The later user decision adds HO as final approver. The trigger for the 3-day timer relative to the new HO gate is [OPEN] and MUST be resolved before implementing the scheduler.

**Acceptance criteria**

- **AC-A1-004a:** Only HO/Admin can reopen an eligible closed record; a reason and actor are audited.
- **AC-A1-004b:** Reopen returns the record to the CRO stage, with the toggle selecting CRO or Corridor Manager.
- **AC-A1-004c:** Scheduler does not close an incident until the HO final-decision rule is satisfied; source timer-origin conflict remains a release blocker.
- **AC-A1-004d:** Rework, escalation, cancellation, 15-minute grace editing, and timer pause/reset behavior follow approved policy; until then no silent deadline extension occurs.

## 5. Work-zone accident (A2) and permits

### FS-A2-001 — Classify and route a work-zone incident [SRC-DRAFT] [OPEN] [USER]

**Actors:** RPO, CRO or toggle-off Corridor Manager, Corridor, Maintenance Manager, Safety Officer, PM, configured HO approver.

**Preconditions:** Incident is identified as OHS/work-zone; permitted activity requires an active permit reference.

**Trigger:** RPO submits incident.

**Main flow:** Capture the common RPO accident triage; derive A1/A2 route from Crash Type and the proposed SPV/Vendor Member Involved flag (Q3); assign CRO stage by toggle; preserve the A2 source path through Corridor, Maintenance, Safety, PM stage, then configured HO final decision. The source marks PM view-only on A2; Q2 recommends PM approval. That conflict remains [OPEN] and does not change the required order before HO. No separate manual routing toggle is introduced.

**Alternate/error paths:** If no active permit exists, capture non-permitted activity with a mandatory reason and route to Safety. If linked permit is active, incident suspends it, raises SWI, and blocks permit close-out until incident closure. Failed safety checklist items remain visible in incident dossier. The SPV/Vendor field 22 is Q3 recommendation, not approved.

**States:** Source names `PENDING_CRO`, `PENDING_CORRIDOR`, `PENDING_PROJECT_MANAGER`, `AUTO_CLOSED`, `REOPENED`, and work-zone `SWI_ACTIVE`. Maintenance/Safety states and the new final HO state are conceptual unless confirmed in implementation design.

**Timers / notification / evidence:** A2-specific capture/completion/review timers are not separately confirmed. Do not apply A1's 1h/5d/48h/3-day rules to A2 without approval. Notify Safety immediately for non-permitted activity and permit/SWI events as described by source; R12 requires immediate phone notification for hazards and same-shift logging. Preserve actual inspection time separately from later PWA entry time.

**Acceptance criteria**

- **AC-A2-001a:** A linked work-zone incident cannot be submitted as permitted without an active permit reference.
- **AC-A2-001b:** A non-permitted incident requires a reason and produces a Safety work item.
- **AC-A2-001c:** Linked incident suspends the permit, raises SWI, preserves failed checks, and prevents permit close-out while the incident remains open.
- **AC-A2-001d:** Toggle-off substitutes Corridor Manager only for CRO completion; downstream Corridor, Maintenance, Safety, PM, and HO stages remain in their source order.
- **AC-A2-001e:** A2 PM approval permission is not inferred from permit authorization; only the separate permit workflow gives PM work authorization.

### FS-PER-001 — Create, endorse, verify, authorize, execute, and close a permit [SRC-DRAFT] [USER] [OPEN]

**Actors / preconditions:** Site Engineer/Supervisor initiates; Corridor endorses; Maintenance Manager physically verifies; Safety Officer verifies/SIMOPS; PM authorizes work; initiator and Safety Officer close out; configured HO list is final under the later workflow decision. The permit must be created before work starts.

**Main flow and state sequence (conceptual):**

`DRAFT -> CORRIDOR_ENDORSED -> MAINTENANCE_VERIFIED -> SAFETY_VERIFIED -> PM_AUTHORIZED -> ACTIVE -> CLOSE_OUT_PENDING -> PENDING_HO -> CLOSED`

1. Initiator records work, method, location, hazards, planned validity, and applicable checklist.
2. Corridor endorses plan and work-zone extent.
3. Maintenance Manager visits the site, performs physical checks, and enters results in PWA.
4. Safety Officer verifies safety arrangements, SOP compliance, and SIMOPS. Inadequate controls trigger SWI; work cannot be authorized while stop-work controls remain open.
5. PM authorizes work to proceed. This is a pre-work operational authorization, not the HO final decision.
6. During execution, weather/method changes require change control; extension is required if validity is exceeded. Exact transition/authority are [OPEN].
7. Initiator surrenders the permit; Safety Officer confirms clear area/head count and close-out.
8. The user-directed HO final gate applies after the existing permit stages. The precise target insertion relative to close-out and PM's pre-work authorization must be approved; do not remove or replace PM authorization.

**Required data/validation:** Work/activity, method, location/extent, hazards, planned validity, permit/checklist identity and version, checklist answers, corrective actions, actual inspection timestamps, entry timestamps, authorizer, authorization conditions, change/extension evidence, surrender and close-out checks. The source names live-road 32-item checks and EHS/FRM/019 work-at-height controls; checklists must be mapped from source forms before implementation.

**Alternate/error paths:** Failed safety-critical check leads to SWI and blocks active work; a linked incident suspends permit and blocks close-out; missing verification/evidence, expired validity, or unresolved SWI cannot silently transition to authorized/closed.

**Acceptance criteria**

- **AC-PER-001a:** No work-active state is reachable before Corridor endorsement, Maintenance verification, Safety verification, and PM authorization.
- **AC-PER-001b:** Maintenance and Safety records retain actual site inspection time and later entry time as separate values.
- **AC-PER-001c:** An inadequate safety control prevents PM authorization until the SWI/corrective path is resolved.
- **AC-PER-001d:** A linked open incident blocks permit close-out; a closed incident alone does not clear an SWI without the required safety disposition.
- **AC-PER-001e:** HO final approval does not substitute for PM's pre-work authorization; exact final-gate placement is an explicit design decision before implementation.

## 6. Encroachment

### FS-ENC-001 — Temporary encroachment [SRC-DRAFT] [USER] [OPEN]

**Actors / preconditions:** RPO identifies and records a temporary encroachment; RPV/field team removes it with or without JCB; Corridor records outcome as needed; existing module chain and configured HO final decision apply, subject to the source's unresolved treatment of the temporary subflow.

**Main flow:** Record date/time/shift, chainage, side, nature/type, temporary/permanent classification, action, responsible team/RPO, and before/after photos. Temporary items are generally removed on the spot within the same shift. No statutory notice cycle applies. Do not invent a separate CRO stage for the temporary flow unless confirmed; if a CRO stage is configured, the CRO toggle changes only its actor.

**Conceptual states:** `DETECTED -> RECORDED -> REMOVED -> PENDING_HO -> CLOSED`; these names are proposed, and the source does not define B1 state codes or the exact point of HO routing.

**Alternate/error paths:** If not removed in the same shift, retain open and visible for follow-up; owner/escalation rules need confirmation. Missing location or evidence follows source validation; exact required photo cardinality is not defined for this module.

**Authorization / timers / notifications / evidence:** RPO creates within assigned scope; the RPV/field team records removal; Corridor records supporting evidence; PM/HO stages follow the approved module chain. PIU/PD/SP are not users. Same-shift removal is typical, not a configured SLA. Queue/notification recipient for delayed removal is [OPEN]. Preserve before/after photos, actor/team, location, shift, event time, and server receipt time.

**Acceptance criteria**

- **AC-ENC-001a:** A temporary record captures required location, shift, type, action, responsible actor/team, and before/after evidence.
- **AC-ENC-001b:** The case is not represented as a statutory notice case unless classified Permanent.
- **AC-ENC-001c:** Same-shift removal is measurable from event timestamps; an unresolved item remains visible rather than being auto-closed.
- **AC-ENC-001d:** The module-level source chain includes a CRO stage; B1-specific application of that stage needs mapping. If it applies, the toggle changes only the completion actor and does not change or bypass any downstream review/approval stage.

### FS-ENC-002 — Permanent encroachment and notice milestones [SRC-DRAFT] [USER] [OPEN]

**Actors / preconditions:** RPO registers the case; PIU issues Notices 1-3 offline; PD issues PD notice and SP assistance letter offline; Corridor records documents and evidence; field team/police assist removal; Finance/PD handle cost recovery. These external issuers are not IMS users.

**Main flow:** Register chainage, side, district/tehsil/village, category, establishment date, distance from road edge, encroacher, and photograph. Append milestones in source order: Notice 1, Notice 2, Notice 3, PD notice, SP assistance letter, removal, cost recovery, and closure outcome (removed/regularized/under appeal). Corridor records each external document reference, printed date, allowed period, scanned copy, and service proof. The master approval order remains CRO stage (or toggle-off Corridor Manager substitution), Corridor, PM, HO. The exact point where this approval chain joins the notice lifecycle is not mapped in the source and remains [OPEN]; neither chain may be reordered to conceal the gap.

**Conceptual states:** `REGISTERED -> NOTICE_1_RECORDED -> NOTICE_2_RECORDED -> NOTICE_3_RECORDED -> PD_NOTICE_RECORDED -> SP_ASSISTANCE_RECORDED -> REMOVAL_RECORDED -> COST_RECOVERY_RECORDED -> CLOSED/REGULARIZED/UNDER_APPEAL`. State names are proposals; each notice/letter is an append-only milestone.

**Authorization / notifications / evidence:** RPO creates the case; Corridor logs offline issuer documents; field team records removal; Finance/PD provide recovery outcome; PM and configured HO handle the approval chain. Ageing/escalation appears on Corridor/PD views. Notify/alert at overdue stages according to configured periods; exact intervals are Q4/open. Require scanned copy/service proof where provided.

**Validation/timers:** Age each notice stage from the printed issue date, never IMS entry date. Store `printedIssueDate`, `recordedAt`, `allowedPeriod`, and `documentReference` separately. Notice intervals are Q4/open; do not invent statutory durations. Late entry is flagged and must not reset ageing. Section 24 appeal pause/handling is Q5/open.

**Alternate/error paths:** Missing scanned evidence/reference is visible as incomplete; overdue notice stage is escalated according to approved period; appeal status and pause behavior stay open. The HO final gate is still required under [USER]; source end-state ownership by Corridor/PD needs reconciliation with that gate.

**Acceptance criteria**

- **AC-ENC-002a:** The event history contains every notice/removal/cost-recovery milestone in source order and records actor, issuer, printed date, entry date, allowed period, reference, evidence, and outcome.
- **AC-ENC-002b:** Editing `recordedAt` cannot change the ageing calculation based on `printedIssueDate`.
- **AC-ENC-002c:** An overdue milestone becomes visible/escalated; no default notice interval is substituted for missing Q4 configuration.
- **AC-ENC-002d:** The source approval chain retains Corridor then PM then HO; disabling CRO only changes the completion-stage actor.
- **AC-ENC-002e:** The user-directed HO final decision is recorded, but implementation is blocked until the relationship between approval, statutory milestones, and terminal outcome is agreed.

## 7. Violence and Public Unrest

### FS-VIO-001 — Violence case [SRC-DRAFT] [USER]

**Actors / preconditions:** RPO records event; CRO or toggle-off Corridor Manager completes the CRO stage; Corridor reviews; PM approves; Security co-approves; configured HO list gives final decision. Exact Security co-approval position in the chain is [OPEN].
**Trigger:** RPO submits a Violence incident within assigned project scope.

**Main flow:** Capture subtype (assault on staff, mob attack, vandalism, robbery/dacoity, vehicle damage, threat, gherao, or strike violence); people involved, weapon, injuries, property/value, CCTV reference, police station/FIR, security deployment, tolling impact and duration. Continue through existing Corridor -> PM -> HO order, retaining Security co-approval.

**Validation / exceptions:** Require source-required module fields before submission to next stage; exact field-level mandatory rules and stage timer values are not specified and must be approved. Returns/cancellation paths are not fully specified.

**Timers / notification / evidence:** No Violence-specific SLA is stated; do not assign one by analogy. Record CCTV reference, FIR/police data, security deployment, injuries and property evidence. Notify the next responsible assigned actor using the approved notification channels; exact co-approval recipients and sequencing remain open.
**Conceptual transitions:** `SUBMITTED -> CRO_STAGE (CRO or toggle-off Corridor Manager) -> CORRIDOR_REVIEW -> PM_APPROVAL -> SECURITY_COAPPROVAL (position open) -> PENDING_HO -> FINAL_HO_DECISION`. State labels are proposals; preserve source stage order and record the actual sequence.
**Authorization:** Each actor acts only on assigned, scoped tasks; Security receives only its co-approval capability. HO decision requires active roster eligibility.

**Acceptance criteria**

- **AC-VIO-001a:** The stored subtype and all captured people/harm/property/police/security/toll fields are associated with one incident and its audit history.
- **AC-VIO-001b:** CRO-off assigns only CRO-stage completion to Corridor Manager; Corridor, PM, Security, and HO stages remain.
- **AC-VIO-001c:** PM's approval occurs before HO's final decision; Security co-approval cannot be omitted, though its exact placement remains open.
- **AC-VIO-001d:** No module-specific SLA is enforced until approved values and calendar/working-day semantics are configured.

### FS-UNR-001 — Public Unrest case [SRC-DRAFT] [USER]

**Actors / preconditions:** RPO, CRO or toggle-off Corridor Manager, Corridor, PM, Management co-approver, configured HO list. Exact Management co-approval position is [OPEN].
**Trigger:** RPO submits a Public Unrest incident within assigned project scope.

**Main flow:** Capture subtype (protest/dharna, blockade, strike/bandh, demonstration, riot, farmers/political agitation, or labour unrest); crowd size; blocked road/directions/duration; demands/cause; organizer; police coordination; alternate route; toll operations impact. Continue through existing Corridor -> PM -> HO order, retaining Management co-approval.

**Validation / exceptions:** Exact required/optional rules for each module field and return/cancellation paths are not specified; require an approved field matrix before implementation.

**Timers / notification / evidence:** No Public Unrest-specific SLA is stated; do not assign one by analogy. Preserve road-blockage duration, police coordination, alternate-route and toll-operation details as evidence. Notify the next responsible assigned actor using the approved notification channels; exact co-approval recipients and sequencing remain open.
**Conceptual transitions:** `SUBMITTED -> CRO_STAGE (CRO or toggle-off Corridor Manager) -> CORRIDOR_REVIEW -> PM_APPROVAL -> MANAGEMENT_COAPPROVAL (position open) -> PENDING_HO -> FINAL_HO_DECISION`. State labels are proposals; preserve source stage order and record the actual sequence.
**Authorization:** Each actor acts only on assigned, scoped tasks; Management receives only its co-approval capability. HO decision requires active roster eligibility.

**Acceptance criteria**

- **AC-UNR-001a:** The listed unrest details are captured and remain linked to the workflow record.
- **AC-UNR-001b:** CRO-off changes only the completion actor; Corridor, PM, Management, and HO stages remain in the configured source order.
- **AC-UNR-001c:** PM approval precedes the final HO decision; Management co-approval cannot be omitted, while its precise sequencing remains open.
- **AC-UNR-001d:** The case cannot enter completed state until configured HO final approval is recorded.

## 8. Reports and linked registers

### FS-RPT-001 — Generate operational reports [SRC-DRAFT] [OPEN]

The system MUST generate the 17 report formats named in source Section 11.1 and support the ten linked registers in Section 11.4 from authorized incident/vehicle data. This does not imply immediate retirement of every paper register.

**Report catalogue:** Highway Incident Report (HIR); Accident Report (RPO/RPV); Incident Log (RPO); Daily accident and incident report; Daily unusual occurrence accident report; Monthly Break Down Report; Monthly Dead Animal Removal Report; Monthly Encroachment Removal data; Monthly unusual occurrence accident data; Monthly utilisation of ambulance; Monthly vehicle responsible accident data; Monthly Vehicle Running Status; Monthly Assets Damage Report; MPR (HQ Format); OMMPR Encroachment List; Year Accident Details; Consolidate Report.

**Linked registers:** Daily Vehicle Inspection Report; RP Vehicle Equipment Checklist; Crane Vehicle Equipment Checklist; Log Book of Vehicles; First Aid Register; Ambulance Medicine Checklist/Consumption Register; Ambulance Patient Care Form (PCR serial only); Bio-Medical Waste Register; Incident Log (RPO); Accident Report (RPV).

**Actors / preconditions:** Authorized user requests the report for a permitted scope and period; required report data and an approved blank template/version are available. Schedules include per-incident, per-shift, daily, monthly, and monthly-cumulative as listed in source Section 11.1.

**Trigger/flow:** User or approved schedule selects report, period, and allowed organizational scope; service validates access and report/template availability; output includes native Excel and PDF generated from the same populated workbook; export is traceable to request, source-data cutoff, and template version. Missing template/data or invalid scope fails explicitly; no success-shaped empty report is emitted.
**Authorization / timers / evidence:** Report scope is enforced before querying source data. Source frequencies define schedules; schedule time zone, correction/reissue policy, and export retention are [OPEN]. Store output references and template/data provenance in audit.

**Template rules:** Populate supplied workbook cell-by-cell without restyling; preserve fonts, colors, borders, row heights, spacing, widths, merged cells, number/date formats, headers/footers, freeze panes, print area, and page setup. Variable rows preserve template styling. Historical regeneration uses the template version effective at the time. Exact field mappings wait on the blank NHAI/NHIT templates.

**Known blocker:** The source names 17 reports but the supplied report matrix/archive is reported as containing 16. Blank NHAI/NHIT workbooks are a P0 dependency. Do not claim production-fidelity exports or invent the missing/extra report.

**Acceptance criteria**

- **AC-RPT-001a:** An export request outside the caller's scope is denied before data is returned.
- **AC-RPT-001b:** Excel and PDF for one request derive from the same populated workbook/template version.
- **AC-RPT-001c:** Export metadata records report code, period/filter, scope, requester, data cutoff, template version, status, and output references.
- **AC-RPT-001d:** Visual/structural fidelity is tested against each supplied blank workbook before a template is accepted for production.
- **AC-RPT-001e:** Report baseline cannot be signed off until the 17-vs-16 discrepancy is reconciled and every P0 blank workbook is supplied.

## 9. Dynamic users, roles, and workflow administration

### FS-IAM-001 — Dynamic users, role templates, and scoped assignments [USER] [SRC-DRAFT] [PROPOSAL]

Users, role templates, and user-to-role assignments are separate data concepts. A small predefined seed set may reflect source roles (RPO, CRO, Corridor, PM, Maintenance Manager, Safety Officer, HO, Admin, HSMU, Security co-approver, Management co-approver); this seed set is a proposal, not a closed list. PIU/PD/SP are not IMS accounts.

**Administration flow (conceptual):** authorized Admin provisions/activates a user identity; selects an enabled role template; assigns user ID, role-template ID, corridor/project/stretch scope, effective-from/until dates, active status, and reason; optionally reviews privileged changes if governance approves; system audits the change and recalculates new requests against active assignments. Reject nonexistent/out-of-parent scope or invalid effective periods. User provisioning and identity source are unresolved.

**Errors / notices:** Invalid scope or expired/disabled template is rejected with a safe actionable error. Assignment changes notify affected user/owner only under an approved policy; such notification rules are not specified by source.

**Authorization:** Every read/write is scoped server-side. A role grant does not widen scope or override state-based permissions. Role-template capability editing, multi-role collision rules, approval/delegation, and in-flight task behavior are [OPEN].

**Acceptance criteria**

- **AC-IAM-001a:** Admin can create/configure users and role assignments without changing application code, subject to an approved Admin capability.
- **AC-IAM-001b:** Assignment is rejected if its organizational scope is invalid or does not exist.
- **AC-IAM-001c:** Deactivated/expired assignments cannot authorize new actions; handling of historical reads and in-flight tasks follows policy not yet approved.
- **AC-IAM-001d:** A user cannot access data outside assigned scope even if a role template includes the relevant capability.
- **AC-IAM-001e:** Every user, role-template, and assignment change records actor, time, reason, and before/after values.

### FS-IAM-002 — Configure workflow policy [USER] [OPEN]

The workflow policy must expose the CRO availability toggle and configured HO final-approver list by an approved scope. It may not allow the toggle to change downstream stage order or the final HO requirement. Scope (global/corridor/project/module), editor/reviewer, effective time, and treatment of in-flight records require approval.

**Acceptance criteria**

- **AC-IAM-002a:** A saved toggle change affects only newly eligible CRO-stage task ownership according to its approved effective-time rule.
- **AC-IAM-002b:** A saved HO roster change is audited and cannot remove the mandatory final HO gate.
- **AC-IAM-002c:** A workflow with no resolvable HO final approver remains pending and reports a configuration error; it is never auto-approved or routed around HO.
- **AC-IAM-002d:** Admin is not automatically included in the HO list and does not gain final-approval rights merely from being Admin.

**Conceptual configuration states:** `DRAFT -> REVIEWED (only if an approved two-person policy exists) -> ACTIVE -> SUPERSEDED`. These names and second-person review are proposals; every effective configuration revision must be auditable.

## 10. Offline synchronization and evidence

### FS-OFF-001 — Offline field capture and synchronization [SRC-DRAFT] [PROPOSAL]

All field forms MUST be usable without network. Device data is held in an encrypted local store, survives app termination/restart, and is synchronized in the background with retry. Source conflict policy is server-wins for master data and client-wins for drafts. The source requires no data loss on app kill. Retry-key/idempotency and concurrent-device policy are technical proposals pending approval.

**Main flow:** Create local draft and evidence metadata; enqueue ordered changes; display pending/syncing/succeeded/failed state; retry safely after connectivity; server validates current authentication, role assignment, scope, and state on receipt; accept each eligible change or return explicit conflict/validation rejection.

**Authorization / timestamps:** The user must be authorized when the server applies the operation, not merely when it was queued. Keep occurred-at, device-captured-at, and server-received-at separate. Exact batch atomicity and submitted-record conflict behavior are [OPEN].

**Acceptance criteria**

- **AC-OFF-001a:** With network unavailable, a user can create/edit a draft and attach permitted evidence; terminating/restarting the app does not lose queued data.
- **AC-OFF-001b:** Reconnection resumes queued synchronization and reports per-item result; failure is visible and retryable.
- **AC-OFF-001c:** Replaying the same accepted operation does not intentionally create duplicate incidents or evidence; exact idempotency key semantics are [PROPOSAL] until approved.
- **AC-OFF-001d:** Master-data conflict resolves server-wins; draft conflict resolves client-wins, as specified by source. Submitted/approved-record conflict behavior remains [OPEN] and must not silently overwrite.
- **AC-OFF-001e:** A change queued while authorized but synchronized after assignment revocation is re-authorized at sync time and rejected if no longer permitted.

### FS-EVD-001 — Capture and retain evidence [SRC-DRAFT]

Evidence includes media metadata, source event/capture time, device/GPS provenance, upload state, and server receipt time. Accident evidence uses live camera only and source watermarks; do not treat client timestamps alone as trusted server time. Accident/encroachment records are retained for 8 years or more as required by source.

**Actors / preconditions / flow:** RPO captures accident images on device; authorized field/office users attach permitted supporting evidence. Evidence is associated with an in-scope incident, queued offline, uploaded, validated, and retained under the approved retention policy. Failed upload remains pending/failed, not accepted evidence.

**Acceptance criteria**

- **AC-EVD-001a:** Accident photo count, source, watermark, and per-file size constraints are enforced at submission.
- **AC-EVD-001b:** Event time, device capture time, server receipt time, and printed notice issue date are represented separately.
- **AC-EVD-001c:** Failed evidence upload is visible and retryable; an incident cannot satisfy required evidence with a missing/unavailable upload.
- **AC-EVD-001d:** A user cannot alter or delete an accepted evidence/audit reference except through an explicit, audited correction process.

## 11. Audit, notifications, and operational visibility

### FS-AUD-001 — Append-only audit history [SRC-DRAFT]

Record field-level old/new values, actor, event time, and reason for changes. Preserve audit history on state transition, submission, review, approval/return, reopen, cancellation, evidence upload, workflow-setting change, report export, permit check/SWI, and notice milestone. Audit history is immutable to all roles, including Admin.

**Actor/trigger/authorization:** Any authorized mutation or workflow/system event triggers audit creation. Actor identity is resolved by the service; system scheduler and external/offline issuers are represented distinctly from human IMS actors. Audit reads are scope-filtered; no role can mutate accepted events.

**Acceptance criteria**

- **AC-AUD-001a:** Each accepted mutation produces an audit event identifying actor, action, timestamp, changed values, and reason when required.
- **AC-AUD-001b:** No supported application role can update or delete an accepted audit event.
- **AC-AUD-001c:** Audit access is scope-filtered and does not expose another corridor's identifiers or values.
- **AC-AUD-001d:** Workflow configuration version used for assignment/final decision is retained with the transition evidence.

### FS-NOT-001 — Notifications and SLA visibility [SRC-DRAFT] [OPEN]

In-app and email notifications are required by source. SMS/WhatsApp notification is required for fatal/major incidents and notice-stage breaches, subject to integration availability. Queues/dashboard expose severity, age, next stage, pending approvals, SLA breach, and encroachment notice age. Exact escalation levels, recipients, and retries remain [OPEN].

**Acceptance criteria**

- **AC-NOT-001a:** Each workflow transition creates an in-app/email notification attempt for the next responsible actor/observer specified by the workflow.
- **AC-NOT-001b:** Fatal/major incident and notice-breach notification conditions are recorded; unavailable external messaging does not falsely mark delivery as complete.
- **AC-NOT-001c:** Dashboard ageing uses configured stage entry/due times and does not reset due to late data entry unless policy says so.

## 12. Cross-cutting non-functional requirements

All values below are source targets or constraints, not verified product capabilities. Test volumes, measurement conditions, and acceptance owners must be agreed before release.

| ID | Requirement | Status / trace | Acceptance criterion |
|---|---|---|---|
| **NFR-SEC-001** | Enforce corridor/project/stretch data isolation server-side on every query and mutation. | [SRC-DRAFT], Section 12 | **AC-SEC-001:** Negative tests for cross-scope list, lookup, update, and export return no protected data. |
| **NFR-SEC-002** | Encrypt offline local store; preserve server-side authorization on sync. | [SRC-DRAFT], Sections 10, 12 | **AC-SEC-002:** Queued data survives restart; unauthorized sync is rejected and audited without applying the mutation. |
| **NFR-REL-001** | Offline field forms and retry/resume must not lose data on app kill; all field forms work without network. | [SRC-DRAFT], Sections 1 and 12 | **AC-REL-001:** Interrupt network and terminate app at each queued-operation stage; after restart, every accepted local operation is either synchronized or remains visibly retryable, with no silent loss. |
| **NFR-AUD-001** | Audit is append-only and captures old/new value, actor, time, and reason. | [SRC-DRAFT], Section 12 | **AC-AUD-001a-d** above. |
| **NFR-DATA-001** | Accident and encroachment records are retained for at least 8 years. | [SRC-DRAFT], Section 12 | **AC-DATA-001:** Retention policy test proves no record/evidence is purged before the approved minimum; legal-hold/deletion policy remains open. |
| **NFR-LOC-001** | Service is hosted in India. | [SRC-DRAFT], Sections 2 and 12 | **AC-LOC-001:** Deployment evidence confirms all in-scope hosting/data-residency controls meet India-hosting requirement; vendor/region choice is undecided. |
| **NFR-PERF-001** | Source targets: cold start <=3 s; lists <=2 s; API p95 <=500 ms; monthly report pack <=30 s. | [SRC-DRAFT], Section 12 | **AC-PERF-001:** Measure each target under an approved workload; test volumes and timing boundaries are open (Q8). |
| **NFR-OPS-001** | SLA age, overdue state, queue assignment, sync status, and report-template status must be observable to authorized operators. | [SRC-DRAFT], Sections 9, 11-12 | **AC-OPS-001:** Simulated overdue stage, failed sync, unresolved notice, and unavailable template are visible with a responsible next action. |
| **NFR-INT-001** | Integrations are prioritized, not guaranteed: high GIS/SMS-or-WhatsApp/SMTP/SSO; medium FIR reference/VMS/ATMS; low CCTV/weather/ERP. | [SRC-DRAFT], Section 12; Q9 open | **AC-INT-001:** Each integration has an availability/owner decision before it becomes a release dependency; core workflow does not pretend an unavailable integration succeeded. |

### 12.1 Non-functional items not specified

The source does not establish an uptime/SLO, RPO/RTO, backup frequency, key custody/rotation, detailed threat model, accessibility standard, supported language set (language support is a detailed-design question), browser/device matrix, or a full data-deletion/legal-hold policy. These are not requirements in this document and need separate decisions.

## 13. Release blockers and unresolved decisions

Do not treat this specification as implementation-ready until the following are resolved:

1. Source scope approval and per-module acceptance of the later user-directed HO final gate.
2. HO roster selection semantics, scope, editor/reviewer, effective time, and in-flight behavior.
3. A1 auto-close trigger/timer relative to the new HO final decision.
4. A2 Project Manager permission conflict (Section 4.2 vs Q2), SPV/Vendor field 22 (Q3), and A2-specific SLA values.
5. Whether the corridor reviewer may also have completed the CRO stage (segregation of duties).
6. Permit final HO gate placement relative to PM pre-work authorization and permit close-out.
7. Temporary encroachment's relationship to the master CRO/Corridor/PM/HO chain.
8. Permanent encroachment approval-chain join point relative to notice/removal/cost-recovery milestones; Q4 notice intervals and Q5 appeal handling.
9. Violence/Public Unrest co-approval placement and return/cancel policy.
10. Calendar versus working-day semantics (Q6), workload/sizing (Q8), integration availability (Q9), and blank templates/report archive reconciliation (Q7).
11. Blank NHAI/NHIT templates for every report are a P0 dependency; scope lists 17 reports while supplied matrix/archive is reported to contain 16.
12. Identity/SSO/provisioning, multi-role rules, role administration governance, accessibility/language, backup/recovery, and operational service targets.

## 14. Source traceability index

| Spec area | Primary source |
|---|---|
| Shared roles, scope, approvals | [IMS Scope Document](../IMS-Scope-Document.md), Sections 2-4, 9, 12, 14-16 |
| A1/A2 fields and flows | Source Sections 4-5; A1 process-flow artifact referenced in Section 14 |
| Permits / work-zone | Source Section 6, including live-road and EHS/FRM/019 checklists |
| Encroachment | Source Section 7; Section 15 Q4-Q5 |
| Violence / Public Unrest | Source Section 8 |
| Automation and reports | Source Sections 9 and 11 |
| Offline, security, audit, notifications, performance | Source Section 12 |
| Delivery/P0 dependency | Source Sections 13, 14, 15 Q6-Q9, R6/R9/R11 |
| Confirmed CRO/HO decisions | This document §1.2; also [Scope and traceability](scope-and-traceability.md) and [Workflows](workflows.md) |

For the actor-by-actor journey and detailed conceptual transition tables, see [End-to-end workflows](workflows.md). For permission-model proposals, see [Users, roles, and access](roles-and-access.md); for non-binding API examples, see [Conceptual API contract](api-contract.md).
