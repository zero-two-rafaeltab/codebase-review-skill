---
name: codebase-review
description: Use when performing a focused manual codebase health review or when explicitly setting up codebase-review support for a repository. Guides review intake, setup intake, approval gates, strict read-only discovery, report writing, concise executive-summary handoff, and user-controlled post-discovery decisions.
version: 0.1.0
author: Zero Two / Rafael
license: MIT
metadata:
  hermes:
    tags: [codebase-review, architecture, maintainability, manual-review]
    related_skills: []
    linked_files:
      - references/workflow.md
      - references/setup.md
      - references/discovery.md
      - references/finding-taxonomy.md
      - references/reporting.md
      - references/decision-phase.md
      - references/subagent-manifest.md
---

# Codebase Review

## Purpose

Use this skill to run a focused manual codebase health review or to start an explicit setup intake for adding review support to a repository. It is an orchestration entry point: keep chat concise, distinguish setup requests from review/discovery requests, load detailed references only when needed, delegate the post-approval cartography checkpoint for reviews, present a lightweight specialist review plan for approval, launch approved read-only specialist slices when useful, synthesize their manifests/findings, and preserve the setup/review/discovery/decision boundaries.

Setup, discovery, and decision-making are separate. Setup starts with a setup-specific scope proposal and approval gate before any mutation. During discovery, stay read-only except for the agreed report artifact and keep setup/tooling changes out of scope. After discovery, help the user decide what to do with findings without automatically taking action.

## Progressive reference map

Load the smallest reference that matches the current step:

- [Workflow](references/workflow.md) — when to use the skill, request classification, review intake/scope approval, setup entry point, supported review scope shapes, manual review flow, discovery side-effect boundary, out-of-scope behavior, and the verification checklist.
- [Review setup](references/setup.md) — explicit setup request triggers, setup proposal template, pre-approval no-mutation rule, allowed/forbidden setup changes, and setup verification checklist.
- [Discovery guidance](references/discovery.md) — bounded repository/context inspection and focused manual discovery procedure.
- [Finding taxonomy](references/finding-taxonomy.md) — severity, confidence, evidence strength, churn signals, and weak-signal handling.
- [Report and chat output contract](references/reporting.md) — report artifact path, minimum report contract, report skeleton, finding fields, decision queue, and concise chat summary format.
- [Decision phase](references/decision-phase.md) — post-discovery decision queue behavior and user-controlled follow-up options.
- [Subagent manifest contract](references/subagent-manifest.md) — compact Markdown audit trail for delegated cartography and specialist review outputs, distinguishing inspected evidence from inherited or unverified context.

## Use this workflow for

Use this skill when the user asks for either:

- a codebase health, architecture, maintainability, refactoring-opportunity, pain-spot, testing/documentation-gap, risk, workflow, domain, package/module, horizontal-concern, or dependency/usage review; or
- explicit setup of codebase-review support for a repository, such as “set up codebase review for this repo”, “add review docs/commands”, “prepare this repo for future codebase reviews”, or similar setup/tooling/documentation language.

Do not use the review/discovery workflow for implementation, debugging a specific failure, PR-diff review, setup/tooling installation, CI integration, complex delegated planning beyond the approved lightweight specialist review plan, language-specific playbooks, or creating issues/ADRs/commits/PR comments/code changes during discovery. Setup requests must go through the separate setup intake and approval gate; CI changes remain forbidden unless a later issue explicitly adds that capability.

## Non-negotiable operating rules

1. **Classify the request first.** Decide whether the user is asking for review discovery or explicit review setup. If setup/tooling/documentation/commands are requested, load [Review setup](references/setup.md), present the setup proposal, and do not run review discovery.
2. **Review intake first.** Before discovery begins, propose an interpreted review scope in chat and ask for go/no-go unless the user already gave explicit scope and approval in the same request. Include target, supported scope shape, focused manual depth, discovery actions, planned report path, and the side-effect boundary.
3. **Setup intake first.** Before setup inspection or mutation begins, propose a setup scope in chat and ask for go/no-go. The proposal must name target repository, planned inspection, possible file/doc/command changes, forbidden production/CI changes, expected output artifacts, and the approval gate. Do not install tooling, add commands, edit docs, touch CI, or make other mutations before approval.
4. **Stay within approved scope.** Keep ambiguous requests small enough to complete manually. Prefer a narrow package, domain, workflow, concern, or dependency usage review over an unbounded deep review.
5. **Run delegated cartography after review approval.** Once the initial review scope is approved, ask a subagent for a lightweight read-only cartography/sizing pass using the contract in [Workflow](references/workflow.md), including the relevant [subagent manifest](references/subagent-manifest.md) subset. Work from its summary and manifest; do not make the top-level orchestrator do deep raw-file archaeology itself.
6. **Ask for a second go/no-go on the review plan.** Convert the cartography summary into a concise specialist review plan proposal before full specialist/manual review begins. Name review slices/lenses, expected artifacts, coverage gaps, and HITL questions. If the user rejects or adjusts it, revise the plan and ask again.
7. **Launch only approved read-only specialist slices.** After the second go/no-go, specialist subagents may review only their assigned scope/lens from the approved plan, must stay inside the discovery side-effect boundary, and must return slice summaries, findings/observations, and manifests. Do not add unapproved lenses or a deeper delegation tree.
8. **Synthesize from manifests and reports.** Use specialist manifests, slice reports, and emitted findings as the primary synthesis inputs. The top-level orchestrator may verify narrow facts, but must not redo broad raw-file archaeology. For duplicates or conflicts, preserve uncertainty, cite the contributing source manifests/findings, and avoid overstating confidence.
9. **Inspect before judging.** Use the cartography summary, returned manifests, and approved review plan to choose any necessary representative follow-up paths, then record architecture, maintainability, testing, documentation, and risk signals with concrete evidence.
10. **Classify carefully.** Keep severity, confidence, and evidence strength separate. Severity is project pain if left alone; confidence is certainty; evidence strength is the kind/directness of support. Route weak signals to `Observation` or low-confidence findings and label uncertainty directly.
11. **Write exactly one primary report artifact.** Prefer `reviews/<YYYY-MM-DD>-codebase-review-<scope-slug>.md` unless the project has a clearer convention. The report is the durable handoff; chat should point to it instead of copying details.
12. **Use progressive disclosure in outputs.** Reports begin with triage information; chat contains only the report path, 2-4 executive-summary bullets, key caveats, a decision-candidate reminder, and next options.
13. **Findings are decision candidates.** Do not treat findings as accepted work. After discovery, offer a decision phase where the user can accept, reject/ignore, create an issue, document an ADR/decision, launch deeper research, revise severity with user context, or plan/implement separately.
14. **No global health labels.** Use counts by severity and linked finding IDs instead of arbitrary overall health scores or labels such as "pass", "fail", "healthy", or "unhealthy".

## Minimal run sequence

1. Confirm the request fits this skill and classify it as review discovery or explicit review setup.
2. **Setup path:** for setup requests, load [Review setup](references/setup.md), propose setup scope, and wait for go/no-go before any inspection or mutation. Continue only inside the approved setup scope, then stop unless the user starts a separate review workflow.
3. **Review path:** for review requests, propose interpreted scope and wait for go/no-go.
4. Load [Workflow](references/workflow.md), delegate the cartography checkpoint, and keep the returned summary and manifest as the top-level map.
5. Present a lightweight specialist review plan from the cartography summary and wait for the second go/no-go; revise and ask again if needed.
6. After approval, launch the plan's read-only specialist review subagents or perform the approved slices directly when delegation is unnecessary; require specialist manifests and scoped findings/observations.
7. Load [Discovery guidance](references/discovery.md) and [Finding taxonomy](references/finding-taxonomy.md) as needed, then synthesize from cartography plus specialist manifests/reports/findings, doing only narrow verification rather than broad archaeology.
8. Load [Report and chat output contract](references/reporting.md), write the report artifact with manifest-backed findings, duplicate/conflict caveats, and a decision queue, then reply with the concise chat summary.
9. If the user wants follow-up, load [Decision phase](references/decision-phase.md) and walk the decision queue without mutating the project unless a separate explicitly approved workflow begins.
