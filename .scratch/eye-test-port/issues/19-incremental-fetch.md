# 19: Incremental fetch policy

**What to build:** Port the Python tool's fetch policy: never refetch completed games, refetch weeks with an incomplete game, always refetch polls, full fetch only when forced or the season changes. Default season is the previous year before August 1. Fetches weeks up to the latest started regular-season week.

**Blocked by:** 18

**Status:** ready-for-human

- [ ] Call counts per scenario are tested
- [ ] Stored games act as the cache; no separate raw-response cache
- [ ] Teams and conferences are fetched only on a full fetch
- [ ] A full fetch happens when forced, when the stored season differs, or when nothing is stored
- [ ] A week is refetched when it was never fetched or holds a game that is not completed
- [ ] Each fetched week costs two calls: games and per-game touchdown stats
