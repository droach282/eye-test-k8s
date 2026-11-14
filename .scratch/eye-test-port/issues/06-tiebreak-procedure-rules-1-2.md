# 06: Tiebreak procedure with head-to-head sweep and conference record

**What to build:** The tiebreak procedure as a procedure, not a sort: each rule is a pure function over a tied group that returns one top team or falls through; the procedure places one team and restarts the rest from rule 1. Ships with rules 1 and 2 (head-to-head sweep, conference record: every team scores 0 for a group containing an independent, and the rule is skipped for a group containing a team with no conference games).

**Blocked by:** 04

**Status:** ready-for-human

- [ ] Each rule has its own unit tests
- [ ] Procedure restarts at rule 1 after each placement
- [ ] Rule 2 scores 0 for every team when the group contains an independent
- [ ] Golden fixtures for rules 1 and 2 pass with and without k
- [ ] Head-to-head sweep: a team scores +1 only if it beat every other team in the group, -1 if it lost to every one, otherwise 0, including a group that is not a full round robin (ported as-is)
- [ ] Rule 2 is skipped for the whole group when any team has no conference games; no other rule can be skipped
