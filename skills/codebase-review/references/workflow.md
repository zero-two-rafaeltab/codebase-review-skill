# Codebase Review Workflow

Use this reference for request classification, review intake, setup entry point handoff, scope approval, side-effect boundaries, and the end-to-end manual review sequence.

## When to Use

Use this skill when the user asks for either a codebase health review or explicit setup of codebase-review support for a repository. Classify the request before doing inspection.

Review trigger phrases include:

- "review this codebase";
- "look for architecture risks";
- "find maintainability pain";
- "what should we refactor?";
- "where are the testing or documentation gaps?";
- "inspect this package/domain/workflow/dependency usage."

Setup trigger phrases include:

- "set up codebase review for this repo";
- "prepare this repository for future codebase reviews";
- "add review docs/commands/templates";
- "configure/install review tooling";
- "create a repo convention for codebase-review reports or manifests."

When setup/tooling/documentation/command language is present, use the setup entry point below and [Review setup](setup.md) instead of starting review discovery.

Do not use this workflow for:

- implementing a requested code change;
- debugging a specific failing test or production bug;
- reviewing a PR diff as a code reviewer;
- automated setup of review tooling during review discovery;
- complex delegated review planning beyond the approved lightweight specialist review plan;
- language-specific review playbooks;
- CI integration;
- creating issues, ADRs, commits, PR comments, or code changes during discovery.

Those are separate workflows.

## Request Classification

Before proposing scope, decide whether the user is asking for review discovery or review setup:

- **Review discovery:** the user wants analysis of architecture, maintainability, refactoring opportunities, pain spots, tests/docs gaps, risks, workflows, domains, modules, horizontal concerns, or dependency usage. Continue with [Intake and Interpreted Scope](#intake-and-interpreted-scope).
- **Review setup:** the user wants repository support for future reviews, such as docs, commands, report conventions, templates, or tooling setup. Do not start discovery. Load [Review setup](setup.md), present the setup-specific proposal, and wait for approval before inspection or mutation.
- **Both in one request:** start with setup only. Treat the health review itself as a later, separately approved workflow after setup is complete or explicitly skipped.

If a discovery request later asks to add tooling, commands, docs, CI, or repository setup, stop the discovery flow and move to a separate setup intake after completing or pausing the review.

## Review Setup Entry Point

Setup has its own approval gate. Before any setup inspection, tooling install, command addition, docs edit, CI change, or other mutation, present the setup proposal from [Review setup](setup.md). The proposal must name:

- target repository;
- planned lightweight inspection;
- possible file, documentation, or command changes;
- forbidden production, deployment, secrets, dependency-install, and CI changes;
- planned documentation or command artifacts;
- expected output artifacts;
- the approval gate and no-mutation-before-approval rule.

After the user approves the setup scope, run the bounded capability detection and setup-plan step from [Review setup](setup.md) before applying any changes. The plan must make the next setup decision clear: what already exists, what new repo-local docs/commands are recommended, what optional tooling could help, and what requires separate approval. CI and production changes are forbidden for this setup slice. If they appear necessary, ask for a separate explicit workflow instead of doing them.

## Intake and Interpreted Scope

Before discovery work begins, propose an interpreted scope in chat and ask the user for go/no-go. Do not start expensive reading, command execution, report writing, or subagent work until the user approves.

The review scope proposal should be short and explicit:

```text
Proposed review scope:
- Target: <repo/path/package/domain/workflow/concern>
- Scope shape: <one of the supported scope shapes>
- Review depth: focused manual pass
- Discovery actions: after approval, delegate lightweight cartography, present a concise specialist review plan for a second go/no-go, then read files/docs and run safe inspection commands only as needed
- Report artifact: <planned report path>
- Side-effect boundary: no code changes, setup/tooling installs, command additions, docs edits, CI changes, commits, issues, ADRs, or PR comments during discovery

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

1. **Identify whether this workflow applies.** Confirm the request is a codebase health, architecture, maintainability, refactoring, pain-spot, or risk review rather than implementation/debugging. If it is a setup request, use the setup entry point and do not continue into review discovery.
2. **Propose an interpreted scope.** Name the target, scope shape, depth, expected artifact path, and discovery side-effect boundary.
3. **Ask for go/no-go before discovery.** Wait for approval before expensive review work. If the user rejects the proposal, adjust and ask again.
4. **Delegate the cartography checkpoint.** After approval, ask a subagent for a lightweight read-only cartography/sizing pass over the approved scope using the output contract below and the relevant subagent manifest subset. This is the required delegated cartography pass for this workflow.
5. **Use the cartography summary and manifest as the top-level map.** The orchestrator may ask targeted follow-up questions if the summary is missing something important, but should not redo deep raw-file archaeology itself.
6. **Propose a lightweight specialist review plan.** Turn the cartography summary into a short plan for the main specialist/manual review. The plan must name review slices/lenses, expected artifacts, coverage gaps, and HITL questions, and explain why the split fits the approved scope.
7. **Ask for a second go/no-go before specialist review begins.** If the user approves, continue. If the user rejects or adjusts the plan, revise it and ask again instead of starting review work.
8. **Launch the approved specialist review slices.** After the second go/no-go, run the plan's read-only specialist subagents or perform explicitly approved slices directly when delegation is unnecessary. Each specialist subagent gets only its assigned scope/lens, the relevant cartography context, the discovery side-effect boundary, and the manifest/finding return contract.
9. **Synthesize specialist outputs.** Build the report from cartography plus specialist manifests, slice reports, and emitted findings/observations. Do only narrow fact checks needed to avoid misquoting evidence; do not redo broad raw-file archaeology.
10. **Write a basic report artifact.** Put the report in a predictable local path such as `reviews/<YYYY-MM-DD>-codebase-review-<scope-slug>.md` unless the project already has a clearer review folder convention. Start the report with triage information: severity counts, top findings, main pain sources, notable caveats, and links or anchors to detailed findings.
11. **Reply with a concise executive summary.** Keep chat short and progressively disclosed. Include severity counts, 1-3 top findings or pain sources, notable confidence/evidence caveats, and a link or pointer to the report. Leave detailed evidence in the artifact. Present findings as decision candidates, not accepted work.
12. **Offer a post-discovery decision phase.** If the user wants to continue after the report, walk through the decision queue one item at a time or in a short batch. Do not create issues, ADRs, commits, PR comments, or code changes unless the user explicitly chooses a separate action workflow for selected findings.

## Delegated Cartography Checkpoint

Run this checkpoint immediately after the initial interpreted-scope go/no-go and before the main discovery pass. Delegate it to a subagent as a bounded, read-only mapping task. The subagent should inspect repository metadata, docs, manifests, file names, and a small number of obvious files only as needed to size and orient the review; it should not perform exhaustive architecture analysis, language-specific tooling setup, or specialist review planning.

The cartography subagent must return the summary plus the relevant subset of the [Subagent Manifest Contract](subagent-manifest.md), distinguishing evidence it actually inspected from inherited assumptions or unverified context.

The orchestrator should work from the returned summary when choosing review paths. It can verify narrow facts while reviewing evidence, but should avoid broad directory-by-directory or file-by-file archaeology that duplicates the delegated pass.

Use this concise output contract:

```markdown
## Cartography summary

- Approved scope shape: <full repository | package/module | domain | workflow | horizontal concern | dependency/usage review>, target `<target>`, and any explicit exclusions.
- Discovered components: <main directories/packages/services/modules and one-line roles relevant to scope>.
- Likely entry points: <routes/handlers/CLIs/jobs/config/schema/adapters/public APIs/call sites, or "unknown/not obvious">.
- Docs and tests: <relevant README/docs/examples/test locations and obvious gaps>.
- Cheap repo metrics: <small counts or size hints from cheap commands/searches, e.g. tracked files/directories in scope, key languages/manifests; mark unknown/not checked if unavailable>.
- Cheap churn/hotspot signals: <recent commits/status/file history or hotspot hints when cheap; explicitly write "unknown" or "not checked" when unavailable or skipped>.
- Likely review slices: <2-5 candidate slices for the main manual discovery pass, ordered by likely value>.
- Known gaps: <areas not mapped, generated/vendor/build outputs skipped, missing local context, unavailable commands>.
- Uncertainty: <what might be wrong about the map and what would change the review focus>.
```

Do not turn this checkpoint itself into the specialist review plan: it may name likely review slices, but it should not assign multiple specialists, ask the second go/no-go, or create a complex delegation tree. The orchestrator creates the lightweight plan after cartography returns.

If a later approved review plan delegates a specialist slice to a subagent rather than doing it directly, instruct that specialist subagent to return its slice summary, any findings/observations using the existing severity/confidence/evidence-strength fields, and the relevant subset of the [Subagent Manifest Contract](subagent-manifest.md). Use the manifest to audit coverage and assumptions during synthesis; do not replace the report's finding fields or decision queue.

## Lightweight Specialist Review Plan Checkpoint

Run this checkpoint after cartography and before full specialist/manual review begins. The goal is to give the user one concise chance to confirm that the intended split still matches the approved scope. Keep it short enough for chat; do not introduce repository setup, normalized command workflows, language-specific playbooks, advanced synthesis, or a complex delegation tree.

Use the cartography summary to propose 2-5 review slices. Each slice should have a practical lens such as architecture boundaries, core workflow/data flow, maintainability/refactoring pain, test/documentation gaps, risk/operability, or dependency usage. Choose lenses because they fit the approved scope, not because of a language framework checklist.

Use this concise proposal shape:

```text
Specialist review plan proposal:
- Review slices/lenses:
  1. <slice/lens>: <paths/components/workflow to inspect and why it fits scope>
  2. <slice/lens>: <paths/components/workflow to inspect and why it fits scope>
- Expected artifacts: one primary Markdown review report at <path>, with findings/observations, evidence links, caveats, and decision candidates.
- Coverage gaps: <known gaps from cartography, generated/vendor/build areas skipped, missing docs/tests/context, anything intentionally out of scope>.
- HITL questions: <questions that could change focus, or "none before review">.
- Scope fit: <one sentence explaining why this split is small, representative, and inside the approved target>.

Go/no-go for this review plan?
```

If the user says no, changes priorities, adds exclusions, or asks for another lens, revise the proposal and ask again. Do not start the specialist/manual review until the user approves the revised plan.

## Launching Specialist Read-Only Reviews

Run specialist reviews only after the second go/no-go approves the lightweight plan. This is still discovery, so specialists inherit the same read-only side-effect boundary as the orchestrator.

For each approved slice, send a compact delegation prompt with:

- the approved overall scope and explicit exclusions;
- the specialist's assigned slice/lens and allowed paths, components, workflow, dependency usage, or concern;
- the cartography summary and any inherited context needed to orient the slice;
- the discovery side-effect boundary: read files/repository metadata, run safe inspection commands, and write only the requested slice output/manifest if an artifact is needed; do not edit code, install tooling, create issues/ADRs/commits/PR comments, or start setup workflows;
- the required return contract: slice summary, findings/observations using the normal severity/confidence/evidence-strength fields, coverage gaps and uncertainties, plus the specialist subset of the subagent manifest.

Specialist subagents must stay within their assigned scope and lens. They may inspect nearby files only when needed to understand directly referenced evidence, and must record that expansion in the manifest. They must not add new lenses, spawn additional agents, perform exhaustive architecture analysis, or turn findings into accepted work.

Use this concise prompt shape:

```text
Specialist read-only review request:
- Approved review scope: <target and scope shape, with exclusions>
- Assigned slice/lens: <one approved slice/lens only>
- Allowed focus: <paths/components/workflow/dependency/concern to inspect>
- Context: <cartography summary excerpt and relevant inherited assumptions>
- Side-effect boundary: read-only discovery; safe inspection commands only; no code changes, setup/tooling installs, commits, issues, ADRs, or PR comments; write only the requested slice output/manifest if needed.
- Return: slice summary; findings/observations with ID or provisional title, severity, confidence, evidence strength, churn signal if cheap/known, evidence references, impact, and suggested next decisions; coverage gaps/uncertainties; specialist subagent manifest subset.
```

If the approved plan has only one small slice or the user explicitly wants the orchestrator to do the pass directly, the orchestrator may perform the slice without launching a specialist. Still follow the approved plan, keep the same boundary, and document the absence of specialist manifests in the report.

## Lightweight Synthesis Rules

The orchestrator synthesizes; it should not repeat the specialists' raw archaeology. Use the returned cartography summary, subagent manifests, slice reports, and emitted findings/observations as the primary evidence map.

Apply these conservative rules:

- **Trace every carried-forward finding.** A report finding or observation derived from a specialist output should cite the contributing specialist finding/manifest and the underlying file/doc/command evidence that the specialist reported.
- **Merge obvious duplicates cautiously.** If two specialists report the same issue, merge it into one finding only when the affected area, cause, and impact are substantially the same. Cite both sources and keep the lower confidence or a clearly caveated confidence unless the combined evidence justifies more.
- **Preserve conflicts and uncertainty.** If specialists disagree about cause, severity, or confidence, do not force a winner. Record the disagreement in the finding evidence/caveats or split into separate findings/observations when they are materially different.
- **Avoid confidence inflation.** More reports are not automatically stronger evidence. Raise confidence only when the cited evidence is more direct or covers materially different representative paths.
- **Keep decision candidates intact.** The synthesized report still uses the normal finding fields, triage overview, progressive-disclosure structure, and decision queue. Specialist outputs inform the queue; they do not approve issues, ADRs, or implementation work.
- **Do narrow verification only.** The orchestrator may open a cited file or rerun a cheap command to confirm a quote, path, or interpretation, but should not conduct a broad second review over the same raw files.

## Discovery Side-Effect Boundary

Allowed during discovery:

- read files and repository metadata;
- run safe inspection commands;
- request and use the delegated read-only cartography checkpoint;
- propose and use the approved lightweight specialist review plan;
- launch approved read-only specialist review subagents within their assigned scopes/lenses;
- synthesize from specialist manifests, slice reports, and findings/observations;
- write the agreed review report artifact.

Not allowed during discovery:

- code edits;
- documentation edits or command additions;
- commits or branches;
- GitHub issues;
- ADRs or durable project decisions;
- PR comments;
- dependency, CI, or tooling changes;
- setup workflows or installing review tooling;
- separate sizing passes beyond the required delegated cartography checkpoint;
- specialist work outside the approved slices/lenses;
- complex delegated review plans beyond the approved lightweight specialist review plan.

If the user wants action after the report, start a separate post-discovery decision or implementation workflow.

## Out of Scope for This Workflow

Do not expand a manual discovery review into other behavior unless the user explicitly starts a separate workflow. In particular, do not add or require:

- setup tooling for target repositories;
- normalized review commands;
- language-specific playbooks;
- separate sizing or mapping workflows beyond the required delegated cartography checkpoint;
- specialist subagents outside the second-go/no-go approved slices or beyond the discovery side-effect boundary;
- complex delegated review plans beyond the approved lightweight specialist review plan;
- CI integration;
- arbitrary overall health scores or status labels such as "pass", "fail", "healthy", or "unhealthy".

## Verification Checklist

- [ ] The request matched a codebase health review trigger rather than implementation/debugging.
- [ ] Setup/tooling/docs/commands language was classified as setup and routed to the setup entry point instead of discovery.
- [ ] The interpreted scope was proposed before discovery.
- [ ] The scope proposal used one of the supported scope shapes.
- [ ] User go/no-go was received before discovery.
- [ ] A delegated cartography checkpoint was run after initial scope approval.
- [ ] The cartography summary covered scope shape, components, entry points, docs/tests, cheap metrics, churn/hotspots or unknown/not checked, likely review slices, gaps, and uncertainty.
- [ ] The cartography subagent returned a manifest or relevant subset covering assigned scope, lens, inspected evidence, commands, artifacts, uncertainties, out-of-scope areas, inherited/unverified context, and coverage confidence.
- [ ] A lightweight specialist review plan was proposed after cartography and before full specialist/manual review.
- [ ] The plan named review slices/lenses, expected artifacts, coverage gaps, HITL questions, and why the split fit the approved scope.
- [ ] User go/no-go was received for the specialist review plan before full specialist/manual review began.
- [ ] If the user rejected or adjusted the plan, the orchestrator revised it and asked again instead of proceeding.
- [ ] After the second go/no-go, any specialist subagents were launched only for approved slices/lenses and received their assigned scope, inherited context, side-effect boundary, and return contract.
- [ ] Specialist subagents stayed read-only, did not edit code/install tooling/create issues/ADRs/commits/PR comments, and did not spawn deeper delegation.
- [ ] Specialist outputs included slice summaries, findings/observations with severity/confidence/evidence strength where applicable, uncertainties/gaps, and manifests or relevant subsets.
- [ ] The top-level review used the cartography summary instead of doing broad raw-file archaeology itself.
- [ ] Any delegated specialist subagent returned a manifest or relevant subset and kept severity, confidence, evidence strength, and decision candidates in the existing report model.
- [ ] The orchestrator synthesized from subagent manifests, reports, and emitted findings/observations, using only narrow verification instead of broad raw archaeology.
- [ ] Duplicate findings were merged only when substantially the same, with contributing source manifests/findings cited.
- [ ] Conflicting or uncertain specialist outputs preserved uncertainty, cited sources, and avoided overstating confidence.
- [ ] Discovery stayed read-only except for the report artifact.
- [ ] No issues, ADRs, commits, PR comments, or code changes were created during discovery.
- [ ] No setup, tooling install, command addition, docs edit, extra sizing/mapping, language-playbook, advanced synthesis, complex delegated review plan beyond the approved lightweight plan, or CI workflow was introduced during discovery.
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
- [ ] The report included a decision queue or equivalent section listing findings that need user decisions with finding reference, severity, confidence, evidence strength, impact, and suggested next decisions.
- [ ] Decision options included accept, reject/ignore, create issue, document an ADR/decision, launch deeper research, revise severity, and plan/implement separately.
- [ ] No issues, ADRs, docs, refactor plans, or implementation work were created automatically from findings.
