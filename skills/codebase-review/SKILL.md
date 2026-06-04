---
name: codebase-review
description: Use when performing a focused manual codebase health review. Guides intake, interpreted scope approval, strict read-only discovery, basic report writing, concise executive-summary handoff, and user-controlled post-discovery decisions without complex delegation.
version: 0.1.0
author: Zero Two / Rafael
license: MIT
metadata:
  hermes:
    tags: [codebase-review, architecture, maintainability, manual-review]
    related_skills: []
---

# Codebase Review

## Overview

Use this skill to run a focused manual codebase health review. It guides the reviewer through intake, scope confirmation, read-only discovery, a basic report artifact, and a concise executive summary for the user.

Discovery and decision-making are separate. During discovery, stay read-only except for report artifacts. After discovery, help the user decide what to do with findings without automatically taking action.

## When to Use

Use this skill when the user asks for a codebase health, architecture, maintainability, refactoring-opportunity, pain-spot, or risk review.

Typical trigger phrases include:

- "review this codebase";
- "look for architecture risks";
- "find maintainability pain";
- "what should we refactor?";
- "where are the testing or documentation gaps?";
- "inspect this package/domain/workflow/dependency usage."

Do not use this workflow for:

- implementing a requested code change;
- debugging a specific failing test or production bug;
- reviewing a PR diff as a code reviewer;
- automated setup of review tooling;
- complex delegated review planning;
- language-specific review playbooks;
- CI integration;
- creating issues, ADRs, commits, PR comments, or code changes during discovery.

Those are separate workflows.

## Intake and Interpreted Scope

Before discovery work begins, propose an interpreted scope in chat and ask the user for go/no-go. Do not start expensive reading, command execution, report writing, or subagent work until the user approves.

The scope proposal should be short and explicit:

```text
Proposed review scope:
- Target: <repo/path/package/domain/workflow/concern>
- Scope shape: <one of the supported scope shapes>
- Review depth: focused manual pass
- Discovery actions: read files/docs and run safe inspection commands only as needed
- Report artifact: <planned report path>
- Side-effect boundary: no code changes, commits, issues, ADRs, or PR comments during discovery

Go/no-go?
```

If the user says no or corrects the scope, revise the proposal and ask again.
If the user has already given an explicit scope and approval in the same request, restate the interpreted scope briefly before proceeding.

## Supported Scope Shapes

Use simple scope language. This workflow supports these scope shapes:

| Scope shape | Use when | Example phrasing |
| --- | --- | --- |
| Full repository | The user wants a broad health pass across the whole repo. | "Full repository review of `~/app`." |
| Package or module | The user names a package, directory, crate, service, or module. | "Package/module review of `src/billing`." |
| Domain | The user names a business or technical domain that may span files. | "Domain review of authentication and sessions." |
| Workflow | The user asks about an end-to-end user or system flow. | "Workflow review of image upload through processing." |
| Horizontal concern | The user asks about a cross-cutting quality or architecture concern. | "Horizontal review of testing boundaries." |
| Dependency/usage review | The user asks how a dependency, API, or internal abstraction is used. | "Dependency usage review of Prisma access patterns." |

Keep ambiguous requests small enough to complete manually. Prefer a narrow package, domain, workflow, or concern over an unbounded deep review.

## Manual Review Flow

1. **Identify whether this workflow applies.** Confirm the request is a codebase health, architecture, maintainability, refactoring, pain-spot, or risk review rather than implementation/debugging.
2. **Propose an interpreted scope.** Name the target, scope shape, depth, expected artifact path, and discovery side-effect boundary.
3. **Ask for go/no-go before discovery.** Wait for approval before expensive review work. If the user rejects the proposal, adjust and ask again.
4. **Inspect the repository context.** Build a small map of only the approved scope before judging it. See the inspection checklist below.
5. **Do a focused manual discovery pass.** Read the most relevant files and note architecture shape, maintainability pain, refactoring opportunities, and risks that could compound later. Keep the pass small enough to complete manually.
6. **Write a basic report artifact.** Put the report in a predictable local path such as `reviews/<YYYY-MM-DD>-codebase-review-<scope-slug>.md` unless the project already has a clearer review folder convention. Start the report with triage information: severity counts, top findings, main pain sources, notable caveats, and links or anchors to detailed findings.
7. **Reply with a concise executive summary.** Keep chat short and progressively disclosed. Include severity counts, 1-3 top findings or pain sources, notable confidence/evidence caveats, and a link or pointer to the report. Leave detailed evidence in the artifact. Present findings as decision candidates, not accepted work.
8. **Offer a post-discovery decision phase.** If the user wants to continue after the report, discuss findings one by one or as a short queue. Do not create issues, ADRs, commits, PR comments, or code changes unless the user explicitly chooses a separate action workflow for selected findings.

## Repository and Context Inspection

After scope approval, start with a lightweight orientation pass. Keep this bounded to the approved target; do not turn it into a whole-repository mapping exercise unless the approved scope is the whole repository.

Inspect only what helps answer the approved review question:

- **Repository shape:** top-level directories, package/service boundaries, generated or vendored areas to ignore, and where the approved target lives.
- On repositories with local build outputs or dependencies present, prefer tracked-file lists, manifest-aware searches, or ignore-aware searches before broad file listings. Explicitly exclude generated/dependency areas such as `node_modules`, `dist`, `build`, `.next`, and coverage outputs unless they are part of the approved scope.
- **Project intent:** README, docs, comments, package metadata, or other nearby context that explains what the component is meant to do.
- **Build and dependency hints:** manifests, lockfiles, module files, workspace config, and scripts that reveal frameworks, entry points, or dependency boundaries.
- **Runtime and data flow clues:** routes, handlers, jobs, CLI entry points, configuration, database/schema files, adapters, or integration points connected to the approved scope.
- **Tests and examples:** test files, fixtures, examples, or snapshots that show expected behavior and coverage gaps.
- **Recent local context when cheap:** git status and recent commits can reveal active churn, but do not rely on churn as a required scoring system. Record cheap churn context as a churn signal on each finding; use `unknown` or `not checked` when it was unavailable or not worth checking.

Safe inspection commands are allowed when useful, for example `git status --short`, `git log --oneline -5`, file searches, tree/listing commands, or inspection of existing test/build command names in manifests or scripts. Treat failures or missing commands as context; do not fix them during discovery.

## Focused Manual Discovery Procedure

Use this procedure for small repositories, a narrow package/module, a single workflow, or a horizontal concern. The goal is a practical human-readable review, not exhaustive automation.

1. **Restate the approved scope in your notes.** Include target paths or concerns, what is intentionally out of scope, and the side-effect boundary.
2. **Create a tiny component map.** Identify the main entry points, core modules, dependencies, data/configuration flows, tests, and documentation relevant to the scope.
3. **Trace one or two representative paths.** Follow a typical request, command, job, data operation, or dependency usage through the code. For horizontal concerns, sample a few representative call sites instead.
4. **Look for architecture and boundary signals.** Note unclear ownership, tangled dependencies, circular knowledge, excessive coupling, leaky abstractions, duplicated patterns, or places where policy and implementation are hard to separate.
5. **Look for maintainability and refactoring signals.** Note hard-to-change files, repeated logic, naming mismatches, oversized modules, hidden conventions, fragile configuration, missing seams for tests, or code that requires broad edits for small behavior changes.
6. **Look for pain spots and future-risk prevention.** Identify issues likely to slow future work, such as undocumented invariants, weak error handling, untested critical paths, brittle integration boundaries, dependency lock-in, or unclear extension points. Do not increase severity for possible future work unless the user explicitly supplied that roadmap, migration, business, or operational context during intake or scope approval.
7. **Classify findings without mixing concepts.** Record evidence with file paths, docs, command summaries, and a cheap churn signal when available. Use the finding taxonomy below to label severity, confidence, and evidence strength separately. Route weak signals to Observation or low-confidence findings instead of overstating them.
8. **Keep optional delegation light.** If the scope has a clearly separable area, you may ask another agent for a small read-only inspection of that area, but do not require or design a complex delegation plan.
9. **Synthesize into decision candidates.** Group related observations, explain impact, and suggest next decisions such as accept, reject, ignore, research, document, create an issue, or plan a refactor.

## Discovery Side-Effect Boundary

Allowed during discovery:

- read files and repository metadata;
- run safe inspection commands;
- write the agreed review report artifact.

Not allowed during discovery:

- code edits;
- commits or branches;
- GitHub issues;
- ADRs or durable project decisions;
- PR comments;
- dependency, CI, or tooling changes;
- setup workflows or installing review tooling;
- separate sizing passes or complex delegated review plans.

If the user wants action after the report, start a separate post-discovery decision or implementation workflow.

## Finding Taxonomy

Use this taxonomy to make findings easier to compare and decide on. Keep the concepts separate:

- **Severity** is the project pain if the finding is left alone. It includes architecture and maintainability pain, delivery drag, operational risk, reliability risk, security/data-loss risk, and future-change cost. It is not a measure of reviewer certainty.
- **Confidence** is how sure the reviewer is that the finding is real and accurately characterized. Confidence is separate from severity: a potentially severe concern can still be low confidence if the review only saw partial evidence.
- **Evidence strength** is the kind and directness of support behind the finding. Evidence strength is separate from confidence: strong evidence can still be interpreted cautiously, and weak evidence should not be presented as proof.

### Severity levels

| Severity | Use when the pain if left alone is... |
| --- | --- |
| Critical | Likely to cause major project harm soon or block essential work, such as data loss/security exposure, severe production risk, or an architectural constraint that already prevents necessary delivery. |
| High | Likely to create serious reliability, maintainability, architecture, or delivery pain across an important path or team if not addressed. |
| Medium | Meaningful but bounded pain that will slow change, increase defects, or compound in a specific component, workflow, or concern. |
| Low | Minor, localized, or easily reversible pain that is worth noting but unlikely to drive near-term decisions by itself. |
| Observation | A weak, early, contextual, or informational signal that may deserve attention but is not strong enough to treat as a proven issue. |

Prefer the lowest severity that honestly describes the expected project pain. Do not raise severity because evidence is plentiful or because the reviewer feels certain; use confidence and evidence strength for that. Do not raise severity for speculative future work or roadmap assumptions unless that context was explicitly supplied by the user during intake or scope approval.

Use `Observation` when the item is primarily an early signal, useful context, a pattern worth watching, or a research prompt. Use Low/Critical severity levels only for findings that are supported strongly enough to describe actual project pain if left alone.

### Confidence levels

| Confidence | Use when... |
| --- | --- |
| High | Multiple signals or direct inspection make the finding very likely to be real and accurately scoped. |
| Medium | The finding is plausible and supported, but some scope, impact, or intent is uncertain. |
| Low | The signal may be real, but the review lacks enough context to claim it confidently. Low-confidence items should usually be phrased as candidates for research. |

### Evidence strength categories

List the strongest applicable evidence category or categories for each finding:

- **Source evidence:** direct code/configuration/schema references show the behavior, dependency, coupling, missing boundary, or maintainability problem.
- **Tool output:** safe inspection commands, linters, type checkers, dependency analyzers, test runners, or other existing project tools reported relevant signal.
- **Runtime/test evidence:** executed tests, examples, local runs, logs, fixtures, or reproducible behavior show the issue or gap.
- **Git history or churn:** recent commits, blame, file churn, or repeated changes suggest active pain or unstable ownership. Use only when cheap to inspect; do not require automated churn analysis. If cheap churn data is not available, the finding's churn signal may be `unknown` or `not checked`.
- **Documentation mismatch:** README, docs, comments, examples, or API contracts disagree with the implementation or with each other.
- **Heuristic pattern evidence:** reviewer-recognized maintainability or architecture pattern, such as duplication, hidden conventions, oversized modules, tangled ownership, or missing seams, without stronger direct proof yet.

Weak signals, especially heuristic-only signals, should be routed to Observation or labeled as low confidence unless supported by stronger evidence. Do not present a hunch, single ambiguous example, or unexplored pattern as a proven issue. When evidence is weak, say so directly in the Evidence field instead of implying there are references you did not inspect.

## Post-Discovery Decision Phase

The post-discovery decision phase begins only after the report artifact is written and the executive summary is delivered. Its purpose is to help the user interpret and triage findings; it is not part of discovery and should not be treated as approval to mutate the repository or project systems.

In this phase, keep each finding as a decision candidate until the user chooses what to do. Useful user choices include:

| Decision | Meaning |
| --- | --- |
| Accept | The user agrees the finding is valid enough to track or act on later. |
| Reject | The user disagrees with the finding or considers it invalid. |
| Ignore | The user accepts the observation may be true but chooses no follow-up now. |
| Research | More investigation is needed before deciding. |
| Document | Capture context or rationale in docs or a decision record in a separate workflow. |
| Create an issue | Select a finding for issue creation; open or draft the tracking issue only in a separate workflow after explicit user approval. |
| Plan a refactor | Turn a selected finding into a refactoring plan in a separate planning workflow. |

During decision discussion:

- do not automatically create GitHub issues, ADRs, docs, commits, PR comments, or code changes from findings;
- keep discovery assessment separate from the user's decision context;
- if the user adds business, roadmap, ownership, migration, or operational context, record it as user-provided decision context rather than rewriting the original discovery evidence;
- if the user adjusts priority or severity, including after adding future-work or business context, label it as user-adjusted decision severity/context, distinct from the discovery assessment;
- when an action is chosen, start or hand off to the appropriate separate workflow for that selected action.

## Out of Scope for This Workflow

Do not expand a manual discovery review into other behavior unless the user explicitly starts a separate workflow. In particular, do not add or require:

- setup tooling for target repositories;
- normalized review commands;
- language-specific playbooks;
- separate sizing or mapping workflows;
- complex delegated review plans;
- CI integration;
- arbitrary overall health scores or status labels such as "pass", "fail", "healthy", or "unhealthy".

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
- include a severity justification, evidence references or an explicit weak-evidence statement, and a churn signal on each finding.

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
- Suggested next decisions: <accept/reject/ignore/research/document/create an issue/plan a refactor>

Use `F-###` IDs for supported findings and `O-###` IDs for observations. Keep these IDs stable within the report so the executive summary, triage overview, and chat response can point to detailed sections without copying all details into chat.

## Coverage gaps

- <what a deeper review should inspect later>

## Decision queue

Findings above are candidates for discussion. They are not approved issues, ADRs, or implementation work.
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
- **Suggested next decisions:** concrete decision paths such as accept, reject, ignore, research, document, create an issue, or plan a refactor.

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
Next options: <walk through findings / accept, reject, or ignore candidates / research deeper / document context / create an issue for a selected finding / plan a refactor>
```

Keep chat to the report path, 2-4 executive-summary bullets, the decision-candidate reminder, and clear next options. The bullets may mention only the top 1-3 finding IDs/titles and should point to the report for details. Do not paste the full finding list unless the user asks.

## Verification Checklist

- [ ] The request matched a codebase health review trigger rather than implementation/debugging.
- [ ] The interpreted scope was proposed before discovery.
- [ ] The scope proposal used one of the supported scope shapes.
- [ ] User go/no-go was received before discovery.
- [ ] Discovery stayed read-only except for the report artifact.
- [ ] No issues, ADRs, commits, PR comments, or code changes were created during discovery.
- [ ] No setup, separate sizing/mapping, language-playbook, or CI workflow was introduced.
- [ ] A basic report file was written.
- [ ] The report overview used severity counts instead of an arbitrary overall health status.
- [ ] The report overview linked or pointed to individual finding details using stable finding IDs, heading anchors, or section links.
- [ ] Chat response stayed concise and pointed to the report.
- [ ] Chat summary used progressive disclosure: severity counts, top findings or pain sources, caveats, and next options without copying full finding details.
- [ ] Findings were framed as decision candidates with severity, confidence, and evidence strength kept separate.
- [ ] Each finding included severity, severity justification, confidence, evidence strength, churn signal, impact, and suggested next decisions.
- [ ] Evidence was cited with file paths/docs/command summaries, or weak evidence was clearly labeled.
- [ ] Observations were distinguished from better-supported findings.
- [ ] Weak signals were routed to Observation or Low confidence rather than presented as proven issues.
- [ ] Speculative future work did not affect discovery severity unless the user explicitly supplied that context during intake or scope approval.
- [ ] No arbitrary overall health score or global status label was introduced.
- [ ] Any post-discovery discussion kept user decisions separate from discovery assessment and allowed user-adjusted severity after added future-work or business context.
- [ ] No issues, ADRs, docs, refactor plans, or implementation work were created automatically from findings.
