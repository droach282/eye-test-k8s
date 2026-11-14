# 28: CronJob, Sealed Secrets and NetworkPolicy

**What to build:** The weekly CronJob on the API image's batch profile (schedule a chart value, default Wednesday 12:00 UTC), database and CFBD credentials as Sealed Secrets the chart expects by name with the CFBD key readable only by the job, and default-deny network policy with the specified allowances.

**Blocked by:** 20, 27

**Status:** ready-for-human

- [ ] Nothing sensitive in committed values files
- [ ] API pods reach only the database and DNS; the job reaches the database, DNS and CFBD on 443
- [ ] A CronJob run never loses stored data
- [ ] The kind setup installs the Sealed Secrets controller
