# 01: Algorithm spec from the Python tool

**What to build:** A short written spec of the ranking, extracted from the Python tool's current main: win score and quality weight, FBS-only counting, disqualification, the twelve tiebreaker rules in order, the compared poll and the playoff field. Each accidental-looking behavior is recorded as ported as-is: how the head-to-head rule treats a group that is not a full round robin, how poll ranks are scored, how a missing win percentage sorts, and which rules skip or return a flat zero. The glossary gains a term for the head-to-head sweep.

**Blocked by:** None (can start immediately)

**Status:** resolved

- [x] Spec covers every rule of the tiebreak procedure, in Python's order, with its skip conditions
- [x] Each accidental-looking behavior has a recorded decision
- [x] CONTEXT.md defines the head-to-head sweep
- [x] Spec is precise enough that expected results for a fixture league can be worked out by hand from it alone, with at least one worked example per tiebreaker rule
- [x] Fixture list for the baseline gate is drafted from the spec
- [x] Spec lists what the engine needs per game: week, both scores, both touchdown counts, completed; and that only completed games with both scores count
- [x] Spec states poll selection: the requested poll's rankings for the poll week, the AP Top 25's for that week as fallback, null if neither or polls are off
- [x] Spec states that a team with several non-FBS losses is reported with its worst margin

## Comments

**Open question (product owner):** Head-to-head sweep. Python scores a group that is not a full round robin as 0 for everyone, so the rule falls through. Intended NFL-style behavior, or an accident to log as a Python issue after parity?

**Open question (product owner):** Poll scoring. Python scores a poll rank as 26 minus the rank, which assumes a Top 25. Port as-is, or record it in the spec as a known limit?

**Answer (owner):** Python behavior is correct in all instances, until the owner says otherwise. The head-to-head sweep (non-round-robin groups fall through) and the 26-minus-rank poll scoring are ported as-is. The spec records Python's behavior as the decision for each quirk; it does not log them as bugs.

**Draft complete (product owner):** The spec is in `docs/ranking-spec.md`, drafted from the Python tool at `eye-test` commit 3cc6e98, with a worked example per rule, the playoff format rules, a quirks table and a draft fixture list. CONTEXT.md now defines the head-to-head sweep. Because the spec is the only oracle, it needs the owner's line-by-line review against the Python before ticket 03 starts. Boxes stay unticked until the owner has reviewed the spec. The first review pass found and fixed: rule 12 cannot fail on duplicate codes; independent detection is a substring test on the short name; the default k; poll selection details; the unmatched-conference playoff case; and missing fixtures.

**Signed off (owner):** spec reviewed and accepted, including the fixture list. Owner changes made during review: rules return a decision or null; rule 8 returns null for a team with no conference or no conference games; disqualified teams are removed before ranking; rankings are requested for the regular season only.
