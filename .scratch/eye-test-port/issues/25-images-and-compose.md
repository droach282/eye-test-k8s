# 25: Images and compose stack

**What to build:** The API and web images and one docker compose up that starts API, web and PostgreSQL and seeds the sample season through the batch profile, with no CFBD key. API runs as non-root on a JRE-only base with container-aware memory; web is unprivileged nginx with single-page-app fallback.

**Blocked by:** 21

**Status:** ready-for-human

- [ ] Images tagged with commit SHA; latest never deployed
- [ ] No secrets baked into an image
- [ ] API image under 300 MB and web image under 60 MB
