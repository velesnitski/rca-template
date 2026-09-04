# RCA Template

Use this template to structure your Root Cause Analysis. Each section serves a specific purpose in building a complete picture of the incident.

---

## 1. Incident Summary

| Field | Value |
|-------|-------|
| **Title** | <!-- Short description --> |
| **Status** | INITIAL / FINAL <!-- An initial RCA is expected within 48h and may carry open questions — list them in the Appendix. Promote to FINAL only when none remain. --> |
| **Date** | <!-- Incident date range --> |
| **Time reference** | <!-- e.g. "UTC. Chat timestamps are shown in local time (UTC+2)." See the Timeline note on mixed sources. --> |
| **Severity** | CRITICAL / HIGH / MEDIUM / LOW |
| **Impact** | <!-- Business impact in measurable terms --> |
| **Detection Gap** | <!-- Time between incident start and detection --> |
| **Resolution Time** | <!-- Time between detection and resolution --> |
| **Systems Affected** | <!-- List of affected services/systems --> |

---

## 2. Timeline

Reconstruct the chronological sequence of events. Include:
- What triggered the incident
- When each person/system became aware
- What actions were taken and when
- When the incident was resolved
- **Controls that fired before anyone reacted** — a scan that ran, an alert that triggered, a report that was generated but not read. These rows are where the detection gap gets explained.

| Timestamp | Event | Actor | Category | Source |
|-----------|-------|-------|----------|--------|
| | | | Trigger / Detection / Action / Resolution | <!-- chat, ticket, log, tool output --> |

**Attribution.** The Actor is who *found* or *did* the thing — not who reported it, asked for it, or was informed. "Engineer B found host X infected (Lead A informed, on the call)" is right; crediting the lead is wrong, and it is the first thing the people who were there will correct.

**Mixed time sources.** Tool output is usually UTC; chat and tickets usually show local time; a host's own clock may be neither. State the reference in the summary, and when sources disagree compute the key intervals (detection gap, time to containment) from two anchors taken from the *same* source. A `Z` suffix on a log timestamp is a claim, not a guarantee — verify it against an epoch value before trusting it.

**Key metric:** Time between trigger and detection = detection gap. This is often the most important number in the RCA. If a control produced evidence before a human acted on it, also record **control-to-human time** — the interval between the finding existing and someone reading it. It is frequently the largest single component of the gap.

---

## 3. Root Cause Analysis

### 3.1 Root Cause Statement

State the root cause in one clear sentence. A good root cause:
- Is specific and falsifiable
- Points to a systemic issue, not a person
- Explains WHY the incident happened, not just WHAT happened

> **Root Cause:** [statement]

### 3.2 Contributing Factors

List all factors that contributed to the incident. Each factor should be categorized:

| Factor | Category | Description |
|--------|----------|-------------|
| | Configuration / Process / Monitoring / Access Control / Communication | |

### 3.3 Five Whys

Drill down from the visible symptom to the underlying cause:

```
WHY 1: Why did [symptom] happen?
  -> Because [answer]

WHY 2: Why did [answer from WHY 1] happen?
  -> Because [answer]

WHY 3: Why did [answer from WHY 2] happen?
  -> Because [answer]

WHY 4: Why did [answer from WHY 3] happen?
  -> Because [answer]

WHY 5: Why did [answer from WHY 4] happen?
  -> Because [answer] <-- This is typically the root cause
```

**Two chains, not one.** Most incidents worth an RCA have two independent causal paths: *why it happened* and *why it took so long to notice*. Drilling only the first yields a root cause about the trigger and leaves the detection gap — often the larger cost — unexplained. When the detection gap is material, run a second chain:

```
CHAIN A — why it became possible:   symptom → ... → the missing process
CHAIN B — why it went unnoticed:    delay   → ... → why the control that saw it never reached a human
```

Chain B almost always terminates in one of five links: the control did not exist · existed but did not fire · fired but classified the finding below the alert threshold · alerted but nobody owned triage · was triaged but not acted on. Name the exact link — each one has a different fix, and "add more monitoring" is the wrong answer to three of the five.

### 3.4 Fault Tree

Visual representation of how contributing factors combined to cause the incident:

```
                [INCIDENT OUTCOME]
                       |
              +--------+--------+
              |                 |
      [Factor Group A]   [Factor Group B]
              |                 |
         +----+----+       +---+---+
         |         |       |       |
    [Cause 1] [Cause 2] [Cause 3] [Cause 4]
```

**Key insight:** If removing ANY branch would have prevented the incident, note it. This identifies the most effective corrective actions.

**Cheapest removable leaf.** Among the leaves whose removal *alone* would have prevented the incident, name the one that costs least to remove. It is frequently not the obvious fix — enabling a setting that already exists, closing one port, or raising one severity level can block the same path as a multi-week upgrade. Put that leaf first in the Immediate actions.

---

## 4. Responsibility Matrix

Map accountability by role. This is NOT a blame assignment — it identifies where processes and controls failed.

| Role | What Was Expected | What Actually Happened | Gap | Severity |
|------|-------------------|----------------------|-----|----------|
| | | | | ROOT CAUSE / HIGH / MEDIUM / LOW |

### Guidance for Severity
- **ROOT CAUSE**: This gap directly caused the incident
- **HIGH**: This gap significantly extended the duration or impact
- **MEDIUM**: This gap contributed but wasn't determinative
- **LOW**: Minor contribution, systemic improvement opportunity

### Evaluating Blame Narratives

If management has a pre-existing narrative about who is at fault, evaluate it against these questions:

1. **Was the person set up to fail?** (missing tools, unclear expectations, no alerts)
2. **Would a different person in the same role have done better?** (if not — it's a systemic issue)
3. **Does the proposed action prevent recurrence?** (firing someone doesn't fix missing monitoring)
4. **Is the proposed accountability proportional?** (match severity to actual contribution)
5. **Was the gap already being closed before the incident — and by whom?** Check the timeline for work that predates the trigger: a control deployed days earlier, systems recently brought back under monitoring, a debt item already in progress. If the team nominally "at fault" is the one that had started fixing it, accountability that punishes them punishes the pace of repair, not the cause.

---

## 5. Impact Assessment

| Area | Severity | Description | Measured/Estimated |
|------|----------|-------------|-------------------|
| Revenue | | | |
| Users/Customers | | | |
| Brand/Reputation | | | |
| Security | | | |
| Compliance | | | |
| Team/Morale | | | |

**Disputed values.** When two participants assess the same impact differently ("data was taken" vs "no evidence of exfiltration"), do not record both as findings. Mark the cell DISPUTED, state each position with its evidence, and add it to Open Questions. A FINAL RCA carries no disputed values — resolve them, or state explicitly why they cannot be resolved.

---

## 6. Corrective Actions

### Immediate (0-48 hours)

| # | Action | Owner | Deadline | Status |
|---|--------|-------|----------|--------|
| 1 | | | | |

### Short-term (1-2 weeks)

| # | Action | Owner | Deadline | Status |
|---|--------|-------|----------|--------|
| 1 | | | | |

### Long-term (1-3 months)

| # | Action | Owner | Deadline | Status |
|---|--------|-------|----------|--------|
| 1 | | | | |

### Corrective Action Quality Checklist

Each action should be:
- [ ] **Specific** — not "improve monitoring" but "add container health check alerting to Slack #infra-alerts"
- [ ] **Owned** — assigned to a specific role or person
- [ ] **Time-bound** — has a deadline
- [ ] **Preventive** — addresses root cause, not just this instance
- [ ] **Verifiable** — you can confirm it was done
- [ ] **Tunes before it adds** — if an existing control saw the problem, fixing its threshold, routing or ownership comes before buying a new tool
- [ ] **Cheapest leaf first** — the lowest-cost change that alone would have blocked the incident is in the Immediate table

---

## 7. Lessons Learned

### What went well
<!-- What worked during the incident response? -->

### What went poorly
<!-- What failed or was slower than expected? -->

### What was lucky
<!-- What could have made this worse but didn't? -->

---

## 8. Key Insight

> Summarize the most important takeaway from this RCA in 2-3 sentences. This should capture the systemic lesson, not just the technical fix.

---

## Appendix

### Swiss Cheese Model Assessment

Which defensive layers exist and which failed?

| Layer | Exists? | Worked? | Notes |
|-------|---------|---------|-------|
| Asset inventory / lifecycle tracking | | | <!-- can you list what you run, and its support status? --> |
| Code review | | | |
| Automated testing | | | |
| Staging environment | | | |
| Deploy checklist / hardening baseline | | | |
| Network segmentation / exposure control | | | |
| Authentication hardening (MFA, key scoping) | | | |
| Detection — a control observes the condition | | | |
| Classification — the finding gets a severity that reflects its risk | | | |
| Delivery — the finding reaches a human (alert, ticket, report) | | | |
| Triage — someone owns reading it, with an SLA | | | |
| Health checks | | | |
| Backup isolation / encryption | | | |
| Manual verification | | | |
| Incident response playbook | | | |

Detection is split into four rows on purpose. "Monitoring failed" hides which link broke, and each link has a different fix: a control that fired but rated the finding informational is a **classification** failure; one that produced a report nobody read is a **triage** failure. Neither is fixed by adding another scanner.

### Open Questions / Unverified Claims

Required while Status is INITIAL. Each row names what is unknown, what evidence would settle it, and who is getting that evidence. Empty this table before promoting the RCA to FINAL.

| # | Question | Evidence that would settle it | Owner |
|---|----------|-------------------------------|-------|
| | | | |

### Related Incidents
<!-- Links or references to similar past incidents -->

### References
<!-- Links to relevant documentation, runbooks, dashboards -->
