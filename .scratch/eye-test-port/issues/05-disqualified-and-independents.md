# 05: Disqualified teams and independents

**What to build:** A team that lost to a non-FBS opponent is excluded from the final ranking with its non-FBS loss reported, while its games still count when scoring others. An independent team ranks normally. Fixtures cover FCS-loss disqualification and an independent, each with and without k.

**Blocked by:** 04

**Status:** ready-for-human

- [ ] Disqualified team has no rank and its non-FBS loss is reported
- [ ] A disqualified team is removed before ranking: never in a tied group, never seeded, but counted everywhere else as an opponent and in the rule 6 and 7 pools (a deliberate change from the Python, which removes it after tiebreaking)
- [ ] FCS games count in neither wins nor losses
- [ ] Golden fixtures pass with and without k
- [ ] A team with several non-FBS losses is reported with its worst margin
