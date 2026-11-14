# 09: Tiebreak rules: net points and net touchdowns

**What to build:** Rules 8 to 10: net points in conference games, net points against all FBS opponents, and net touchdowns (rushing, passing, kick return, punt return and defensive touchdowns, with interception returns not counted again).

**Blocked by:** 06

**Status:** ready-for-human

- [ ] Only FBS-opponent games count
- [ ] Independents score 0 on the conference rule
- [ ] Golden fixtures for each rule pass with and without k
- [ ] Net points in conference games count only same-conference opponents
- [ ] Rule 8 returns null when any tied team has no conference or has played no conference games (a deliberate change from the Python, which scores such a team 0)
