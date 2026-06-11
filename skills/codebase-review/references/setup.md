# Review Setup Workflow

Use this reference only when the user explicitly asks to set up codebase-review support for a repository. Setup is separate from review discovery/execution: it may eventually add documentation, commands, or repo-local helper conventions, but only after the user approves a setup-specific scope proposal.

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
- prefer repository-local docs and command conventions that already exist;
- make only the approved file/doc/command changes;
- keep production code, CI, deployment, secrets, and unrelated tooling out of scope;
- summarize exactly what changed and how to use it;
- do not run a health review as part of setup unless the user starts the separate review workflow.

If setup reveals that a dependency install, CI change, production change, or broader migration would be useful, stop and ask for a separate explicit approval workflow instead of doing it.

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
- [ ] Any performed changes stayed within approved setup scope.