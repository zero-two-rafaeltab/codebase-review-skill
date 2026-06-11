# Codebase Review Skill

This repository contains the goal documents for a local Hermes skill that supports explicit codebase health reviews, plus an installable skill package.

The current implementation provides a focused manual review workflow, report/finding output guidance, and a setup workflow for preparing repositories with durable review documentation and normalized local review commands.

## Install locally

From a clone of this repository:

```bash
mkdir -p ~/.hermes/skills
ln -sfn "$(pwd)/skills/codebase-review" ~/.hermes/skills/codebase-review
```

Then start a fresh Hermes session and load the skill:

```bash
hermes --skills codebase-review
```

Or from an existing session after restarting/reloading skills:

```text
/skill codebase-review
```

You can verify that Hermes sees the package with:

```bash
hermes skills list | grep codebase-review
```

## Skill package

- [Codebase review skill](skills/codebase-review/SKILL.md)

## Goal docs

- [Goal overview](goal/README.md)
- [Product requirements](goal/product-requirements.md)
- [Design decisions](goal/decisions.md)
- [Open questions](goal/open-questions.md)
