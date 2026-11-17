# Incident Management System documentation

These are foundational planning documents for the Incident Management System (IMS). They describe a proposed design and do not imply that an application, API, identity provider, or deployment stack has been implemented or approved.

## Source of truth

- [IMS Scope Document v1.5](../IMS-Scope-Document.md) is the canonical source scope. Keep it in the repository root; these documents link to it rather than copying it.
- The source is marked **Draft for approval**. Where it contains recommendations or open questions, this documentation preserves that status instead of treating a recommendation as a decision.
- The source files and gaps are summarized in [Scope and traceability](scope-and-traceability.md).

## Foundation documents

| Document | Purpose | Status |
|---|---|---|
| [Architecture](architecture.md) | Proposed logical components and boundaries | Conceptual proposal |
| [Scope and traceability](scope-and-traceability.md) | Map source requirements, exclusions, risks, discrepancies, and open decisions | Source-derived |
| [API contract](api-contract.md) | Illustrative resource and operation contract for a future API | Conceptual; not an implementation contract |
| [Workflows](workflows.md) | Describe the incident, permit, and encroachment lifecycles | Source-derived; unresolved cases called out |
| [Users, roles, and access](roles-and-access.md) | Propose dynamic user/role assignments and initial seed roles | User direction plus proposal; permission details need approval |

## Status labels used here

- **Source requirement**: stated as an in-scope or cross-cutting requirement in the scope document; still subject to the source document's draft approval status.
- **User direction**: requested for this documentation effort, but not yet translated into an approved implementation design.
- **Proposal**: a suggested structure or contract to review; not a selected technology or final decision.
- **Open**: explicitly unresolved in the source or requiring confirmation.
- **Out of scope**: excluded or deferred by the source scope.
