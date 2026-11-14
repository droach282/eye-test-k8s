# 27: Helm chart core on kind

**What to build:** A chart that installs web, API, a dev-only PostgreSQL, ingress with one host and probes on the health endpoints onto an empty kind cluster with no manual steps. Migrations run at API startup relying on Flyway's lock; no Helm hook.

**Blocked by:** 25

**Status:** ready-for-human

- [ ] All pods ready in under 5 minutes on an empty kind cluster
- [ ] Two replicas each for web and API, a PodDisruptionBudget, resource requests and limits, read-only root filesystem on web
- [ ] Autoscaler present and off by default
- [ ] Removing a pod loses no stored data
- [ ] The kind setup installs the ingress controller; the chart does not
