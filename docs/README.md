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
| [Glossary](glossary.md) | Define domain terms and acronyms used by the source and foundation docs | Source-derived; expansions not supplied by the source are marked unknown |

## Reader guide

1. Read the root [IMS Scope Document](../IMS-Scope-Document.md) for the business requirements and approval status.
2. Read [Scope and traceability](scope-and-traceability.md) to understand the boundaries, source mapping, required outcomes, and unresolved decisions.
3. Use [Workflows](workflows.md) for day-to-day journeys: who creates, reviews, approves, records evidence, and handles exceptions.
4. Use [Users, roles, and access](roles-and-access.md) and [Architecture](architecture.md) to understand how actors, clients, and system responsibilities fit together.
5. Use [API contract](api-contract.md) only as a proposed technical interface for future implementation; it is not an existing API.

## What this documentation does and does not decide

The root scope is the authority for business requirements, subject to its draft status. These docs add explanations and technical proposals so a reader can follow the intended operation. A proposal is not an approval. Where the source conflicts with itself or asks a question, these docs show the conflict and do not silently select an answer.

No application code exists in this documentation branch. The architecture, API, role configuration, error behavior, and diagrams describe a possible implementation shape only. Frameworks, database, hosting vendor, identity provider, authentication protocol, storage product, and deployment topology remain undecided.

## Quick user journey

An assigned RPO records an incident on an Android device, even when offline. The record and evidence synchronize to the service, which checks the user's scope and creates the next work item. A configurable CRO toggle selects whether CRO or the Corridor Manager completes the CRO stage; it changes no downstream stage. Both paths continue through the workflow's existing review and approval order, including PM before HO where specified, and every in-scope workflow ends at the configured HO final-approver list. A1 keeps PM as a read-only observer. Same-identity eligibility for later review/final approval and how the HO list is applied remain design decisions. Permit checks, notice milestones, audit events, notifications, and report exports are supporting workflows, not separate sources of truth.

This is a navigation-level summary. See [Workflows](workflows.md) for stage-by-stage rules and [Scope and traceability](scope-and-traceability.md) for exceptions and decisions.

## Status labels used here

- **Source requirement**: stated as an in-scope or cross-cutting requirement in the scope document; still subject to the source document's draft approval status.
- **User direction**: requested for this documentation effort, but not yet translated into an approved implementation design.
- **Proposal**: a suggested structure or contract to review; not a selected technology or final decision.
- **Open**: explicitly unresolved in the source or requiring confirmation.
- **Out of scope**: excluded or deferred by the source scope.
