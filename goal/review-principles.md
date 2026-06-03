# Review Principles

## Preserve user control

The user approves scope before review work starts and approves the full subagent plan after sizing.

## Do not turn findings into actions automatically

A finding may become an issue, an ADR, a research task, a refactoring plan, or nothing. The user decides after discovery.

## Prefer evidence over vibes

Findings need evidence, confidence, and severity justification. Weak signals should be labeled as observations or low-confidence findings.

## Make the top-level report skimmable

The user should be able to quickly see severity counts, top findings, main pain sources, and where to look next.

## Keep raw output away from the user unless requested

Raw logs and command outputs are useful for auditability, but they should not dominate chat or summary reports.

## Let commands reveal problems

Review commands are diagnostic. A non-zero exit because a tool found issues is useful signal, not setup failure.

## Do not pretend planned work exists

Discovery should not infer roadmap pressure unless the user explicitly provides it.

## Delegate investigation

The top-level orchestrator should protect context and coordinate. Cartography and review work belong to subagents.
