# Decision Log

| Date | ID | Decision | Status | Owner |
|---|---|---|---|---|
| 2026-11-19 | ADR-001 | PostgreSQL 18.x for the relational graph. Local development uses Docker Compose. | Accepted for engine and graph model | Owner |
| 2026-11-19 | ADR-002 | .NET 10 LTS, EF Core 10 and Npgsql approved as targets. Exact versions and runtime compatibility await verification. | Proposed | Owner |
| 2026-11-19 | ADR-003 | Single-user private auth/session strategy | Open | Owner |
| 2026-11-19 | ADR-004 | Private media storage provider | Open | Owner |
| 2026-11-19 | ADR-005 | Hosting/backup/restore choices | Open | Owner |
| 2026-11-19 | ADR-006 | Kinship glossary and confidence policy | Proposed | Owner |

## Decision history

- **2026-11-19 · ADR-001 · Accepted** under `APPROVE IMPLEMENT: M0-T02-DB`. PostgreSQL supersedes the SQL Server 2025 proposal. Recursive traversal, transactional coordination and partial-name search options support the relational graph design. Verification moves to the relevant implementation milestones. See [ADR-001](../architecture/adr/ADR-001-relational-graph.md), [ADR-002](../architecture/adr/ADR-002-runtime-target.md), [Architecture](../architecture/ARCHITECTURE.md), [Data Model](../architecture/DATA-MODEL.md), [Test Strategy](TEST-STRATEGY.md), [CI Quality Gates](CI-QUALITY-GATES.md) and [README](../../README.md).
- **2026-11-19 · ADR-005 · Open.** The production hosting provider and backup policy require separate owner approval.

Append accepted/rejected decision entries with date, rationale, affected docs and alternatives. Never silently rewrite a historical accepted decision.
