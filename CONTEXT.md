# eye-test

Ranks FBS college football teams using only on-field results, with no human voting, and compares the ranking to the published polls. The purpose is to set a mechanical, codified method beside the College Football Playoff Committee's rankings. This repo is the Spring Boot, Angular and Kubernetes port of the Python eye-test tool; the Python tool is the source of truth for ranking behavior.

## Language

### Ranking

**Win percentage**:
Wins divided by games played against FBS opponents. Games against FCS opponents are not counted, neither as wins nor as losses (an FCS loss is handled separately). What the ranking sorts by when the quality weight is 0, and how an opponent's quality is measured for the win score.
_Avoid_: W/L points, win-loss points

**Win score**:
The ranking's primary measure: what the initial ranking sorts by. Each win over an FBS opponent is worth `1 + k × (opponent's win percentage − 0.5)` and each loss is worth 0; the score is the total win value divided by FBS games. A win over a .500 team is always worth 1. With `k = 0` every win is worth 1 and the score is the win percentage. Compared as exact fractions, never as floating-point values.
_Avoid_: Weighted win percentage, quality points

**Quality weight (`k`)**:
The weight, from 0 to 2 and defaulting to 1, that sets the gap in wins between beating an unbeaten team and beating a winless one. Always set on a run, never absent. Above 2 a win over a winless team would be worth less than nothing. Not the same thing as _strength of victories_.
_Avoid_: Strength-of-victory weight, strength factor, multiplier

**Baseline**:
A ranking computed with the quality weight at 0, so the win score is the plain win percentage.
_Avoid_: Unweighted run, `k` unset

**Resume**:
A team's schedule for a season: each game, its result, the opponent, and what that game contributed to the win score. The product's word for the page that explains why a team ranks where it does. It is not a second scoring step.
_Avoid_: Performance against the resume

**Disqualified team**:
A team that lost to any non-FBS opponent. It is excluded from the final ranking for the rest of the season, so it has no rank in a snapshot, but its games still count as results when scoring other teams. An FBS team is expected to beat any FCS team, which is also why FCS games are not counted in win percentage.
_Avoid_: Ineligible team, banned team

### Tiebreaking

**Tied group**:
The set of teams with equal win score that the tiebreakers are trying to separate. Tiebreakers place one team at a time and restart for the rest, in the manner of the NFL wild-card procedure.
_Avoid_: Logjam, tie bucket

**Head-to-head sweep**:
A tiebreaker: a tied team scores +1 if it played every other team in the tied group and beat all of them, -1 if it played them all and lost to all of them, and 0 otherwise. A group that is not a full round robin therefore usually scores 0 for everyone and falls through. Only the highest score counts, so a -1 never decides a placement.
_Avoid_: Head-to-head record

**Conference record**:
A team's win percentage counting only games against opponents in its own conference. Compared directly across conferences, since each team is measured within its own. Independents have no conference record: for a group containing one, every team scores 0 and the rule falls through. A group containing a team with no conference games yet skips the rule.
_Avoid_: League record

**Common games**:
Games against opponents that every team in the tied group has played. Counts only if the group shares at least 4 such opponents. FCS opponents are never common games.
_Avoid_: Common opponents, shared opponents

**Strength of victories**:
A tiebreaker: the combined win percentage of the FBS teams a team beat, as total wins over total games. Not the quality weight.
_Avoid_: Strength-of-victory weight

**Strength of schedule**:
A tiebreaker: the combined win percentage of all FBS opponents a team played, as total wins over total games.
_Avoid_: Schedule difficulty

**Combined ranking**:
For each tied team, its rank in points scored (FBS opponents only) plus its rank in points allowed; the lower sum wins. Tied values share a rank (1, 2, 2, 4). Computed once among the teams of the tied team's own conference (so it compares across conferences), and once among all FBS teams. For a group containing a team with no conference, every team scores 0 and the rule falls through.
_Avoid_: Composite rank

**Net touchdowns**:
Touchdowns a team scored minus touchdowns it allowed, counting the same games as net points (FBS opponents only). A team's touchdowns in a game are its rushing, passing, kick return, punt return and defensive touchdowns; interception return touchdowns are a subset of defensive touchdowns and are not added again.
_Avoid_: Touchdown differential

### Polls and playoff

**Requested poll**:
The poll a run asks to be compared against. Defaults to the Playoff Committee Rankings.
_Avoid_: Default poll

**Compared poll**:
The published poll a ranking is actually compared against; its ranks feed the poll tiebreaker and the comparison view. The requested poll's rankings for the poll week; the AP Top 25's rankings for that week if the requested poll has none; null when neither has any or polls are turned off.
_Avoid_: Popularity contest, subjective rank

**Poll week**:
The week number a poll is filed under, meaning the week whose games it precedes. A run uses its snapshot's week unless told otherwise, and a snapshot records its compared poll's week.
_Avoid_: Poll round

**Power Four**:
The ACC, Big 12, Big Ten and SEC. Officially named the Autonomous Four (A4); this project keeps calling them the Power Four. Notre Dame is not a member, but in the 12-team playoff field it is treated as one.
_Avoid_: P4 conferences, power conferences, Autonomous Four, A4

**Group of Six**:
The American, Conference USA, MAC, Mountain West, Pac-12 and Sun Belt. Independents are not in it.
_Avoid_: Group of Five, G5, non-Power Four

**Conference leader**:
The highest-ranked team in a conference. As of the 2027 season, conferences will no longer play title games, so the conference leader is assumed to be its champion. This also facilitates mid-season "What if the playoffs were to start today?" brackets. The Group of Six leader is the single highest-ranked team across all six Group of Six conferences.
_Avoid_: Conference champion

**Playoff field**:
The teams that would make the playoff if the season ended now, seeded by rank. A disqualified team is never in it. Its size and rules depend on the season: 12 teams through the 2026 season (with Notre Dame rule and seeds 1-4 getting byes), 24 teams from 2027 (Group of Six leader, seeds 1-8 getting byes).
_Avoid_: Bracket, playoff picture

### The port

**Season**:
All the teams, conferences and games for one year that a run ranks.
_Avoid_: League, dataset

**Week**:
A regular-season week of a season. A snapshot's week is the highest week with a completed game, and it counts every completed regular-season game so far. Postseason games are not part of any week.
_Avoid_: Round

**Snapshot**:
An immutable stored ranking for a season and week, computed with a given quality weight. Every computation creates a new snapshot, and the latest one for a season, week and quality weight is the one shown. History is read from snapshots, never recomputed.
_Avoid_: Ranking run, result set

**Parity**:
The Java engine returning the order and exact scores that the written spec calls for, for every synthetic league in the golden set, with and without a quality weight. Expected results are worked out by hand from the spec; the Python tool is not run to produce or check them. The golden set contains no real game data. The Python logic is treated as correct and ported as-is.
_Avoid_: Compatibility, equivalence
