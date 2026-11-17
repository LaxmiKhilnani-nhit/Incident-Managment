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
| `GET /admin/users`, `/admin/roles`, `/admin/assignments`, `/admin/workflow-settings` | Administer dynamic users, role templates, scoped assignments, CRO toggle, and HO final-approver roster | Settings endpoints are conceptual; editor, reviewer, scope, effective-time, and list-selection rules are open |

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

The API should treat users, role templates, scoped assignments, CRO availability, and the HO final-approver roster as configuration data, while keeping invariant security rules in code. Every in-scope workflow must route to the configured HO list. The exact route before HO when CRO is enabled, roster selection rule (one/all/quorum/sequence), settings scope/effective-time behavior, settings editor/reviewer, and same-person completion/final-approval guard are open. Authentication/SSO, role-combination semantics, user provisioning, token/session policy, and A2 Project Manager rights are also not yet defined. PIU, PD, and SP are not IMS principals in the source scope.

## Proposed contract conventions

These conventions make the conceptual API testable; none are established by the source:

- **Representation:** JSON for resource metadata; binary evidence is uploaded through an API operation whose transport/storage implementation remains undecided.
- **Versioning:** `/api/v1` is an example path. A breaking contract change would require a new major path or an explicitly approved compatibility policy; do not assume URL versioning is final.
- **Timestamps:** ISO 8601 with offset. Preserve separate `occurredAt`, `deviceCapturedAt`, `receivedAt`, and `issuedAt` values where applicable. Never replace the printed notice date with server entry time.
- **Concurrency:** return an opaque revision/ETag. Mutations use `If-Match`; stale revisions return a conflict rather than silently overwriting another actor's work.
- **Retry:** all create/sync mutations carry an `Idempotency-Key` or stable client request ID. Same key and same payload returns the original accepted result; same key with a different payload is rejected.
- **Identity:** authentication is supplied by an undecided approved identity integration. The API derives `actorId`, roles, and scope from the verified request context; it ignores role/scope claims in user-editable JSON.
- **Pagination:** list endpoints use cursor pagination and bounded page size (exact limits are open); filters are applied after authorization, not before.

## Resource model

| Resource | Required conceptual fields | Owner / lifecycle |
|---|---|---|
| User | `id`, display identity, active status | Admin-managed; identity source is open |
| Role template | `id`, name, capability set, version, enabled | Admin-managed proposal; source only states Admin manages users/workflows |
| Assignment | user, role template, scope, effective period, status | Determines effective access; approval and multi-role rules are open |
| Workflow settings | scope, CRO-stage enabled flag, HO final-approver user list, effective period, revision | User-directed configuration; setting editor, scope, and approver-selection semantics are open |
| Incident | id, family/subtype, location, occurrence time, state, revision, created/updated metadata | Shared core; module detail is family-specific |
| Workflow action | action, actor, reason, timestamp, prior/resulting state | Append-only evidence of an allowed transition |
| Evidence | id, media metadata, capture/receipt timestamps, provenance, upload status | Binary storage implementation is open |
| Permit | id, project/scope, validity, checks, status, linked incidents | Work-zone support workflow |
| Encroachment milestone | case, stage, printed issue date, allowed period, entry time, document reference | Corridor records offline-issued stages |
| Report export | type, period/filter, template version, status, requester, output references | Generated from authorized data; template files supplied externally |

## Proposed endpoint matrix

`actor` below means the authenticated principal's server-resolved assignments and scope. No request parameter can widen it.

| Method and path | Request / result | State / authorization |
|---|---|---|
| `GET /api/v1/me` | Current profile and effective assignments | Only the caller's own safe identity and assignments |
| `GET /api/v1/incidents` | Filters: state, family, project, date, assigned queue; cursor response | Scope and role filters applied server-side |
| `POST /api/v1/incidents` | Create draft; returns `201` with incident representation and revision | Assigned RPO, allowed project/family |
| `GET /api/v1/incidents/{id}` | Incident and permitted module details | RPO denied after submit; PM accident access is read-only |
| `PATCH /api/v1/incidents/{id}` | Partial fields in caller's editable section | Draft/state/field ownership checked; requires `If-Match` |
| `POST /api/v1/incidents/{id}/submit` | Validate required fields and submit | RPO draft; creates completion-stage task for CRO or Corridor Manager according to toggle and audit event |
| `POST /api/v1/incidents/{id}/actions` | `approve`, `return`, or `reopen` plus reason/comment | Server enforces state, scope, and active HO roster for the final approval action |
| `POST /api/v1/incidents/{id}/evidence` | Accept evidence metadata and content (or an approved chunked-upload protocol); return evidence id/status | Incident write permission; camera/watermark/count/size rules validated; storage mechanism is undecided |
| `GET /api/v1/incidents/{id}/audit` | Authorized audit page | Read-only and scope-filtered |
| `POST /api/v1/sync/batches` | Ordered offline operations, each with client idempotency key and base revision | Each item is authorized independently; returns per-item accepted/conflict/rejected result |
| `POST /api/v1/permits`; `POST /api/v1/permits/{id}/checks` | Create permit/checklist response | Project Manager authorization and assigned Maintenance/Safety checks precede the required HO final stage; exact sequence needs approval |
| `POST /api/v1/encroachments/{id}/milestones` | Append notice/removal/cost-recovery event | Corridor assignment; printed date and recorded date are both required |
| `POST /api/v1/reports/{reportCode}/exports` | Request report for period/scope; return export job/status | Scope, report permission, template availability/version checked |
| `GET` / `POST /api/v1/admin/users`; `GET` / `POST` / `PATCH /api/v1/admin/role-templates`; `GET` / `POST` / `PATCH /api/v1/admin/assignments` | Manage dynamic users, role templates, and scoped assignments | Admin capability proposed; exact delegation and approval rules open |
| `GET` / `PATCH /api/v1/admin/workflow-settings/{scopeId}` | Read/update CRO toggle and HO final-approver user list for a workflow scope | Conceptual endpoint; editor/reviewer, scope, effective time, and list-selection semantics are open; every change is audited |
| `GET /api/v1/masters/{name}` | Read permitted master values | Organization scope; write operations require approved Admin policy |

The list is a proposed starting surface, not a complete endpoint commitment. In particular, payloads for all 21 RPO fields, CRO report sections, each module, checklist answers, exports, and binary transfer need to be traced against the source files before implementation.

## Example: create an offline accident draft

```http
POST /api/v1/incidents
Content-Type: application/json
Idempotency-Key: rpo-device-42:local-incident-981
```

```json
{
  "family": "accident",
  "subtype": "road-user",
  "occurredAt": "2026-11-17T09:00:00+05:30",
  "location": {
    "projectId": "assigned-project",
    "chainage": "000.000",
    "direction": "LHS",
    "roadType": "main-carriageway",
    "latitude": 12.0,
    "longitude": 77.0,
    "accuracyMeters": 25
  },
  "fieldData": {
    "accidentCategory": "NON_OHS",
    "crashType": "unknown",
    "vehicleCount": 0,
    "totalPersons": 0,
    "killed": 0,
    "grievous": 0,
    "minor": 0
  },
  "capture": {
    "deviceCapturedAt": "2026-11-17T09:05:00+05:30",
    "evidenceClientIds": []
  }
}
```

Illustrative response:

```http
HTTP/1.1 201 Created
ETag: "rev-1"
```

```json
{
  "id": "server-generated-id",
  "state": "DRAFT",
  "revision": 1,
  "createdAt": "2026-11-17T09:05:05Z",
  "sync": {
    "clientRequestId": "rpo-device-42:local-incident-981",
    "status": "accepted"
  }
}
```

The example omits required fields and is not a valid submission payload. `projectId`, coordinates, and timestamps are placeholders; the server must check that the authenticated RPO is assigned to that project. The field names and accepted enum values are proposals pending field-sheet reconciliation.

## Example: submit and handle a conflict

```http
POST /api/v1/incidents/server-generated-id/submit
If-Match: "rev-1"
Idempotency-Key: rpo-device-42:submit-981
Content-Type: application/json
```

```json
{"comment": null}
```

On successful validation, a conceptual response is `202 Accepted` with the completion-stage state (the source calls it `PENDING_CRO`), a new revision, server `receivedAt`, and a task/deadline assigned to CRO or Corridor Manager according to the active toggle. If required photos or casualty validation fail, return `422` with stable field errors; do not partially transition the incident. If another accepted write changed the revision, return `409` and the latest revision only if the caller is still authorized to read it.

## Example workflow settings (illustrative)

```json
{
  "scope": {
    "type": "corridor",
    "id": "corridor-identifier"
  },
  "croStageEnabled": false,
  "hoFinalApproverUserIds": ["ho-user-17", "ho-user-29"],
  "effectiveFrom": "2026-11-18T00:00:00+05:30",
  "revision": 4
}
```

The toggle changes only the completion-stage owner; it never bypasses the HO final stage. When disabled, the Corridor Manager's completion action routes directly to HO without a separate Corridor review. This proposal does not decide whether a listed HO approver acts alone or whether all/a quorum/sequence is required, which level may edit settings, whether changes apply to in-flight records, or whether the same person can complete and later approve.

## Offline sync and conflict rules

1. The client stores a durable local operation ID, base revision, operation order, and payload; it retries with the same idempotency key.
2. The service deduplicates replayed operations. Reusing a key with a different body returns `409 IDEMPOTENCY_KEY_REUSED`.
3. A sync batch returns an outcome for every item. One invalid item must not make unrelated results ambiguous; whether batches are atomic is an open contract decision.
4. Source conflict policy is server-wins for master data and client-wins for a draft. Do not extend client-wins to submitted/approved records; require return/amendment/reopen policy and audit.
5. Keep device capture time, occurrence time, server receipt time, and notice printed issue time separate for audit and SLA calculations.

## Error contract proposal

```json
{
  "error": {
    "code": "VALIDATION_FAILED",
    "message": "The incident cannot be submitted yet.",
    "correlationId": "opaque-support-id",
    "fieldErrors": [
      {"field": "evidence.accidentPhotos", "code": "MINIMUM_NOT_MET"}
    ]
  }
}
```

| HTTP status | Proposed use |
|---|---|
| `400` | Malformed request or unsupported enum |
| `401` | No valid authenticated principal |
| `403` | Principal lacks assignment, scope, or action |
| `404` | Unknown or intentionally undisclosed resource |
| `409` | Stale revision, invalid state transition, duplicate key with different body |
| `413` | Evidence exceeds approved upload limit |
| `422` | Business validation failed; include field-level errors |
| `429` | Rate limit, if one is selected |
| `5xx` | Unexpected service failure; return correlation ID, never a success-shaped fallback |

Do not expose another corridor's identifiers or validation data in errors. Error shape, retry policy, and rate limits must be approved.

## Authorization matrix for the API

This is the source baseline, not a complete policy:

| Actor | Accident | B/C/E | Special |
|---|---|---|---|
| RPO | Create own-project draft, submit; no view after submit | Capture/submit according to assigned scope | Android only in baseline |
| CRO | Complete assigned descriptive report | Complete assigned report | PWA |
| Corridor | Source review; completes CRO stage when toggle is off and then routes directly to HO | Pre-HO review when applicable; record notice stages | PWA; no implied separate review after its completion fallback |
| Project Manager | Source read-only accident observer | Existing source approvals before HO where retained | Authorizes permits; exact pre-HO route and A2 authority remain open |
| HO | Final approval for every workflow through configured roster; may reopen closed cases | Same final-approver rule | Roster selection and route before HO are open |
| Admin | Configure users/masters/workflows/templates; may reopen | Same | Not an HO final approver by implication |
| Maintenance/Safety | Work-zone checks only as assigned | Not a general approval role | PWA baseline |

All roles remain subject to organization scope, record state, and field ownership. PIU, PD, and SP are not API users.
