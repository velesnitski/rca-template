# RCA Template

Use this template to structure your Root Cause Analysis. Each section serves a specific purpose in building a complete picture of the incident.

---

## 1. Incident Summary

| Field | Value |
|-------|-------|
| **Title** | <!-- Short description --> |
| **Date** | <!-- Incident date range --> |
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

| Timestamp | Event | Actor | Category |
|-----------|-------|-------|----------|
| | | | Trigger / Detection / Action / Resolution |

**Key metric:** Time between trigger and detection = detection gap. This is often the most important number in the RCA.

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
| Code review | | | |
| Automated testing | | | |
| Staging environment | | | |
| Deploy checklist | | | |
| Monitoring/Alerting | | | |
| Health checks | | | |
| Manual verification | | | |
| Incident response process | | | |

### Related Incidents
<!-- Links or references to similar past incidents -->

### References
<!-- Links to relevant documentation, runbooks, dashboards -->
