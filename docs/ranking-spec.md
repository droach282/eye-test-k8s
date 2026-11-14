# Ranking algorithm spec

This is the written specification of the ranking, taken from the Python tool at `eye-test` commit `3cc6e98` (`main`). It is the oracle for the Java engine: expected results in the golden fixtures are worked out by hand from this document, and nothing runs the Python tool to produce or check them. The Python logic is treated as correct, so every quirk below is ported as-is unless the owner decides otherwise (see [Quirks ported as-is](#quirks-ported-as-is)).

Terms are defined in [CONTEXT.md](../CONTEXT.md). All arithmetic on win percentage, win score and `k` is exact rational arithmetic ([ADR 0001](./adr/0001-exact-rational-scoring.md)); "equal" always means equal as fractions.

## 1. Inputs

**Config.** `k` (the quality weight), an exact fraction with `0 <= k <= 2`, given as a fraction or a decimal (for example `1`, `0.5` or `3/2`); the default is `1`, and a value outside the range is rejected. A run also says whether polls are on, and the season year (it selects the playoff format).

**Teams.** For each team: an id, an abbreviation (the code), a school name, a conference, and whether it is FBS. A conference is either a real conference or an *independent* pseudo-conference. A conference is independent if its short name contains the text "independent" anywhere, compared case-insensitively (a substring test on the short name, not the full name). A team's conference is its **real conference** unless that conference is independent, in which case it has none.

**Games.** For each game: a week, home and away team, home and away points, home and away touchdowns, and a completed flag. A game **counts** only if it is completed and both scores are present. Scope is games where at least one team is FBS; games between lower-division teams are not part of the data. A missing touchdown count is treated as 0. Only games up to the run's week are used, and only counting games are used anywhere below.

**Poll ranks.** For each team, its rank in the compared poll, or 0 if it is unranked or there is no poll. The engine takes poll ranks keyed by team id. The Python matches the poll's entries to teams by school name, because it only reads the school and rank from each entry; the port should match by team id when the poll data carries one (ticket 18), and by school name only as a fallback. An entry that matches no team is ignored, and a team with no entry is unranked, so a name mismatch silently drops a rank.

## 2. What each counting game contributes

For one team, a counting game against an opponent contributes to the team's statistics **only if the opponent is FBS**. A game against a non-FBS opponent is not a win, not a loss and not a game: it adds nothing to the record, points or touchdowns. (It still matters for disqualification, section 7.)

For a team and a counting game against an FBS opponent, with `us` and `them` the points scored by each side:

| Statistic | Contribution |
| --- | --- |
| games | +1 |
| wins | +1 if `us > them`, otherwise 0 |
| net points | `us - them` |
| points scored | `us` |
| points allowed | `them` |
| net points in conference games | `us - them`, only if the two teams are in the same **real** conference |
| net touchdowns | touchdowns scored minus touchdowns allowed |

Consequences:

- Losses are `games - wins`. Every game is assumed to have a winner: ties do not occur in college football, and the spec defines no behavior for equal scores.
- Two teams in the same independent pseudo-conference do not share a conference. An independent has no conference games.

A **win** against an opponent means `us > them` in a game against an FBS opponent. This is the only meaning of "win" below.

**Win percentage** is `wins / games` as an exact fraction, or *none* if `games = 0`.

## 3. Win score

For a team with `games > 0`:

```
win score = ( sum over its wins W of [ 1 + k * (win percentage of W's opponent - 1/2) ] ) / games
```

A loss (or tie) contributes 0 to the sum. A team with `games = 0` has no win score (none).

With `k = 0` every win is worth 1 and the win score is the win percentage (the **baseline**). A win over a .500 team is worth 1 at every `k`.

**Worked example.** Team T plays three FBS games: it beats O1 (win percentage 3/4), beats O2 (win percentage 1/2) and loses to O3. T has 2 wins in 3 games.

| k | Value of win over O1 | Value of win over O2 | Sum | Win score |
| --- | --- | --- | --- | --- |
| 0 | 1 | 1 | 2 | 2/3 |
| 1 | 1 + 1/4 = 5/4 | 1 | 9/4 | 9/4 ÷ 3 = 3/4 |
| 2 | 1 + 2/4 = 3/2 | 1 | 5/2 | 5/2 ÷ 3 = 5/6 |

## 4. Initial ordering and tied groups

Only FBS teams are ranked. Each FBS team's win score is computed and the teams are ordered highest first, with a team whose win score is none after all others. (The result does not depend on the order teams are listed in the input.)

Teams are then grouped by **exactly equal win score**. Groups are processed in descending score order, and each group occupies the next block of rank positions:

- A group of one team is placed.
- A group of two or more teams is a **tied group** and is resolved by the tiebreak procedure (section 5). A group of two is handled exactly like a larger one.
- All teams whose win score is none form one tied group, placed last.

A tied group of size *n* yields *n* consecutive positions.

## 5. Tiebreak procedure

Given a tied group, the procedure places one team at a time:

1. Walk the twelve rules below in order, starting at rule 1.
2. A rule produces one value per team in the group. If **any** team's value is none, the rule is **skipped for the whole group**, and the walk moves to the next rule.
3. Otherwise take the highest value. If **exactly one** team has it, that team is placed at the next position and removed from the group. If more than one team has it, the walk moves to the next rule.
4. After a placement, if more than one team remains, start again at rule 1 with the remaining group. Every rule is recomputed over the smaller group. When one team remains, it is placed.

Team codes are unique, so rule 12 always separates a group and the walk always ends with a placement.

**Worked example (restart).** A, B and C are tied. A beat B and C, and B beat C. Rule 1 scores A = +1, B = 0, C = -1; the highest value is A's alone, so A is placed first. The group is now {B, C}. Rule 1 again: B beat C, so B = +1, C = -1, and B is placed. C is the last team and is placed.

## 6. The twelve rules

Each rule returns a value per team; the highest value wins. "Group" is the current tied group.

### Rule 1. Head-to-head sweep

For each team T in the group, look at its games against every other team in the group (FBS games only).

- **+1** if T has played every other team in the group and won every one of those games.
- **-1** if T has played every other team in the group and lost every one of those games.
- **0** otherwise, including when T has not played every other team, so a group that is not a full round robin usually scores 0 for everyone and falls through.

A team that lost to every other team scores -1, but this never decides a placement, because only the highest value is used. Never skipped.

**Example.** Group {A, B, C}: A beat B and C. A = +1 and is placed. Group {A, B, C} where A beat B, B beat C, A and C never played: A = 0 (not played C), B = 0 (lost to A, beat C), C = 0. All tie at 0, so the rule falls through.

### Rule 2. Conference record

Each team's win percentage counting only games against opponents in the team's own real conference (an opponent counts if its conference id equals the team's). Compared directly across conferences.

Skipped for the whole group if any team in it has no real conference (independent), or if any team has no conference games (win percentage none).

**Example.** A (conference X) is 5-1 in conference games, 5/6. B (conference Y) is 6-2, 3/4. 5/6 > 3/4, so A is placed. If B were an independent, the rule is skipped.

### Rule 3. Common games

An **opponent in common** is an FBS opponent that every team in the group has played. If the group has fewer than **4** opponents in common, the rule is skipped. Otherwise each team's value is its win percentage in all its games against those opponents.

**Example.** A and B share opponents P, Q, R, S. A beat P, Q, R and lost to S: 3/4. B beat P, Q and lost to R, S: 2/4. A is placed. With only three opponents in common the rule is skipped.

### Rule 4. Strength of victories

For each team, take the FBS opponents it beat and divide the opponents' total wins by their total games: combined wins over combined games, not an average of percentages. The value is none, and so the rule is skipped for the whole group, if the team has no beaten FBS opponents with games (a team with no wins, for instance).

**Example.** A beat opponents with a combined 20 wins in 30 games: 2/3. B beat opponents with a combined 18 wins in 30 games: 3/5. A is placed.

### Rule 5. Strength of schedule

For each team, take every FBS opponent it played and divide their total wins by their total games. Opponents that are not FBS contribute nothing. None, and so skipped for the whole group, if there are no such games.

**Example.** A's opponents combine for 61 wins in 120 games, B's for 59 in 120. 61/120 > 59/120, so A is placed.

### Rule 6. Combined ranking among conference teams

Each team is ranked among all the teams in its own conference (all teams with the same conference id), twice:

- in points scored, highest first;
- in points allowed, lowest first.

Equal values share a rank and the next rank is skipped: values 30, 25, 25, 20 rank 1, 2, 2, 4. The team's value is minus the sum of its two ranks, so the lower sum wins. This compares across conferences because each team is measured within its own.

If any team in the group has no real conference, every team scores 0, so the rule ties and falls through. It is never skipped by the none-rule.

**Example.** A is 3rd in points scored and 5th in points allowed in its conference: sum 8, value -8. B is 2nd and 7th in its: sum 9, value -9. A is placed.

### Rule 7. Combined ranking among all FBS teams

The same, with every FBS team as the pool.

**Example.** A is 10th scored and 40th allowed among all FBS: sum 50. B is 20th and 25th: sum 45. B is placed.

### Rule 8. Net points in conference games

Each team's net points in games against same-conference FBS opponents (section 2). An independent has none and scores 0; the rule is not skipped for a group containing one.

**Example.** A is +45, B +30. A is placed.

### Rule 9. Net points against all opponents

Each team's net points in all FBS games.

**Example.** A is +120, B +95. A is placed.

### Rule 10. Net touchdowns against all opponents

Each team's touchdowns scored minus touchdowns allowed in FBS games. A team's touchdowns in a game are its rushing, passing, kick return, punt return and defensive touchdowns; interception return touchdowns are a subset of defensive touchdowns and are not added again. A missing count is 0.

**Example.** A is +14, B +9. A is placed.

### Rule 11. Poll ranking

If polls are off, every team scores 0 and the rule falls through. Otherwise a team ranked `r` in the compared poll scores `26 - r`, and an unranked team scores 0.

**Example.** A is ranked 12 (14), B ranked 20 (6), C unranked (0). A is placed.

### Rule 12. Alphabetical

Teams are sorted by code, ascending, by plain string comparison (so uppercase before lowercase). The first scores highest: for *n* teams the values are *n*, *n - 1*, … 1. Always separates every team, since codes are unique.

**Example.** Codes `BAMA` and `AUB`: `AUB` sorts first and is placed.

## 7. Disqualification

An FBS team with a loss (`us < them`) to a non-FBS opponent is **disqualified**. If it has several, the one with the worst margin (the most negative net points) is the one reported.

Disqualification happens **after** all ranking is done. A disqualified team is ranked and tiebroken like any other (so it can win a tiebreak over a team that is not disqualified), and its games count in every other team's win score, strength and rules. It is then removed from the ordered list, so every other team keeps its relative order. It has no rank in the final ranking and is listed separately with its non-FBS loss.

## 8. Compared poll

A run asks for a **requested poll**, by default the Playoff Committee Rankings, for a **poll week** (by default the snapshot's week). If the requested poll has rankings for that week it is used; otherwise the AP Top 25's rankings for that week are used; otherwise there is no poll and every team is unranked. When polls are off, no poll is requested. If the response lists the same poll name more than once, the first entry listed is used, and a poll with an empty list of ranks counts as having no rankings. The Python requests rankings with no season type; requesting them under both season types is a deliberate change by the owner (ticket 18). The poll used, its name and its week are recorded with the result. See [CONTEXT.md](../CONTEXT.md) for **Compared poll** and **Poll week**.

## 9. Playoff field

The field is built from the final ranking (disqualified teams already removed), best team first. The format comes from the season year: **12 teams** through 2026, **24 teams** from 2027.

A conference's code identifies its group. The **Power Four** are `ACC`, `B12`, `B1G`, `SEC`. The **Group of Six** are `AAC`, `CUSA`, `MAC`, `MWC`, `PAC`, `SBC`. Notre Dame is identified by the school name "Notre Dame".

Qualification, walking the ranking in order:

1. **Power Four leader.** The first team seen from each Power Four conference qualifies.
2. **24-team only, Group of Six leader.** The first team from any Group of Six conference qualifies.
3. **12-team only, Notre Dame.** If Notre Dame is ranked in the top 12, it qualifies by its own rule.
4. **12-team only, highest non-Power Four.** The first team not in a Power Four conference qualifies, skipping Notre Dame if it qualified by its own rule. It may be a Group of Six team, an independent, or Notre Dame when Notre Dame is ranked below 12.
5. **At-large.** Teams in rank order that have not qualified are added until the field has the format's size.

A team that qualifies by more than one rule is listed once, under the first rule that matched. The field is listed in rank order, and **seeds are positions in that list**, 1 to the field's size. A team outside the top of the ranking that qualified by a rule therefore takes the seed after the qualifiers ranked above it, which can be a lower number than its rank.

If the ranking has fewer teams than the field size, the field is short and there is no bracket.

**Bracket** (full field only). Let *size* and *byes* be 12 and 4, or 24 and 8.

- Seeds 1 to *byes* skip the first round.
- **First round:** seed *h* plays seed *size + byes + 1 - h*, for *h* from *byes + 1* up to *(size + byes) / 2*.
- **Second round** (the "quarterfinals" in the 12-team format, the "second round" in the 24-team format): seed *t*, for *t* from 1 to *byes*, plays the winner of the first-round game involving seed *2 × byes + 1 - t*.

| Format | First round | Second round |
| --- | --- | --- |
| 12 teams, 4 byes | 5v12, 6v11, 7v10, 8v9 | 1 v winner 8/9, 2 v winner 7/10, 3 v winner 6/11, 4 v winner 5/12 |
| 24 teams, 8 byes | 9v24, 10v23, 11v22, 12v21, 13v20, 14v19, 15v18, 16v17 | 1 v winner 16/17, 2 v winner 15/18, 3 v winner 14/19, 4 v winner 13/20, 5 v winner 12/21, 6 v winner 11/22, 7 v winner 10/23, 8 v winner 9/24 |

**Example (12-team).** The ranking is team 1 to team 20. Power Four leaders: the top team of each of the four conferences are ranks 1, 2, 4 and 9. Notre Dame is ranked 5 and qualifies by its own rule. The highest non-Power Four team is rank 3 (a Group of Six team) and qualifies. That is six teams. At-large teams are then added in rank order: the ranks not already in are 6, 7, 8, 10, 11, 12. The field has twelve teams: ranks 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, seeded 1 to 12. If the fourth Power Four leader had been ranked 15, it still qualifies, and it takes the 12th seed in place of the rank-12 team, who is left out.

## Quirks ported as-is

These look accidental or surprising; each is recorded as a decision to port the Python behavior.

| # | Behaviour | Where |
| --- | --- | --- |
| 1 | A rule that returns none for any one team is skipped for the whole group | Section 5 |
| 2 | A team with no wins has no strength of victories, which skips rule 4 for any group containing it | Rule 4 |
| 3 | Head-to-head needs a sweep of every other group member; a group that is not a full round robin scores 0 and falls through | Rule 1 |
| 4 | A -1 sweep score never decides anything, because only the highest value counts | Rule 1 |
| 5 | Rule 6 scores 0 for everyone when any team has no real conference; rule 8 scores independents 0 and still runs | Rules 6, 8 |
| 6 | Teams with no FBS games have no win score, sort after all others and form one tied group | Section 4 |
| 7 | Disqualified teams are ranked and tiebroken normally, then removed | Section 7 |
| 8 | Seeds are positions among qualifiers, not ranks | Section 9 |
| 9 | Rule 12 compares codes as plain strings, case-sensitively | Rule 12 |
| 10 | When a poll response lists a poll name twice the first entry wins, and an empty list of ranks counts as no rankings | Section 8 |

## Draft fixture list for the baseline gate

Each league is synthetic (ADR 0002) and runs at `k = 0` and at `k = 1`, with the expected order and exact scores worked out by hand from this spec. Leagues are kept small.

| # | League | What it covers |
| --- | --- | --- |
| F01 | Tiny league, no tied groups | Win score, FBS-only counting, ordering at both `k` |
| F02 | League where `k` changes the order | Quality weight |
| F03 | A team with no FBS games | None score, last tied group |
| F04 | A team that lost to an FCS team | Disqualification, others' order unchanged, worst margin |
| F05 | A disqualified team that wins a tiebreak | Section 7 |
| F06 | An independent team in a tied group | Rule 2 skipped, rule 6 flat, rule 8 scores 0 |
| F07 | Head-to-head sweep decides | Rule 1 |
| F08 | Head-to-head, not a round robin | Rule 1 falls through |
| F09 | Three-team group with restart | Section 5 |
| F10 | Conference record decides | Rule 2 |
| F11 | Common games decide (4 or more) | Rule 3 |
| F12 | Fewer than 4 common games | Rule 3 skipped |
| F13 | Strength of victories decides; a team with no wins | Rule 4 and its skip |
| F14 | Strength of schedule decides | Rule 5 |
| F15 | Combined ranking in conference decides | Rule 6, shared ranks |
| F16 | Combined ranking among FBS decides | Rule 7 |
| F17 | Net points in conference decide | Rule 8 |
| F18 | Net points decide | Rule 9 |
| F19 | Net touchdowns decide | Rule 10 |
| F20 | Poll decides; polls off; no poll | Rule 11 and the fallback |
| F21 | Alphabetical decides | Rule 12 |
| F22 | 12-team field with Notre Dame in, then out | Section 9 |
| F23 | 12-team field with a Power Four leader outside the cut | Seeds as positions |
| F24 | 24-team field with a Group of Six leader | Section 9 |
| F25 | Short field | No bracket |
| F26 | A game not completed, and a game with no score | Skipped games |
| F27 | A missing touchdown count | Counted as 0 in rule 10 |
| F28 | Notre Dame ranked exactly 12, then 13 | Boundary of its own rule |
| F29 | A conference whose short name contains "independent" | Independent detection |
| F30 | `k` = 2 and a non-integer `k`; `k` below 0 or above 2 rejected | Config |
