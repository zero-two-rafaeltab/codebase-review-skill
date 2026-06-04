# Codebase Review — wallpaperdb Phase 2 replay dogfood

Date: 2026-06-04
Scope: Focused replay of the existing `/home/rafaeltab/wallpaperdb` full-repository dogfood review using the Phase 2 decision-ready reporting rubric
Mode: Focused manual review / replay

## Executive summary

- Severity counts: Critical 0, High 0, Medium 2, Low 0, Observations 1.
- Top findings: [F-001](#f-001-event-consumer-terminal-failure-handling-lacks-a-durable-decision) and [F-002](#f-002-orphaned-minio-reconciliation-has-a-known-pagination-gap) are the decision-relevant hardening candidates because both sit on reliability/recovery paths and have direct source evidence.
- Main pain sources: terminal event-consumer failure handling and storage reconciliation scaling assumptions are currently documented in code as TODOs rather than explicit decisions.
- Caveats: [O-001](#o-001-new-service-guide-may-be-illustrative-rather-than-synchronized) is intentionally an observation because the review only sampled documentation/version mismatch evidence and did not confirm whether the guide is meant to be copy-paste authoritative.

## Triage overview

| Severity | Count | Items |
| --- | ---: | --- |
| Critical | 0 | None |
| High | 0 | None |
| Medium | 2 | [F-001](#f-001-event-consumer-terminal-failure-handling-lacks-a-durable-decision), [F-002](#f-002-orphaned-minio-reconciliation-has-a-known-pagination-gap) |
| Low | 0 | None |
| Observation | 1 | [O-001](#o-001-new-service-guide-may-be-illustrative-rather-than-synchronized) |

Top findings to inspect first:

- [F-001](#f-001-event-consumer-terminal-failure-handling-lacks-a-durable-decision) — Medium severity, High confidence: repeated direct source evidence shows messages are terminated after max retries while concrete DLQ/alerting behavior remains a TODO in sampled consumers.
- [F-002](#f-002-orphaned-minio-reconciliation-has-a-known-pagination-gap) — Medium severity, High confidence: the reconciliation implementation and comments directly state that pagination is not supported for large buckets.

Main pain sources: recovery-path behavior is implemented far enough to reveal the seam, but the project still needs explicit triage decisions on whether these seams are acceptable assumptions, tracked hardening work, or decision records.

Confidence/evidence caveats: the replay reused a small Phase 1 dogfood review and rechecked only representative files/commands; weak documentation drift stayed as an observation instead of a supported finding.

## Scope and method

- Reviewed:
  - Phase 2 guidance in `skills/codebase-review/SKILL.md`, especially the severity/confidence/evidence taxonomy, basic report skeleton, finding metadata format, weak-signal handling, and decision queue requirements.
  - Existing Phase 1 dogfood artifact: `reviews/2026-06-03-codebase-review-wallpaperdb-full-repo.md`.
  - Target repository status/history for cheap context: `git status --short --branch` and `git log --oneline -5` in `/home/rafaeltab/wallpaperdb`.
  - Representative target source/docs: `packages/events/src/consumer/base-event-consumer.ts`, `apps/variant-generator/src/services/consumers/wallpaper-uploaded-consumer.service.ts`, `apps/media/src/services/consumers/wallpaper-uploaded-consumer.service.ts`, `apps/media/src/services/consumers/wallpaper-variant-uploaded-consumer.service.ts`, `apps/ingestor/src/services/reconciliation/orphaned-minio-reconciliation.service.ts`, root `package.json`, and `apps/docs/content/docs/guides/creating-new-service.mdx`.
  - Safe inspection searches for repeated DLQ TODOs, pagination notes, `ListObjectsV2Command`, and `vitest` version references.
- Not reviewed:
  - No full fresh architecture audit of `wallpaperdb`.
  - No target repository tests, service startup, Docker, migrations, dependency audit, security review, full churn analysis, issue creation, ADR writing, or implementation planning.
  - No broad review of generated/dependency/build output.
- Side effects: this report artifact only in the `codebase-review-skill` repository; the reviewed target repository stayed read-only.

Findings below are decision candidates, not accepted issues, ADRs, or implementation work.

## Findings / observations

### F-001: Event consumer terminal-failure handling lacks a durable decision

- ID: F-001
- Area: Event consumers in `apps/media/src/services/consumers/`, `apps/variant-generator/src/services/consumers/`, and shared retry behavior in `packages/events/src/consumer/base-event-consumer.ts`.
- Severity: Medium
- Severity justification: This is meaningful but bounded reliability and operability pain on an event-processing path. It can hide poison-message/downstream-outage recovery decisions, but the review did not prove current production incidents or a project-wide delivery blocker, so Medium is more honest than High.
- Confidence: High
- Evidence strength: source evidence; tool output; heuristic pattern evidence
- Churn signal: recent repository churn seen, but not localized to these files; target repo `git log --oneline -5` showed recent CI/runtime/service-start work, while no file-by-file churn analysis was performed.
- Summary: The shared consumer base terminates messages after max retries, and sampled service consumers log max-retry failures, but their concrete DLQ/alerting/recovery behavior remains a TODO.
- Evidence: `packages/events/src/consumer/base-event-consumer.ts` calls `onMaxRetriesExceeded(...)` when `deliveryAttempt >= this.maxRetries` and then calls `msg.term()`; `apps/variant-generator/src/services/consumers/wallpaper-uploaded-consumer.service.ts:101-112` logs max retries then leaves `// TODO: Send to DLQ or alerting system`; search output found the same TODO in `apps/media/src/services/consumers/wallpaper-uploaded-consumer.service.ts:109` and `apps/media/src/services/consumers/wallpaper-variant-uploaded-consumer.service.ts:129`.
- Impact: If a consumer repeatedly fails on a poison message or downstream outage, post-failure handling may rely only on logs and manual inference. That can make replay, alerting, and data-drift decisions slower during incidents or hardening work.
- Suggested next decisions: accept; reject/ignore if logs-only termination is intentional for current scale; create an issue for DLQ/alerting convention; document an ADR/decision if the team wants an explicit logs-only or JetStream-native policy; launch deeper research into NATS/JetStream recovery semantics; revise severity with production/error-volume context; plan/implement separately after a selected decision.

### F-002: Orphaned MinIO reconciliation has a known pagination gap

- ID: F-002
- Area: Ingestor storage reconciliation, especially `apps/ingestor/src/services/reconciliation/orphaned-minio-reconciliation.service.ts`.
- Severity: Medium
- Severity justification: This is meaningful but bounded reliability/operability pain in a reconciliation safety net. It could miss or inefficiently process large buckets, but the code explicitly scopes the risk to large object counts and the review did not establish current bucket size or live failure.
- Confidence: High
- Evidence strength: source evidence; tool output
- Churn signal: unknown for this file; cheap repo history was checked, but no file-specific churn/blame analysis was performed.
- Summary: The orphaned-MinIO reconciliation path lists a single page of objects and documents that pagination is missing, even though reconciliation is part of cleanup/recovery behavior.
- Evidence: `apps/ingestor/src/services/reconciliation/orphaned-minio-reconciliation.service.ts:21-22` states the implementation does not currently support pagination and has a TODO for buckets with large numbers of objects; lines 37-39 state pagination should be implemented to prevent memory exhaustion; lines 43-48 create one `ListObjectsV2Command` and send it once; lines 54-68 batch only the returned `Contents` array.
- Impact: On larger buckets, cleanup can miss objects beyond the first list response page or consume more memory/time than intended. Because this path cleans storage/database drift, unclear scale assumptions can weaken recovery confidence even when the main upload path is robust.
- Suggested next decisions: accept; reject/ignore if bucket sizes are intentionally capped; create an issue for paginated reconciliation; document an ADR/decision or operational assumption for expected bucket size; launch deeper research into current object counts and S3-compatible pagination behavior; revise severity with production scale context; plan/implement separately after triage.

### O-001: New-service guide may be illustrative rather than synchronized

- ID: O-001
- Area: Service-creation documentation in `apps/docs/content/docs/guides/creating-new-service.mdx` compared with root/service package conventions.
- Severity: Observation
- Severity justification: This is not yet a supported finding because the sampled mismatch may be intentional if the guide is conceptual rather than copy-paste authoritative. It is useful context for documentation triage, not proven project pain.
- Confidence: Low
- Evidence strength: documentation mismatch; heuristic pattern evidence; weak evidence explicitly limited to sampled docs/package metadata
- Churn signal: not checked for this documentation path.
- Summary: The guide is helpful for orientation, but at least one dependency-version snippet appears older than root package metadata, and the review did not determine whether snippets are maintained as exact templates.
- Evidence: Weak evidence: root `package.json` declares `vitest` `^3.0.0`, while `apps/docs/content/docs/guides/creating-new-service.mdx:99-106` shows `vitest` `^2.1.8`; the existing Phase 1 report also noted that mature services have richer builders, connection classes, OpenAPI generation, and test patterns than the basic guide. No full documentation audit was performed.
- Impact: If copied literally, stale or illustrative snippets could cause minor setup drift for new services. If the guide is intentionally conceptual, the only action may be to label that intent.
- Suggested next decisions: accept as context; reject/ignore if the guide is explicitly illustrative; launch deeper research with a docs/service-template comparison; document the guide's intended authority level; revise severity only if user context shows new-service creation is near-term or repeatedly painful.

## Coverage gaps

- A fresh Phase 2 review of `wallpaperdb` should inspect newer gateway/color-extractor consumers and current operational docs rather than relying on the narrower Phase 1 sample.
- This replay did not run tests or local services, so no runtime/test evidence is claimed for the findings.
- Churn was intentionally cheap and coarse; the report uses `unknown`, `not checked`, or qualified churn signals instead of implying automated churn analysis.
- The review did not validate production scale, current bucket object counts, incident history, or team roadmap, so severity is based only on discovered project pain and not future-work assumptions.

## Decision queue

Findings above are candidates for discussion. They are not approved issues, ADRs, or implementation work.

Use this queue to decide what to do next. Keep discovery conclusions separate from user-provided decision context; if context changes priority, record it as a decision-phase adjustment rather than silently rewriting the original finding.

| Ref | Severity | Confidence | Evidence strength | Impact | Suggested next decisions |
| --- | --- | --- | --- | --- | --- |
| [F-001](#f-001-event-consumer-terminal-failure-handling-lacks-a-durable-decision) | Medium | High | Source evidence; tool output; heuristic pattern evidence | Terminal event-consumer failures may lack durable DLQ/alerting/replay policy beyond logs. | accept; reject/ignore; create issue; document ADR/decision; launch deeper research; revise severity; plan/implement separately |
| [F-002](#f-002-orphaned-minio-reconciliation-has-a-known-pagination-gap) | Medium | High | Source evidence; tool output | Storage cleanup may miss or inefficiently process large buckets unless scale assumptions are explicit. | accept; reject/ignore; create issue; document ADR/decision; launch deeper research; revise severity; plan/implement separately |
| [O-001](#o-001-new-service-guide-may-be-illustrative-rather-than-synchronized) | Observation | Low | Weak evidence: sampled documentation mismatch; heuristic pattern evidence | Clarifying guide authority could prevent minor new-service setup drift if the guide is meant to be copied. | accept as context; reject/ignore; launch deeper research; document decision; revise severity if user context supports promotion |

## Dogfood lessons and follow-up candidates

- Phase 2 materially improves the Phase 1 artifact by replacing an arbitrary overall impression with severity counts and stable finding IDs, making it obvious that the replay produced two Medium candidates and one weak Observation rather than a global health verdict.
- Separating severity, confidence, and evidence strength prevented two common overstatements: the DLQ and pagination items stayed Medium despite high confidence, while the documentation-drift signal stayed a low-confidence Observation.
- Focused follow-up documentation candidate: add a short note to the report skeleton or reviewer guidance that replay/dogfood reports should state which evidence was freshly rechecked versus inherited from a prior review. This is a narrow reporting clarity improvement, not new roadmap scope.
- Focused follow-up issue candidate: consider one small issue for an example decision-ready report fixture once the Phase 2 report format stabilizes. Do not add automation or CI; the value would be a human-readable exemplar only.
