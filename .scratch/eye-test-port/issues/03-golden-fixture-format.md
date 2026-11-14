# 03: Fixture format and first golden fixtures

**What to build:** The synthetic JSON fixture format (teams with abbreviation, FBS flag and conference; games with week, both scores, both touchdown counts and completed flag; compared-poll ranks; season year; config) and a first tiny league whose expected order and exact scores are worked out by hand from the spec, at k = 0 and k = 1. No real game data (ADR 0002). No Python is run to produce expected results: the spec is the oracle.

**Blocked by:** 01

**Status:** ready-for-human

- [ ] Format documented and loadable from the engine module's tests
- [ ] Expected output stores scores as exact fractions
- [ ] First league's expected order and exact fractions are derived by hand from the spec at k = 0 and k = 1, with the working shown in the fixture notes

## Comments

**Decision (owner):** no Python harness or oracle. The Python logic is correct, so expected results are derived by hand from the spec; nothing runs the original to check them.
