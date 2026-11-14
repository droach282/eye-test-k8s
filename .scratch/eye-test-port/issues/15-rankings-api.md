# 15: Rankings API

**What to build:** Read-only endpoints to list seasons with stored data, fetch the latest snapshot for a season, week and k with the k values stored for that week, and fetch one snapshot with all entries. Scores return as exact fraction plus display decimal. The OpenAPI document is generated and diffed against the committed copy in CI.

**Blocked by:** 14

**Status:** ready-for-human

- [ ] Endpoints return derived data only; no write path
- [ ] Input validated on every endpoint
- [ ] CI fails when the generated OpenAPI differs from the committed copy
- [ ] Integration tests run against a real PostgreSQL
- [ ] Week omitted means the latest stored week; k omitted means 1
