---
name: codebase-review
description: Use when performing a focused manual codebase health review. Guides intake, interpreted scope approval, strict read-only discovery, report writing, concise executive-summary handoff, and user-controlled post-discovery decisions.
version: 0.1.0
author: Zero Two / Rafael
license: MIT
metadata:
  hermes:
    tags: [codebase-review, architecture, maintainability, manual-review]
    related_skills: []
    linked_files:
      - references/workflow.md
      - references/discovery.md
      - references/finding-taxonomy.md
      - references/reporting.md
      - references/decision-phase.md
      - references/subagent-manifest.md
---

# Codebase Review

## Purpose

Use this skill to run a focused manual codebase health review. It is an orchestration entry point: keep chat concise, load detailed references only when needed, delegate the post-approval cartography checkpoint, present a lightweight specialist review plan for approval, and preserve the discovery/decision boundary.

Discovery and decision-making are separate. During discovery, stay read-only except for the agreed report artifact. After discovery, help the user decide what to do with findings without automatically taking action.

## Progressive reference map

Load the smallest reference that matches the current step:

- [Workflow](references/workflow.md) — when to use the skill, intake/scope approval, supported scope shapes, manual review flow, discovery side-effect boundary, out-of-scope behavior, and the verification checklist.
- [Discovery guidance](references/discovery.md) — bounded repository/context inspection and focused manual discovery procedure.
- [Finding taxonomy](references/finding-taxonomy.md) — severity, confidence, evidence strength, churn signals, and weak-signal handling.
- [Report and chat output contract](references/reporting.md) — report artifact path, minimum report contract, report skeleton, finding fields, decision queue, and concise chat summary format.
- [Decision phase](references/decision-phase.md) — post-discovery decision queue behavior and user-controlled follow-up options.
- [Subagent manifest contract](references/subagent-manifest.md) — compact Markdown audit trail for delegated cartography and specialist review outputs, distinguishing inspected evidence from inherited or unverified context.

## Use this workflow for

Use this skill when the user asks for a codebase health, architecture, maintainability, refactoring-opportunity, pain-spot, testing/documentation-gap, risk, workflow, domain, package/module, horizontal-concern, or dependency/usage review.

Do not use it for implementation, debugging a specific failure, PR-diff review, setup/tooling installation, CI integration, complex delegated planning beyond the approved lightweight specialist review plan, language-specific playbooks, or creating issues/ADRs/commits/PR comments/code changes during discovery. Those are separate workflows.

## Non-negotiable operating rules

1. **Intake first.** Before discovery begins, propose an interpreted scope in chat and ask for go/no-go unless the user already gave explicit scope and approval in the same request. Include target, supported scope shape, focused manual depth, discovery actions, planned report path, and the side-effect boundary.
2. **Stay within approved scope.** Keep ambiguous requests small enough to complete manually. Prefer a narrow package, domain, workflow, concern, or dependency usage review over an unbounded deep review.
3. **Run delegated cartography after approval.** Once the initial scope is approved, ask a subagent for a lightweight read-only cartography/sizing pass using the contract in [Workflow](references/workflow.md), including the relevant [subagent manifest](references/subagent-manifest.md) subset. Work from its summary and manifest; do not make the top-level orchestrator do deep raw-file archaeology itself.
4. **Ask for a second go/no-go on the review plan.** Convert the cartography summary into a concise specialist review plan proposal before full specialist/manual review begins. Name review slices/lenses, expected artifacts, coverage gaps, and HITL questions. If the user rejects or adjusts it, revise the plan and ask again.
5. **Discovery is read-only.** During discovery, only read files/repository metadata, run safe inspection commands, use the delegated cartography summary and approved lightweight review plan, and write the agreed Markdown review report. Do not edit code, create issues, write ADRs, commit, comment on PRs, change dependencies/tooling/CI, install review tooling, or introduce setup workflows, language playbooks, advanced synthesis, or complex delegated review plans.
6. **Inspect before judging.** Use the cartography summary, returned manifest, and approved review plan to choose representative paths, then perform bounded manual discovery and record architecture, maintainability, testing, documentation, and risk signals with concrete evidence.
7. **Classify carefully.** Keep severity, confidence, and evidence strength separate. Severity is project pain if left alone; confidence is certainty; evidence strength is the kind/directness of support. Route weak signals to `Observation` or low-confidence findings and label uncertainty directly.
8. **Write exactly one primary report artifact.** Prefer `reviews/<YYYY-MM-DD>-codebase-review-<scope-slug>.md` unless the project has a clearer convention. The report is the durable handoff; chat should point to it instead of copying details.
9. **Use progressive disclosure in outputs.** Reports begin with triage information; chat contains only the report path, 2-4 executive-summary bullets, key caveats, a decision-candidate reminder, and next options.
10. **Findings are decision candidates.** Do not treat findings as accepted work. After discovery, offer a decision phase where the user can accept, reject/ignore, create an issue, document an ADR/decision, launch deeper research, revise severity with user context, or plan/implement separately.
11. **No global health labels.** Use counts by severity and linked finding IDs instead of arbitrary overall health scores or labels such as "pass", "fail", "healthy", or "unhealthy".

## Minimal run sequence

1. Confirm the request fits this skill.
2. Propose interpreted scope and wait for go/no-go.
3. Load [Workflow](references/workflow.md), delegate the cartography checkpoint, and keep the returned summary and manifest as the top-level map.
4. Present a lightweight specialist review plan from the cartography summary and wait for the second go/no-go; revise and ask again if needed.
5. Load [Discovery guidance](references/discovery.md) and perform bounded read-only discovery from the cartography summary and approved plan.
6. Load [Finding taxonomy](references/finding-taxonomy.md) while classifying findings and weak signals.
7. Load [Report and chat output contract](references/reporting.md), write the report artifact, and reply with the concise chat summary.
8. If the user wants follow-up, load [Decision phase](references/decision-phase.md) and walk the decision queue without mutating the project unless a separate explicitly approved workflow begins.
