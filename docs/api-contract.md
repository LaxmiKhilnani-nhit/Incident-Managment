# Conceptual API contract

**Status:** Proposal only. The repository has no implemented API. Paths, payloads, and response rules below are examples for design review, not approved interfaces. No framework, hosting vendor, database, identity provider, or authentication protocol is selected.

## Contract principles

- The API is the server-side enforcement point for user assignment, corridor/project/stretch scope, module permissions, workflow state, and audit. A hidden UI control is never authorization.
- Offline clients must be able to retry synchronization safely. Proposed client-generated idempotency keys and record revisions are design options, not source decisions.
- Store event times consistently and distinguish the event time, capture time, server receipt time, and (for offline-issued notices) the date printed on the notice.
- Return only data the current principal can view. In particular, a submitted RPO must not regain record-view access; Project Manager accident access is read-only under the baseline.
- API consumers must not choose arbitrary workflow states or write audit history.

## Conceptual resources and operations

`/api/v1` is illustrative; versioning and URL layout are open.

| Operation | Purpose | Authorization notes |
|---|---|---|
| `POST /incidents` | Create an incident draft and receive an identifier | RPO assignment, active project scope, allowed family |
| `GET /incidents` | List permitted records/queues with filters and pagination | Server applies scope and role filters before returning data |
| `GET /incidents/{id}` | Read incident and authorized details | RPO is denied after submit; PM accident access is observer-only |
| `PATCH /incidents/{id}` | Update an editable draft or assigned completion fields | Field-level/state-aware policy; submitted RPO fields are not casually overwritten |
| `POST /incidents/{id}/submit` | Validate and submit to the next workflow stage | Server validates required fields, locks the RPO submission, creates audit/work items |
| `POST /incidents/{id}/actions` | Request `approve`, `return`, or authorized `reopen` | Server derives valid actions from role, module, state, and scope; clients cannot set state directly |
| `POST /incidents/{id}/evidence` | Register/upload evidence and provenance metadata | Camera, watermark, size, and offline-queue requirements follow the approved policy |
| `GET /incidents/{id}/audit` | Read allowed audit history | Append-only; callers cannot mutate or delete events |
| `POST /permits` / `GET /permits/{id}` | Create/read a work permit and its checks | Project Manager authorizes work; Maintenance/Safety verification channel is PWA in source baseline |
| `POST /incidents/{id}/notice-milestones` | Record an offline-issued encroachment notice/document milestone | Corridor records it; ageing uses printed issue date, not entry date |
| `POST /reports/{reportType}/exports` | Request a report export | Permission and data scope are server-enforced; export records template version |
| `GET /admin/users`, `/admin/roles`, `/admin/assignments` | Administer dynamic users, role templates, and scoped assignments | These resources are a proposal derived from the user's dynamic-role direction; exact administration policy is open |

The work-permit, notice, export, and user-management paths are conceptual examples; the scope does not define HTTP routes.

## Example request shape (illustrative)

```json
{
  "clientRequestId": "client-generated-retry-key",
  "family": "accident",
  "subtype": "road-user",
  "occurredAt": "2026-11-17T09:00:00+05:30",
  "location": {
    "projectId": "assigned-project",
    "chainage": "000.000",
    "direction": "LHS",
    "latitude": 0.0,
    "longitude": 0.0,
    "accuracyMeters": 25
  },
  "capture": {
    "deviceCapturedAt": "2026-11-17T09:05:00+05:30",
    "evidenceIds": []
  }
}
```

Identifiers and values are placeholders. The full RPO form, enums, validation rules, and the proposed SPV/Vendor field must be reconciled with the source field sheet and Q3 before implementation.

## Suggested response and error behavior

These are proposals to make client behavior predictable:

- Return a server representation and a revision/ETag after accepted writes.
- Use `400` for malformed requests, `401` for unauthenticated callers, `403` for denied scope/action, `404` for inaccessible or unknown identifiers (subject to privacy policy), `409` for stale revision or invalid transition, `413` for evidence over an approved size limit, and `422` for field validation failures.
- Include a stable error code, safe message, and field-level validation details where appropriate. Never return success for a rejected transition.
- Require a reason for returned/reopened or audited corrections where the approved workflow calls for it.

## Offline synchronization proposal

The device keeps draft changes and evidence in an encrypted local store, retries safely, and shows pending/failed/synchronized states. A retry key prevents duplicate incidents; record revision/conflict behavior must match the source rule (server wins for master data, client wins for a draft) while protecting submitted/approved records. Conflict handling and multi-device ownership need design approval.

## Authorization decisions still open

The API should treat users, role templates, and scoped assignments as data, while keeping only invariant security rules in code. The exact authentication/SSO integration, role-combination semantics, user provisioning, dynamic permission editing, approval delegation, token/session policy, and A2 Project Manager rights are not yet defined. PIU, PD, and SP are not IMS principals in the source scope.
