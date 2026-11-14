# 10: Tiebreak rules: poll and alphabetical, with poll fallback

**What to build:** Rules 11 and 12: the compared poll's ranks as a tiebreaker, and alphabetical by abbreviation as the final rule that always separates. The engine accepts the compared poll's ranks as input, absent when no poll applies or polls are off; the poll fallback scenario is covered.

**Blocked by:** 06

**Status:** ready-for-human

- [ ] Unranked teams and absent poll behave as the spec decides
- [ ] The procedure never exhausts its rules
- [ ] Golden fixtures for the poll rule and the fallback pass with and without k
- [ ] Ranked teams score 26 minus their rank; unranked teams, and every team when polls are off, score 0 (ported as-is)
