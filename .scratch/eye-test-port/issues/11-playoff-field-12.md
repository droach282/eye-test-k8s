# 11: 12-team playoff field

**What to build:** The engine derives the playoff field from the final ranking for seasons through 2026: Power Four leaders, Notre Dame treated as a Power Four team, the highest-ranked non-Power Four team, at-large teams, seeds 1-4 with byes, and the opening-round matchups. A disqualified team is never in it. The format comes from the season year, not the config.

**Blocked by:** 05

**Status:** ready-for-human

- [ ] Qualifying rule is recorded per team
- [ ] Disqualified teams never appear
- [ ] Golden fixtures match their hand-derived expected results
- [ ] Field is listed in rank order and seeds follow rank
- [ ] Each Power Four conference's highest-ranked team qualifies, even below the cut
- [ ] Notre Dame inside the top 12 qualifies by its own rule and then never takes the highest non-Power Four place
- [ ] Remaining places go to the next-highest-ranked teams
- [ ] A short ranking gives a short field and no bracket
- [ ] Opening round: 5v12, 6v11, 7v10, 8v9; seed 1 meets the winner of 8v9, 2 of 7v10, 3 of 6v11, 4 of 5v12
