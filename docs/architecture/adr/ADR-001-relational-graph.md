# ADR-001 — Relational Family Graph in PostgreSQL

- Status: **Accepted — database engine and relational graph**
- Date: 2026-11-19
- Owner approval: `APPROVE IMPLEMENT: M0-T02-DB`

## Context
FamilyMap needs multi-parent, multi-union, provisional edges, graph traversal, user-defined forms of address; deployment is one-person MVP.

## Decision

- Target PostgreSQL 18.x with EF Core 10 and Npgsql. Use PostgreSQL locally through Docker Compose for development.
- Persist `Persons`, `ParentChildRelations`, `Unions`/`UnionMembers` and `RelationClaims` in relational tables. Keep provisional claims separate from verified ancestry. Use this database for the graph throughout the MVP.
- Keep recursive SQL and PostgreSQL locking in Infrastructure. Application services interpret eligible verified paths for kinship and handle uncertainty.
- Support P0 search by name and alias, with or without accents, including partial names. Begin with normalized search keys. Revisit `pg_trgm` only if measurements justify it.
- The proposed write strategy is a transaction-scoped advisory lock. Integration tests must verify that it protects every mutation that changes the effective ancestry graph.

## Rationale and consequences

SQL Server 2025 was considered. PostgreSQL offers recursive CTEs, transaction-scoped advisory locks and options for accelerating partial name search if the workload warrants it. Both engines can enforce relational constraints and support the expected workload. PostgreSQL-specific SQL and migrations create a portability cost, so keep them within Infrastructure. The first approved migration will target an empty PostgreSQL database.

## Open verification gates

| Milestone | Evidence to collect |
|---|---|
| M1 | Resolve SDK and provider dependencies, build the backend, and connect to an isolated PostgreSQL 18.x instance. Review the ERD and initial migration before applying it under an approved task. |
| M2 | Verify normalized exact and partial lookup for names and aliases, and measure directory search on 50, 500 and 5,000 synthetic persons. |
| M3 | Verify FK and uniqueness constraints, rollback and cycle detection with concurrent requests on separate connections. Confirm lock and retry behavior and reject cycles beyond the depth used for graph views. |
| M4 | Measure traversal and inspect query plans on 50, 500 and 5,000 synthetic persons. Verify that multiple valid paths retain their evidence and graph responses stay bounded. |
| M5 | Restore a synthetic export into isolated PostgreSQL and media storage, then verify integrity and behavior. |

The owner approved collecting benchmark evidence during implementation. All measurements are pending. The production hosting provider and backup policy remain open for separate owner approval. See [Test Strategy](../../delivery/TEST-STRATEGY.md).
