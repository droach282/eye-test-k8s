# 18: CFBD adapter

**What to build:** Ingest module that calls the CollegeFootballData API through a client generated from its OpenAPI spec, behind a thin adapter, and normalizes teams, conferences, regular-season games with touchdowns, and rankings for one poll week requested for the regular season only. Tested against hand-written responses shaped like the API's; no live calls and no real data committed (ADR 0002).

**Blocked by:** 14

**Status:** ready-for-human

- [ ] Adapter sits behind an interface the rest of the app uses
- [ ] Touchdown categories match the Python tool's definition
- [ ] Postseason games are never stored as ranked
- [ ] Rankings are requested with the season type set to regular
- [ ] Poll entries are mapped to teams by team id; an unmatched entry is ignored

## Comments

**Decision (owner): follow Python.** The adapter asks for the requested poll's rankings for the poll week and falls back to the AP Top 25 for that week, returning null if neither has any. The earlier open question, for the record: Python asks CFBD for rankings for one poll week (default: latest started week) with no season type, and falls back to the AP Top 25 for that week if the committee has nothing. CONTEXT.md and the PRD say: latest committee poll published this season, AP until the first release, and ask under both season types. Which does the port follow? Blocks this ticket and 20.

**Decision (owner):** request rankings under both season types, so where CFBD files the final committee poll does not matter. This is the one deliberate difference from Python's call.

**Superseded (owner, after a live check):** request the regular season only. With both season types (or none, as Python sends), week 1 also returns the postseason week 1, the final polls after the bowls, under the same poll names. The final committee poll is regular season week 16 with `isFinal: true` in 2024 and 2025, so nothing is lost.

**Decision (owner):** match poll entries to teams by id, not school name, where possible. Verified against CFBD's OpenAPI spec: `PollRank` has a required `teamId` (int32). No name fallback.
