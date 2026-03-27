# Example RCA: Development Environment Publicly Exposed

> This is a sanitized, real-world-based example. All names and identifying details have been changed.

## Incident Summary

**Title:** Dev environment became publicly accessible, causing SEO tool to index cross-environment link from production — corrupted backlink metrics

**Severity:** CRITICAL (Marketing impact)

**Impact:** Production site had a link pointing to dev environment. While dev was behind VPN, the link was harmless (dead link). When dev became publicly accessible, SEO crawlers (Ahrefs) followed the link, indexed the dev site, and corrupted the backlink profile and SEO metrics.

---

## Timeline

| Timestamp | Event | Actor |
|-----------|-------|-------|
| T0 (months ago) | A link to dev.example.com is added to the production site | Developer |
| T0 — T2 | dev.example.com is behind VPN. SEO crawlers get connection refused → dead link → ignored | — |
| T2 | dev.example.com becomes publicly accessible (infrastructure change) | DevOps / Infrastructure |
| T2 + minutes | Ahrefs crawler follows link from production → gets HTTP 200 from dev → indexes it | Automated (Ahrefs) |
| T2 + hours | dev.example.com appears in Ahrefs backlink profile, corrupting SEO metrics | — |
| T2 + hours | Marketing team notices anomalies in Ahrefs data | Marketing |

---

## Root Cause

This incident has TWO root causes that worked in combination:

**Cause A — Latent Defect:** A hardcoded link to dev.example.com existed in production code. This was a dormant bug that didn't manifest because of a compensating control (VPN).

**Cause B — Control Failure:** dev.example.com became publicly accessible, removing the compensating control (VPN/firewall) that was masking Cause A.

### Why SEO tools didn't react before

| State of dev | What crawler sees | Result |
|---|---|---|
| Behind VPN (connection refused) | TCP connection fails | Link marked as broken/dead — NOT indexed |
| Behind VPN (timeout) | No HTTP response | Same — dead link |
| **Publicly accessible (HTTP 200)** | **Valid page** | **Link becomes live → enters backlink profile** |

SEO tools (Ahrefs, Google) do not count links to unreachable resources in their metrics. The moment dev started responding with HTTP 200 was the trigger that activated the latent defect.

---

## 5 Whys

| # | Question | Answer |
|---|----------|--------|
| WHY 1 | Why were Ahrefs metrics corrupted? | Ahrefs indexed a cross-environment link from production to dev |
| WHY 2 | Why could Ahrefs index dev? | dev.example.com became publicly accessible |
| WHY 3 | Why did dev become public? | Infrastructure change removed VPN/firewall restriction |
| WHY 4 | Why was there a link to dev on production? | Hardcoded URL left by a developer, no pre-deploy validation |
| WHY 5 | Why was there no control catching cross-env links? | No automated check (linter, CI, crawler) for non-production URLs in production content |

---

## Fault Tree

```
              [SEO metrics corrupted]
                       |
              +--------+--------+
              |                 |
    [Live cross-env link  [Ahrefs crawler
     on production]        reached dev]
              |                 |
              |          +------+------+
              |          |             |
              |    [dev publicly  [No robots.txt
              |     accessible]   Disallow on dev]
              |          |
              |    +-----+-----+
              |    |           |
              |  [VPN/FW    [No monitoring
              |   removed]  of exposure]
              |
       +------+------+
       |             |
  [Hardcoded    [No CI/CD check
   dev URL]     for dev URLs]
```

---

## Key Concept: Swiss Cheese Model

The system appeared secure not because there was no defect, but because another control (VPN) was accidentally compensating for it. When that compensating control was removed, the dormant defect activated.

This is a classic pattern: **every defensive layer must work independently**. Relying on one control to mask another creates hidden risk.

---

## Corrective Actions

### Immediate
1. Block public access to dev.example.com — DevOps
2. Remove the dev link from production — Development
3. Add `robots.txt` with `Disallow: /` on dev — DevOps
4. Add `<meta name="robots" content="noindex, nofollow">` on dev — Development
5. Request re-crawl in Ahrefs to clean data — Marketing

### Short-term
6. CI/CD check: scan for `dev.`, `staging.`, `localhost` patterns before production deploy — DevOps
7. Network exposure monitoring — alert if dev/staging responds on public IPs — DevOps
8. HTTP Basic Auth or IP whitelist on all non-production environments — DevOps

### Long-term
9. Environment-aware URL generation — URLs from env vars, not hardcoded — Development
10. Periodic cross-environment link audit — scheduled crawl of production for dev/staging URLs — QA + DevOps
11. Ahrefs monitoring dashboard — alert on anomalies in referring domains — Marketing

---

## Key Insight

> A link to a dead resource is harmless. A link to a live resource in the wrong environment is an incident waiting to happen. The system had a dormant defect (hardcoded dev URL) masked by a compensating control (VPN). When the compensating control was removed, the defect activated instantly. This is the Swiss Cheese Model in action — every layer of defense must work independently, because you never know when another layer will fail.
