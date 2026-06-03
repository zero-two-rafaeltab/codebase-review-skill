# Goal

Create a local Hermes skill, distributed from a GitHub repository, that guides agents through explicit codebase health reviews focused on architecture, maintainability, refactoring opportunities, pain spots, and prevention of future issues.

This repository contains historical goal context plus the initial minimal local skill package. Goal documents may describe broader future capabilities; the current shipped workflow is defined in skills/codebase-review/SKILL.md.

## What we want to achieve

We want a repeatable review procedure that can handle large codebases better than a single-agent, ad-hoc read-through. The review should produce a useful executive summary for the user, while writing richer report artifacts to files for progressive disclosure.

The workflow should help users answer:

- What are the most important code quality or architecture risks in this scope?
- Which pain spots are likely to slow down future work?
- Which refactoring opportunities are worth discussing?
- Which findings are evidence-backed versus speculative?
- What should we do next, if anything?

## Key principle

Discovery and decision-making are separate.

A review should discover and document findings, not immediately create issues, write ADRs, refactor code, or mutate project workflow. After discovery, the user decides what to do with each finding.

## Current package scope

The current shipped skill is intentionally minimal. It does not include:

- skill templates;
- install scripts;
- review command setup;
- language playbooks;
- CI integration;
- GitHub issues created from these requirements.
