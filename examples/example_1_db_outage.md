# Example RCA: Database Container Outage

> This is a sanitized, real-world-based example. All names and identifying details have been changed.

## Incident Summary

**Title:** Database container failed to restart after Docker daemon restart — 2-day payment outage

**Severity:** CRITICAL

**Impact:** ~2 days of lost purchases through the website. Payment page was accessible but unable to generate payment links due to missing database connection.

---

## Timeline

| Timestamp | Event | Actor |
|-----------|-------|-------|
| Day 0 | Production server disconnected by hosting provider (billing issue) | Hosting provider |
| Day 2 | Server restored. DevOps Engineer A restores firewall rules, runs `systemctl restart docker` | DevOps Engineer A |
| Day 2 | Docker daemon restarts. nginx and php-fpm containers come up (have restart policy). Database container (Percona) does NOT start — missing `restart: unless-stopped` in docker-compose.yml | Automated |
| Day 2 | DevOps Engineer A checks site — homepage loads (HTTP 200). Assumes everything is OK | DevOps Engineer A |
| Day 2 | QA Engineer receives message in team channel: "check if everything is OK with [product]" | Team Lead |
| Day 2 — Day 4 | Payment page opens but payment links don't generate. Purchases via website drop to near zero | — |
| Day 4, 17:15 | Business stakeholder notices major revenue drop over last 2 days | Business |
| Day 4, 17:22 | Support confirms: "purchases dropped, especially through the website" | Support |
| Day 4, 20:31 | DevOps Lead investigates: "Database container was stopped" | DevOps Lead |
| Day 4, 20:37 | Root cause found: `restart: unless-stopped` not set on DB service. All containers restarted. Service restored | DevOps Lead |
| Day 4, 20:39 | Business stakeholder: "For 2 days this is significant damage. Can we set up alerts?" | Business |
| Day 4, ~21:00 | QA Engineer responds: "I don't see problems" — checks AFTER the fix was applied | QA Engineer |
| Day 4, 21:03 | DevOps Engineer A: "After me all containers were working and I checked site availability" | DevOps Engineer A |
| Day 4, 21:27 | DevOps Lead: "Percona was stopped. No auto-restart in compose" | DevOps Lead |

---

## Root Cause

**Cause A — Configuration Defect:** `docker-compose.yml` for the database service lacked `restart: unless-stopped`. When Docker daemon was restarted, nginx and php-fpm came back automatically, but the database did not.

**Cause B — Inadequate Verification:** Post-recovery check was limited to "does the homepage load?" (HTTP 200). The critical payment flow was not tested.

**Cause C — No Container Monitoring:** No alerting on container status. A stopped database container went undetected for 2 days.

**Cause D — QA Non-Response:** QA Engineer received a direct request to verify the product but did not respond or check until after the issue was already resolved by others.

---

## 5 Whys

| # | Question | Answer |
|---|----------|--------|
| WHY 1 | Why were purchases lost for 2 days? | Payment links couldn't be generated — no database connection |
| WHY 2 | Why was the database unavailable? | Percona container didn't start after Docker daemon restart |
| WHY 3 | Why didn't the container start? | Missing `restart: unless-stopped` in docker-compose.yml |
| WHY 4 | Why wasn't this detected for 2 days? | Superficial post-recovery check + no monitoring + QA didn't verify when asked |
| WHY 5 | Why was there a manual restart needed at all? | Server was disconnected for a billing issue — payment process for infrastructure not managed |

---

## Responsibility Matrix

| Role | What Was Expected | What Happened | Severity |
|------|-------------------|---------------|----------|
| DevOps (IaC owner) | All services in docker-compose have restart policies | Database service missing restart policy | ROOT CAUSE |
| DevOps Engineer A | Verify full functionality after server recovery | Checked HTTP 200 only, didn't test payment flow | HIGH |
| DevOps / SRE | Container monitoring with alerts | No monitoring — 2-day silent failure | HIGH |
| QA Engineer | Respond to verification request, test product | No response for ~2 days, checked only after fix | HIGH |
| Finance / Management | Ensure production server billing is current | Server disconnected for non-payment | MEDIUM |

---

## Corrective Actions

### Immediate
1. Add `restart: unless-stopped` to ALL services in ALL docker-compose files — DevOps
2. Audit all docker-compose configs across all servers — DevOps
3. Set up auto-payment or billing alerts for hosting — Finance

### Short-term (1-2 weeks)
4. Deploy container monitoring with alerting (Zabbix Docker template / Portainer / Uptime Kuma) — DevOps
5. Add `/health` endpoint that checks DB connectivity — Dev + DevOps
6. Create post-recovery checklist: verify key user flows, not just HTTP status — DevOps
7. Define SLA for QA response to verification requests: acknowledge ≤ 30 min, result ≤ 2h — QA Lead

### Long-term
8. Synthetic monitoring — automated payment flow check every 5 min — QA + DevOps
9. Create server recovery runbook with verification checklist — DevOps
10. IaC review process — docker-compose changes require PR review — DevOps + Lead

---

## Key Insight

> A partially working system is more dangerous than a fully broken one. The site loaded, the payment page opened — but the critical function (payment link generation) was silently broken. The combination of a missing restart policy, a superficial health check, and absent monitoring created a 2-day silent failure that was only detected when business metrics dropped. Defense in depth means every layer works independently — restart policies, health checks, monitoring, AND human verification.
