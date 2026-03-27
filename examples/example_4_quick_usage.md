# Quick Usage: Single-Command RCA with Claude Code

## One-liner

```bash
cat INCIDENT_INPUT.md prompts/rca_analysis.md | claude -p "Analyze this incident and produce RCA per the template"
```

## Step-by-step (3 commands)

```bash
# 1. Copy and fill the input template
cp INCIDENT_INPUT.md my_incident.md
nano my_incident.md  # fill in your data

# 2. Generate RCA
cat my_incident.md prompts/rca_analysis.md | claude -p "Produce RCA report" > rca_output.md

# 3. Export to HTML for Google Docs
cat rca_output.md RCA_OUTPUT_TEMPLATE.html | claude -p "Format this RCA as HTML using the template" > report.html
```

## Minimal Input Example

You don't need to fill every field. Here's the minimum viable input:

```markdown
## Incident Summary
Redis cache container stopped after host reboot. API returned 500 for 4 hours.
Severity: High

## Timeline
| When | What | Who |
|------|------|-----|
| Mar 15, 02:00 | Host rebooted (kernel update) | SysAdmin |
| Mar 15, 02:05 | App + DB containers restarted. Redis did not (no restart policy) | Automated |
| Mar 15, 06:10 | On-call engineer gets paged by users reporting errors | SRE |
| Mar 15, 06:15 | `docker ps -a` shows redis exited. `docker compose up -d` fixes it | SRE |

## What Changed
Server rebooted for kernel update. Redis container had no restart: unless-stopped.

## Questions
1. Why wasn't this caught by monitoring?
2. What's the fastest fix to prevent recurrence?
```

## Run it

```bash
claude -p "You are an RCA specialist. Analyze this incident using 5 Whys, Fault Tree, and Responsibility Matrix. Be specific with corrective actions.

$(cat << 'INPUT'
Redis cache container stopped after host reboot. API returned 500 for 4 hours.

Timeline:
- 02:00 Host rebooted (kernel update) by SysAdmin
- 02:05 App + DB restarted automatically. Redis did not — no restart policy
- 06:10 On-call SRE paged by user reports
- 06:15 docker compose up -d fixes it

No container monitoring. No health checks. Alerting only via user complaints.
INPUT
)"
```

## With Claude Code (interactive)

```bash
cd rca-template
claude
# then type: "Read INCIDENT_INPUT.md, fill it with [paste your data], then analyze using prompts/rca_analysis.md"
```

## Validate an existing process

```bash
cat my_deploy_process.docx | textutil -convert txt -stdout | claude -p "$(cat prompts/validate_process.md)"
```
