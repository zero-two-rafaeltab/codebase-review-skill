# Codebase Review Workflow

Use this reference for intake, scope approval, side-effect boundaries, and the end-to-end manual review sequence.

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
8. **Offer a post-discovery decision phase.** If the user wants to continue after the report, walk through the decision queue one item at a time or in a short batch. Do not create issues, ADRs, commits, PR comments, or code changes unless the user explicitly chooses a separate action workflow for selected findings.

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

## Out of Scope for This Workflow

Do not expand a manual discovery review into other behavior unless the user explicitly starts a separate workflow. In particular, do not add or require:

- setup tooling for target repositories;
- normalized review commands;
- language-specific playbooks;
- separate sizing or mapping workflows;
- complex delegated review plans;
- CI integration;
- arbitrary overall health scores or status labels such as "pass", "fail", "healthy", or "unhealthy".

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
- [ ] The report included a decision queue or equivalent section listing findings that need user decisions with finding reference, severity, confidence, evidence strength, impact, and suggested next decisions.
- [ ] Decision options included accept, reject/ignore, create issue, document an ADR/decision, launch deeper research, revise severity, and plan/implement separately.
- [ ] No issues, ADRs, docs, refactor plans, or implementation work were created automatically from findings.
