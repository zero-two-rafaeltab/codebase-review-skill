# Discovery Guidance

Use this reference after the user approves scope, the delegated cartography checkpoint returns, and the lightweight specialist review plan receives the second go/no-go. It keeps repository orientation and manual discovery bounded to the approved target.

## Repository and Context Inspection

After scope approval, the top-level orchestrator should receive a lightweight cartography summary from a delegated subagent, convert it into a lightweight specialist review plan, and get the user's second go/no-go before this discovery procedure begins. Use the cartography summary and approved plan as the orientation pass. Keep any follow-up inspection bounded to the approved target; do not turn it into a whole-repository mapping exercise unless the approved scope is the whole repository, and do not redo broad raw-file archaeology that the cartography pass already covered.

Inspect only what helps answer the approved review question:

- **Repository shape:** top-level directories, package/service boundaries, generated or vendored areas to ignore, and where the approved target lives.
- On repositories with local build outputs or dependencies present, prefer tracked-file lists, manifest-aware searches, or ignore-aware searches before broad file listings. Explicitly exclude generated/dependency areas such as `node_modules`, `dist`, `build`, `.next`, and coverage outputs unless they are part of the approved scope.
- **Project intent:** README, docs, comments, package metadata, or other nearby context that explains what the component is meant to do.
- **Build and dependency hints:** manifests, lockfiles, module files, workspace config, and scripts that reveal frameworks, entry points, or dependency boundaries.
- **Runtime and data flow clues:** routes, handlers, jobs, CLI entry points, configuration, database/schema files, adapters, or integration points connected to the approved scope.
- **Tests and examples:** test files, fixtures, examples, or snapshots that show expected behavior and coverage gaps.
- **Recent local context when cheap:** git status and recent commits can reveal active churn, but do not rely on churn as a required scoring system. Record cheap churn context as a churn signal on each finding; use `unknown` or `not checked` when it was unavailable or not worth checking.

Safe inspection commands are allowed when useful, for example `git status --short`, `git log --oneline -5`, file searches, tree/listing commands, or inspection of existing test/build command names in manifests or scripts. Prefer using the cartography summary for broad metrics, churn/hotspot hints, docs/tests, and entry-point discovery; use additional commands only for narrow verification or evidence. Treat failures or missing commands as context; do not fix them during discovery.

## Focused Manual Discovery Procedure

Use this procedure for small repositories, a narrow package/module, a single workflow, or a horizontal concern. The goal is a practical human-readable review, not exhaustive automation.

1. **Restate the approved scope, cartography summary, and approved review plan in your notes.** Include target paths or concerns, what is intentionally out of scope, the side-effect boundary, selected review slices/lenses, known gaps, HITL answers, and uncertainty.
2. **Choose a tiny component map from cartography and the approved plan.** Identify the main entry points, core modules, dependencies, data/configuration flows, tests, and documentation relevant to the scope without redoing broad mapping.
3. **Trace one or two representative paths.** Follow a typical request, command, job, data operation, or dependency usage through the code, selected from the approved review slices/lenses. For horizontal concerns, sample a few representative call sites instead.
4. **Look for architecture and boundary signals.** Note unclear ownership, tangled dependencies, circular knowledge, excessive coupling, leaky abstractions, duplicated patterns, or places where policy and implementation are hard to separate.
5. **Look for maintainability and refactoring signals.** Note hard-to-change files, repeated logic, naming mismatches, oversized modules, hidden conventions, fragile configuration, missing seams for tests, or code that requires broad edits for small behavior changes.
6. **Look for pain spots and future-risk prevention.** Identify issues likely to slow future work, such as undocumented invariants, weak error handling, untested critical paths, brittle integration boundaries, dependency lock-in, or unclear extension points. Do not increase severity for possible future work unless the user explicitly supplied that roadmap, migration, business, or operational context during intake or scope approval.
7. **Classify findings without mixing concepts.** Record evidence with file paths, docs, command summaries, and a cheap churn signal when available. Use the finding taxonomy below to label severity, confidence, and evidence strength separately. Route weak signals to Observation or low-confidence findings instead of overstating them.
8. **Keep additional delegation out of the main pass.** The required pre-discovery steps are the cartography checkpoint and the approved lightweight specialist review plan. Do not expand the plan into setup workflow, language playbooks, advanced synthesis, or a complex delegation tree during this discovery pass.
9. **Synthesize into decision candidates.** Group related observations, explain impact, and suggest next decisions such as accept, reject or ignore, create an issue, document an ADR/decision, launch deeper research, revise severity, or plan/implement separately.
