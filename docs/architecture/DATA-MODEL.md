# Data Model v0.1 — Proposed Relational Graph

The candidate schema targets PostgreSQL 18.x as approved in [ADR-001](adr/ADR-001-relational-graph.md). Use `uuid` for `Guid` identifiers and `timestamptz` for system timestamps written as UTC. Date-only values use `date` where a complete date is known.

## Entity inventory (candidate schema)
### Persons
`Id: uuid PK`, `DisplayName: varchar(255) required`, `FullName: varchar(255)?`, `Gender: enum?`, `BirthDate: date?`, `BirthDatePrecision: enum?`, `LivingStatus: enum?`, `Hometown: varchar(255)?`, `CreatedAtUtc`, `UpdatedAtUtc`, `ArchivedAtUtc?`. Store separate normalized search keys for the display name and the full name when provided.

### PersonAliases
`Id`, `PersonId FK`, `Alias`, `AliasType` (nickname/family-call/other). Allow multiple aliases and store a normalized search key for each alias.

### ParentChildRelations
`Id`, `ParentPersonId FK`, `ChildPersonId FK`, `Type` (biological/adoptive/step/guardian), `VerificationStatus`, `EvidenceNote?`, timestamps. Unique parent-child-type; parent != child; transaction-safe ancestral cycle prevention **for ancestry-relevant types**. Step/guardian edges must not automatically imply biological ancestry.

### Unions / UnionMembers
`Unions(Id, UnionType, StartDate?, EndDate?, Status?, Notes?)`; `UnionMembers(UnionId FK, PersonId FK)`. For pair union types, enforce max two participants and no self-union. Multiple historical unions allowed.

### RelationClaims
`Id`, `SubjectPersonId`, `ObjectPersonId`, `RelationKind`, `VerificationStatus`, `Note?`, timestamps. Provisional statements such as 'sibling of', 'might be uncle' never treated as verified ancestry.

### PreferredAddressLabels
`Id`, `ReferencePersonId`, `TargetPersonId`, `AddressLabel`, `Notes?`. Unique ordered pair, allows user's preferred term without rewriting graph.

### PersonMedia / PersonalNotes
`PersonMedia(Id, PersonId, StorageKey, MimeType, SizeBytes, IsPrimary, CreatedAtUtc)`; `PersonalNotes(Id, PersonId, Content, LastMetDate?, CreatedAtUtc, UpdatedAtUtc)`.

### Users / AppSettings / AuditLogs
A single private authenticated account. `AppSettings.RootPersonId` references `Persons.Id`. AuditLogs capture action + IDs, not raw profile PII or tokens. Authentication provider choice recorded in ADR before implementation.

## Relationship semantics
- `Persons` are people, not necessarily registered users.
- Each person may have 0–N verified parent links, with business constraints on parent role/type as designed; do **not** force exactly two.
- `RelationClaims` are independent of confirmed `ParentChildRelations` and never promoted automatically.
- View labels `bên nội/bên ngoại` are derived when identifiable; can be unknown/ambiguous.
- Empty fields mean unknown, not zero or intentionally absent. Date precision avoids fabricated dates.

## Integrity and consistency
- Foreign keys for every person-to-person edge, unique constraints by edge meaning.
- Enforce uniqueness on `(ParentPersonId, ChildPersonId, Type)` and disallow `ParentPersonId = ChildPersonId`. The unique index supports traversal from the parent end. Add an index on `(ChildPersonId, ParentPersonId)` for traversal from the child end.
- The proposed write protocol uses one shared transaction-scoped advisory lock for mutations of the effective ancestry graph. Under `READ COMMITTED`, acquire the lock, check reachability in a separate statement, then write and commit in the same transaction. For a candidate `parent → child` edge, reject it if the child can already reach the parent through eligible ancestry edges. A check that times out or cannot complete aborts the write. Verify this protocol in M3 with separate PostgreSQL connections.
- Merge/archive requires impact preview and consistent rewiring of all related tables.
- Deleting root person disallowed until root reference has been deliberately reassigned.

## P0 name and alias search

- Preserve the original spelling for display. Normalize stored search keys and queries consistently for case, Vietnamese accents, `Đ/đ` and equivalent Unicode forms. Update the key in the same write as its name or alias.
- Proposed initial matching rule: match a literal substring of the normalized name or alias. For example, `hung` and `van hung` can both find `Nguyễn Văn Hùng`. Escape wildcard characters if using `LIKE`. Multiple profiles may share a key.
- Evaluate a B-tree for exact matches and check the plan for partial matches on synthetic data. Arbitrary substring matches can require a scan. Consider `pg_trgm` only if the M2 measurements show that the simpler implementation misses the agreed search target.

## ERD task gate
Before creating EF migrations, Codex must produce concrete diagram/DDL candidate, FK rules, indexes, deletion strategy and sample fixtures, then receive explicit approval.
