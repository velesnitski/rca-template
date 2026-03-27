# Incident Input Data

Fill in the sections below with your incident data. The more detail you provide, the better the RCA analysis will be. Delete any sections that don't apply.

> **Important:** Sanitize all data before sharing — remove real names, IPs, credentials, and customer PII. Use roles (e.g., "DevOps Engineer A") instead of names.

---

## Incident Summary

**One-line description:**
<!-- e.g., "Payment processing was down for 2 days after server restart" -->

**Severity:** Critical / High / Medium / Low

**Business Impact:**
<!-- e.g., "~2 days of lost revenue through website purchases" -->

---

## Timeline

Fill in what you know. Approximate times are fine.

| When | What happened | Who was involved |
|------|--------------|-----------------|
| <!-- date/time --> | <!-- event --> | <!-- role --> |
| | | |
| | | |
| | | |
| | | |

**When did the incident START?**
<!-- date/time + what triggered it -->

**When was it DETECTED?**
<!-- date/time + how/by whom -->

**When was it RESOLVED?**
<!-- date/time + what fixed it -->

**Detection gap:**
<!-- How long between start and detection? -->

---

## What Changed

What changed in the system before the incident? Check all that apply:

- [ ] Code deployment
- [ ] Infrastructure change (server restart, migration, scaling)
- [ ] Configuration change (DNS, firewall, load balancer)
- [ ] Third-party service change
- [ ] Scheduled maintenance
- [ ] Unplanned outage (power, network, hosting)
- [ ] Other: ___

**Details of the change:**
<!-- What specifically was changed and why? -->

---

## Communication Log

Paste or summarize relevant chat threads, emails, or call notes. This is critical for understanding detection gaps and response times.

### Thread 1
<!--
Format:
[timestamp] Role/Person: message
[timestamp] Role/Person: message
-->

### Thread 2 (if applicable)
<!--
[timestamp] Role/Person: message
-->

---

## Technical Evidence

### Logs / Commands / Output
```
<!-- Paste relevant logs, command output, error messages -->
<!-- Remove any sensitive data (IPs, passwords, tokens) -->
```

### System State
<!-- What was the state of the system when the problem was found? -->
<!-- e.g., container status, service health, error rates -->

### Configuration
<!-- Any relevant config that contributed to the issue -->
<!-- e.g., missing restart policy, wrong environment variable -->

---

## Alerts and Monitoring

**What alerts fired?**
<!-- List alerts that triggered, or state "none" -->

**What alerts SHOULD have fired but didn't?**
<!-- What monitoring gap allowed this to go undetected? -->

**Existing monitoring in place:**
<!-- What monitoring/alerting exists for this system? -->

---

## People and Roles

| Role | What they did | What was expected |
|------|--------------|-------------------|
| <!-- e.g., DevOps Engineer A --> | <!-- what they actually did --> | <!-- what should have happened --> |
| | | |
| | | |

---

## Previous Incidents

Has something similar happened before?

- [ ] Yes — describe: ___
- [ ] No
- [ ] Unknown

---

## Context and Assumptions

**Architecture notes:**
<!-- Brief description of the system architecture relevant to this incident -->

**Deployment process:**
<!-- How are changes deployed? CI/CD? Manual? -->

**Team structure:**
<!-- Who is responsible for what? Is there an on-call rotation? -->

---

## Proposed Narrative (if any)

If your team already has a theory about what happened and who is responsible, write it here. The AI will validate or challenge this narrative.

<!-- e.g., "Management believes QA should have caught this earlier" -->

---

## Questions for Analysis

What specific questions do you want the RCA to answer?

1. <!-- e.g., "Was firing QA the right response?" -->
2. <!-- e.g., "What monitoring would have prevented this?" -->
3.
