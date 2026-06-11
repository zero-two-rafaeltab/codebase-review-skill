# Review Setup Workflow

Use this reference only when the user explicitly asks to set up codebase-review support for a repository. Setup is separate from review discovery/execution: it may eventually add documentation, commands, or repo-local helper conventions, but only after the user approves a setup-specific scope proposal. After that approval, perform bounded repository capability detection and present a concrete setup plan before applying any setup changes.

## Setup Request Triggers

Treat the request as setup, not discovery, when the user asks to:

- "set up codebase review" for a repository;
- "prepare this repo for future reviews";
- add review documentation, command aliases, scripts, templates, or local helper commands;
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
- Forbidden changes: no production code changes, no CI changes, no dependency/tool installs, no deployment/secrets/config changes, no issues/ADRs/commits unless separately approved
- Planned documentation/commands: <expected docs paths and command/script names, or "proposal only if inspection shows an existing convention">
- Expected output artifacts: <setup summary, changed files if approved, usage notes, and any follow-up review command/instructions>
- Approval gate: I will not inspect deeply, install tooling, add commands, edit docs, touch CI, or make other repository mutations until you approve this setup scope.

Go/no-go?
```

If the user rejects or edits the scope, revise the proposal and ask again. If the user only approves inspection but not mutation, inspect only the approved lightweight sources and return a second change proposal before editing.

## After Setup Approval

After approval, keep setup work narrowly tied to the proposal:

- perform only the planned lightweight inspection needed to choose setup artifacts;
- run the bounded repository capability detection below and report unknowns instead of guessing;
- produce the setup plan below before adding/editing docs, commands, templates, or tooling configuration;
- prefer repository-local docs and command conventions that already exist;
- stop after presenting the setup plan and next approval question; actual file/doc/command changes are deferred to a later explicitly approved setup-application workflow;
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

1. **Just first:** if a `Justfile` or `.justfile` exists, prefer adding or documenting normalized `just` recipes such as `just review`, `just review-test`, `just review-lint`, `just review-typecheck`, or narrower names that fit existing recipe style.
2. **Make second:** if no Just runner exists but a `Makefile` exists, prefer `make` targets with the repository's existing naming style.
3. **npm/package scripts third:** if neither Just nor Make exists but `package.json` scripts or equivalent package-manager scripts exist, prefer package scripts and the detected package manager invocation (`npm run`, `pnpm run`, `yarn`, `bun run`) based on lockfiles/docs.
4. **Propose adding Just when no runner exists:** if no runner exists at all, recommend adding a small repo-local `Justfile` as the normalized review command surface. This is a plan item only; do not create it in this planning slice.

Do not overwrite or rename existing commands in this slice. If multiple runners already exist, recommend the highest-precedence runner for normalized review commands while noting lower-precedence existing commands as supporting evidence or compatibility options.

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
- Optional repo-local/dev tooling additions: <candidate docs/templates/helper files/dev dependencies; mark optional and do not install yet>
- Items requiring separate approval: <dependency installs, CI changes, production/config changes, broader migrations, review execution, issue/ADR/commit creation, or "none">
- Proposed files to create/edit: <paths and one-line purpose, or "none">
- Next approval needed: <exact go/no-go question for applying the plan, or state that no changes are recommended>
```

The setup plan must distinguish existing commands from recommended new commands. It must also separate optional tooling from items that require separate approval. If detection is inconclusive, include the uncertainty in the plan and choose the lowest-risk next step rather than guessing.

Do not apply plan changes in this slice. If the user approves all or part of the plan, record what was approved as the next intended setup action and defer actual changes to a later explicitly approved setup-application workflow.

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
- [ ] Runner planning applied the normalized precedence: Just, then Make, then npm/package scripts, then propose adding Just if no runner exists.
- [ ] A setup plan was produced before applying changes.
- [ ] The setup plan distinguished existing commands, recommended new commands, optional repo-local/dev tooling additions, and items requiring separate approval.
- [ ] Detection failures or unknowns were reported as setup context rather than silently guessed.
- [ ] Any performed changes stayed within approved setup scope and the approved setup plan.
