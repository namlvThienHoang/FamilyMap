# CI Quality Gates — To Implement After M1 Approval

The workflow described here is a specification, **not** a CI system already configured in this starter pack.

## Pull request checks
- .NET restore/build/test for changed backend projects.
- Frontend install lockfile-consistently, lint, typecheck, unit tests, production build.
- Integration tests on isolated, disposable PostgreSQL 18.x with synthetic data and test credentials. Run PostgreSQL-specific migration, search and concurrent-write checks as those features enter scope. See [Test Strategy](TEST-STRATEGY.md).
- Dependency/secrets scanning; verify no real family datasets, media or backups.
- API contract and migration checks where changed.

## Merge gate
- Checks green or owner explicitly records evidence/accepted exceptions.
- No Critical/High review finding open for privacy or kinship data correctness.
- Owner approves merging. Branch protection should enforce review/CI independently of `AGENTS.md` instructions.

## Release gate
- Separate deployment authorization.
- Private staging smoke tests, synthetic data and restore drill.
- Document rollback, monitoring and custody of backup encryption keys.
