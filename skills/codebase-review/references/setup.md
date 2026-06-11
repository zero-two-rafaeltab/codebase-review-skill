# Review Setup Workflow

Use this reference only when the user explicitly asks to set up codebase-review support for a repository. Setup is separate from review discovery/execution: it may add documentation, normalized review commands, or repo-local helper conventions, but only behind explicit approval gates. First get approval for a setup-specific scope proposal; after that approval, perform bounded repository capability detection and present a concrete setup plan; only after the user approves that plan may you add or update repo-local/dev review commands and stable setup documentation.

## Setup Request Triggers

Treat the request as setup, not discovery, when the user asks to:

- "set up codebase review" for a repository;
- "prepare this repo for future reviews";
- add review documentation, command aliases, scripts, templates, or local helper commands;
- add or update normalized review commands for sizing, static checks, dependency/architecture checks, tests, coverage, or aggregate review;
- install or configure review tooling;
- create a repository convention for where review reports, manifests, or commands should live.

If the user asks to "review", "inspect", "find risks", "find refactors", or "look for gaps" without setup/tooling language, use the review discovery workflow instead. Do not silently combine setup with discovery; if the user asks for both, start with the setup proposal and make review execution a later, separately approved step.

## Pre-Approval Setup Boundary

Before approval, setup work is limited to clarifying the target and proposing scope. Do not perform deep detection or mutation before the user approves.

Allowed before approval:

- identify the target repository/path from the user's request or the current working directory;
- ask one clarifying question only if the target or desired setup outcome is genuinely ambiguous;
- draft the setup scope proposal in chat.

Not allowed before approval:

- installing dependencies or review tooling;
- adding, editing, or deleting files;
- adding package scripts, task runner commands, Make targets, shell aliases, or Hermes commands;
- editing README/docs/templates;
- touching CI, production configuration, deployment configuration, or secrets;
- running deep framework/tool detection, broad scans, tests, builds, or linters;
- committing, opening issues, writing ADRs, or launching review discovery.

## Setup Scope Proposal

Use this proposal shape before any setup inspection or mutation:

```text
Proposed review setup scope:
- Target repository: <repo name/path and how it was inferred>
- Setup goal: <what future codebase-review workflow should be easier or more consistent>
- Planned inspection after approval: <lightweight repo inspection needed to choose docs/commands/artifacts, e.g. manifests, README/docs, existing scripts, review/report folders>
- Possible allowed changes after approval: <specific candidate docs/files/commands/templates; keep conditional until inspected>
- Forbidden changes: no production code changes, no production dependency changes, no CI changes, no dependency/tool installs unless later approved as repo-local/dev review-only in the setup plan, no deployment/secrets/config changes, no issues/ADRs/commits unless separately approved
- Planned documentation/commands: <expected docs paths and command/script names, or "proposal only if inspection shows an existing convention">
- Expected output artifacts: <setup summary, changed files if approved, usage notes, and any follow-up review command/instructions>
- Approval gate: I will not inspect deeply, install tooling, add commands, edit docs, touch CI, or make other repository mutations until you approve this setup scope.

Go/no-go?
```

If the user rejects or edits the scope, revise the proposal and ask again. If the user only approves inspection but not mutation, inspect only the approved lightweight sources and return a second change proposal before editing.

## After Setup Scope Approval

After approval, keep setup work narrowly tied to the proposal:

- perform only the planned lightweight inspection needed to choose setup artifacts;
- run the bounded repository capability detection below and report unknowns instead of guessing;
- produce the setup plan below before adding/editing docs, commands, templates, or tooling configuration;
- prefer repository-local docs and command conventions that already exist;
- stop after presenting the setup plan and next approval question; actual file/doc/command changes require explicit approval of the plan;
- keep production code, CI, deployment, secrets, and unrelated tooling out of scope;
- summarize the setup plan, approval status, deferred changes, and how the proposed commands/docs would be used;
- do not run a health review as part of setup unless the user starts the separate review workflow.

If setup reveals that a dependency install, CI change, production change, or broader migration would be useful, stop and ask for a separate explicit approval workflow instead of doing it.

## Bounded Repository Capability Detection

Run this detection only after the setup scope is approved. Keep it lightweight and evidence-based: inspect manifests, runner files, obvious README/docs/setup files, repository metadata, and cheap file-name/path signals. Do not run tests, builds, package installs, dependency resolution, broad source archaeology, or framework-specific deep analysis during this detection slice. If those actions seem necessary, list them under items requiring separate approval instead of performing them.

Detect and cite evidence for:

- **Likely languages:** infer from manifests, lockfiles, dominant source extensions, tool config files, and README/docs claims. Mark partial or conflicting signals as unknown/uncertain.
- **Likely frameworks/platforms:** infer from manifests and config names such as `package.json` dependencies, `pyproject.toml`, `Cargo.toml`, `go.mod`, `pom.xml`, `build.gradle`, `composer.json`, `Gemfile`, framework config files, or README instructions. Do not invent framework claims from directory names alone.
- **Package managers and dependency files:** identify files such as `package-lock.json`, `pnpm-lock.yaml`, `yarn.lock`, `bun.lockb`, `uv.lock`, `poetry.lock`, `Pipfile.lock`, `requirements*.txt`, `Cargo.lock`, `go.sum`, `gradle.lockfile`, `composer.lock`, `Gemfile.lock`, or equivalents.
- **Existing runners:** check for `Justfile`/`.justfile`, `Makefile`, npm-style `scripts` in `package.json`, task files (`Taskfile.yml`, `mise.toml`, `tox.ini`, `noxfile.py`, `Rakefile`, etc.), documented commands in README/docs, and repo-local scripts under obvious paths such as `scripts/`, `bin/`, or `tools/`.
- **Existing review/test/static tooling:** identify test, lint, typecheck, format, security, docs, coverage, or review-related tools from manifests/configs/docs. Include both commands that already exist and tools present without a normalized command.
- **Review/report conventions:** note existing `reviews/`, docs, templates, issue/ADR conventions, or repository guidance relevant to future codebase reviews.
- **Known gaps and failures:** record unreadable files, missing manifests, ambiguous package managers, absent runners, unavailable tools, skipped generated/vendor paths, or detection commands that failed. Treat these as setup context, not as silent defaults.

Use only cheap commands needed to list files, inspect small manifests, parse obvious JSON/TOML/YAML when available, or query git metadata. If a manifest is too large or malformed, quote the failure/limit and continue with lower confidence.

## Normalized Runner Planning Precedence

When planning candidate review commands, apply this precedence:

1. **Existing Just:** if a `Justfile` or `.justfile` exists, prefer adding or documenting normalized `just` recipes such as `just review`, `just review-size`, `just review-static`, `just review-deps`, `just review-test`, and `just review-coverage`, using narrower names only when they fit existing recipe style better.
2. **Existing Make:** if no Just runner exists but a `Makefile` exists, prefer `make` targets with the repository's existing naming style.
3. **Existing npm/package scripts:** if neither Just nor Make exists but `package.json` scripts or equivalent package-manager scripts exist, prefer package scripts and the detected package manager invocation (`npm run`, `pnpm run`, `yarn`, `bun run`) based on lockfiles/docs.
4. **Add Just if no runner exists and the user approved it:** if no runner exists at all, recommend a small repo-local `Justfile` as the normalized review command surface. Create it only after the setup plan explicitly names `Justfile` and the user approves that plan.

Do not overwrite or rename existing commands in this slice. If multiple runners already exist, recommend the highest-precedence runner for normalized review commands while noting lower-precedence existing commands as supporting evidence or compatibility options.

The runner selection precedence must be exactly: existing Just, existing Make, existing npm/package scripts, then add Just if no runner exists and the user approved it.

## Normalized Review Command Set

When a setup plan recommends command aliases, cover these review capabilities when the target repository has enough existing tooling or safe shell equivalents to do so:

| Capability | Preferred normalized alias | What it should run |
| --- | --- | --- |
| Sizing | `review-size` | cheap size inventory such as tracked file counts, dominant extensions, directory counts, lines by language if a repo-local/dev tool already exists, or a documented fallback command |
| Static checks | `review-static` | existing lint, typecheck, formatting-check, static-analysis, or documentation-check commands; do not add production dependencies to make this possible |
| Dependency/architecture checks | `review-deps` | existing dependency audit, unused-dependency, import-boundary, architecture, cycle, license, or package-health checks, or a clearly labeled placeholder/documented manual command when no safe tool exists |
| Tests | `review-test` | existing unit/integration test commands appropriate for local diagnostic review |
| Coverage | `review-coverage` | existing coverage command, or a dev/review-only wrapper around already-present test tooling when available |
| Aggregate review | `review` | a reasonable aggregate over the safe normalized commands, usually `review-size`, `review-static`, `review-deps`, `review-test`, and `review-coverage` where those aliases exist and are not prohibitively expensive |

Use aliases that match the target runner's naming style when the repository already has a strong convention, but keep the capability mapping obvious. If a capability is not supported by existing tooling and adding repo-local/dev tooling was not approved, document it as `not configured` or add a non-failing placeholder that prints the missing capability and points to the setup docs; do not invent a command that silently succeeds as if it performed the check.

Aggregate commands should make future reviews less ad hoc without hiding diagnostic failures. A non-zero exit from lint, tests, dependency checks, or coverage is useful review signal; document that setup success means the command executes and produces diagnostic output, not that the repository passes every check.

## Stable Setup Documentation Contract

When setup adds or updates review commands, it must also add or update durable target-repository setup documentation. This documentation explains the review command surface and setup boundaries without committing raw command baselines that will go stale.

### Documentation location selection

Use this predictable selection rule unless the approved setup plan explicitly chooses a narrower existing repository convention:

1. Prefer an existing `docs/codebase-review.md` file if present.
2. Otherwise, create `docs/codebase-review.md` as the canonical setup documentation path, creating `docs/` if needed.
3. If the repository already has an obvious equivalent developer/review command document (for example `docs/development.md`, `docs/testing.md`, `CONTRIBUTING.md`, or a README section dedicated to local quality commands), the setup plan may choose that existing location instead, but it must name the chosen path and why it is clearer than `docs/codebase-review.md`.
4. If documentation is split across multiple existing files, still name one canonical setup documentation path and add pointers from or to related files only if approved.

Do not hide the contract only in runner comments, package scripts, CI files, chat output, or a one-time setup summary. Runner comments may supplement the documentation but are not a substitute for the canonical setup documentation path.

### Required documentation contents

The canonical setup documentation must record:

- **Command purpose:** each normalized review command or documented existing command and what review question it helps answer.
- **Runner invocation:** the exact local invocation a reviewer should run, using the selected runner or package manager prefix (for example `just review-static`, `make review-test`, or `pnpm run review:coverage`).
- **Diagnostic interpretation:** how to read command results, including that non-zero exits or reported findings are review signals, not proof that setup failed. Setup success means commands execute and emit useful diagnostic output.
- **Known limitations:** unsupported capabilities, expensive commands that were intentionally omitted from aggregates, flaky/unavailable tooling, partial coverage, ambiguous package-manager/framework detection, or manual review steps that remain necessary.
- **Intentionally skipped areas:** explicit setup boundaries such as no production code changes, no production dependency changes, no runtime/deployment/secrets changes, no raw baseline snapshots, and no CI modifications.
- **CI policy:** whether CI was detected if that was part of approved lightweight inspection, how these commands could be considered for future CI integration, and that setup did not create or modify CI. CI changes require a separate explicit approval workflow.
- **Command status table:** a durable status table or template summarizing command coverage and setup verification without embedding raw outputs.

### Command status table/template

Include a table like this in the canonical setup documentation and adapt the rows to the approved command set:

```markdown
| Capability | Command | Purpose | Setup status | How to interpret diagnostics | Limitations / skipped areas |
| --- | --- | --- | --- | --- | --- |
| Sizing | `<runner> review-size` | Estimate repository/review surface area. | configured / documented / not configured | Counts or size summaries are orientation data, not a pass/fail baseline. | Do not commit raw counts as durable baselines; rerun when needed. |
| Static checks | `<runner> review-static` | Run existing lint/typecheck/static analysis signals. | configured / documented / not configured | Findings or non-zero exits are review signals to inspect. | Uses only existing approved tooling; no production dependency changes. |
| Dependency/architecture | `<runner> review-deps` | Surface dependency, import-boundary, architecture, audit, or manual dependency-review signals. | configured / documented / not configured | Treat warnings/errors as candidates for manual review. | Document missing safe tooling or manual-only coverage honestly. |
| Tests | `<runner> review-test` | Run existing test diagnostics relevant to review. | configured / documented / not configured | Failures indicate behavior or environment signals to investigate. | Expensive or environment-dependent suites may be omitted from aggregates. |
| Coverage | `<runner> review-coverage` | Produce coverage diagnostics from existing tooling when available. | configured / documented / not configured | Coverage values are current diagnostics, not committed setup baselines. | Mark unavailable coverage as not configured rather than inventing it. |
| Aggregate review | `<runner> review` | Run the safe configured review commands in one local workflow. | configured / documented / not configured | Aggregate failure preserves underlying diagnostic failure; inspect individual command output. | Excludes unsupported, destructive, expensive, or separately approved-only steps. |
```

The setup application summary may reuse this table shape with concrete command names and statuses. Keep it concise in chat and point to the canonical documentation for durable details.

### Durable-output rules

- Do not commit raw command stdout/stderr, current warning lists, coverage percentages, file counts, dependency audit dumps, snapshots, or other volatile baselines as part of stable setup documentation.
- Do document command intent, invocation, expected categories of diagnostics, and how a future reviewer should rerun commands.
- If verification output is useful for the current setup handoff, include it in the chat/application summary or a transient setup note only when approved, not as a durable baseline committed to the target repository.
- If the target repository already has stale raw baseline sections, do not update them as part of this setup slice; list cleanup as a separate follow-up unless the approved plan explicitly includes it.

### CI policy for setup documentation

Setup may document CI policy but must not modify CI:

- If no CI files are detected during approved lightweight inspection, state that setup did not create CI and that future CI integration requires separate approval.
- If CI exists, state whether the normalized commands appear suitable for future CI consideration, but do not edit workflows, required checks, badges, branch protections, or deployment automation.
- If CI suitability is unknown because setup did not inspect CI or commands are too expensive/environment-dependent, say so directly.
- Any CI integration, required-check policy, cache/dependency install behavior, or workflow-matrix change is outside this setup workflow unless a later separately approved workflow explicitly targets CI.

## Setup Plan Output

After capability detection and before applying changes, present a concrete setup plan. Keep it concise but decision-ready:

```markdown
## Repository capability detection

- Target repository: <path/name>
- Evidence inspected: <manifests/docs/runner files/metadata inspected, with notable limits>
- Likely languages: <language — evidence, confidence/unknowns>
- Likely frameworks/platforms: <framework/platform — evidence, confidence/unknowns>
- Package managers/dependency files: <detected files and inferred command prefix, or unknown>
- Existing runners: <Just/Make/package scripts/other documented commands, or none detected>
- Existing review/test/static tooling: <tests/lint/typecheck/format/docs/security/review tooling and evidence>
- Review/report conventions: <existing folders/templates/docs or none detected>
- Known gaps/unknowns: <detection failures, conflicting evidence, missing commands, skipped areas>

## Setup plan

- Existing commands to reuse/document: <commands already present; include runner/package-manager prefix and source evidence>
- Recommended new normalized commands: <planned Just/Make/package-script recipes/targets/scripts, applying runner precedence; say "none" if not needed>
- Stable setup documentation path: <canonical path from the documentation location selection rule, usually docs/codebase-review.md, and why>
- Documentation contents to add/update: <command purposes, runner invocations, diagnostic interpretation, known limitations, intentionally skipped areas, command status table, and CI policy>
- Optional repo-local/dev tooling additions: <candidate docs/templates/helper files/dev dependencies; mark optional and do not install yet>
- Items requiring separate approval: <dependency installs, CI changes, production/config changes, broader migrations, review execution, issue/ADR/commit creation, or "none">
- Proposed files to create/edit: <paths and one-line purpose, or "none">
- Next approval needed: <exact go/no-go question for applying the plan, or state that no changes are recommended>
```

The setup plan must distinguish existing commands from recommended new commands. It must also separate optional tooling from items that require separate approval. If detection is inconclusive, include the uncertainty in the plan and choose the lowest-risk next step rather than guessing.

Do not apply plan changes until the user approves all or part of the setup plan. If the user approves only some commands/files, apply only that approved subset and report skipped items.

## Approved Setup Plan Application

After the user approves the setup plan, you may add or update the approved repo-local/dev review command aliases and documentation. Keep application narrow and auditable:

1. Re-read the runner/config files you will edit immediately before changing them so you do not overwrite user edits.
2. Apply the approved runner selection precedence exactly: existing Just, existing Make, existing npm/package scripts, then add Just if no runner exists and the user approved it.
3. Add or update aliases for the normalized command set: sizing, static checks, dependency/architecture checks, tests, coverage, and a reasonable aggregate `review` command where appropriate for the target repo.
4. Reuse existing commands and tools whenever possible. Prefer wrapper aliases over new tools.
5. Do not change production dependencies, runtime manifests, deployment config, secrets, or CI. Do not move dependencies from dev/review-only scope into production scope.
6. Add new tooling only when it is repo-local or dev/review-only, was listed in the setup plan, and was explicitly approved. Examples: a small `Justfile`, a `scripts/review-*` helper, review documentation, or a dev-only package script that wraps already-present tooling. Package-manager installs or lockfile changes require the user's explicit approval for those exact dev/review-only changes.
7. Add or update the canonical setup documentation path from the approved plan using the [Stable Setup Documentation Contract](#stable-setup-documentation-contract). Record command purposes, runner invocations, diagnostic interpretation, limitations, intentionally skipped areas, command status table/template, and CI policy; do not commit raw command outputs or volatile baselines.
8. Document any repo-local/dev review-only additions in the edited runner file comments where practical and in the setup docs path from the approved plan.
9. Verify edited command definitions and the setup documentation for syntax/parseability when cheap, and run only the approved lightweight verification commands. If running the full aggregate review would be expensive or destructive, verify with dry-run/list/parse checks and say so.

### Runner edit templates

Adapt these templates to the repository's actual commands. Omit or mark unsupported capabilities rather than adding fake checks.

**Just (`Justfile` or `.justfile`):**

```just
# Codebase-review aliases. Repo-local/dev review-only; no production dependency changes.
review:
    just review-size
    just review-static
    just review-deps
    just review-test
    just review-coverage

review-size:
    git ls-files | wc -l

review-static:
    <existing lint/typecheck/static command>

review-deps:
    <existing dependency/architecture command or documented placeholder>

review-test:
    <existing test command>

review-coverage:
    <existing coverage command>
```

**Make (`Makefile`):**

```make
.PHONY: review review-size review-static review-deps review-test review-coverage

# Codebase-review aliases. Repo-local/dev review-only; no production dependency changes.
review: review-size review-static review-deps review-test review-coverage

review-size:
	git ls-files | wc -l

review-static:
	<existing lint/typecheck/static command>

review-deps:
	<existing dependency/architecture command or documented placeholder>

review-test:
	<existing test command>

review-coverage:
	<existing coverage command>
```

**npm/package scripts (`package.json`):**

```json
{
  "scripts": {
    "review": "<package-manager run> review:size && <package-manager run> review:static && <package-manager run> review:deps && <package-manager run> review:test && <package-manager run> review:coverage",
    "review:size": "git ls-files | wc -l",
    "review:static": "<existing lint/typecheck/static command>",
    "review:deps": "<existing dependency/architecture command or documented placeholder>",
    "review:test": "<existing test command>",
    "review:coverage": "<existing coverage command>"
  }
}
```

For package scripts, preserve existing script names and JSON formatting as much as possible. When adapting the aggregate script, replace `<package-manager run>` with the detected package-manager invocation (`npm run`, `pnpm run`, `yarn`, or `bun run`) based on lockfiles/docs; if uncertain, document the uncertainty in the setup plan instead of defaulting silently to npm.

### Application summary output

After applying approved setup changes, reply with:

```markdown
## Applied review setup

- Approval applied: <what the user approved, including any subset limits>
- Runner selected: <Just | Make | package scripts | newly added Just>, based on <evidence>
- Commands added/updated: <review, review-size, review-static, review-deps, review-test, review-coverage, with unsupported/skipped items called out>
- Stable setup docs: <canonical path, command status table coverage, limitations/skipped areas, and CI policy captured>
- Files changed: <paths and purpose>
- Dev/review-only boundary: <state that no production dependencies/config/CI were changed, or call out any separately approved dev/review-only changes>
- Verification run: <parse/list/dry-run/full command outputs or why skipped>
- Follow-up: <remaining optional tooling or separate approvals needed, or none>
```

## Setup Verification Checklist

- [ ] The request was classified as setup rather than review discovery.
- [ ] A setup-specific scope proposal was shown before inspection or mutation.
- [ ] The proposal named the target repository.
- [ ] The proposal named planned lightweight inspection.
- [ ] The proposal named possible file/doc/command changes.
- [ ] The proposal explicitly forbade production and CI changes.
- [ ] The proposal named planned documentation/commands or stated when they would be proposed.
- [ ] The proposal named expected output artifacts.
- [ ] The approval gate stated no installs, command additions, docs edits, CI changes, or other mutations before approval.
- [ ] No setup mutation occurred before approval.
- [ ] Bounded capability detection inspected manifests, docs, repo metadata, runners, and obvious tooling signals after setup-scope approval.
- [ ] Detection reported likely languages, frameworks, package managers, runners, existing review/test/static tooling, and known gaps or unknowns with evidence.
- [ ] Runner planning applied the exact precedence: existing Just, existing Make, existing npm/package scripts, then add Just if no runner exists and the user approved it.
- [ ] A setup plan was produced before applying changes.
- [ ] The setup plan distinguished existing commands, recommended new commands, optional repo-local/dev tooling additions, and items requiring separate approval.
- [ ] The setup plan named the stable setup documentation path using the documented location selection rule.
- [ ] The setup plan named documentation contents covering command purpose, runner invocation, diagnostic interpretation, known limitations, intentionally skipped areas, command status table, and CI policy.
- [ ] No setup plan changes were applied until the user approved the setup plan.
- [ ] Approved normalized command changes used the exact precedence: existing Just, existing Make, existing npm/package scripts, then add Just if no runner exists and the user approved it.
- [ ] Approved command aliases covered sizing, static checks, dependency/architecture checks, tests, coverage, and a reasonable aggregate review command where appropriate, or documented unsupported capabilities honestly.
- [ ] The canonical setup documentation was added or updated when commands/docs were approved.
- [ ] Setup documentation included a command status table/template suitable for setup output.
- [ ] Setup documentation described diagnostic interpretation and documented unsupported, expensive, unavailable, or intentionally skipped areas honestly.
- [ ] Setup documentation captured CI policy while leaving CI unmodified.
- [ ] Raw command stdout/stderr, coverage percentages, file counts, audit dumps, snapshots, or other volatile baselines were not committed as durable setup baselines.
- [ ] Repo-local/dev review-only additions were documented as such.
- [ ] Production dependencies, runtime config, deployment config, secrets, and CI were not changed.
- [ ] Detection failures or unknowns were reported as setup context rather than silently guessed.
- [ ] Any performed changes stayed within approved setup scope and the approved setup plan.
