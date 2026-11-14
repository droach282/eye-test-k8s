# 04: Rank by win score

**What to build:** The engine returns an ordered ranking of FBS teams by win score, as exact rational numbers, with FBS-only counting, the quality weight k from 0 to 2, and alphabetical order as the placeholder for ties. Proven against the first golden fixtures.

**Blocked by:** 02, 03

**Status:** ready-for-human

- [ ] Golden fixtures match in order and exactly equal scores at k = 0 and k = 1
- [ ] No floating-point arithmetic in scoring (ADR 0001)
- [ ] k outside 0 to 2 is rejected
- [ ] Per-team scoring detail (record, each game's contribution) is returned
- [ ] A team with no FBS games has no win score and sorts last
- [ ] Only completed games with both scores count
