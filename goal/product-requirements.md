# Product Requirements

## Problem Statement

Large codebases are difficult to review deeply with a single agent. A useful review must look beyond bugs and include architecture quality, refactoring opportunities, maintainability pain, testing gaps, documentation gaps, and risks that could compound over time.

The user experience must avoid overwhelming the user. Detailed reports can exist in files, but the chat response should present only a concise executive summary and clear next options.

## Solution Goal

Create a future local Hermes skill that supports explicit codebase health reviews with a structured, multi-stage, multi-agent workflow.

The skill should support:

- explicit intake and scope confirmation;
- setup of review tooling before reviews;
- subagent-based cartography and sizing;
- adaptive subagent planning;
- discovery-only review execution;
- progressive-disclosure report files;
- concise executive summaries in chat;
- a post-review decision phase controlled by the user.

## Users

1. As a user, I want to request a codebase health review, so that I can understand architectural and maintainability risks before deciding what to change.
2. As a user, I want the agent to propose the interpreted review scope before starting, so that I can correct misunderstandings early.
3. As a user, I want the agent to ask for go/no-go before expensive review work, so that I stay in control of scope and effort.
4. As a user, I want a concise executive summary in chat, so that I can quickly understand the most important findings.
5. As a user, I want detailed reports in files, so that I can dig deeper only where needed.
6. As a user, I want findings to be decision candidates, so that I can accept, reject, research, document, or ignore each one.
7. As a user, I want no GitHub issues or ADRs created during discovery, so that the review does not prematurely commit the project to actions I may disagree with.
8. As a user, I want severity to reflect project pain, not only production bugs, so that architectural and maintainability problems can be prioritized appropriately.
9. As a user, I want confidence and evidence strength shown separately from severity, so that I can distinguish proven issues from plausible concerns.
10. As a user, I want reviews to support full repositories, domains, packages, vertical workflows, horizontal concerns, and dependency/usage reviews, so that the workflow fits the actual question.
11. As a user, I want review setup to establish predictable local commands and docs, so that future reviews are less ad hoc.

## Review Modes and Scope Shapes

The future skill should support multiple review depths:

- birds-eye;
- standard;
- deep;
- aggressive;
- relaxed.

It should support multiple scope shapes:

- full repository;
- package or module;
- domain or subdomain;
- feature or vertical workflow;
- horizontal cross-cutting concern;
- dependency and usage review.

The agent should infer a proposed scope from the user query, present it clearly, and ask for go/no-go. If the user says no, the user may adjust scope and the agent should propose again until approval.

## Discovery Versus Decision Phase

The discovery phase should be read-only except for report artifacts. It should not create issues, write ADRs, make code changes, commit, or post PR comments.

The decision phase begins only after the executive summary is delivered. During decision-making, the user may choose to:

- walk through findings one by one;
- create selected issues;
- reject or ignore findings;
- document a decision with an ADR;
- launch deeper asynchronous research;
- revise severity based on context known to the user;
- create a refactoring plan;
- implement changes in a separate workflow.

## Progressive Disclosure

The chat response should show only the executive summary and next options. Detailed information should be written to a predictable report folder.

The report should be easy to skim. The top-level report should quickly show counts by severity, top findings, main pain sources, and links to deeper component or category reports.

Every report page should begin with triage information before details.

## Setup Requirements

Before reviews, the user should be able to run an explicit setup workflow for a repository.

Setup should:

- detect languages, frameworks, runners, and existing tooling;
- install appropriate repo-local or dev review tooling after approval;
- create predictable review command aliases;
- document the review setup in a stable location;
- verify that commands run and produce useful diagnostic output;
- distinguish command findings from broken tooling;
- avoid storing raw baseline outputs as durable repo docs.

Setup should not change production dependencies, alter CI, create CI from scratch, refactor code, or enforce new merge blockers without separate approval.

## Normalized Review Command Expectations

Review-enabled repositories should expose normalized local review commands using the repository's existing runner convention when possible.

Preferred runner order:

1. Just
2. Make
3. npm/package scripts
4. If no runner exists, add Just

The normalized command set should cover sizing, static checks, dependency/architecture checks, tests, coverage, and a reasonable aggregate command where appropriate.

Review command failures are expected when they reveal findings. Setup success means the commands execute and provide useful diagnostic signal, not that the codebase passes every check.

## CI Expectations

CI integration is separate from setup.

If no CI exists, the workflow should not create CI.

If CI exists, setup may inspect it and document possible integration, but CI changes require explicit approval after setup.

## Reporting Requirements

The final review report should include:

- executive summary;
- counts by severity;
- top findings;
- coverage and gaps;
- component/domain overviews;
- category reports;
- individual finding pages;
- subagent manifests;
- raw command outputs for that review run only.

The user should be guided through the folder structure. The main report should help the user quickly understand how serious the situation is and where to look next.

## Finding Requirements

Every finding should include:

- severity;
- confidence;
- evidence strength;
- churn signal when cheaply available;
- severity justification;
- evidence references;
- impact;
- suggested next actions;
- decision options.

Severity means how much the issue can hurt the project if left alone. It does not only mean likelihood of a current production bug.

## Future Work Context

Future work should generally be ignored during discovery severity unless the user explicitly provides it during intake.

During the post-discovery decision phase, the user may provide future-work context and explicitly revise severity.
