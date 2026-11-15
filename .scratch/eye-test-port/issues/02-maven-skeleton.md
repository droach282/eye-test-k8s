# 02: Maven multi-module skeleton with CI test run

**What to build:** The four-module Maven layout (engine, ingest, persistence, api) with enforced module boundaries and the engine free of Spring and database dependencies. One passing engine test runs in GitHub Actions. This is the prefactor the engine tickets build on.

**Blocked by:** None (can start immediately)

**Status:** in-progress

- [ ] Build and test pass locally and in CI
- [ ] A dependency from engine to any other module fails the build
- [ ] Java version is pinned
- [ ] Java 25 LTS and Spring Boot 4.x pinned in the build
