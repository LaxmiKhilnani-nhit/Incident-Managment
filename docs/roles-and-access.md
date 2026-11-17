# Dynamic users, roles, and access

**Status:** The user direction for this foundation is that users and roles are dynamic, with some predefined starting roles. The source scope also assigns Admin responsibility for user/master/workflow management. The data model and seed set below are proposals, not approved permission policy.

## Proposed model

Keep the person/account, role definition, and assignment separate:

- **User**: identity and active/inactive lifecycle; identity provider and provisioning mechanism are open.
- **Role template**: named, configurable capabilities such as `incident.create` or `workflow.approve`. Seed templates should be editable by authorized administrators rather than compiled as a closed role enum.
- **Assignment**: user + role template + organizational scope (corridor, project, and/or stretch) + effective period/status. A person may have multiple assignments; exact combination and conflict rules are open.
- **Workflow policy**: module/state/action matrix determines which capabilities are valid at each step. Roles alone must not bypass state transitions or scope restrictions.

This model expresses dynamic users and roles without assuming a vendor, identity protocol, framework, or cloud product. The source's server-side corridor -> project -> stretch visibility rule is a requirement. Least-privilege defaults and explicit deny on missing assignment are proposed safety rules for review.

## Initial seed role templates

The names and baseline responsibilities below come from source Sections 3-4 and 6-8. They are starting templates; actual users, assignments, and final permissions remain configurable.

| Seed template | Channel in source | Baseline responsibilities / limits |
|---|---|---|
| **RPO** | Android | Create basic incident/draft for own project, capture location/evidence, submit within 1 hour. Cannot view the record after submission. |
| **CRO** | PWA | Complete incident and descriptive accident report, gather department inputs, submit to Corridor within 5 days. |
| **Corridor Incharge / Operations Manager Corridor** | PWA | Review/comment/edit and approve accidents within 48 hours; record offline-issued encroachment notice evidence. |
| **Project Manager** | PWA | View-only on accidents under baseline; approves encroachment, violence, and public unrest; authorizes work permits. Work-zone incident approval is unresolved (Q2). |
| **HO** | PWA | Final approval in non-accident master chain; may reopen closed incidents. |
| **Admin** | PWA | Manage masters, workflow/report templates, SLAs, and users; may reopen closed incidents. Section 3 groups HO/Admin for final approval, while later sections name HO; alternate Admin approval is unresolved. |
| **Maintenance Manager** | PWA | Record physical permit verification and work-zone fact checks after site visit; no mobile app in baseline. |
| **Safety Officer / EHS** | PWA | Record safety/SIMOPS checks, co-approve work-zone safety, issue/acknowledge/close SWI; no mobile app in baseline. |
| **HSMU** | PWA | Prepare Highway Incident Report investigation, cause analysis, summary, and appendices. |
| **Security co-approver** | PWA (implied) | Co-approve Violence cases; detailed assignment/approval rules need definition. |
| **Management co-approver** | PWA (implied) | Co-approve Public Unrest cases; detailed assignment/approval rules need definition. |

The source says PIU, PD, and SP have **no system access**. Their notices/letters are issued offline and logged by the Corridor; do not create external-party seed accounts.

## Access rules to preserve

1. Enforce corridor/project/stretch scope on the server for every query and mutation.
2. Revoke RPO record-view access immediately on submit; exceptions or the proposed 15-minute grace edit in Risk R4 need explicit approval.
3. PM is an observer for A1 accidents, not an approver. Q2 conflicts on A2 work-zone accidents; do not infer a final permission.
4. PM approval applies to B, C, and E in the source baseline; HO is a later approval for those non-accident modules.
5. Only HO or Admin may reopen a closed incident. Reopening restarts at CRO.
6. Maintenance Manager and Safety Officer use the PWA in the baseline. Q11 asks whether Safety should also receive an Android handset; this is unresolved.
7. Approver assignments, backup coverage, temporary delegation, multi-role collision behavior, user onboarding/offboarding, and role-template change audit need decisions before implementation.

## Decisions required before implementation

Approve the permission matrix by module and state (especially A2), who may define/assign role templates, whether users can hold multiple concurrent roles, the granularity of user scope, identity/SSO and provisioning, delegation/backup behavior, external integration accounts, and how policy changes affect in-flight incidents. Until decided, this file is a proposal and must not be used as an authorization specification.

## Administration lifecycle proposal

This process expresses the requested dynamic model. The scope names Admin as owner of users and configurable masters/workflows, but does not define the exact screens, approvals, or identity lifecycle.

1. **Provision user:** Admin creates or synchronizes a user identity and marks the account active/inactive. Do not store passwords or select an identity provider as part of this proposal.
2. **Choose templates:** Admin selects one or more enabled seed/custom role templates. A template describes capabilities, not a person's organization.
3. **Scope assignment:** Admin assigns the template to a corridor/project/stretch and an effective period. Validate that the selected scope exists and that a child scope is within its parent.
4. **Review/activate:** a proposed second-person review for privileged or broad assignments is recommended but not source-confirmed. If adopted, record requester, reviewer, timestamp, reason, and before/after values.
5. **Evaluate access at request time:** resolve active user -> active assignments -> scope intersection -> resource/action -> workflow state/field ownership. Every check is server-side.
6. **Change/revoke:** role-template edits and assignment changes are versioned and audited. Deactivation should stop new actions immediately; handling of in-flight tasks and historical read access needs policy.
7. **Periodic review:** provide a report of dormant users, broad scopes, expired assignments, and users with multiple conflicting roles; review frequency is open.

### Permission evaluation proposal

For each request, evaluate the following in order:

1. Is there a verified, active principal?
2. Is there at least one active assignment covering the requested organization scope?
3. Does an enabled role template grant the requested capability for this resource type?
4. Does the workflow allow this action from the current state and module?
5. Does record visibility/field ownership allow this caller to read or change the requested fields?
6. If all checks pass, record the accepted event and audit metadata; otherwise reject without partial state change.

Assignments do not override the source's invariants: RPO cannot view a submitted record, a PM cannot approve A1, and only HO/Admin can reopen a closed record. A2 remains unresolved. A role grant must never confer data access outside its assigned scope.

### Seed template capability matrix

`C` = create, `R` = read, `U` = update, `S` = submit, `A` = approve/review, `X` = special action. This table is a review aid; exact field and state policy must be approved.

| Template | Incident operations | Other capabilities | Explicit boundary |
|---|---|---|---|
| RPO | C/U own draft; S to workflow; R only before submission as specified | Capture GPS/photos; report immediate field facts | No view after submit; no approval |
| CRO | R assigned queue; U CRO-owned descriptive sections; S to Corridor | Add department response details | Cannot silently overwrite RPO-declared values; changes require return/audited correction |
| Corridor | R/U assigned incident; A for accident; intermediate review on other modules | X record notice milestones; endorse work-zone permit | Accident approval is accountable; notice dates use printed issue date |
| Project Manager | R accident (observer only); A for B/C/E | X authorize permit | A2 incident A permission is open; no A1 edit/approve/return |
| HO | R assigned scope; A final non-accident step | X reopen closed incident | No A1 approval gate in source |
| Admin | No blanket incident grant implied; non-accident final approval is unresolved | X manage user/templates/masters/workflows/SLAs; X reopen | Section 3 groups HO/Admin for final approval, but Section 4.2 and assumptions say HO; do not grant default approval until reconciled |
| Maintenance Manager | R linked work-zone permit; U physical verification fields | Record site inspection | PWA only in baseline; no general incident approval |
| Safety Officer/EHS | R linked permit/work-zone record; U safety checks; A safety co-approval | X issue/close SWI | PWA only in baseline; handset is Q11 |
| HSMU | R assigned accident dossier; U HIR/investigation fields | Add cause analysis and appendices | No approval right is stated |
| Security co-approver | R/U assigned Violence review; A only if approved | Security review details | Implied by source; authority/order needs confirmation |
| Management co-approver | R/U assigned Public Unrest review; A only if approved | Management review details | Implied by source; authority/order needs confirmation |

### Configuration and change controls

- Seed templates are starting defaults; organizations may add or adjust templates only through an approved governance process.
- Keep role-template version, capability diff, editor, timestamp, reason, and approval. Do not rewrite past audit events when a template changes.
- Keep assignment effective dates and scope explicit. Decide whether future-effective changes apply to work already in progress.
- A workflow configuration must name applicable module, current state, permitted action, eligible template/capability, next state, timer, and escalation target. The A2 PM decision must be blocked from activation until resolved.
- User-facing role labels may be localized or renamed without changing stable internal capability identifiers; localization itself remains an open product decision.
- No dynamic permission editor should permit an administrator to remove mandatory invariants such as scope isolation, append-only audit, or HO/Admin-only reopen unless the source requirement is formally revised.

## Example assignment (illustrative)

```json
{
  "userId": "user-identifier",
  "roleTemplateId": "corridor-reviewer",
  "scope": {
    "corridorId": "corridor-identifier",
    "projectId": "project-identifier"
  },
  "validFrom": "2026-11-17T00:00:00+05:30",
  "validUntil": null,
  "status": "active",
  "reason": "Assigned to the project's incident review queue"
}
```

This is not a production schema. The source does not define IDs, lifecycle fields, assignment approvers, or whether a user may have simultaneous assignments at different scopes.
