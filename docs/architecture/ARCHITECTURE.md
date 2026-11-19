# Architecture — Proposed

## Context
Một người dùng, một website riêng tư responsive, một API. Tránh phân tán hệ thống không cần thiết.

## Logical components
- `FamilyMap.Web`: Next.js App Router / TypeScript; React Query; shadcn/ui + Tailwind. UI copy tiếng Việt.
- `FamilyMap.Api`: ASP.NET Core Web API trên .NET 10 LTS; auth/session, REST endpoints, upload proxy, validation.
- `FamilyMap.Application`: use cases/query handlers, DTOs, validation, application services.
- `FamilyMap.Domain`: Person, ParentChild, Union, RelationClaim, AddressLabel; resolver interfaces + domain invariants.
- `FamilyMap.Infrastructure`: EF Core 10 with Npgsql for PostgreSQL 18.x, private object storage, backup, logging, time/date helpers.

## Architectural decisions
- Modular monolith, not microservices.
- Use relational tables for the MVP graph. Keep PostgreSQL-specific SQL for traversal, search and transactional locking in Infrastructure. Application and Domain own the relationship rules and kinship interpretation.
- Use one Infrastructure write path for changes to the effective ancestry graph. The proposed transaction-scoped advisory lock and cycle check require M3 integration verification. See [ADR-001](adr/ADR-001-relational-graph.md).
- **Do not** expose entity objects directly as public API responses; DTOs with explicit permitted fields.
- Avoid global state and sensitive profile data in shared caches or public Next.js static rendering.
- Mutations must use transactions where multiple linked records change.
- Authentication is mandatory for all PII endpoints including media and exports.
- Image URLs must not be publicly permanent unless explicitly approved as a policy change.

## Database environments

Development uses a local PostgreSQL 18.x instance through Docker Compose. Integration tests use an isolated, disposable instance of the same major version and synthetic data. The production hosting provider and backup policy await separate owner approval.

## Proposed repo layout (implementation stage only)
```
apps/web/
src/FamilyMap.Api/
src/FamilyMap.Application/
src/FamilyMap.Domain/
src/FamilyMap.Infrastructure/
tests/FamilyMap.UnitTests/
tests/FamilyMap.IntegrationTests/
tests/FamilyMap.E2E/
```

## Dependency rule
Domain has no infrastructure dependencies. Application depends on Domain. Infrastructure implements application/domain interfaces. API wires DI and auth. Web consumes API contract.

## Out of scope
Redis, RabbitMQ, Elasticsearch, recognition AI, public profile pages, collaborative access in MVP.
