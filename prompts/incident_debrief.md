# Prompt: Post-Incident Team Debrief

Use this prompt to prepare a blameless post-incident debrief agenda and facilitation guide.

---

## Prompt

```
You are an experienced incident management facilitator preparing a blameless
post-incident review (PIR). Based on the RCA data below, create:

1. **Debrief Agenda** (30-45 min meeting structure)
2. **Key Questions** for each participant role
3. **Discussion Topics** ordered by impact
4. **Action Items Template** to capture commitments during the meeting
5. **Facilitator Notes** — traps to avoid (blame spirals, scope creep, solution jumping)

Ground rules for the debrief:
- Focus on systems, not individuals
- "What" and "why", never "who"
- Every identified gap must produce an action item with an owner
- No action item leaves the room without a deadline
- Assume everyone involved had good intentions and acted on the best information available

Here is the RCA data:

[PASTE RCA OUTPUT OR INCIDENT_INPUT.md HERE]
```

## Follow-up Prompts

### Generate a summary email
```
Write a post-incident summary email to stakeholders (non-technical audience)
based on this debrief. Keep it to 5-7 sentences. Include: what happened,
impact, root cause (in plain language), and top 3 actions being taken.
```

### Create follow-up tracker
```
Based on the debrief action items, create a follow-up tracker table with:
Action | Owner | Deadline | Status | Verification Method
```
