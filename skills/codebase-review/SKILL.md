---
name: codebase-review
description: Use when performing a Phase 1 minimal manual codebase health review. Guides scope confirmation, read-only discovery, basic report writing, and concise executive-summary handoff without advanced orchestration.
version: 0.1.0
author: Zero Two / Rafael
license: MIT
metadata:
  hermes:
    tags: [codebase-review, architecture, maintainability, manual-review]
    related_skills: []
---

# Codebase Review

## Overview

This is the **minimal Phase 1 workflow** for a local Hermes codebase health review skill.
It is intentionally thin: use it to guide a small or narrow manual review, write a basic report artifact, and give the user a concise executive summary.

Discovery and decision-making are separate. During discovery, stay read-only except for report artifacts.
Do not create issues, ADRs, commits, PR comments, or code changes unless the user starts a separate post-discovery workflow.

## When to Use

Use this skill when the user asks for a codebase health, architecture, maintainability, refactoring-opportunity, pain-spot, or risk review.

Do not use this minimal Phase 1 workflow for:

- automated setup of review tooling;
- full multi-agent orchestration;
- language-specific review playbooks;
- CI integration;
- implementing fixes during discovery.

Those are later-phase capabilities.

## Minimal Phase 1 Flow

1. **Confirm the target and scope.** Identify the repository, package, domain, feature path, or concern to review. If the user did not provide a narrow scope, propose one that is small enough for a manual Phase 1 pass.
2. **Ask for go/no-go before discovery.** Summarize the proposed scope, review depth, expected artifact path, and side-effect boundary. Wait for approval before expensive review work.
3. **Review read-only.** Inspect files, docs, tests, dependency manifests, and local commands only as needed for the approved scope. Treat command failures as diagnostic signal, not permission to fix.
4. **Write a basic report artifact.** Put the report in a predictable local path such as `review/reviews/<YYYY-MM-DD>-codebase-review.md` unless the project already has a clearer review folder convention.
5. **Reply with a concise executive summary.** Keep chat short. Link or point to the report file. Present findings as decision candidates, not accepted work.

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
- dependency, CI, or tooling changes.

If the user wants action after the report, start a separate post-discovery decision or implementation workflow.

## Basic Report Skeleton

Use this skeleton for the minimal Phase 1 artifact:

```markdown
# Codebase Review — <scope>

Date: <YYYY-MM-DD>
Scope: <approved scope>
Mode: Minimal Phase 1 manual review

## Executive summary

- <top observation or finding>
- <top risk or pain spot>
- <recommended discussion focus>

## Scope and method

- Reviewed: <paths, docs, commands>
- Not reviewed: <known gaps>
- Side effects: report artifact only

## Findings / observations

### <Finding title>

- Area: <component, path, or concern>
- Summary: <what appears to be happening>
- Evidence: <file paths, command output summaries, or doc references>
- Impact: <why this might matter>
- Suggested next discussion: <accept/reject/research/document/plan/fix later>

## Coverage gaps

- <what a deeper review should inspect later>

## Decision queue

Findings above are candidates for discussion. They are not approved issues, ADRs, or implementation work.
```

## Chat Summary Format

After writing the report, keep the chat response short:

1. Report path.
2. 2-4 bullets for the most important observations.
3. Clear next options, such as walking through findings, accepting/rejecting candidates, creating selected issues, planning a refactor, or doing deeper research.

## Verification Checklist

- [ ] Scope and go/no-go were approved before discovery.
- [ ] Discovery stayed read-only except for the report artifact.
- [ ] No issues, ADRs, commits, PR comments, or code changes were created during discovery.
- [ ] A basic report file was written.
- [ ] Chat response stayed concise and pointed to the report.
- [ ] Findings were framed as decision candidates.
