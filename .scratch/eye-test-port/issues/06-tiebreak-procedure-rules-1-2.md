# 06: Tiebreak procedure with head-to-head sweep and conference record

**What to build:** The tiebreak procedure as a procedure, not a sort: each rule is a pure function over a tied group that returns one top team or falls through; the procedure places one team and restarts the rest from rule 1. Ships with rules 1 and 2 (head-to-head sweep, conference record, which scores every team 0 for any group containing an independent).

**Blocked by:** 04

**Status:** ready-for-human

- [ ] Each rule has its own unit tests
- [ ] Procedure restarts at rule 1 after each placement
- [ ] Rule 2 scores every team 0 when the group contains an independent, so it falls through
- [ ] Golden fixtures for rules 1 and 2 pass with and without k
- [ ] Head-to-head sweep: a team scores +1 only if it beat every other team in the group, -1 if it lost to every one, otherwise 0, including a group that is not a full round robin (ported as-is)
- [ ] A value that cannot be computed is 0, not none, so no rule is skipped (a deliberate change from Python)
