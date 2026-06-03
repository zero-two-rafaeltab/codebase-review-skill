# Open Questions

These questions are intentionally left for later product design. They should not block the current goal documentation.

## Naming

- What should the repository be named long-term?
- What should the final local Hermes skill be named exactly?

## Report folder naming

- Should review runs use `review/YYYY-MM-DD/` or `reviews/YYYY-MM-DD/`?
- Should report folders include the target name when multiple reviews are run on the same day?

## Setup documentation location

- Should target repositories document setup under `docs/codebase-review/`?
- Are there cases where `.hermes/`-scoped docs are preferable?

## Language playbooks

- Which ecosystem playbooks should be written first?
- How much research is required before a playbook is considered trustworthy?
- Should framework-specific playbooks eventually split from language playbooks?

## Severity rubric wording

- What exact wording should define Critical, High, Medium, Low, and Observation?
- Should the user-facing language be blunt, formal, or configurable?

## Review profiles

- What precise expectations define birds-eye, standard, deep, aggressive, and relaxed profiles?
- Should profiles include time/compute budgets?

## Tool setup policy

- Which review tools are considered appropriate repo-local or dev dependencies by default?
- When should setup avoid adding tools even if useful?

## Installation workflow

- Should the repository provide an install script, symlink mode, or both?
- Should validation be included before installing the local Hermes skill?

## Governance

- How should changes to the goal documents be reviewed before implementation starts?
- Should future implementation work be tracked in issues only after the goal is accepted?
