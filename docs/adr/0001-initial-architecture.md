# ADR 0001 — Initial architecture

Date: 2026-11-13 (baseline recorded retroactively)
Status: accepted (describes what is built)

## Context

Gest_Comm is the server side of a two-repo *gestion commerciale* system:
an ADF/BC4J model exposed as REST for a JavaFX desktop client. It must run
on the Oracle stack the team already uses (JDeveloper + WebLogic + MySQL).

## Decisions

1. **Oracle ADF/BC4J, not plain Java EE.** Model as XML metadata
   (entity/view object/app module) exposed by ADF's `ResourceServlet`.
   Rationale: the team's stack; ADF generates the REST layer, so no
   hand-written API code exists to maintain.
2. **JDeveloper workspace, no Maven/Gradle.** `.jpr`/`.jws` projects.
   Rationale: ADF's tooling model; a plain-JDK build isn't possible, which
   is why there is no CI.
3. **MySQL as the datastore.** `gest_comm_db` via the legacy
   `com.mysql.jdbc.Driver` connection. Rationale: existing infrastructure;
   the ADF connection descriptor abstracts it.
4. **Skeleton scope: Article only.** One entity + one view object + one
   app module; the wider domain is unmodelled. Rationale: incremental
   build-out; the structure is proven before breadth.
5. **REST via ResourceServlet at `/rest/*`.** Standard ADF REST exposure —
   resources derive from view objects automatically. Rationale: zero
   custom endpoint code; consistent with ADF conventions.
6. **WebLogic deployment target.** Integrated or standalone WLS with the
   `adf.oracle.domain` shared library. Rationale: ADF runtime requires it.

## Consequences

- **No CI, ever, on GitHub-hosted runners** — proprietary deps. Verification
  is manual (JDeveloper deploy).
- The REST contract is generated, not designed — once the client consumes
  it, ADF's shapes become load-bearing.
- Schema changes mean entity regeneration + manual DDL coordination.
