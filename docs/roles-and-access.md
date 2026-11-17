# Dynamic users, roles, and access

**Status:** User-directed requirements establish dynamic users/roles with predefined starting roles, a CRO-stage toggle, and a configurable HO final-approver list. The source scope also assigns Admin responsibility for user/master/workflow management. The model and exact permissions below remain proposals, not an approved authorization policy.

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
| **CRO** | PWA | Completes the CRO stage and descriptive accident report when the availability toggle is enabled; gathers department inputs. The source's 5-day completion target attaches to this stage. |
| **Corridor Incharge / Operations Manager Corridor** | PWA | When the CRO toggle is disabled, completes the CRO stage and routes directly to the configured HO final-approver list; no separate Corridor review is inserted in that path. Enabled-toggle pre-HO review details remain open. |
| **Project Manager** | PWA | Existing source behavior before HO: observer on A1 accidents; approves encroachment, violence, and public unrest; authorizes work permits. How these stages route to HO, and A2 incident authority, remain open. |
| **HO** | PWA | Final approver for every workflow, acting through a configurable HO-level approver list; may also reopen closed incidents under source rules. Roster selection semantics and the enabled-toggle route before HO are open. |
| **Admin** | PWA | Manage masters, workflow/report templates, SLAs, users, and potentially workflow settings if authorized; may reopen closed incidents under source rules. Admin is not an HO final approver by implication. |
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
4. Every workflow ends at the configured HO final-approver list under the later user-directed requirement. Preserve source roles (including PM decisions) as possible preceding stages, but do not assume their exact order before HO.
5. Only HO or Admin may reopen a closed incident. Reopening restarts at CRO.
6. Maintenance Manager and Safety Officer use the PWA in the baseline. Q11 asks whether Safety should also receive an Android handset; this is unresolved.
7. Approver assignments, backup coverage, temporary delegation, multi-role collision behavior, user onboarding/offboarding, and role-template change audit need decisions before implementation.

## Decisions required before implementation

Approve the permission matrix by module and state (especially A2), who may define/assign role templates, whether users can hold multiple concurrent roles, the granularity of user scope, identity/SSO and provisioning, delegation/backup behavior, external integration accounts, and how policy changes affect in-flight incidents. The CRO toggle's scope/default and effect on in-flight cases, the authorized editor/reviewer for HO roster settings, roster selection semantics, and whether a completion actor may also be selected as HO approver are open. Enabled-toggle pre-HO review/actor overlap also needs policy. Until decided, this file is a proposal and must not be used as an authorization specification.

## Administration lifecycle proposal

This process expresses the requested dynamic model. The scope names Admin as owner of users and configurable masters/workflows, but does not define the exact screens, approvals, or identity lifecycle.

1. **Provision user:** Admin creates or synchronizes a user identity and marks the account active/inactive. Do not store passwords or select an identity provider as part of this proposal.
2. **Choose templates:** Admin selects one or more enabled seed/custom role templates. A template describes capabilities, not a person's organization.
3. **Scope assignment:** Admin assigns the template to a corridor/project/stretch and an effective period. Validate that the selected scope exists and that a child scope is within its parent.
4. **Review/activate:** a proposed second-person review for privileged or broad assignments is recommended but not source-confirmed. If adopted, record requester, reviewer, timestamp, reason, and before/after values.
5. **Configure workflow routing:** configure the CRO availability toggle and the HO final-approver list as versioned workflow settings. When disabled, the Corridor Manager completes the CRO stage and the task goes directly to HO, without a separate Corridor review step. Which Admin/HO actor may change or approve these settings is open; changes must be audited.
6. **Evaluate access at request time:** resolve active user -> active assignments -> scope intersection -> resource/action -> workflow state/field ownership -> final-approver roster membership where the transition is the HO gate. Every check is server-side.
7. **Change/revoke:** role-template edits, assignments, and workflow settings are versioned and audited. Deactivation should stop new actions immediately; handling of in-flight tasks and historical read access needs policy.
8. **Periodic review:** provide a report of dormant users, broad scopes, expired assignments, and users with multiple conflicting roles; review frequency is open.

### Permission evaluation proposal

For each request, evaluate the following in order:

1. Is there a verified, active principal?
2. Is there at least one active assignment covering the requested organization scope?
3. Does an enabled role template grant the requested capability for this resource type?
4. Does the workflow allow this action from the current state and module?
5. For a final approval transition, is the caller eligible under the active configured HO approver list? Whether one listed approver, all, a quorum, or an ordered sequence is required remains open.
6. Does record visibility/field ownership allow this caller to read or change the requested fields?
7. If all checks pass, record the accepted event and audit metadata; otherwise reject without partial state change.

Assignments do not override the source's invariants: RPO cannot view a submitted record, the configured HO list is the final approval authority under the new user direction, and only HO/Admin can reopen a closed record. A2's enabled-toggle pre-HO route remains unresolved. A role grant must never confer data access outside its assigned scope. When CRO is disabled, whether the Corridor Manager completing the CRO stage may also be selected on the HO list is open; do not infer permission from holding both role templates.

### Seed template capability matrix

`C` = create, `R` = read, `U` = update, `S` = submit, `A` = approve/review, `X` = special action. This table is a review aid; exact field and state policy must be approved.

| Template | Incident operations | Other capabilities | Explicit boundary |
|---|---|---|---|
| RPO | C/U own draft; S to workflow; R only before submission as specified | Capture GPS/photos; report immediate field facts | No view after submit; no approval |
| CRO | R assigned queue; U CRO-owned descriptive sections; S to next stage when toggle enabled | Add department response details | The toggle selects who performs this completion stage; other workflow roles are unchanged |
| Corridor | When CRO disabled, U/S for CRO-stage completion, then direct route to HO; source Corridor review when separately configured | X record notice milestones; endorse work-zone permit | No separate Corridor review in disabled-toggle route; same-identity overlap with HO roster remains open |
| Project Manager | Existing source observer/review/authorization duties before HO | X authorize permit; source PM approvals where still applicable | Exact pre-HO route and A2 incident role need reconciliation; HO remains final |
| HO | R assigned scope; A final approval for every workflow through configured roster | X reopen closed incident | Roster selection semantics and eligible users are configurable/open |
| Admin | No blanket incident grant or HO final-approval grant implied | X manage user/templates/masters/workflows/SLAs; X reopen; workflow-setting authority needs approval | Admin is not an HO approver unless separately configured through the approved HO roster |
| Maintenance Manager | R linked work-zone permit; U physical verification fields | Record site inspection | PWA only in baseline; no general incident approval |
| Safety Officer/EHS | R linked permit/work-zone record; U safety checks; A safety co-approval | X issue/close SWI | PWA only in baseline; handset is Q11 |
| HSMU | R assigned accident dossier; U HIR/investigation fields | Add cause analysis and appendices | No approval right is stated |
| Security co-approver | R/U assigned Violence review; A only if approved | Security review details | Implied by source; authority/order needs confirmation |
| Management co-approver | R/U assigned Public Unrest review; A only if approved | Management review details | Implied by source; authority/order needs confirmation |

### Configuration and change controls

- Seed templates are starting defaults; organizations may add or adjust templates only through an approved governance process.
- Keep role-template version, capability diff, editor, timestamp, reason, and approval. Do not rewrite past audit events when a template changes.
- Keep assignment effective dates and scope explicit. Decide whether future-effective changes apply to work already in progress.
- A workflow configuration must name applicable module, current state, permitted action, eligible template/capability, next state, timer, escalation target, CRO-toggle behavior, and final HO approver list. Every workflow ends at the HO roster; the route before it remains open.
- Treat the CRO availability toggle as controlling only the CRO completion-stage owner. When off, completion routes directly to HO and does not add a separate Corridor review step.
- Whether a Corridor Manager completing the CRO stage may also appear on the HO list and give the final approval is an unresolved segregation-of-duties decision or recommendation, not an approved restriction or permission.
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
