# Glossary

Terms below follow [IMS Scope Document v1.5](../IMS-Scope-Document.md). Where that document uses an acronym without expanding it, this glossary says so instead of guessing.

| Term | Meaning in this documentation |
|---|---|
| **A1** | Road-user accident module. The source defines its reference flow as RPO -> CRO -> Corridor, PM observer, auto-close, and HO/Admin-only reopen. |
| **A2** | Work-zone accident module, linked to a work permit and additional Maintenance/Safety checks. Project Manager approval is unresolved. |
| **API** | Application programming interface. The API described here is conceptual; no implementation exists. |
| **ATMS** | System named as a medium-priority integration in the source; its expansion and actual interface are not specified. |
| **Chainage** | Position along the project road, recorded in the source as a masked kilometre/metre value and checked against project bounds. |
| **Corridor** | Organizational/operational review level between CRO and higher approval roles. The source also refers to Corridor Incharge / Operations Manager Corridor. |
| **CRO** | Control Room Officer; completes incident information and the descriptive accident report, then submits to Corridor. |
| **EHS** | Environment, Health and Safety wording appears in source form names and Safety Officer role labels; the formal organizational expansion is not defined there. |
| **ERP** | System category named for payroll/procurement/asset-depreciation boundaries and a low-priority integration; expansion is not given in the source. |
| **GIS** | Geographic/chainage integration category named by the source; exact system and contract are not specified. |
| **HIR** | Highway Incident Report; formal investigation document prepared by HSMU with cause analysis and appendices. |
| **HO** | Final corporate approval role in non-accident flows and one of the only roles allowed to reopen closed incidents. The source does not expand the acronym; Section 3 groups HO/Admin for final approval, while later text names HO, so that authority needs reconciliation. |
| **HSMU** | Highway Safety Management Unit; investigation team for the HIR. |
| **IMS** | Incident Management System. |
| **Idempotency key** | Proposed API retry identifier that lets a client replay one operation without intentionally creating duplicate incidents. It is not a source-defined API field. |
| **MPR** | Monthly report identified as “MPR (HQ Format)” in the report list; the source does not expand the acronym. |
| **MLR** | Number captured for an injured person's record; the source does not expand the acronym. |
| **MoRTH** | Ministry name referenced by the source for accident-reporting formats and encroachment guidance; the root scope does not spell out the acronym. |
| **NHAI** | Authority named as the recipient/source of prescribed report templates; the scope does not spell out the acronym. |
| **NHIPPL / NHIT** | Organization names used by the source for the incident system and reports; their formal expansions are not provided in the scope document. |
| **NON-OHS / OHS** | Accident classifications used by the source for road-user/non-work and work-related safety routing. The source does not formally expand these labels. |
| **OHS** | See NON-OHS / OHS; work-zone accidents default to OHS in the source. |
| **OMMPR** | Name used for the permanent encroachment list/report. Its expansion is not supplied in the source. |
| **PCR** | Ambulance Patient Care Form reference; IMS stores the PCR serial number only, not the clinical content. |
| **PD** | Project Director; issues an offline notice and sends the SP assistance letter in the permanent-encroachment process. |
| **PIU** | Project Implementation Unit; issues offline encroachment notices, which Corridor records in IMS. |
| **PM / Project Manager** | Project Manager role. Read-only observer on accidents in the baseline; approves B/C/E and authorizes work permits. A2 incident authority is open. |
| **PWA** | Progressive Web App; office console for the source's non-RPO users. |
| **RPO** | Route Patrolling Officer; creates the initial field incident in the Android app and loses view access after submission. |
| **RPV** | Vehicle/report label appearing in the source (for example RPV-I and Accident Report RPO/RPV); its expansion is not specified. |
| **SLA** | Time limit or service-level target attached to workflow stages. The source does not specify whether all day-based targets are calendar or working days. |
| **SIMOPS** | Safety verification term used for permit checks; expansion is not supplied in the source. |
| **SP** | Superintendent of Police; receives an offline assistance letter in the permanent-encroachment process and has no IMS account. |
| **SPV** | Abbreviation used in “SPV/Vendor Member Involved”; expansion is not given in the source. The proposed field is Q3, not an approved field. |
| **SSO** | Integration category named as high priority; identity provider and protocol are not selected, and the source does not expand the acronym. |
| **SWI** | Stop Work Instruction; raised for a work-zone safety issue/incident and acknowledged/closed by Safety in the console. |
| **VMS** | System category named as a medium-priority integration; expansion and actual interface are not specified. |
| **WAH** | Work at Height; permit/checklist reference EHS/FRM/019 in the source. |

## Status vocabulary

| Label | Meaning |
|---|---|
| **Source requirement** | Explicitly stated in the root scope; still subject to its draft approval status. |
| **Proposal** | A technical suggestion in these docs; not an approved implementation decision. |
| **Open decision** | Source question or unresolved conflict requiring an owner decision. |
| **Out of scope / deferred** | Explicitly excluded from this delivery or deferred until an input is supplied. |

## Workflow vocabulary

| Term | Meaning |
|---|---|
| **Draft** | Work-in-progress incident data not yet submitted into the approval chain. |
| **Submit** | A validated action that hands the record to the next workflow actor and starts the next stage clock. |
| **Return / rework** | A reviewer sends a record back with a reason; the exact allowed actors and clock-pause rules remain open. |
| **Approve** | A role-authorized decision that advances the current workflow stage; a client cannot directly set the state. |
| **Auto-close** | Scheduler closes an approved accident three days after approval, per the source baseline. |
| **Reopen** | HO/Admin-only action for a closed incident; the accident cycle resumes at CRO. |
| **Notice milestone** | Dated permanent-encroachment event with issuer, printed date, allowed period, entry time, and evidence. |
| **Template-locked export** | Populate an approved NHAI/NHIT workbook without restyling it; create the PDF from that populated workbook. |
