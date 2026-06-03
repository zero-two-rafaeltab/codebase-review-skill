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
6. **Write a basic report artifact.** Put the report in a predictable local path such as `reviews/<YYYY-MM-DD>-codebase-review-<scope-slug>.md` unless the project already has a clearer review folder convention.
7. **Reply with a concise executive summary.** Keep chat short. Link or point to the report file. Present findings as decision candidates, not accepted work.
8. **Offer a post-discovery decision phase.** If the user wants to continue after the report, discuss findings one by one or as a short queue. Do not create issues, ADRs, commits, PR comments, or code changes unless the user explicitly chooses a separate action workflow for selected findings.

## Repository and Context Inspection

After scope approval, start with a lightweight orientation pass. Keep this bounded to the approved target; do not turn it into a whole-repository mapping exercise unless the approved scope is the whole repository.

Inspect only what helps answer the approved review question:

- **Repository shape:** top-level directories, package/service boundaries, generated or vendored areas to ignore, and where the approved target lives.
- **Project intent:** README, docs, comments, package metadata, or other nearby context that explains what the component is meant to do.
- **Build and dependency hints:** manifests, lockfiles, module files, workspace config, and scripts that reveal frameworks, entry points, or dependency boundaries.
- **Runtime and data flow clues:** routes, handlers, jobs, CLI entry points, configuration, database/schema files, adapters, or integration points connected to the approved scope.
- **Tests and examples:** test files, fixtures, examples, or snapshots that show expected behavior and coverage gaps.
- **Recent local context when cheap:** git status and recent commits can reveal active churn, but do not rely on churn as a required scoring system.

Safe inspection commands are allowed when useful, for example `git status --short`, `git log --oneline -5`, file searches, tree/listing commands, or inspection of existing test/build command names in manifests or scripts. Treat failures or missing commands as context; do not fix them during discovery.

## Focused Manual Discovery Procedure

Use this procedure for small repositories, a narrow package/module, a single workflow, or a horizontal concern. The goal is a practical human-readable review, not exhaustive automation.

1. **Restate the approved scope in your notes.** Include target paths or concerns, what is intentionally out of scope, and the side-effect boundary.
2. **Create a tiny component map.** Identify the main entry points, core modules, dependencies, data/configuration flows, tests, and documentation relevant to the scope.
3. **Trace one or two representative paths.** Follow a typical request, command, job, data operation, or dependency usage through the code. For horizontal concerns, sample a few representative call sites instead.
4. **Look for architecture and boundary signals.** Note unclear ownership, tangled dependencies, circular knowledge, excessive coupling, leaky abstractions, duplicated patterns, or places where policy and implementation are hard to separate.
5. **Look for maintainability and refactoring signals.** Note hard-to-change files, repeated logic, naming mismatches, oversized modules, hidden conventions, fragile configuration, missing seams for tests, or code that requires broad edits for small behavior changes.
6. **Look for pain spots and future-risk prevention.** Identify issues likely to slow future work, such as undocumented invariants, weak error handling, untested critical paths, brittle integration boundaries, dependency lock-in, or unclear extension points.
7. **Separate findings from uncertainties.** Record evidence with file paths, docs, or command summaries. Label weak signals as observations or questions instead of overstating them.
8. **Keep optional delegation light.** If the scope has a clearly separable area, you may ask another agent for a small read-only inspection of that area, but do not require or design a complex delegation plan.
9. **Synthesize into decision candidates.** Group related observations, explain impact, and suggest next discussion options such as accept, reject, ignore, research, document, create an issue, plan a refactor, or fix later.

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
| Create an issue | Open or draft a selected tracking issue only after explicit user approval. |
| Plan a refactor | Turn a selected finding into a refactoring plan in a separate planning workflow. |

During decision discussion:

- do not automatically create GitHub issues, ADRs, docs, commits, PR comments, or code changes from findings;
- keep discovery assessment separate from the user's decision context;
- if the user adds business, roadmap, ownership, migration, or operational context, record it as user-provided decision context rather than rewriting the original discovery evidence;
- if the user adjusts priority or severity, label it as user-adjusted decision severity/context, distinct from the discovery assessment;
- when an action is chosen, start or hand off to the appropriate separate workflow for that selected action.

Do not introduce advanced finding metadata in this phase. Keep the discussion lightweight: finding, evidence, impact, user decision, and optional user-provided context are enough.

## Out of Scope for This Workflow

Do not expand a manual discovery review into other behavior unless the user explicitly starts a separate workflow. In particular, do not add or require:

- setup tooling for target repositories;
- normalized review commands;
- language-specific playbooks;
- separate sizing or mapping workflows;
- complex delegated review plans;
- CI integration;
- advanced severity, confidence, or evidence metadata.

## Basic Report Artifact

Write one Markdown report for each review. Prefer this path pattern unless the repository already has an obvious review artifact convention:

```text
reviews/<YYYY-MM-DD>-codebase-review-<scope-slug>.md
```

Use a short lowercase scope slug such as `full-repo`, `auth-workflow`, or `src-billing`. If a same-day report already exists for the same scope, append a short suffix such as `-2` or `-1430`.

The report is the durable handoff. Chat should point to it rather than copying the full details.

Minimum output contract:

- create exactly one primary Markdown report artifact for the review;
- include a 2-4 bullet executive summary near the top of the report;
- include compact findings or observations using the basic finding fields below;
- state in the report that findings are decision candidates, not accepted work;
- keep advanced severity, confidence, scoring, and evidence-strength metadata out of the artifact.

## Basic Report Skeleton

Use this skeleton for the review artifact:

```markdown
# Codebase Review — <scope>

Date: <YYYY-MM-DD>
Scope: <approved scope>
Mode: Focused manual review

## Executive summary

- <top observation or finding>
- <top risk or pain spot>
- <recommended discussion focus>

Keep this section to 2-4 bullets. State what matters most, why it matters, and what discussion would unblock a decision.

## Scope and method

- Reviewed: <paths, docs, commands>
- Not reviewed: <known gaps>
- Side effects: report artifact only

## Findings / observations

### <Finding title>

- Area: <component, path, or concern>
- Summary: <what appears to be happening>
- Evidence: <file paths, command output summaries, or doc references>
- Impact: <why this might matter>
- Suggested next discussion: <accept/reject/ignore/research/document/create issue/plan refactor/fix later>

## Coverage gaps

- <what a deeper review should inspect later>

## Decision queue

Findings above are candidates for discussion. They are not approved issues, ADRs, or implementation work.
```

## Basic Finding Format

Each finding should be a compact decision candidate, not an accepted task. Include only these fields:

- **Title:** concise name for the candidate finding.
- **Area:** component, path, workflow, dependency, or concern affected.
- **Summary:** what appears to be happening in plain language.
- **Evidence:** file paths, documentation references, or command-output summaries that support the observation.
- **Impact:** why the pattern could matter for maintainability, architecture, delivery speed, reliability, or future change.
- **Suggested next discussion:** a concrete decision path such as accept, reject, ignore, research, document, create an issue, plan a refactor, or fix later.

Do not add severity, confidence, evidence-strength, scoring, or other advanced metadata in this workflow. If a signal is weak, say so in the summary or evidence instead of introducing a richer rubric.

## Chat Summary Format

After writing the report, keep the chat response short:

```text
Report: <path/to/report.md>

Executive summary:
- <most important observation>
- <most important risk or pain spot>
- <recommended discussion focus>

These findings are decision candidates, not accepted work.
Next options: <walk through findings / accept, reject, or ignore candidates / research deeper / document context / create selected issues / plan a refactor>
```

Keep chat to the report path, 2-4 executive-summary bullets, the decision-candidate reminder, and clear next options. Do not paste the full finding list unless the user asks.

## Verification Checklist

- [ ] The request matched a codebase health review trigger rather than implementation/debugging.
- [ ] The interpreted scope was proposed before discovery.
- [ ] The scope proposal used one of the supported scope shapes.
- [ ] User go/no-go was received before discovery.
- [ ] Discovery stayed read-only except for the report artifact.
- [ ] No issues, ADRs, commits, PR comments, or code changes were created during discovery.
- [ ] No setup, separate sizing/mapping, language-playbook, or CI workflow was introduced.
- [ ] A basic report file was written.
- [ ] Chat response stayed concise and pointed to the report.
- [ ] Findings were framed as decision candidates.
- [ ] Any post-discovery discussion kept user decisions separate from discovery assessment.
- [ ] No issues, ADRs, docs, refactor plans, or implementation work were created automatically from findings.
