# 14: Persistence and a seeded snapshot

**What to build:** A batch profile that loads the invented sample season, computes a ranking and stores it as an immutable snapshot (entries, per-game resume rows, playoff field, compared poll name and week), on a Flyway-managed PostgreSQL schema. Verifiable by reading the snapshot back.

**Blocked by:** 13

**Status:** ready-for-human

- [ ] Schema migrates on a real PostgreSQL via Testcontainers
- [ ] Scores stored as exact fractions
- [ ] Every compute adds a new snapshot; none is updated
- [ ] Sample season has fictional teams in two conferences plus an independent, an FCS upset and a deep tiebreak
- [ ] Sample reuses the synthetic fixtures where possible and needs no CFBD key

## Comments

**Decision (owner):** no "sample data" label and no sample flag. Fictional teams and the absence of a CFBD key make the sample season obvious; the README says so instead.
