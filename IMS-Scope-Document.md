# Incident Management System (IMS)
## Scope Document

| | |
|---|---|
| **Version** | v1.5 (supersedes v1.4) |
| **Date** | 17 November 2026 |
| **Organisation** | NHIT / NHIPPL |
| **Status** | Draft for approval - 20 decisions locked, open items in Section 15 |
| **Basis** | Road User Accident Module Process Flow - Accident Module Mobile Interface.xlsx - Create Accident mock-up - SOP for Permit to Work (01.01.08/EHS P-08/R1) - Work Permit Live Road Activities - WAH Permit EHS/FRM/019 - NHIT_Incident_Management_Reports.xlsx and the 17 live report formats in Reports/ - the 10 register formats in Formats/ - the NHAI / NHIT Excel report templates - Control of National Highways (Land and Traffic) Act 2002 - MoRTH encroachment SOP (Aug 2025) |
| **Owner** | Operations / EHS |

### At a glance

| | |
|---|---|
| **What** | One platform for five incident families: Accidents (road user and work zone), Encroachment, Violence, Natural Disaster, Public Unrest |
| **Chain** | RPO -> CRO -> Corridor -> Project Manager -> HO. On **accidents** the Project Manager is **view-only** and the record auto-closes 3 days after Corridor approval; on **all other modules** the Project Manager and HO approve. Natural Disaster follows its own SOP and is out of this application |
| **Field** | Android native app, offline-first, camera-only evidence, GPS and timestamp stamped at capture; RPO loses view access after submit |
| **Office** | PWA console - review queues, the descriptive accident report, encroachment notice log, master data, dashboards, template-driven report export |
| **Reports** | 17 live NHIT formats, each exported into its own NHAI / NHIT Excel template - same colours, line spacing, column widths and print layout - with the PDF produced from that same populated workbook |
| **Delivery** | 5 phases, approximately 29-36 weeks; usable MVP in Phase 1 |

---

## 1. Purpose and objectives

Road operations on NHIT concessioned stretches generate a continuous stream of incidents: road accidents involving road users, injuries to work-zone personnel, illegal encroachment on the right of way, violence against toll and patrol staff, natural disasters affecting carriageway availability, and public unrest that blocks traffic.

These are captured today on paper formats, messaging groups and a set of 17 monthly and daily Excel reports that are assembled by hand. The result is inconsistent data, no chain of custody, no audit trail, unreliable response-time measurement, late statutory returns, and lessons that never reach the people who need them.

The requirement is a single application that captures incidents at source on a mobile device, routes each through a defined multi-level approval chain with hard time limits, tracks the statutory notice cycle for permanent encroachment, and generates every existing report from the same data.

| # | Objective | Measure of success |
|---|---|---|
| O1 | Capture every incident at the point of occurrence, by the RPO who is present | 95% or more created in-app; RPO entry within 1 hour |
| O2 | Guarantee a complete, tamper-evident record | 100% carry GPS, timestamp and photo evidence; every post-submission edit audited |
| O3 | Enforce the approval chain and its time limits | 100% traverse every level; SLA breaches visible and escalated |
| O4 | Enable offline field capture | 100% of field forms usable with no network; zero data loss on sync |
| O5 | Replace manual report assembly | All 17 NHIT formats produced from system data, each in its prescribed NHAI / NHIT Excel template |
| O6 | Track the permanent-encroachment notice cycle | Notice 1-3, PD notice, SP letter and removal dated and aged per case |
| O7 | Measure and improve response performance | Call response, arrival and clearance times computed for every incident |

## 2. Scope

### 2.1 In scope

1. Five incident families on one platform: **A** Accidents (road user and work zone), **B** Encroachment (temporary and permanent), **C** Violence, **D** Natural Disaster, **E** Public Unrest.
2. **Android native** field application for RPOs, offline-first. It is the only role with a handset.
3. **PWA** console for CRO, Corridor, Project Manager, Maintenance Manager, Safety Officer, HO and Admin.
4. Accident capture to a **descriptive accident report** - the RPO's 21 fields expanded by the CRO into a full narrative, vehicle, casualty and response record. No statutory reporting form.
5. **The Road User Accident Module flow** exactly as specified - time limits, auto-close, restricted reopen, loss of RPO view access after submit, and **Project Manager as a view-only observer**.
6. **Work-zone accidents linked to a Work Permit**, with the live-road and work-at-height checklists.
7. **Permanent encroachment notice lifecycle** - three notices issued through the PIU, one PD notice, a letter from the SP, then removal and cost recovery. All of these are issued offline; the system holds the dated evidence log.
8. **Report generation** for all 17 live NHIT formats, exported into the prescribed Excel template with the original formatting preserved, plus a matching PDF.
9. **Supporting registers** linked to incidents - vehicle inspection, equipment checklists, log book, first aid, ambulance PCR reference, bio-medical waste.
10. **Master data, audit trail, notifications, evidence and document management.**

### 2.2 Out of scope

| Excluded | Where it belongs |
|---|---|
| **Natural Disaster module** - has its own SOPs | Separate process; to be specified later (see Section 4.4) |
| **MoRTH Road Accident Recording Form (Sections A-E) and the 18 MoRTH aggregate tables** | Deferred. The accident report is the internal descriptive record only. Can be added later as an extra section without redesign |
| **System access for PIU, PD and SP** | Their notices and letters are issued offline. The system holds only the dated evidence log, recorded internally |
| Police FIR registration, prosecution, court follow-up | Police domain - IMS records the FIR reference and status |
| Clinical content of the ambulance Patient Care Form | Clinical record held by the ambulance operator - IMS stores only the PCR serial number |
| Insurance claim settlement and vendor recovery accounting | Finance - IMS supplies the incident dossier |
| Toll transaction and revenue systems | Separate system - toll-plaza reference data only |
| Design, drawing and BOQ of permanent civil rectification | Maintenance works module |
| Vehicle telematics and CCTV analytics | Integration only, if available |
| iOS application | Android first |
| Payroll, procurement, asset depreciation | ERP |

### 2.3 Module map

```
INCIDENT CORE  (shared by all modules)
  identity | location | evidence | lifecycle | workflow engine | audit | notification | reports
      |
      +-- A   Accidents                   Project Manager: VIEW ONLY
      |       +-- A1  Road user             RPO > CRO > Corridor > (PM view) > auto-close > HO reopen
      |       +-- A2  Work zone             + Maintenance Manager + Safety Officer > (PM view) > auto-close
      |
      +-- B   Encroachment                Project Manager: APPROVE
      |       +-- B1  Temporary             removed on the spot, same shift
      |       +-- B2  Permanent             statutory notice lifecycle (Section 7)
      |
      +-- C   Violence                    Project Manager: APPROVE
      +-- D   Natural Disaster            OUT OF SCOPE - own SOP
      +-- E   Public Unrest               Project Manager: APPROVE
```

## 3. Organisation and roles

| Role | Channel | Powers |
|---|---|---|
| **RPO** - Route Patrolling Officer | Android only | Creates the incident with basic information. Own project only. **Cannot view the record after submitting.** Entry within 1 hour of receiving information |
| **CRO** - Control Room Officer | PWA | Collects all required information from the relevant departments and completes the **descriptive accident report** - vehicles, injured persons, fatalities, ambulance and crane response, damage, cause analysis. Then submits to the Corridor. Within 5 days of creation |
| **Corridor Incharge / Operations Manager Corridor** | PWA | Reviews the incident; may add comments, change any information, and approve. Within 48 hours of CRO submission. Also **records the encroachment notice stages** issued offline by the PIU, PD and SP |
| **Project Manager** | PWA | **View-only on every accident** - notified on submission, can see all accident records in the project, but cannot approve, edit or return. **Approves** all other modules: encroachment, violence and public unrest. Authorises work permits |
| **HO / Admin** | PWA | Final approval in the master chain. **The only role that can reopen a closed incident** |
| **Maintenance Manager** | **PWA only** | Permit physical verification and work-zone fact verification, entered on the web console after the site visit. No mobile app |
| **Safety Officer / EHS** | **PWA only** | Safety verification, SIMOPS clearance, stop-work authority and work-zone co-approval, entered on the web console. No mobile app |
| **HSMU** - Highway Safety Management Unit | PWA | Investigation team for the Highway Incident Report: cause analysis, summary, appendices |
| **Admin** | PWA | Masters, workflow templates, report templates, SLAs, user management |

> **External parties - no system access.** The **PIU** (Project Implementation Unit), the **PD** (Project Director) and the **SP** (Superintendent of Police) do not log in to the system and have no manual work in it. They issue notices and letters offline in the normal way. The Corridor records the notice number, issue date, period allowed and the scanned proof, so the statutory trail and its ageing clocks are still complete inside IMS.
>
> **Visibility rule.** Access is scoped corridor -> project -> stretch and enforced server-side on every query. The RPO's loss of view access after submit is a security requirement, not a UI preference.

## 4. Approval chains and time limits

### 4.1 Road User Accident Module - the reference flow

This is the flow specified in the Road User Accident Module Process Flow and it governs module A1.

| # | Stage | Role | Action | Time limit |
|---|---|---|---|---|
| 1 | Create | RPO | Creates the accident with basic information. Mobile only, own project only. Loses view access on submit | **Within 1 hour** of receiving accident information |
| 2 | Complete | CRO | Collects all required information from the relevant departments and completes the descriptive accident report, then submits to the Corridor for review | **Within 5 days** of accident creation |
| 3 | Review | Corridor Incharge | Reviews, comments, changes any information, approves. **This is the accountable approval step for accidents** | **Within 48 hours** of CRO submission |
| 4 | Observe | Project Manager | **View only.** Notified on submission, can read the record and its evidence, cannot approve, edit or return | No gate |
| 5 | Close | System | **Auto-closes** the accident after approval, with no manual action | **3 days after approval** |
| 6 | Reopen | HO / Admin | **Only role** that can reopen a closed accident. The record then shows under the Reopened stage | As required |

```
STAGE 1  RPO creates (1 hour)                     ->  record locked, RPO view revoked
STAGE 2  CRO completes (5 days)                   ->  descriptive report, department inputs
STAGE 3  Corridor reviews (48 hours)              ->  comments, edits any field, approves
STAGE 4  Project Manager observes                 ->  view only, notified, no approval right
STAGE 5  System auto-closes (3 days after approval)
STAGE 6  HO / Admin reopens                       ->  status REOPENED, flow restarts at STAGE 2
```

**Reopen cycle.** HO or Admin reopens a closed accident. It appears under Reopened. CRO and Corridor Incharge make the changes, and the full flow repeats from the CRO step - including the 5-day and 48-hour limits, and a fresh 3-day auto-close on re-approval.



> **Project Manager access on accidents.** The Project Manager sits in the accident flow as an **observer**, not an approver: notified on submission, able to read every accident record in the project, but with no approve, edit or return rights. Approval authority on accidents rests with the Corridor. The Project Manager's approve rights apply to all non-accident modules (Section 4.2). HO and Admin are not a gate in the accident flow - they hold reopen authority only.

### 4.2 Master chain - all modules except Natural Disaster

```
V-A2  work-zone accident   RPO -> CRO -> Corridor -> Maintenance Manager -> Safety Officer
                              -> Project Manager (view only) -> auto-close -> HO reopen
V-B   encroachment         RPO -> CRO -> Corridor -> Project Manager -> HO   (+ PIU / PD / SP notice log, recorded by Corridor)
V-C   violence             RPO -> CRO -> Corridor -> Project Manager -> HO   (+ Security co-approver)
V-E   public unrest        RPO -> CRO -> Corridor -> Project Manager -> HO   (+ Management co-approver)
```

The Project Manager **approves** on B, C and E. On A2 the Project Manager is **view-only**, consistent with A1, even though the Maintenance Manager and Safety Officer approve the safety content (see Q2 - work-zone incidents are OHS cases and may warrant Project Manager approval).

### 4.3 Lifecycle states

```
DRAFT
  -> SUBMITTED                    (RPO, within 1 hour)
  -> PENDING_CRO                  (CRO completing, 5 days)
  -> PENDING_CORRIDOR             (Corridor reviewing, 48 hours)
  -> APPROVED                     (accidents: Project Manager notified, view only)
  -> AUTO_CLOSED                  (3 days after approval, system job)
  -> REOPENED                     (HO/Admin only; restarts at PENDING_CRO)

Non-accident modules continue past the Corridor:
  -> PENDING_PROJECT_MANAGER      (Project Manager approves)
  -> PENDING_HO                   (HO approves)
  -> CLOSED

Additional: REWORK_REQUIRED (returned with reason) - ESCALATED (SLA breach)
            CANCELLED (duplicate or invalid) - SWI_ACTIVE (work zone only)
```

### 4.4 Natural Disaster - excluded

Natural Disaster has its own SOPs and is **not** built in this phase. The application will still allow a Natural Disaster incident to be recorded as a minimal record and cross-referenced, so that road-block and downtime reporting is not lost, but the approval chain, checklists and reports for this family are deferred until the SOP is supplied. See Q1.

## 5. Module A - Accidents

### 5.1 Road user versus work zone

| Aspect | **A1 Road user (external)** | **A2 Work zone** |
|---|---|---|
| Injured person | Road user or third party | Worker, staff or contractor |
| OHS class | NON-OHS (default) | OHS (default) |
| Flow | RPO > CRO > Corridor > auto-close; Project Manager view-only (Section 4.1) | Same, plus Maintenance Manager and Safety Officer approval before auto-close; Project Manager view-only |
| Linked record | None | Mandatory active permit |
| Downstream | Insurance or third-party claim | Statutory OHS reporting, root cause, permit suspension |

A1 covers accidents outside the toll plaza involving people who do not work there, oil spillage, animal strike and similar. A2 covers injury while working, work-zone intrusion by a vehicle, plant or equipment injury, and injury during a lane closure.

Routing is **derived** from Crash Type plus the SPV/Vendor Member Involved flag - never left to a manual toggle, which can send a worker injury down the road-user path.

### 5.2 Stage 1 - RPO mobile form (21 fields)

| # | Field | Type | Validation and logic |
|---|---|---|---|
| 1-2 | Date, Time | Picker | DD-MM-YYYY, HH:MM; not future; older than 48 h needs CRO approval |
| 3 | Chainage | Masked 000.000 km.m | Within project start and end chainage master |
| 4-5 | Direction, Road Type | Dropdown | LHS/RHS; Main Carriageway, Service Road, Slip Road or Ramp, Toll Plaza Approach |
| 6 | Live GPS | System | Lat, long, accuracy and timestamp at entry; retry if accuracy worse than 50 m; warn and ask reason if more than 1 km from chainage |
| 7 | Accident Category | Dropdown | NON-OHS or OHS; defaults NON-OHS; selects the flow |
| 8 | Crash Type | Dropdown | 10 values; drives the vehicle-count check |
| 9 | Nature of Accident | Dependent dropdown | 14 values filtered by Crash Type |
| 10 | Accident Type (severity) | Dropdown | Fatal, Grievous, Minor, Non-Injury; must agree with casualty counts; recomputed by CRO |
| 11 | Hit and Run | Dropdown | Yes, No, Not Applicable; auto-set Not Applicable for Single Vehicle |
| 12-16 | Vehicles, total persons, killed, grievous, minor | Integer | Killed + Grievous + Minor not more than Total; defaults 0 |
| 17 | Uninjured | System | Total minus the three casualty counts; read-only; never negative |
| 18 | Accident photos | Multi camera | Minimum 2, maximum 10; live camera only; watermark date, time, lat, long and chainage; 2 MB max; offline |
| 19 | Safety arrangement photos | Multi camera | Minimum 1, maximum 10 - barricading, cones, signage, diversion, warning lights |
| 20 | Description | Text 20-1000 | Not numeric or special characters only; optional voice to text |
| 21 | Save Draft / Submit | Action | Draft allows incomplete data; Submit validates, locks the record, revokes RPO view, starts the 1-hour clock |

**Crash type to nature.** Single Vehicle: Overturned, Fixed-Object, Run off-Road, Skidding. Vehicle to Vehicle: Head-on, Rear-end, Side Impact, Sideswipe. Vehicle to Parked: Rear-end, Side Impact, Sideswipe. Vehicle to Pedestrian: Pedestrian Hit. Vehicle to Work Crew: Work Crew Strike. Vehicle to Animal: Animal Strike. Vehicle to Bicycle: Head-on, Rear-end, Side Impact, Sideswipe. Multiple Vehicles: Chain-reaction, Rear-end, Side Impact, Sideswipe, Head-on. Other and Unknown: all options.

**Recommended addition.** Add SPV/Vendor Member Involved as field 22, so work-zone routing is decided at submit (Q3).

### 5.3 Stage 2 - CRO completes the descriptive report

The CRO turns the RPO's 21-field triage into the full **Accident Report** - the same incident, described properly. There is **no statutory reporting form**. The report is the internal record and the source for every downstream report, the police dossier and the insurance pack.

**Part 1 - carried from Stage 1, read-only.** Date and time, chainage, direction, road type, GPS, accident category, crash type, nature, severity, hit and run, vehicle count, persons, killed, grievous, minor, uninjured, photographs, RPO description. The CRO cannot change these; if one is wrong, the record is returned to the RPO's supervisor or corrected with an audited amendment and a reason.

**Part 2 - CRO completes.**

| Group | Fields |
|---|---|
| **Report identity** | Accident report number (system generated); date and time information received; date and time reported to the control room; corridor, project, plaza reference |
| **Police** | Police station; district; FIR number and date; investigating officer name and contact; FIR registered yes/no; if not, reason |
| **Location detail** | Exact location description and nearest landmark; nearest toll plaza and distance; lane or lanes affected; urban or rural; whether inside a work zone |
| **Vehicles** | One row per vehicle: type, registration, make and model, owner, driver name, driving licence number and class, insurance policy and validity |
| **Injured persons** | One row per person: name, age, sex, road user or worker, nature of injury, hospital, admitted or discharged, MLR number |
| **Fatalities** | One row per fatality: name, age, sex, time of death, post-mortem status, body handed over to |
| **Emergency response** | Ambulance reached site yes/no and time; hospital; PCR reference. Recovery crane reached site yes/no and time; recovery completed time |
| **Traffic impact** | Time carriageway cleared; time traffic restored; lane blockage duration; diversion imposed yes/no |
| **Property damage** | NHIPPL road furniture and private property recorded separately, itemised with quantity and estimated value; links to the Assets Damage Report |
| **Investigation** | Cause analysis - primary cause and contributory causes; eyewitnesses; weather, light and surface condition; remedial action taken |
| **Sign-off** | RPO and Corridor Incharge digital signature with timestamp |

> **Approval gate.** The CRO cannot submit to the Corridor until report identity, location detail, vehicles, injured persons and cause analysis are complete, and the vehicle and casualty counts in Part 2 reconcile with the counts declared in Part 1. Any change to the declared severity must be flagged and justified. Both values are retained; fatality counts are never silently overwritten.

### 5.4 Highway Incident Report (HIR) and HSMU

The HIR is the formal investigation document. It is completed by the **HSMU** and is the basis for the cause analysis and the appendix pack.

| HIR section | Content |
|---|---|
| 1. Date of incident | Date, time in 24-hour format, day |
| 2. Location | Brief statement meaningful to a lay reader; carriageway left or right; nearest junction and chainage; distance from nearest toll plaza |
| 3. Persons and vehicles | Number injured; vehicle type (passenger or commercial); registration, make and model |
| 4. **Nature of incident** | Dangerous Occurrence - Minor or Major Injury - Fatal - Property damage (NHIPPL) - Fire incident - **Third Party Violence** |
| 5. Classification of injury | Non-Injury or Minor - Major - Fatal |
| 6. Immediate action taken | First aid, hospitalisation, aid provided by ambulance or patrol vehicle |
| 7. HSMU in charge / investigation team | Names and designations, including EMT and RPO |
| 8. Summary of incident | Narrative based on eyewitness accounts, with photographs |
| 9. Cause of incident | Statement of causes including contributory causes, by the investigation team |
| Appendices | Sketches, drawings, plans, photographs, eyewitness statements, correspondence |

The **Nature of Incident** taxonomy in HIR section 4 is adopted as the top-level incident classification across the platform, because it is the language already used in operations.

## 6. Work-zone accidents and work permits

### 6.1 Permit lifecycle (SOP 01.01.08/EHS P-08/R1, with the Corridor step)

| Step | Actor | Action |
|---|---|---|
| 1 | Site Engineer / Supervisor | Work identified; permit initiated with full details before work starts |
| 2 | **Corridor** | Endorses the plan and work-zone extent for the corridor |
| 3 | Maintenance Manager | Physically inspects the work area against the permit checks, then records the result on the **web console** - no mobile app |
| 4 | Safety Officer | Verifies safety arrangements, SOP compliance and SIMOPS, recorded on the web console |
| 5 | Safety Officer | If controls are inadequate, a **Stop Work Instruction** applies until issues are closed and re-verified |
| 6 | Project Manager | Authorises work to proceed |
| 7 | All | Controlled execution; change control on weather or method; extension if validity is exceeded |
| 8 | Initiator + Safety Officer | Close-out - permit surrendered, area cleared, head count done, confirmed on the web console |

### 6.2 Checklists captured in the console

| Checklist set | Key items |
|---|---|
| **Live-road, general (1-7)** | Risk assessment at location; induction records; traffic devices to specification; sign visibility; unneeded signs covered; taper sight distance; supervisor present throughout |
| **Live-road, worker and vehicle protection (8-18)** | Safe access; PPE including reflective vest, helmet and safety shoes; equipment condition; fuel away from sunlight; wedges; RC and insurance; no child or over-55 workers; first aid; daily TBT; fire protection; drinking water |
| **Live-road, traffic management (19-32)** | Taper and buffer zones clear; sign lines unobstructed; misleading signs covered; carriageway clear; diversion pavement condition; 1 m paved and 1.5 m earthen shoulder; chevron lights; delineators with three-row reflector bollards; Type IV barricade boards, water barricades and NJ barriers for permanent diversion; cones at 5 m with rope for temporary diversion; reflective tape and rope lights at night; service-road diversion lighting; flagmen at both ends; MS board 500 m ahead |
| **Work at height (EHS/FRM/019)** | PPE matrix; scaffold certification and green tag; ladder; platform; lifeline; fall arrestor; barricading; illumination; pre-task briefing block; suspension block; clearance with head count |

### 6.3 Safety notes and linkage rules

- **Hard-coded safety notes:** no tractor-driven water tankers for median works; no old-generation Hydra cranes for any lifting including incident management; manpower never lifted in a makeshift man basket; no manpower in tractor trolleys, passenger vehicles at all times; permit copy available at site; permit valid for daylight operations only.
- **Linkage:** a work-zone accident must reference an active permit, otherwise it is recorded as a non-permitted activity with a mandatory reason and routed to the Safety Officer.
- **Automatic suspension:** an incident against a permit sets that permit to SUSPENDED and raises an SWI, which the Safety Officer acknowledges and closes in the console.
- **Close-out block:** permit close-out is prevented while a linked incident is open.
- **Evidence:** any "No" on a safety-critical checklist item is surfaced in the incident dossier.
- **Desk-based verification.** Maintenance Manager and Safety Officer both work from the web console only. Verification is therefore recorded after the site visit rather than at the work face, so the console must timestamp the actual inspection time separately from the entry time and flag any gap. See R12.

## 7. Module B - Encroachment

Encroachment is split into two types at capture, because they have completely different lifecycles, owners and reports.

### 7.1 Temporary encroachment

Detected and removed on the spot, usually within the same shift, by the RPV team with or without a JCB. No notice cycle. This is the bulk of the volume and appears in the monthly removal report.

| Field | Notes |
|---|---|
| Date, time, shift (A/B/C) | Shift drives the RPV log |
| Chainage, side (LHS/RHS/Median) | |
| Nature | Hoarding, banner, wallpaper, cart, iron board, temporary structure, unauthorised access, scrap shop, barbed fencing |
| Temporary or Permanent | Default Temporary |
| Action taken | Removed by RPV team / access cut with JCB / removed with police assistance |
| Action taken by and RPO name | |
| Photographs | Before and after |

### 7.2 Permanent encroachment - the statutory notice lifecycle

Permanent encroachment cannot be removed by the patrol team. It requires a documented notice trail under the **Control of National Highways (Land and Traffic) Act, 2002** before removal. The system must hold this lifecycle as a trackable case with dates, documents and ageing.

| # | Stage | Issued by (offline) | Recorded in IMS by | Evidence recorded |
|---|---|---|---|---|
| 1 | **Detected and registered** | RPO | RPO | Chainage, side, district, tehsil, village, category (private, agriculture, commercial), establishment date, distance from road edge, name of encroacher, photograph. Enters the OMMPR encroachment list |
| 2 | **Notice 1 issued** | PIU | Corridor | Notice number, date, period allowed, scanned copy and acknowledgement or service proof |
| 3 | **Notice 2 issued** | PIU | Corridor | As above, second reminder |
| 4 | **Notice 3 issued** | PIU | Corridor | As above, final notice through the PIU |
| 5 | **PD notice** | PD | Corridor | Single escalation notice from the PD, with period allowed and scanned copy |
| 6 | **SP letter** | PD to SP | Corridor | Request for police assistance to enforce removal - Section 37, Control of National Highways (Land and Traffic) Act 2002. Reference, date and scanned copy |
| 7 | **Removal** | Field team with police assistance | Corridor | Crane or JCB deployed, date, manpower, cost incurred, photographs before and after, panchnama |
| 8 | **Cost recovery** | Finance / PD | Corridor | Bill raised under Section 25; amount, recovery status |
| 9 | **Closed** | Corridor / PD | Corridor | Closure with outcome - removed, regularised, or under appeal |

**Statutory anchors.** Section 21 (no occupation without written permission), Section 23 (notice to remove, and summary removal for trivial or temporary cases with police help), Section 24 (appeal before the Collector - suspends further action), Section 25 (recovery of the cost of removal), Section 37 (Commissioner of Police or District Superintendent of Police enforces removal). MoRTH's August 2025 SOP adds monthly project-level inspection, quarterly drone and aerial survey, preliminary notices through field officers with local administration and police assistance, District Magistrate enforcement for persistent cases, and escalation to state government for unresolved cases.

> **No access for the issuing parties.** The PIU, PD and SP are not system users and have no manual work in IMS. Each notice or letter is issued and served outside the system; the Corridor then records the reference, date, period allowed and the scanned proof. The milestone is therefore complete on the day the document is logged, and the ageing clock starts from the date printed on the notice, not from the date of entry - so a late entry cannot hide a delay.
>
> **Design rule.** Every notice stage is a dated, document-bearing milestone with its own ageing clock. The system must show, per case: days since detection, which notice is current, days since last notice, and whether the statutory period has expired. A case that has issued Notice 3 and no PD notice after the prescribed period is an escalation breach and must appear on the Corridor dashboard.

### 7.3 Encroachment register fields (OMMPR format)

| Field | Notes |
|---|---|
| Stretch chainage | |
| Side | LHS / RHS / Median |
| District / Tehsil | |
| Village | |
| Encroachment type | Temporary / Permanent |
| Category | Private / Agriculture / Commercial |
| Establishment date | Date the encroachment was established |
| Distance from road edge | Metres, or "Median" |
| Name of encroacher | |
| Photograph | One or more, watermarked |
| Notice status | Current stage, dates of each notice, next action due |

## 8. Modules C and E - Violence and Public Unrest

| Module | Sub-types | Extra capture | Chain |
|---|---|---|---|
| **C Violence** | Assault on staff, mob attack, vandalism, robbery or dacoity, vehicle damage, threat, gherao, strike violence | People involved, weapon, injuries, property damaged and value, CCTV reference, police station and FIR number, security deployment, tolling affected and duration | Master chain plus Security co-approver |
| **E Public Unrest** | Protest or dharna, blockade, strike or bandh, demonstration, riot, farmers or political agitation, labour unrest | Crowd size, road blocked, directions affected, duration, demands or cause, organiser, police coordination, alternate route, toll operations affected | Master chain plus Management co-approver |

## 9. Automation scope

| Area | Automated in the system | Residual manual work | Value |
|---|---|---|---|
| Identity and location | Incident ID, GPS, device timestamp, chainage bounds check, duplicate detection within 100 m | RPO confirms the pin | No transcription errors, no duplicates |
| Field validation | Casualty arithmetic, severity versus counts, vehicle count versus crash type, photo minimums, future-date block | None | Bad data rejected at source |
| Routing | Flow selected from accident category and crash type; work-zone inserts permit and safety steps | Approver judgement | A worker injury cannot enter the road-user queue |
| **Time limits** | 1-hour RPO, 5-day CRO, 48-hour Corridor, 3-day auto-close, all enforced by a scheduler; breach escalates | None | The flow is a control, not a guideline |
| **Auto-close** | Scheduled job closes the record 3 days after approval | None | No manual housekeeping |
| **Reopen control** | Only HO or Admin can reopen; reopening restarts the flow at CRO | Reopen decision | Integrity of the closed register |
| **Approval rights** | Role-action matrix enforced server-side: Project Manager is view-only on accidents and an approver on B, C and E; the Project Manager is notified automatically on every accident submission | None | No accidental approval at a level that does not own the decision |
| Response timing | Call response, arrival on site, total response time computed from timestamps | Verification | Emergency response protocol report without manual entry |
| Report generation | All 17 NHIT formats produced from live data, on schedule, each written into its prescribed Excel template | Review before release | Removes the largest manual workload in the current process |
| **Template-locked export** | The system populates the NHAI / NHIT workbook cell by cell and never restyles it, so colours, fonts, row heights, line spacing, column widths, merged cells and print area are exactly as issued. The PDF is printed from that same workbook | Template maintenance | What NHAI receives is identical to the prescribed format, from both Excel and PDF |
| **Notice ageing** | Encroachment notice stages dated, aged and escalated automatically | Notice issue and service happen offline; the Corridor records the reference and proof | Statutory trail is auditable |
| Dossier assembly | Incident PDF pack - photos, timeline, persons, vehicles, FIR, permit link | Review | Police and insurer submissions in minutes |
| Permit linkage | Auto-suspend permit, raise SWI, block close-out, flag failed checklist items | Investigation | Evidence trail showing which control failed |
| Master data | Dropdowns, crash-nature matrix, chainage bounds, SLA targets, report templates and their versions | Admin maintenance | No hard-coded values |

> **Not automatable - deliberately human.** Final severity confirmation, root-cause analysis, police coordination, encroachment removal decisions, notice service, legal action, insurance negotiation and dispute resolution. The system supplies the evidence; it does not make the call.

## 10. Design options - advantages and disadvantages

| Decision | Chosen approach | Advantages | Disadvantages | Mitigation |
|---|---|---|---|---|
| **Field platform** | Android native; PWA for office | Reliable offline store and background sync; live camera control; hardware GPS; no app-store gate for approvers | Two codebases; native app needs device management; PWA limited offline | Shared API and design system; single backend |
| **Architecture** | One incident core, module configurations | Roughly 70% reuse; one audit model; one reporting layer; a sixth family is cheap | Core must stay generic; over-abstraction risk; edge cases leak into the core | Type-specific block held as a schema-driven form definition |
| **Capture model** | Two stages - RPO triage, CRO completes | RPO form stays a 3-minute task at scene; the descriptive write-up is owned by a trained desk | CRO is the bottleneck; second handoff adds delay | Photo minimums, mandatory fields, CRO workload dashboard, 5-day clock |
| **Approval depth** | Fixed chain per module with hard time limits | Predictable, auditable, explainable to any auditor | Rigid if the organisation changes; low-severity volume still traverses all levels | Flow templates configurable without a code release |
| **Statutory reporting** | No MoRTH form; internal descriptive report only | CRO workload drops sharply; no dependence on MoRTH code lists; nothing to keep in step with a changing central format | NHAI or MoRTH statutory returns must be produced elsewhere if later demanded | The descriptive report already holds every source field; MoRTH A-E can be added as a derived section later |
| **External parties** | PIU, PD and SP have no system access; the Corridor records their notices | No licensing, training or login management for people outside the company; the statutory trail is still complete | Recording depends on the Corridor; a notice can be logged late | Ageing runs from the date printed on the notice, not the entry date; late entry is visible |
| **Report export** | Template-locked - the prescribed Excel workbook is populated, never restyled | Excel and PDF are identical to the NHAI format by construction; no formatting disputes | A template change needs a new template version and a cell remap | Template registry with versions, effective dates and locked regions; historical exports regenerate from the version in force |
| **Auto-close** | 3 days after approval, system job | Register stays current; no stale open cases | A late fact cannot be added once closed; depends on reopen discipline | Reopen is a one-click HO action and is fully audited |
| **Connectivity** | Offline-first | Works in dead zones and tunnels; no lost reports; true-time capture | Sync conflicts; device storage; harder support | Record-level locking; resumable upload; sync diagnostics |
| **Evidence** | Live camera only | Anti-fraud; genuine timestamp; no reuse of stock photos | Cannot reuse an existing photo; poor night quality | CRO may attach external evidence at Stage 2 |
| **Permit system** | PTW inside IMS | Native incident-to-permit linkage; one login; no integration cost | IMS becomes safety-critical; broader user base | Modular permissions; permit usable standalone |
| **Encroachment** | Temporary and permanent as separate lifecycles | Volume work stays fast; legal work gets a proper statutory trail | Two code paths and two report sets | Shared register, different milestone engine |
| **Deployment** | Single multi-corridor instance | One master data set; cross-corridor analytics; lower operating cost | Single point of failure; isolation must be airtight | Server-side scoping; per-corridor SLA; HA deployment |

## 11. Reporting

All of the following are produced today by hand and must be generated by the system.

### 11.1 NHIT operational reports

| # | Report | Frequency | Key content |
|---|---|---|---|
| 1 | **Highway Incident Report (HIR)** | Per incident | Nine sections plus appendices, HSMU cause analysis, photographs |
| 2 | **Accident Report (RPO / RPV)** | Per accident | Ambulance and crane arrival, casualties, first aid, property damage, analysis, dual signature |
| 3 | **Incident Log (RPO)** | Per shift | Serial, chainage, time, incident, action taken, plus shift summary counts (fuel, pedestrians, breakdown, cyclists, accidents, theft, unauthorised parking) and patrol count |
| 4 | **Daily accident and incident report** | Daily | Fatal, major, minor and non-injury counts |
| 5 | **Daily unusual occurrence accident report** (Article 16, clause 16.6 of the Concession Agreement) | Daily | Location, side, nature, classification, cause, road feature, road condition, intersection type, weather, vehicle responsible, persons affected by severity, animals killed, help provided, action taken, victim age, latitude and longitude in DMS, police station |
| 6 | **Monthly Break Down Report** | Monthly | Occurrence and attended time, chainage, side, incident, reason, action taken |
| 7 | **Monthly Dead Animal Removal Report** | Monthly | Date, time, location, side, type and count (dog, cow, other), action taken |
| 8 | **Monthly Encroachment Removal data** | Monthly | Nature of hoarding or encroachment, location, side, shift, temporary or permanent, action taken by and RPO name, remarks |
| 9 | **Monthly unusual occurrence accident data** | Monthly | As item 5, consolidated |
| 10 | **Monthly utilisation of ambulance** | Monthly | Ambulances deployed, life-support equipped, calls received, calls attended, average response time, victims taken to hospital, ambulances actually used |
| 11 | **Monthly vehicle responsible accident data** | Monthly | Accidents by responsible vehicle class, month-wise and cumulative |
| 12 | **Monthly Vehicle Running Status** | Monthly | Per vehicle (RPV-I, RPV-II, Crane-I, Crane-II, Ambulance-I, Ambulance-II, cow catcher): shift-wise start and end KM, total KM, fuel, PUC, insurance, fitness and road-tax expiry, last service KM |
| 13 | **Monthly Assets Damage Report** | Monthly | Visual inspection, damage, theft and missing: date, time, chainage, item description, quantity, loss type |
| 14 | **MPR (HQ Format)** | Monthly | 5.1.1 ambulance services, 5.1.2 crane services, 5.1.4 encroachments and banners, 5.1.5 accident and incident summary, 5.1.6 emergency response protocol |
| 15 | **OMMPR Encroachment List** | Monthly | Full permanent encroachment register with district, tehsil, village, category, encroacher name and photographs |
| 16 | **Year Accident Details** | Monthly cumulative | Fatal, major, minor and non-injury counts by month with annual total |
| 17 | **Consolidate Report** | Monthly | Combined monthly pack |

### 11.2 Export format - Excel and PDF must match NHAI exactly

The reports are downloaded for submission to NHAI, so the output is not "a report that contains the right data" - it is **the prescribed workbook, filled in**. The requirement is visual and structural fidelity, not just data fidelity:

| Aspect | Requirement |
|---|---|
| **Template** | One blank NHAI / NHIT workbook per report, held in a versioned template registry. The system writes values into named cells and never regenerates the layout |
| **Preserved exactly** | Cell and font colours, fonts and sizes, bold and borders, row heights and line spacing, column widths, merged cells, number and date formats, header and footer, freeze panes, print area and page setup |
| **Excel output** | A native .xlsx identical to the template apart from the data values. It opens and prints exactly as the NHAI original |
| **PDF output** | Printed from the same populated workbook through a fixed conversion profile, so Excel and PDF cannot diverge |
| **Locked content** | Titles, logos, statutory wording, column headings and totals labels are locked and cannot be edited by users |
| **Row behaviour** | Where a report has a variable number of rows, the template's row styling is cloned for each record so the last row and any total row stay correctly formatted |
| **Version control** | Each template carries a version and an effective date. A historical export is regenerated from the version that was in force at the time, so a template revision never rewrites past submissions |
| **Verification** | A visual regression check compares a generated workbook against the blank template, cell by cell and pixel by pixel, before a template version is released |

> **Dependency.** The blank NHAI / NHIT workbooks are the one input the project cannot create. They must be supplied per report at P0. Until they are, the export engine is built against the current Excel files in `Reports/`, and the styling is treated as provisional.

### 11.3 Management dashboards

Control Room live board (open by severity, ageing, awaiting CRO, SLA breaches); Corridor board (volume, trend, pending approvals, encroachment notice ageing); HO board (cross-corridor comparison, closures, compliance); response performance (call response, arrival, clearance, blockage, by chainage, beat and shift); safety (OHS versus NON-OHS, work-zone incidents by activity, permit compliance, SWI count); chainage heat map and repeat-location clustering; compliance (back-dated entries, post-submission edits, missing photos, incomplete reports, pending FIR).

### 11.4 Supporting registers

These ten formats exist as paper registers today and are referenced from the incident rather than replaced outright:

| Register | Link to IMS |
|---|---|
| Daily Vehicle Inspection Report | Vehicle master and shift |
| RP Vehicle Equipment Checklist | RPO shift and vehicle |
| Crane Vehicle Equipment Checklist | Recovery crane used on an incident |
| Log Book of Vehicles | Vehicle master |
| First Aid Register | Incident and casualty records |
| Ambulance Medicine Checklist / Consumption Register | Ambulance deployment per incident |
| Ambulance Patient Care Form (PCR) | Stores the PCR serial number only; clinical content stays with the operator |
| Bio-Medical Waste Register | Incidents with the bio-medical waste flag |
| Incident Log (RPO) | Generated from the shift's incident list |
| Accident Report (RPV) | Generated per accident |

## 12. Cross-cutting requirements

| Area | Requirement |
|---|---|
| Offline | All field forms usable with no network; encrypted local store; background sync with retry; conflict resolution (server wins on masters, client wins on draft); no data loss on app kill |
| Access control | Corridor, project and stretch scoping enforced server-side; RPO view revoked on submit; Project Manager view-only on accidents and approver on all other modules; reopen restricted to HO and Admin |
| Time limits | Every stage has a configurable clock; breach escalates one level and is visible on dashboards |
| Evidence | Live camera only; watermark date, time, lat, long and chainage; 2 MB max; offline upload queue |
| Audit | Append-only log of field, old value, new value, user, time and reason; immutable to all roles including admin |
| Notifications | In-app and e-mail always; SMS or WhatsApp for fatal and major incidents and for notice-stage breaches |
| Master data | All dropdowns admin-maintained: corridors, projects, chainage, toll plazas, incident types, crash-nature matrix, encroachment categories, notice periods, vehicles, SLA targets, report templates and versions |
| **Report export** | Every report is downloaded from its prescribed NHAI / NHIT workbook - colours, fonts, row heights, line spacing, column widths, merged cells and print area preserved. PDF is printed from the same workbook, so the two always match |
| Integrations | High priority: GIS chainage to coordinates, SMS or WhatsApp, SMTP, SSO. Medium: FIR reference, VMS or ATMS. Low: CCTV, weather feed, ERP |
| Data | Hosted in India; retention of 8 years or more for accident and encroachment records |
| Performance | Cold start 3 s or less; lists 2 s or less; API p95 500 ms or less; monthly report pack 30 s or less |

## 13. Delivery

| Phase | Content | Duration |
|---|---|---|
| P0 Foundation | Scope sign-off, SRS, wireframes, data model, masters, workflow engine with time limits, org hierarchy, **report template registry and cell mapping** | 4 weeks |
| P1 MVP | Incident core, road-user accident flow end to end (RPO, CRO descriptive report, Corridor, Project Manager observer, auto-close, reopen), Android app, template-locked export for the core reports, dashboards | 10-12 weeks |
| P2 Work zone | Work-zone accidents, permit module with live-road and WAH checklists, Maintenance Manager and Safety Officer (both web only), SWI, auto-suspension | 5-6 weeks |
| P3 Encroachment | Temporary and permanent lifecycles, notice log recorded by the Corridor for PIU / PD / SP with ageing, OMMPR register, encroachment reports, Project Manager and HO approval | 5-6 weeks |
| P4 Remaining | Violence and Public Unrest with Project Manager and HO approval, full 17-report pack in NHAI templates, MPR and HIR generation, integrations | 6-8 weeks |

Road-user accident first because it is the only module with a written process flow, it carries the highest volume and risk, and it exercises the core including time limits, auto-close and reopen.

## 14. Assumptions and risks

**Assumptions.** The Road User Accident Module Process Flow is the authoritative flow for A1. The field sheet is the authoritative RPO form. Stage 2 is completed by the CRO team and produces an internal descriptive report - **no MoRTH form and no statutory tables are in scope**. The Corridor reviews before the Project Manager, and the Project Manager approves only the non-accident modules. HO approval is a named corporate role, not a committee. PIU, PD and SP do not need system access; their notices are issued offline and logged by the Corridor. Field devices are company-issued Android handsets for RPOs only; the Maintenance Manager and the Safety Officer use the web console only. Offline operation is mandatory. Permits are a supporting module inside IMS. One deployment serves all corridors with data scoped by org unit. Digital chainage masters exist and can be data-loaded. The 17 report formats in the Reports folder are current, and the blank NHAI workbooks will be supplied.

| # | Risk | Impact | Mitigation |
|---|---|---|---|
| R1 | Project Manager is view-only on accidents, which may be perceived as a lack of ownership | Escalation may stall at the Corridor | The Corridor is the accountable approval step; the PM receives every record; HO retains reopen and oversight rights |
| R2 | Hard time limits (1 hour, 5 days, 48 hours) may not match ground reality | Constant SLA breach, then ignored | Validate limits with the field team; make them configurable |
| R3 | Auto-close at 3 days may close a case before police or insurer inputs arrive | Loss of evidence window | Reopen path is fast and audited; allow evidence attachment after closure |
| R4 | RPO view revoked on submit - RPO cannot correct an obvious error | Field frustration, workarounds | CRO can return with reason; allow a 15-minute grace edit |
| R5 | Permanent encroachment cases take months and cross legal boundaries | Stale records, ageing breaches | Milestone ageing dashboard; escalation on statutory period expiry |
| R6 | **Export fidelity** - NHAI rejects a download because a colour, row height or print area differs from its template | Reports unusable, manual rework returns | Template-locked generation with no restyling; visual regression check per template version; freeze templates in P0 against real samples |
| R7 | Large scale with unknown volumes | Cost and performance surprises | Stateless scalable design; load-test at ten times pilot volume |
| R8 | Master data quality - chainage, plazas, villages | Validation blocks submission | Data-load workshop in P0; pending-master flag |
| R9 | **NHAI revises a template mid-contract** | Old exports no longer match the current form | Versioned template registry with effective dates; historical exports regenerate from the version in force |
| R10 | **Notice stages are logged by the Corridor, not by the issuing party** | A notice is entered late or not at all, and the statutory trail looks shorter than it was | Ageing runs from the date printed on the notice; a late entry is flagged; unresolved cases appear on the Corridor and PD dashboards |
| R11 | **Blank NHAI workbooks are not supplied at P0** | Export styling cannot be finalised; late rework | Build against the current files in Reports/; treat styling as provisional; make template supply a P0 exit criterion |
| R12 | **No handset for the Safety Officer** - stop-work authority, SIMOPS clearance and the live-road / WAH checklists are recorded at a desk after the site visit | A stop-work instruction lags the hazard it was meant to stop; the audit trail shows the instruction after the event; checklist detail is recorded from memory | Console records the actual inspection and instruction time separately from the entry time and flags any gap; a stop-work must be raised by phone immediately and logged the same shift; revisit if the incident rate at work zones does not improve |

## 15. Open questions

| # | Question | Recommendation |
|---|---|---|
| **Q1** | Natural Disaster is out of scope. Should a minimal record still be captured so downtime reporting is not lost? | Yes - minimal record only, full module deferred until the SOP is shared |
| **Q2** | **Work-zone (OHS) accidents are treated as view-only for the Project Manager, consistent with road-user accidents. But a worker injury is an OHS case and the Project Manager authorises the permit. Should A2 give the Project Manager approve rights?** | Recommend yes for A2 only - it aligns approval authority with the permit the Project Manager already signs off, while A1 stays view-only |
| **Q3** | Add SPV/Vendor Member Involved as RPO field 22 so work-zone routing is decided at submit? | Yes - prevents worker injuries travelling the road-user path |
| **Q4** | What notice periods apply at each stage - Notice 1, 2, 3, PD notice, before SP letter? | Drives ageing clocks and escalation rules |
| **Q5** | Is a legal/appeal case (Section 24, Collector) tracked in IMS, or in a separate legal register? | Affects whether a case can pause |
| **Q6** | Confirm the 1-hour, 5-day, 48-hour and 3-day limits, and whether they are calendar or working days | Directly configures the scheduler |
| **Q7** | Which 17 report formats are still current, and **do you have the blank NHAI workbooks** to export into? | Without the blank workbooks the export styling stays provisional |
| **Q8** | Actual counts: corridors, projects, toll plazas, RPOs, approvers, annual incidents | Sizing and SLA |
| **Q9** | Which integrations exist at go-live (SAP, CCTNS, VMS, CCTV, AD)? | Architecture and timeline |
| **Q10** | Is a MoRTH statutory return ever required outside this system - and if so, who produces it? | Confirm the deferral in Section 2.2 is acceptable |
| **Q11** | **The Safety Officer is web-only, like the Maintenance Manager. Should he still get an Android handset for stop-work instructions and site checklists?** | Review at P2 against R12 - if stop-work response times slip, issue a handset and revert that one row to PWA + Android |

Further items for detailed design: insurance and claim workflow, language support, master-data ownership, report template versioning and cell mapping, the 48-hour back-dating rule, fatality notification timelines, third-party registration capture, retention period, corrective-action tracking, and the HO approval threshold.

## 16. Sign-off

| | Prepared by | Reviewed by | Approved by |
|---|---|---|---|
| **Name** | | | |
| **Designation** | | | |
| **Signature** | | | |
| **Date** | | | |

---

**Detailed reference** - field-level accident data mapping, the full 32-item permit checklist, the acceptance criteria and the extended risk register: `.opencode/plan/incident-management-scope-v0.2.md` (supplements this document; the flows, time limits, encroachment lifecycle and report catalogue in this version supersede it).

**Traceability.** Section 4.1, 5.4 and the time limits from the Road User Accident Module Process Flow. Sections 5.2 and 5.3 from the RPO field sheet, the Create Accident mock-up and the NHIT Accident Report (RPV). Section 11.1 from the 17 report files in Reports/. Section 11.2 from the NHAI / NHIT Excel report templates. Section 11.4 from the 10 register formats in Formats/. Section 6 from the permit SOP, the Live-Road and WAH formats and the PTW flow diagram. Section 7 from the Control of National Highways (Land and Traffic) Act 2002, MoRTH encroachment SOP 2025, the OMMPR Encroachment List and the Monthly Encroachment Removal data.
