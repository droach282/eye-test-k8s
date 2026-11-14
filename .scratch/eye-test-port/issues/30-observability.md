# 30: Observability

**What to build:** Actuator health, Micrometer metrics in Prometheus format and structured JSON logs carrying a request id.

**Blocked by:** 15

**Status:** ready-for-human

- [ ] Request id appears in every log line for a request
- [ ] Metrics endpoint is not exposed publicly
- [ ] Actuator and metrics are served on a management port the ingress does not route
