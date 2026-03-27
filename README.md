# RCA Template — AI-Assisted Root Cause Analysis

A structured, AI-ready template for conducting Root Cause Analysis on production incidents. Designed to work with LLMs (Claude, ChatGPT, etc.) to produce consistent, thorough RCA reports from raw incident data.

## Why This Exists

Most RCA processes fail because:
- Teams skip structure under pressure and produce inconsistent reports
- Blame gets assigned to individuals instead of systemic failures
- Corrective actions are vague ("improve monitoring") instead of actionable
- The same class of incidents repeats because root causes aren't properly identified

This template solves these problems by providing a repeatable framework that works with AI assistants to analyze raw incident data and produce structured reports.

## Quick Start

### One-liner with Claude Code

```bash
cat INCIDENT_INPUT.md prompts/rca_analysis.md | claude -p "Analyze this incident and produce RCA per the template"
```

### Three-step flow

```bash
# 1. Fill in your incident data
cp INCIDENT_INPUT.md my_incident.md && nano my_incident.md

# 2. Generate RCA
cat my_incident.md prompts/rca_analysis.md | claude -p "Produce RCA report" > rca_output.md

# 3. Export to HTML (for Google Docs import)
cat rca_output.md RCA_OUTPUT_TEMPLATE.html | claude -p "Format as HTML using template" > report.html
```

### Interactive mode

```bash
cd rca-template && claude
# then: "Read INCIDENT_INPUT.md, fill it with [paste your data], analyze using prompts/rca_analysis.md"
```

### Multi-language support

Templates are in English, but Claude will produce the RCA in any language — just change the prompt:

```bash
# Russian
cat my_incident.md prompts/rca_analysis.md | claude -p "Проанализируй инцидент и создай RCA по шаблону на русском языке"

# Spanish
cat my_incident.md prompts/rca_analysis.md | claude -p "Analiza el incidente y genera un RCA en español"

# Chinese
cat my_incident.md prompts/rca_analysis.md | claude -p "分析事件并按模板生成中文RCA报告"
```

No need to translate templates — the AI reads English structure and outputs in your language.

See [`examples/example_4_quick_usage.md`](examples/example_4_quick_usage.md) for more usage patterns, and [`examples/example_5_generated_rca_ru.md`](examples/example_5_generated_rca_ru.md) for a complete Russian RCA generated from a minimal input.

## Repository Structure

```
.
├── README.md                  # This file
├── INCIDENT_INPUT.md          # Fill this with your incident data
├── RCA_TEMPLATE.md            # The RCA framework and methodology
├── RCA_OUTPUT_TEMPLATE.html   # HTML template for Google Docs export
├── examples/
│   ├── example_1_db_outage.md         # Example: Database container didn't restart
│   ├── example_2_env_exposure.md      # Example: Dev environment exposed publicly
│   └── example_4_quick_usage.md      # Quick usage patterns with Claude Code
└── prompts/
    ├── rca_analysis.md        # Main RCA prompt for AI
    ├── validate_process.md    # Prompt to validate an existing process/runbook
    └── incident_debrief.md    # Prompt for post-incident team debrief
```

## What Data to Provide

The quality of the RCA depends entirely on the quality of input data. Here's what to include in `INCIDENT_INPUT.md`:

### Required (minimum for useful analysis)

| Data | Why It's Needed | Example |
|------|----------------|---------|
| **What happened** | Defines the incident scope | "Checkout API returning 500 errors for 6 hours" |
| **When it started** | Establishes timeline | "2025-01-15, 03:00 UTC after scheduled maintenance" |
| **When it was detected** | Measures detection gap | "2025-01-15, 09:12 UTC — support reported spike in user complaints" |
| **When it was resolved** | Measures resolution time | "2025-01-15, 10:45 UTC" |
| **What was the impact** | Quantifies severity | "~6 hours of degraded service, estimated 15% drop in transactions" |
| **What changed before the incident** | Identifies potential triggers | "Scheduled OS patching triggered a service restart" |

### Strongly Recommended

| Data | Why It's Needed | Example |
|------|----------------|---------|
| **Chat thread screenshots or transcripts** | Reveals actual communication flow and gaps | Who said what, when, and response times |
| **Technical logs or commands run** | Shows what actually happened vs. what was assumed | `docker ps` output, bash history, error logs |
| **Who was involved and their roles** | Maps responsibility chain | "DevOps engineer restored server, QA was asked to verify" |
| **What alerts fired (or didn't)** | Identifies monitoring gaps | "No alerts — problem found manually 2 days later" |
| **Previous related incidents** | Reveals patterns | "Similar issue happened 3 months ago with another service" |

### Optional (improves depth)

| Data | Why It's Needed |
|------|----------------|
| Architecture diagram or description | Helps AI understand system dependencies |
| Deployment process description | Identifies process gaps |
| Existing monitoring setup | Shows what's already covered vs. gaps |
| Team structure and on-call rotation | Identifies communication path issues |
| Business context | Helps prioritize corrective actions by impact |

### What NOT to Include (sanitize before sharing)

- Real employee full names — use roles or initials
- Passwords, tokens, API keys
- Customer PII
- Internal IP addresses or server credentials
- Proprietary business metrics — use relative terms: "revenue dropped ~60%"

## RCA Methodology

This template combines proven techniques:

1. **Timeline Reconstruction** — what happened, in what order
2. **5 Whys Analysis** — drilling from symptom to root cause
3. **Fault Tree** — visual mapping of contributing factors
4. **Responsibility Matrix** — role-based accountability (not blame)
5. **Swiss Cheese Model** — identifying which defensive layers failed
6. **Corrective Actions** — prioritized: immediate / short-term / long-term

## AI Best Practices for RCA

### Do
- Provide raw, unedited incident data — AI can structure it
- Include communication logs — they reveal detection gaps and response times
- Ask AI to challenge assumptions ("is QA really at fault, or is it systemic?")
- Request specific corrective actions with owners and timelines
- Ask for severity ratings on each contributing factor

### Don't
- Don't pre-filter data based on what you think matters — let AI find patterns
- Don't ask AI to assign blame to individuals — ask for systemic analysis
- Don't accept vague recommendations ("improve monitoring") — demand specifics
- Don't skip contributing factors — root cause is rarely a single point of failure

### Prompt Tips

| Goal | Prompt Addition |
|------|----------------|
| Challenge blame narrative | "Evaluate whether the proposed accountability is targeting the root cause or a symptom" |
| Get actionable items | "Every corrective action must have: specific deliverable, owner role, and timeframe" |
| Find systemic issues | "Identify which organizational processes failed, not just which people" |
| Validate severity | "Rate each contributing factor as ROOT CAUSE, HIGH, MEDIUM, or LOW with justification" |

## Export to Google Docs

1. Ask AI to use `RCA_OUTPUT_TEMPLATE.html` as the format
2. Google Docs -> File -> Open -> Upload -> select the HTML file
3. Tables, formatting, and structure will be preserved

## Contributing

PRs welcome. If you have anonymized RCA examples or improvements to the prompts, please contribute.

## License

MIT
