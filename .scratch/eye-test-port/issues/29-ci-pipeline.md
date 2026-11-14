# 29: CI pipeline

**What to build:** A GitHub Actions pipeline that builds and tests the backend, lints and tests the Angular app, builds and scans images (failing on critical vulnerabilities), lints the chart, installs it into kind and smoke tests it, and checks that an upgrade to a new image tag drops no requests.

**Blocked by:** 26, 27

**Status:** ready-for-human

- [ ] Full pipeline under 10 minutes
- [ ] Images published to GitHub Container Registry
- [ ] A git tag publishes versioned images and a packaged chart
