# ADR-002 — Backend Runtime Target

- Status: **Proposed — runtime verification pending**
- Date: 2026-11-19

## Context
FamilyMap is a new project with planned long-term personal usage. Prefer a supported LTS and avoid new code on end-of-support runtimes.

## Proposed decision
The owner approved **ASP.NET Core on .NET 10 LTS, EF Core 10 and Npgsql** for PostgreSQL 18.x under [ADR-001](ADR-001-relational-graph.md). Development uses a local PostgreSQL instance through Docker Compose. Pin the exact .NET SDK patch in `global.json` and align the EF Core, design tooling and provider versions under the applicable M1 implementation approval.

## Evidence
- Microsoft lists .NET 10 as LTS. [Microsoft .NET support](https://learn.microsoft.com/en-us/dotnet/core/releases-and-support)
- Published metadata for `Npgsql.EntityFrameworkCore.PostgreSQL` 10.0.3 targets `net10.0` and declares EF Core and EF Core Relational `>= 10.0.4, < 11.0.0`, plus `Npgsql >= 10.0.3`. This provides a reference for selecting compatible patches. [NuGet package metadata](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL/10.0.3#dependencies-body-tab)

## Conditions for acceptance

- In M1, confirm the SDK, resolved package graph and `dotnet-ef` tooling. Record a successful restore, backend build, database connection and representative query on isolated PostgreSQL 18.x after execution approval.
- Verify runtime support for the proposed hosting environment and record the result when that environment is selected.
- Record any deviation as an ADR amendment and obtain owner approval for the full runtime decision once its conditions are met.
