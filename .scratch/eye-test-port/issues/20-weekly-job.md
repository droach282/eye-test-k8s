# 20: Weekly job logic and backfill

**What to build:** The batch entry point for the weekly job: ingest, then compute a snapshot per k in a configured list (default 0 and 1), only when a game or poll for the week changed; a newly published final committee poll triggers one last snapshot. Also backfills a whole season, one snapshot per week per k.

**Blocked by:** 19

**Status:** ready-for-human

- [ ] Offseason run logs no change and writes nothing
- [ ] Snapshot week is the highest week with a completed game
- [ ] Backfill produces one snapshot per week per k
- [ ] Job runs with migration off
- [ ] Once no regular-season games remain, the season's last committee poll is the final one, and if newer than the stored snapshots' compared poll it triggers one last snapshot

## Comments

**Open question (product owner):** How does the job recognise the final committee poll? Postseason games are excluded, and the final ranking is published after the regular season. Is it the last poll before the playoff starts, or the poll CFBD files under postseason? This decides the trigger for the extra last snapshot.

**Answer (owner):** In 2026 the final committee rankings are revealed on the first Sunday in December. Unsure whether the 24-team format from 2027 keeps that timing. Still open: how the job recognises the final poll without hard-coding a date. Needs a decision before this ticket is ready.

**Answer (owner):** Infer the final committee poll: a committee poll published after the regular season's last week, with no regular-season games remaining, is the final one. No hard-coded date and no chart value, so it holds for 2027 whatever the timing.

Also waiting on the poll-selection question in ticket 18.

**Update (product owner):** Poll selection now follows Python (ticket 18): the job asks for the requested poll for the poll week, which defaults to the snapshot's week, with no season type. The final-poll trigger above assumes the job can see a committee poll filed after the last regular-season week. Python's call would not see one filed under the postseason season type. Where CFBD files the final committee poll (week number and season type) is unverified: community wrappers show `/rankings` taking `week` and `seasonType` (regular, postseason, both), but nothing authoritative says where the committee's final poll lives. Needs checking against the official CFBD reference or a live call with the owner's key before this ticket can be written down.

**Answer (owner):** The final poll is the last committee poll of the year. Request rankings under both season types (ticket 18) and do not worry about where CFBD files it. Once no regular-season games remain, the committee poll with the highest week for the season is the final one; if it is newer than the compared poll of the stored snapshots, the job takes one last snapshot with it.
