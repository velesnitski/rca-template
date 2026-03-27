# Example: How to Generate a Complete RCA Report

This example walks through the full process — from raw incident data to finished report.

---

## Step 1: Gather Raw Data

You receive Slack messages, logs, and a complaint from business. Here's what you have:

### Raw Slack Thread

```
[Mar 25, 17:15] Business Lead: Check our product — I see big drop in purchases last 2 days, any reason?

[Mar 25, 17:22] Support: No mass complaints about connectivity. On Friday Mar 20 there was
a spike in purchases (~1200), and now they're declining.

[Mar 25, 17:23] Business Lead: Any understanding which purchases are dropping? Through site or bot?

[Mar 25, 17:27] Support: Mostly purchases dropped through the website, last 2 days.
```

### Raw Technical Thread

```
[Mar 25, 20:31] DevOps Lead: Database container was stopped.

[Mar 25, 20:31] PM: Is there timing? When did this happen and why wasn't it noticed?

[Mar 25, 20:36] PM: Payment page opens but the link doesn't generate.

[Mar 25, 20:37] DevOps Lead: 2 days ago the server was restarted. In the docker-compose
project, the database service didn't have the option restart: unless-stopped.
As a result, everything restarted automatically except the database.
Currently everything is running.

[Mar 25, 20:39] PM: Can we set up some alerts for these things? For 2 days this is
actually significant damage.

[Mar 25, 20:42] DevOps Lead: Who accessed the server on the 24th and restarted docker?
[shows bash history: iptables-restore, systemctl restart docker]

[Mar 25, 21:03] DevOps Engineer A: I was restoring firewall settings after the server
was disconnected for non-payment. After me all containers were working
and I checked site availability.

[Mar 25, 21:27] DevOps Lead: Percona was stopped. No auto-restart in compose config.

[Mar 25, 21:46] DevOps Engineer A: Noted. Will roll out monitoring faster.
```

### Additional Context
```
- QA Engineer was asked to check the product on Mar 23 ("check if everything is OK")
- QA Engineer responded only on Mar 25 evening (after the fix): "I don't see problems, checked in incognito"
- Management is considering firing the QA Engineer
```

---

## Step 2: Fill in INCIDENT_INPUT.md

```markdown
## Incident Summary
**One-line description:** Payment links stopped generating for 2 days after
database container failed to restart following Docker daemon restart.
**Severity:** Critical
**Business Impact:** ~2 days of lost website purchases

## Timeline
| When | What happened | Who |
|------|--------------|-----|
| ~Mar 22 | Server disconnected by hosting (billing) | Hosting |
| Mar 24 | DevOps Engineer A restores firewall, runs systemctl restart docker | DevOps A |
| Mar 24 | All containers restart except DB (no restart policy) | Automated |
| Mar 24 | DevOps A checks site — HTTP 200, considers it OK | DevOps A |
| Mar 23 | QA receives request to verify product | Team Lead |
| Mar 24-25 | Payment page works but links don't generate | — |
| Mar 25 17:15 | Business notices purchase drop | Business |
| Mar 25 20:37 | DevOps Lead finds DB container stopped, fixes it | DevOps Lead |
| Mar 25 ~21:00 | QA responds "no problems" (after fix) | QA |

## What Changed
- [x] Infrastructure change (server restart, migration, scaling)
- [x] Unplanned outage (hosting disconnection for billing)

## Proposed Narrative
Management believes QA should have caught this and is considering termination.

## Questions for Analysis
1. Is firing QA the right response?
2. What monitoring would have prevented the 2-day gap?
3. Who is actually responsible?
```

---

## Step 3: Send to AI

Use the prompt from `prompts/rca_analysis.md` and paste your INCIDENT_INPUT.md content.

---

## Step 4: AI Produces the Report

The AI generates a structured RCA. Below is the complete output:

---

# RCA Report: Database Container Downtime — 2-Day Payment Outage

**Date:** 2026-03-24 — 2026-03-25
**Severity:** CRITICAL (revenue loss)
**Server:** [production-server-1]
**Service:** Database (Percona 5.7) in Docker Compose stack

## 1. Timeline

| Timestamp | Event | Actor |
|-----------|-------|-------|
| ~Mar 22 | Production server disconnected by hosting provider (billing issue) | Hosting |
| Mar 24 | Server restored. DevOps Engineer A restores firewall (iptables-restore), runs `systemctl restart docker` | DevOps Engineer A |
| Mar 24 | Docker daemon restarts. nginx + php-fpm come up (have restart policy). DB container does NOT start (no restart policy) | Automated |
| Mar 24 | DevOps Engineer A checks site — homepage loads (HTTP 200). Assumes OK | DevOps Engineer A |
| Mar 23 | QA Engineer receives message: "check if everything is OK with [product]" | Team Lead |
| Mar 24-25 | Payment page accessible, but payment links don't generate (no DB). Purchases via site drop to near zero | — |
| Mar 25, 17:15 | Business stakeholder notices major revenue drop over last 2 days | Business |
| Mar 25, 17:22 | Support confirms: "purchases dropped, especially through the website" | Support |
| Mar 25, 20:31 | DevOps Lead: "Database container was stopped" | DevOps Lead |
| Mar 25, 20:37 | Root cause found: `restart: unless-stopped` missing on DB service. All containers restarted. Service restored | DevOps Lead |
| Mar 25, ~21:00 | QA Engineer responds: "I don't see problems" — checks AFTER fix applied | QA Engineer |

## 2. Root Cause

**Cause A — Configuration Defect (ROOT CAUSE):** `docker-compose.yml` database service lacked `restart: unless-stopped`. Docker daemon restart brought back web servers but not the database.

**Cause B — Inadequate Verification (HIGH):** Post-recovery check = "does homepage load?" Not "does the payment flow work?"

**Cause C — No Container Monitoring (HIGH):** No alerting on container status. 2-day silent failure.

**Cause D — QA Non-Response (HIGH):** Direct request to verify was received but not acted on for ~2 days.

**Cause E — Billing Process Gap (MEDIUM):** Production server was disconnected for non-payment, triggering the entire chain.

## 3. Five Whys

| # | Question | Answer |
|---|----------|--------|
| WHY 1 | Why were purchases lost for 2 days? | Payment links couldn't generate — no DB connection |
| WHY 2 | Why was DB unavailable? | Container didn't start after Docker daemon restart |
| WHY 3 | Why didn't the container start? | Missing restart policy in docker-compose.yml |
| WHY 4 | Why wasn't this detected for 2 days? | Superficial post-recovery check + no monitoring + QA didn't verify when asked |
| WHY 5 | Why was manual restart needed? | Server disconnected for billing issue |

## 4. Fault Tree

```
              [2 days lost purchases — revenue impact]
                            |
                  +---------+---------+
                  |                   |
          [DB not running]    [Nobody noticed for 2 days]
                  |                   |
          +-------+-------+    +-----+-----+-----+
          |               |    |           |     |
    [container      [no       [check was  [no    [QA didn't
     didn't start] restart   superficial] alerts] respond]
          |        policy]
          |
    [docker daemon restarted]
          |
    [firewall restore + systemctl restart docker]
          |
    [server disconnected for non-payment]
```

## 5. Evaluation of Blame Narrative

**Management proposal:** Fire QA Engineer.

**Analysis:**

| Question | Answer |
|----------|--------|
| Was QA the root cause? | No. Root cause is missing restart policy (DevOps/IaC) |
| Was QA notified? | Yes — received direct request to check ~2 days before resolution |
| Did QA respond? | Not until after the fix was applied. Response ("no problems") had zero value |
| Would firing QA prevent recurrence? | No. Missing restart policy and absent monitoring remain unfixed |
| Is the accountability proportional? | QA missed an opportunity to detect earlier (HIGH), but didn't cause the incident |

**Recommendation:** Formal warning with documented SLA expectations (acknowledge ≤ 30 min, result ≤ 2h). On repeat — escalate. Firing for a first offense where the person wasn't the root cause sends a message that scapegoating is acceptable, which damages team trust and reporting culture.

## 6. Corrective Actions

### Immediate
| # | Action | Owner |
|---|--------|-------|
| 1 | Add `restart: unless-stopped` to ALL services in ALL docker-compose files | DevOps |
| 2 | Audit all docker-compose configs across all servers | DevOps |
| 3 | Set up auto-payment or billing alerts for hosting | Finance |

### Short-term (1-2 weeks)
| # | Action | Owner |
|---|--------|-------|
| 4 | Deploy container monitoring with alerting | DevOps |
| 5 | Add `/health` endpoint checking DB connectivity | Dev + DevOps |
| 6 | Post-recovery checklist: verify key user flows, not just HTTP | DevOps |
| 7 | SLA for QA response: acknowledge ≤ 30 min, result ≤ 2h | QA Lead |

### Long-term
| # | Action | Owner |
|---|--------|-------|
| 8 | Synthetic monitoring — automated payment flow check every 5 min | QA + DevOps |
| 9 | Server recovery runbook with verification checklist | DevOps |
| 10 | IaC review process — docker-compose changes require PR | DevOps + Lead |

## 7. Key Insight

> A partially working system is more dangerous than a fully broken one. The site loaded, the payment page opened — but the critical function was silently broken for 2 days. This happened because three layers failed simultaneously: restart policy (prevention), monitoring (detection), and human verification (last resort). Defense in depth means every layer works independently.

---

## Step 5: Export

Ask the AI to format the output using `RCA_OUTPUT_TEMPLATE.html` for Google Docs import.

```
Format this RCA as HTML using the template in RCA_OUTPUT_TEMPLATE.html.
Replace all placeholders with the actual data. Output a complete HTML file.
```

Upload the resulting `.html` file to Google Docs: File → Open → Upload.
