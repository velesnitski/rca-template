# Changelog

All notable changes to this template are recorded here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [1.1.0] — 2026-09-04

Changes driven by running the template against a real incident under a
48-hour deadline. Each item closes a gap that showed up in practice.

### Added

- **Status field** (INITIAL / FINAL) in the Incident Summary. An initial RCA is
  expected quickly and is allowed to carry open questions; a final one is not.
- **Time reference field** in the Incident Summary, plus timeline guidance for
  mixed sources: compute intervals from same-source anchors, and treat a `Z`
  suffix on a log timestamp as a claim to verify rather than a fact.
- **Source column** in the Timeline table.
- **Attribution rule** for the Actor column: who *found* or *did* the thing, not
  who reported it, requested it, or was informed.
- **Control-to-human time** as a named metric alongside the detection gap.
- **Second Five Whys chain** ("why it went unnoticed") for incidents where the
  detection gap is material, with the five links a detection failure can break at.
- **Cheapest removable leaf** in the Fault Tree section and as a checklist item
  for corrective actions.
- **Fifth blame-narrative question**: was the gap already being closed before the
  incident, and by whom?
- **DISPUTED marker** for contested impact values, and a rule that a FINAL RCA
  carries none.
- **Open Questions / Unverified Claims** table in the Appendix, required while
  the status is INITIAL.
- **Disputed Facts** section and **"Did any control see the problem before a
  human did?"** field in `INCIDENT_INPUT.md`.
- **Lessons Learned** (went well / went poorly / what was lucky) and **Open
  Questions** sections in the HTML output template; **Status** and **Detection
  gap** in its header; an optional Chain B table; a "cheapest removable leaf" line.
- Three follow-up prompts in `prompts/rca_analysis.md`: reconcile disputed facts,
  find the cheapest fix, locate the broken detection link.
- `CHANGELOG.md` (this file).

### Changed

- **Swiss Cheese table**: the single "Monitoring/Alerting" row is split into
  *Detection → Classification → Delivery → Triage*, because each link fails
  differently and only one of them is fixed by adding a tool. Rows for asset
  inventory, exposure control, authentication hardening and backup isolation
  were added.
- **Corrective-action checklist**: two new criteria — *tunes before it adds*,
  *cheapest leaf first*.
- **Analysis prompt**: system principles 6–8 (detection is a chain of links;
  attribute to who found/did; find the cheapest leaf) and an eleven-section
  output structure matching the template.
- **HTML output template**: Key Insight moved from section 8 to 9 to make room
  for Lessons Learned; Open Questions added as section 10.
- **README**: repository tree corrected (three example files were missing);
  methodology list updated to match the template.

## [1.0.0] — 2026-08

Initial release: template, input form, analysis prompt, HTML export, examples.
