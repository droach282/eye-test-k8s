# 13: Parity sign-off

**What to build:** The baseline gate: every synthetic league in the golden set returns the same order and exactly equal scores as the expected results derived from the spec, with and without k, covering every tiebreaker rule, FCS-loss disqualification, an independent team and the poll fallback. A suspected Python bug is raised with the owner.

**Blocked by:** 07, 08, 09, 10, 12

**Status:** ready-for-human

- [ ] 100 percent of golden fixtures match exactly
- [ ] Engine line coverage is at least 90 percent
- [ ] Parity tests run on every change to the engine module
- [ ] A mismatch is checked against the spec; if the spec itself looks wrong, raise it with the owner

## Comments


**Answer (owner):** Python behavior is correct in all instances, until the owner says otherwise. No Python bugs are filed or fixed as part of this ticket. If a mismatch looks like a Python bug, raise it with the owner; do not file it.
