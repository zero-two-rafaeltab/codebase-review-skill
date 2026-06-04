# Report and Chat Output Contract

Use this reference when writing the durable review artifact and concise chat handoff.

## Basic Report Artifact

Write one Markdown report for each review. Prefer this path pattern unless the repository already has an obvious review artifact convention:

```text
reviews/<YYYY-MM-DD>-codebase-review-<scope-slug>.md
```

If the target being reviewed is a different repository from the repository or workspace where the user asked for the review artifact, state the chosen artifact location in the scope proposal and write the report there unless the user directs otherwise.

Use a short lowercase scope slug such as `full-repo`, `auth-workflow`, or `src-billing`. If a same-day report already exists for the same scope, append a short suffix such as `-2` or `-1430`.

The report is the durable handoff. Chat should point to it rather than copying the full details.

Minimum output contract:

- create exactly one primary Markdown report artifact for the review;
- include a 2-4 bullet executive summary near the top of the report that names severity counts, top findings, main pain sources, and notable confidence/evidence caveats;
- include a compact triage overview near the top with counts by severity instead of an arbitrary overall health status;
- point from the overview to individual finding details using stable finding IDs, heading anchors, or section links;
- include compact findings or observations using the basic finding fields below;
- state in the report that findings are decision candidates, not accepted work;
- include severity, confidence, evidence strength, impact, and suggested next decisions on findings without calculating an overall health score or assigning a global status label;
- include a severity justification, evidence references or an explicit weak-evidence statement, and a churn signal on each finding;
- include delegated subagent manifests directly in the report or link to a small nearby manifest artifact when subagents were used;
- when specialist subagents were used, synthesize from their manifests, slice reports, and emitted findings/observations rather than presenting uncited top-level archaeology;
- preserve uncertainty for duplicate or conflicting specialist outputs by citing contributing manifests/findings and avoiding unsupported confidence increases;
- include a decision queue that lists findings needing user decisions and repeats enough metadata to triage without rereading every detail.

## Basic Report Skeleton

Use this skeleton for the review artifact:

```markdown
# Codebase Review — <scope>

Date: <YYYY-MM-DD>
Scope: <approved scope>
Mode: Focused manual review

## Executive summary

- Severity counts: Critical <n>, High <n>, Medium <n>, Low <n>, Observations <n>.
- Top findings: <1-3 most decision-relevant finding IDs/titles and why they matter>.
- Main pain sources: <recurring architecture, maintainability, reliability, testing, documentation, or delivery themes>.
- Caveats: <notable low-confidence items, weak evidence, unreviewed areas, or "none beyond coverage gaps below">.

Keep this section to 2-4 bullets. State what matters most, why it matters, and what discussion would unblock a decision. Do not use a global health label such as "healthy", "unhealthy", "pass", or "fail".

## Triage overview

| Severity | Count | Items |
| --- | ---: | --- |
| Critical | <n> | <links to F-### findings or "None"> |
| High | <n> | <links to F-### findings or "None"> |
| Medium | <n> | <links to F-### findings or "None"> |
| Low | <n> | <links to F-### findings or "None"> |
| Observation | <n> | <links to O-### observations or "None"> |

Top findings to inspect first:

- <List 1-3 supported `F-###` findings when present, linked to their detail sections, with severity, confidence, and a one-line reason to inspect each first. If there are no supported findings, write "No supported findings; see observations below" instead. Do not promote observations into top findings unless explicitly labeled as observations.>

Main pain sources: <short list of recurring areas/themes>.
Confidence/evidence caveats: <short list; weak signals should be observations or low-confidence findings, not mixed into proven findings>.

## Scope and method

- Reviewed: <paths, docs, commands>
- Not reviewed: <known gaps>
- Side effects: report artifact only
- Synthesis basis: <cartography manifest plus specialist manifests/reports/findings used; note any narrow top-level verification and any duplicate/conflict handling>

## Findings / observations

### F-001: <Finding title>

- ID: F-001
- Area: <component, path, or concern>
- Severity: <Critical/High/Medium/Low/Observation>
- Severity justification: <short explanation of project pain if left alone; for Observation, state why it is not yet a supported finding>
- Confidence: <High/Medium/Low>
- Evidence strength: <source evidence/tool output/runtime or test evidence/Git history or churn/documentation mismatch/heuristic pattern evidence>
- Churn signal: <recent churn seen / low churn seen / unknown / not checked>
- Summary: <what appears to be happening>
- Evidence: <file paths, command output summaries, doc references, or "weak evidence: ...">
- Impact: <why this might matter>
- Suggested next decisions: <accept; reject/ignore; create issue; document ADR/decision; launch deeper research; revise severity; plan/implement separately>

Use `F-###` IDs for supported findings and `O-###` IDs for observations. Keep these IDs stable within the report so the executive summary, triage overview, and chat response can point to detailed sections without copying all details into chat.

## Coverage gaps

- <what a deeper review should inspect later>

## Subagent manifests

<Embed any delegated cartography or specialist subagent manifest here, or link to a small nearby Markdown artifact. Manifests are audit trails for assigned scope, lens, inspected evidence, commands, artifacts, emitted findings/observations, uncertainties, out-of-scope areas, inherited/unverified context, and coverage confidence. They support the findings and decision queue; they do not replace finding severity, confidence, evidence strength, evidence references, or suggested next decisions. If no subagents beyond the required cartography pass were used, include the cartography manifest or state where it is linked.>

When multiple specialist outputs overlap, list the source manifests/findings in the relevant finding evidence. Merge duplicates only when they describe substantially the same affected area, cause, and impact. If specialist outputs conflict, keep the disagreement visible in evidence or caveats rather than forcing a single confident conclusion.

## Decision queue

Findings above are candidates for discussion. They are not approved issues, ADRs, or implementation work.

Use this queue to decide what to do next. Keep discovery conclusions separate from user-provided decision context; if context changes priority, record it as a decision-phase adjustment rather than silently rewriting the original finding.

| Ref | Severity | Confidence | Evidence strength | Impact | Suggested next decisions |
| --- | --- | --- | --- | --- | --- |
| [F-001](#f-001-finding-title) | <Critical/High/Medium/Low> | <High/Medium/Low> | <strongest evidence category or explicit weak-evidence note> | <one-line impact> | <accept; reject/ignore; create issue; document ADR/decision; launch deeper research; revise severity; plan/implement separately> |
| [O-001](#o-001-observation-title) | Observation | <High/Medium/Low> | <strongest evidence category or explicit weak-evidence note> | <one-line impact or research value> | <accept as context; reject/ignore; launch deeper research; document decision; revise severity if user context supports promotion> |

If there are no findings or observations that need a user decision, write `No decision queue items.` and briefly say why.
```

## Basic Finding Format

Each finding should be a compact decision candidate, not an accepted task. Include only these fields:

- **Title:** concise name for the candidate finding, normally carried in the `### F-###: <Finding title>` or `### O-###: <Observation title>` heading rather than repeated as a separate detail field.
- **ID:** stable local ID such as `F-001` for supported findings or `O-001` for observations, included in the heading and detail fields so summaries can link to it.
- **Area:** component, path, workflow, dependency, or concern affected.
- **Severity:** Critical, High, Medium, Low, or Observation, based on project pain if left alone.
- **Severity justification:** a short explanation tying the severity to the project pain if left alone. For Observation, explain why the signal is not yet supported enough to treat as a finding.
- **Confidence:** High, Medium, or Low, based on how sure the reviewer is that the finding is real and accurately characterized.
- **Evidence strength:** one or more support categories from the finding taxonomy, such as source evidence, tool output, runtime/test evidence, Git history or churn, documentation mismatch, or heuristic pattern evidence.
- **Churn signal:** cheap local churn context if available, such as `recent churn seen`, `low churn seen`, `unknown`, or `not checked`. Do not perform automated churn analysis just to fill this field.
- **Summary:** what appears to be happening in plain language.
- **Evidence:** file paths, documentation references, or command-output summaries that support the observation. If evidence is weak, state that directly, for example `Weak evidence: single sampled call site; no tests or history checked`.
- **Impact:** why the pattern could matter for maintainability, architecture, delivery speed, reliability, or future change.
- **Suggested next decisions:** concrete decision paths such as accept, reject/ignore, create an issue, document an ADR/decision, launch deeper research, revise severity, or plan/implement separately.

Do not add an overall health score, pass/fail result, or global project status label. Summarize triage with counts by severity instead. If a signal is weak, use Observation or Low confidence and state the uncertainty in the summary or evidence. Keep Observations visibly distinct from better-supported findings: Observations are context or research prompts, while findings assert project pain supported by evidence.

## Chat Summary Format

After writing the report, keep the chat response short:

```text
Report: <path/to/report.md>

Executive summary:
- Severity counts: Critical <n>, High <n>, Medium <n>, Low <n>, Observations <n>.
- Top findings: <1-3 linked IDs/titles or "no supported findings; see observations">.
- Main pain sources: <short themes or affected areas>.
- Caveats: <low-confidence or weak-evidence notes; point to observations instead of overstating them>.

These findings are decision candidates, not accepted work.
Next options: <walk through the decision queue / accept, reject, or ignore candidates / create an issue for a selected finding / document an ADR or decision / launch deeper research / revise severity with user context / plan or implement separately>
```

Keep chat to the report path, 2-4 executive-summary bullets, the decision-candidate reminder, and clear next options. The bullets may mention only the top 1-3 finding IDs/titles and should point to the report for details. Do not paste the full finding list unless the user asks.
