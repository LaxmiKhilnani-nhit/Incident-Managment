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
| **Admin** | PWA | Manage masters, workflow/report templates, SLAs, and users; may reopen closed incidents. Whether every Admin has unrestricted data access is not specified. |
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
