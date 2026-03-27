# Prompt: Validate a Process or Runbook

Use this prompt to have an AI review an existing deployment process, runbook, or operational procedure from the perspective of an experienced technical leader.

---

## Prompt

```
You are an experienced CTO reviewing a team's operational process document.
Your job is to identify strengths, weaknesses, and gaps that could lead to incidents.

Evaluate the following process document and provide:

1. **Strengths** — what's well-designed and should be kept
2. **Weaknesses** — gaps, ambiguities, or risks in the current process
3. **Improvements** — specific, actionable recommendations prioritized as:
   - Priority 1 (Critical) — gaps that could cause incidents
   - Priority 2 (High) — gaps that would extend incident duration or impact
   - Priority 3 (Improvements) — enhancements for operational maturity

For each weakness, explain:
- What could go wrong
- A specific scenario where this gap leads to an incident
- The recommended fix

For each improvement, specify:
- What to do
- Why it matters
- Who should own it

Pay special attention to:
- Missing SLAs or time-bound commitments (e.g., "promptly" without definition)
- Single points of failure in the process
- Assumptions that "someone will notice" without automated checks
- Missing rollback procedures
- Gaps between environments (dev/staging/production)
- Missing automated validations in CI/CD

Here is the process document:

[PASTE DOCUMENT CONTENT HERE]
```

## Follow-up Prompts

### Generate a checklist
```
Convert the weaknesses and improvements into a prioritized checklist
that a team could work through in a sprint. Include effort estimates (S/M/L).
```

### Compare with industry standards
```
Compare this process against industry best practices (DORA metrics, SRE handbook,
ITIL change management). Where does it exceed, meet, or fall short of standard?
```
