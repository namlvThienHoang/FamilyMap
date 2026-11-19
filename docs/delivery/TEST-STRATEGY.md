# Test Strategy

## Levels
- Domain unit: `Persons`, relation invariants, anti-cycle, aliases, kinship path, ambiguous / unknown cases.
- API integration on isolated, disposable PostgreSQL 18.x: auth, authorization, FK integrity, transaction rollback, search, media access and response shape. Use synthetic data and test credentials.
- Frontend component: add person, duplicate warning, missing-data states, keyboard navigation.
- E2E smoke: login → create root → add parent → add sibling or relation claim → search → view graph → preferred label.
- Backup drill: export sample dataset, restore isolated target, compare counts/relations/photo hashes.

## Minimum synthetic fixture
Root, parents (1 unknown), grandparents, two parental siblings, two first cousins, spouse, adoptive relation, duplicate name, nickname-only person, disconnected person, provisional sibling claim, conflicting edge, unknown ages.

## Must-not-happen assertions
- Unauthorized access to person profile/photo/export.
- Confirmed kinship from an unverified claim.
- Child-parent ancestry cycle.
- Exposure of actual addresses, portraits, passwords or backup files in logs/fixtures/repo.
- Deleting a person silently disconnects unrelated nodes.
- A partial failure leaves half-created relation pairs.

## PostgreSQL verification by milestone

| Milestone | Planned checks |
|---|---|
| M1 | Record the resolved SDK, EF Core, Npgsql and tooling versions. Restore dependencies, build and run a representative query against isolated PostgreSQL 18.x. Under migration approval, apply the reviewed initial migration to an empty database and repeat on a fresh database. |
| M2 | Search display names, full names and aliases with accents, without accents and by partial name. Include `Đỗ/do`, `Hùng/hung`, `van hung` against `Nguyễn Văn Hùng`, Unicode composed and decomposed forms, duplicate names and a renamed alias. Verify original spelling, stable pagination and literal wildcard handling if matching uses `LIKE`. Measure search at all three benchmark sizes. |
| M3 | Use separate connections and synchronized transactions to test opposing edges and two concurrent additions that would jointly close a longer cycle. Cover cycles beyond the graph view depth, edge status changes, lock release on rollback and retry. Assert that incompatible writes cannot both commit and failures leave no partial change. |
| M4 | Verify traversal in both directions, distinct valid paths to a shared ancestor, incomplete branches and claims excluded from verified paths. Measure scoped graph queries and kinship lookups at all three benchmark sizes. |
| M5 | Restore a synthetic export into isolated PostgreSQL and media storage. Check the root reference, FKs, acyclicity, claims, preferred labels and media hashes. Repeat representative search and kinship queries on the restored data. |

### Benchmark plan for M2 and M4

- **50 persons:** use a fixture with expected paths and name matches that can be checked by hand.
- **500 persons:** include multiple branches, shared ancestors, repeated names and aliases. Measure typical directory and graph requests.
- **5,000 persons:** include wide and deep branches plus disconnected profiles. Check bounded graph responses and inspect query plans for representative search and traversal queries.
- Record the seed, graph shape, person and edge counts, database version, CPU/RAM, cache conditions, repetitions, p50/p95 duration and query plans. Agree on performance targets before comparing measurements. Evaluate `pg_trgm` only if search misses the agreed target and the query evidence justifies it.

These benchmarks are planned for implementation under milestone approval. Measurements are pending. The database decision was approved at M0.

## Test evidence
For each approved task: command, scope, environment, actual outcome, failures/skips, expected behavior. Tests do not replace human verification.
