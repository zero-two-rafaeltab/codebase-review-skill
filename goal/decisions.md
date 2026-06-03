# Decisions

This document records product and workflow decisions made during initial goal discovery.

## Repository and distribution

- The canonical source will live in a GitHub repository under the Zero Two account.
- The future artifact will be installed as a local Hermes skill.
- The skill will not be bundled into Hermes Agent itself.
- The repository should initially document goals only and must not implement the skill yet.

## Skill shape

- The future skill should be one umbrella skill named around codebase review.
- The main skill document should be concise.
- Detailed behavior should live in progressive-disclosure reference documents.
- Language and framework playbooks should live as reference documents inside the umbrella skill, not as separate skills initially.

## Trigger distinction

- This workflow is for explicit codebase health, architecture, maintainability, refactoring, and technical-debt reviews.
- It should not replace ordinary pull request or diff review.
- If a user asks for normal PR review, existing PR-review workflows apply unless the user explicitly requests this deeper process.

## User experience

- The agent should propose an interpreted scope from the user's query.
- The user must approve the proposed scope before review work begins.
- If the user rejects the scope, the agent should adjust and ask again.
- The chat response after review should show only the executive summary and next options.
- Detailed information should live in report files with progressive disclosure.

## Discovery and action separation

- Discovery produces findings, not decisions.
- During discovery, the workflow may write report artifacts only.
- During discovery, it must not create issues, write ADRs, refactor code, commit, or post PR comments.
- Actions happen only in a later decision phase after the user chooses what to do.

## Review lifecycle

- The workflow should include an initial scope proposal and go/no-go.
- After initial approval, the workflow should create a review plan artifact.
- A sizing/cartography pass should discover how large and complex the approved scope actually is.
- The sizing/cartography pass must be delegated to a subagent, not performed by the top-level orchestrator.
- After sizing, the agent should propose a concise subagent plan and ask for go/no-go again.
- The full review should be delegated to specialist subagents.
- The top-level orchestrator should preserve context and operate on summaries, manifests, and report artifacts rather than raw repository archaeology.

## Subagents

- Subagents should use strict manifests for coordination and synthesis.
- Specialist reports may be flexible and adapted to the review lens.
- Manifests should record assigned scope, inspected files, commands run, artifacts written, findings emitted, uncertainties, out-of-scope areas, and coverage confidence.

## Scoping model

- The workflow must support full repository reviews.
- The workflow must support package or module reviews.
- The workflow must support domain and subdomain reviews.
- The workflow must support vertical workflow reviews.
- The workflow must support horizontal cross-cutting concern reviews.
- The workflow must support dependency and usage reviews.
- Reviews may need both horizontal and vertical perspectives in the same run.

## Review depth model

- The workflow should support birds-eye, standard, deep, aggressive, and relaxed review profiles.
- Standard depth is the default for vague requests such as “review this repo.”
- Aggressive reviews may surface weaker signals, but should label confidence and evidence clearly.

## Reporting model

- The report should be organized for progressive disclosure.
- The main report should show counts by severity, top findings, main pain sources, and links deeper.
- Component or domain reports should provide focused overviews and links to details.
- Individual finding pages should be decision-ready.
- Raw tool output should be isolated from summary reports.

## Severity model

- Do not use an arbitrary overall health score or status label.
- Report counts by severity instead.
- Severity means how much the issue can hurt the project if left alone.
- High severity can mean severe maintainability, architecture, or workflow pain; it does not require a confirmed production issue.
- Confidence must be separate from severity.
- Evidence strength must be separate from confidence.

## Future work

- Discovery should generally ignore planned future work unless the user explicitly provides it.
- During the decision phase, the user may revise severity based on known future work.

## Churn and hotspot analysis

- Churn should be treated as an early prioritization signal when cheaply available.
- The sizing/cartography pass should collect cheap churn and hotspot signals when Git history is available.
- Specialist agents should not manually inspect Git history file-by-file unless assigned a specific follow-up task.

## Setup workflow

- A setup workflow should exist before review execution.
- Setup is an explicit thing the user chooses to do.
- Setup may install repo-local or dev review tooling when clearly appropriate and approved.
- Setup should create predictable review documentation in the target codebase.
- Setup should create normalized review command aliases.
- Setup should verify commands by running them.
- A review command failing because it found issues is acceptable and expected.
- Setup success means commands produce useful insight, not that all checks pass.
- Setup docs should not store raw baseline outputs or stale counts.

## Normalized command runner decision

- Prefer Just if already present.
- Else prefer Make if already present.
- Else prefer npm/package scripts if already the convention.
- If no runner exists, add Just.

## CI policy

- CI integration is separate from setup.
- If no CI exists, do not create CI.
- If CI exists, inspect and document possible integration, then ask for explicit approval before modifying CI.
