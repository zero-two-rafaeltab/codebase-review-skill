# Codebase Review — wallpaperdb full repository dogfood pass

Date: 2026-06-03
Scope: Focused full-repository review of `/home/rafaeltab/wallpaperdb`, intentionally small/narrow for Phase 1 dogfood
Mode: Focused manual review

## Executive summary

- Severity counts: Critical 0, High 1, Medium 1, Low 0, Observations 1.
- Top findings: [F-001](#f-001-event-consumer-terminal-failure-handling-is-still-only-a-todo) event-consumer terminal failures lack a durable recovery/alert path; [F-002](#f-002-orphaned-storage-reconciliation-documents-a-scaling-gap) orphaned-storage reconciliation documents a pagination scaling gap.
- Main pain sources: reliability hardening around asynchronous event recovery and reconciliation; documentation/template drift is an observation to clarify, not a proven high-confidence issue.
- Caveats: intentionally narrow manual pass; no full architecture audit, target test run, CI review, security review, or runtime verification was performed.

## Triage overview

| Severity | Count | Items |
| --- | ---: | --- |
| Critical | 0 | None |
| High | 1 | [F-001: Event consumer terminal-failure handling is still only a TODO](#f-001-event-consumer-terminal-failure-handling-is-still-only-a-todo) |
| Medium | 1 | [F-002: Orphaned storage reconciliation documents a scaling gap](#f-002-orphaned-storage-reconciliation-documents-a-scaling-gap) |
| Low | 0 | None |
| Observation | 1 | [O-001: Service template docs may drift from established implementation details](#o-001-service-template-docs-may-drift-from-established-implementation-details) |

Top findings to inspect first:

1. [F-001: Event consumer terminal-failure handling is still only a TODO](#f-001-event-consumer-terminal-failure-handling-is-still-only-a-todo) — High severity, Medium confidence; this is the clearest operational recovery seam in the sampled event pipeline.
2. [F-002: Orphaned storage reconciliation documents a scaling gap](#f-002-orphaned-storage-reconciliation-documents-a-scaling-gap) — Medium severity, High confidence; the code comments and implementation directly document the pagination limitation.

Main pain sources: asynchronous failure recovery, reconciliation scalability, and keeping service-creation docs aligned with implementation.
Confidence/evidence caveats: O-001 is a weak/documentation-context signal and should be treated as a clarification prompt unless a deeper docs/code sync review confirms it.

## Scope and method

- Interpreted scope: run one intentionally narrow full-repository dogfood pass against `/home/rafaeltab/wallpaperdb` using the Phase 1 `codebase-review` skill, produce only a report artifact in this skill repository, and avoid target-repository mutation or broad implementation work.
- Approval source: implementation issue #13 plus the delegated user request were interpreted as approval to proceed after restating that scope; no additional chat approval was requested because the issue acceptance criteria explicitly required this dogfood pass.
- Skill availability/load verification:
  - Installed the local skill into a disposable `HERMES_HOME` by copying `skills/codebase-review/SKILL.md` to `$TMP_HOME/skills/codebase-review/SKILL.md`.
  - `HERMES_HOME="$TMP_HOME" hermes skills list --source local --enabled-only` reported `codebase-review` as a local enabled skill (`0 hub-installed, 0 builtin, 1 local — 1 enabled shown`).
  - `HERMES_HOME="$TMP_HOME" hermes prompt-size --json` reported a non-empty `skills_index` (`141` chars / `141` bytes), confirming Hermes indexed the disposable local skill for prompt construction.
  - A live `hermes chat --skills codebase-review ...` smoke command resolved the skill name but stopped before model execution because the disposable home had no inference provider configured (`No inference provider configured`). A negative control with `--skills not-a-skill` failed earlier with `Error: Unknown skill(s): not-a-skill`, so the successful resolution of `codebase-review` was real skill availability validation, not just a filesystem check.
- Reviewed:
  - Phase 1 skill source: `skills/codebase-review/SKILL.md` in this repository.
  - Target repository: `/home/rafaeltab/wallpaperdb`.
  - Target repo commands: `git status --short --branch`, `git log --oneline -5`, `git ls-files` via a Python wrapper, and targeted file searches/read-only inspection.
  - Target docs/manifests: `README.md`, `package.json`, `Makefile`, `apps/docs/content/docs/architecture/multi-service.mdx`, `apps/docs/content/docs/architecture/shared-packages.mdx`, `apps/docs/content/docs/guides/creating-new-service.mdx`.
  - Representative code paths: event schemas/consumer base in `packages/events/src/`, media and variant-generator wallpaper-uploaded consumers, ingestor upload orchestration, and ingestor orphaned-MinIO reconciliation.
- Not reviewed:
  - No full architecture audit, full test execution, CI review, dependency audit, security review, or language-specific playbook pass.
  - No generated build artifacts, coverage output, `node_modules`, or full service-by-service source walkthrough.
  - Did not run mutating setup, Docker, migrations, app startup, or target repo tests.
- Side effects: this report artifact in the skill repository only; `/home/rafaeltab/wallpaperdb` remained read-only.

## Findings / observations

### F-001: Event consumer terminal-failure handling is still only a TODO

- ID: F-001
- Area: Event consumers in `apps/media/src/services/consumers/` and `apps/variant-generator/src/services/consumers/`; shared retry behavior in `packages/events/src/consumer/base-event-consumer.ts`.
- Severity: High
- Severity justification: Terminal failure behavior is on an important asynchronous pipeline path and can create serious reliability/operability pain if poison messages or downstream outages occur without durable recovery or alerting.
- Confidence: Medium
- Evidence strength: Source evidence; heuristic pattern evidence
- Churn signal: Not checked
- Summary: The shared consumer base terminates messages after configured retries, and service-specific consumers override max-retry hooks, but the hooks currently log and leave `TODO: Send to DLQ or alerting system`. This is understandable for a growing service, but it is a key operational seam in an at-least-once event pipeline.
- Evidence: `packages/events/src/consumer/base-event-consumer.ts` terminates messages when `deliveryAttempt >= this.maxRetries`; `apps/media/src/services/consumers/wallpaper-uploaded-consumer.service.ts` and `apps/media/src/services/consumers/wallpaper-variant-uploaded-consumer.service.ts` include max-retry TODOs; `apps/variant-generator/src/services/consumers/wallpaper-uploaded-consumer.service.ts` has the same TODO.
- Impact: If a consumer hits a poison message or a downstream dependency repeatedly fails, the system may have no durable recovery queue or alert path beyond logs. That can make event loss/replay decisions harder and can hide data drift between services.
- Suggested next decisions: accept, research, create an issue, or document a lightweight DLQ/alerting convention for event-consumer production hardening.

### F-002: Orphaned storage reconciliation documents a scaling gap

- ID: F-002
- Area: Ingestor reconciliation, especially `apps/ingestor/src/services/reconciliation/orphaned-minio-reconciliation.service.ts`.
- Severity: Medium
- Severity justification: The limitation affects a bounded reconciliation path rather than the main upload flow, but it can slow cleanup or weaken partial-failure recovery as bucket size grows.
- Confidence: High
- Evidence strength: Source evidence; documentation mismatch
- Churn signal: Not checked
- Summary: The upload path has a good write-ahead/reconciliation story, but the orphaned-MinIO reconciliation path explicitly lists all objects at once and documents that pagination is not implemented.
- Evidence: `apps/ingestor/src/services/upload/upload-orchestrator.service.ts` records intent, stores the object, transitions state, and leaves NATS publish failures to reconciliation; `apps/ingestor/src/services/reconciliation/orphaned-minio-reconciliation.service.ts` notes no pagination, uses one `ListObjectsV2Command`, and then batches only the returned `Contents` array.
- Impact: On larger buckets, cleanup could miss objects beyond one list page or consume more memory/time than expected. Because reconciliation is part of the partial-failure safety net, scaling gaps there can weaken the reliability story even if the main upload path is robust.
- Suggested next decisions: accept as a hardening candidate, create an issue, or document expected bucket size assumptions.

### O-001: Service template docs may drift from established implementation details

- ID: O-001
- Area: Documentation for service creation, especially `apps/docs/content/docs/guides/creating-new-service.mdx` compared with current service code.
- Severity: Observation
- Severity justification: The sampled mismatch is useful context but may be intentional template simplification; it is not supported enough by this narrow pass to assert concrete project pain.
- Confidence: Low
- Evidence strength: Documentation mismatch; heuristic pattern evidence
- Churn signal: Not checked
- Summary: The guide is very useful for orientation, but a few snippets look like simplified examples rather than exact current patterns. For example, the new-service package template uses `vitest` versions and a direct `db:migrate` script shape that may not match the root/package versions and mature service migration patterns.
- Evidence: Weak evidence: root `package.json` declares `vitest` `^3.0.0`; `apps/docs/content/docs/guides/creating-new-service.mdx` shows `vitest` `^2.1.8` in the example. Existing services also have service-specific builders, connection classes, OpenAPI generation, and test patterns that are richer than the basic guide, but this pass did not inspect whether the guide is intentionally illustrative.
- Impact: This is not necessarily incorrect if the guide is intentionally illustrative, but it can slow new-service work or create small inconsistencies if copied literally.
- Suggested next decisions: research or document whether the guide is a conceptual template or should keep snippets synchronized with current generated/service patterns.

## Coverage gaps

- A deeper review should inspect each service's app lifecycle, health/readiness semantics, and shutdown behavior consistently.
- A deeper review should trace a complete upload through ingestor, media, variant-generator/color-extractor, gateway, and web UI with tests or local runtime evidence.
- A deeper review should inspect database schema ownership and cross-service duplication boundaries beyond the sampled event schemas.
- A deeper review should exclude generated/build artifacts explicitly when mapping the repository, because `dist/`, `.next/`, coverage, and `node_modules` are present locally and can distract search/listing output.

## Dogfood notes for the Phase 1 skill

- What worked: the intake/scope skeleton was easy to apply, and the basic report skeleton kept the pass from becoming a full architecture audit.
- Friction: the skill did not explicitly say where to write the report when the reviewed target is a different repository from the requesting/skill repository. For this issue, the implementation instructions resolved it by requiring the artifact in this repository.
- Friction addressed in the skill: `search_files` on a real repo surfaced generated and dependency artifacts first (`dist`, `.next`, coverage, `node_modules`). The skill now reminds reviewers to prefer tracked-file lists, manifest-aware searches, or ignore-aware searches on dirty/build-heavy repos and to explicitly exclude generated/dependency areas unless in scope.
- Friction: the acceptance criteria required an approval step, while this delegated implementation request also told the reviewer to treat the issue as approval. Restating the scope plus recording approval source worked well.
- Bad outputs: none from the skill skeleton itself; the main risk was over-scoping due to the richness of `wallpaperdb`.
- Follow-up note: if Phase 1 gets another documentation pass, consider adding a short sentence about report location for external target repositories.

## Decision queue

Findings above are candidates for discussion. They are not approved issues, ADRs, or implementation work.
