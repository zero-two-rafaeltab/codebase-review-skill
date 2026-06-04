# Subagent Manifest Contract

Use this reference when delegating cartography or specialist review slices. The manifest is a compact audit trail for synthesis: it records what a subagent actually inspected, what it assumed, and what it emitted so the top-level orchestrator can cite and compare outputs without rereading every raw file or command output.

The manifest supports the normal finding model. It does not replace severity, confidence, evidence strength, evidence references, observations, or the decision queue.

## Format

Return the manifest as Markdown. It may be embedded directly in the review report, pasted below a subagent summary, or saved as a small linked Markdown artifact. Do not require new tooling or machine-readable schemas.

Use this template unless a smaller relevant subset is enough for the delegated task:

```markdown
## Subagent manifest — <cartography | specialist: lens/slice>

- Assigned scope: <approved scope target plus explicit exclusions>
- Review lens: <cartography | architecture boundaries | workflow/data flow | maintainability/refactoring | tests/docs | risk/operability | dependency usage | other approved lens>
- Evidence inspected:
  - Files/docs: <paths or docs actually opened/read; include short purpose when helpful>
  - Commands: <safe inspection commands actually run, with one-line outcome summaries; write "none" if none>
- Artifacts written: <report/manifests/notes written by this subagent, or "none">
- Findings/observations emitted: <F-###/O-### IDs or provisional titles; include severity, confidence, and evidence strength when this subagent classified them>
- Uncertainties: <unknowns, weak evidence, unavailable commands, or places where interpretation may be wrong>
- Out of scope / not inspected: <approved exclusions, generated/vendor/build areas skipped, paths/lenses not covered>
- Inherited or unverified context: <assumptions from the user, orchestrator, cartography, docs, or prior summaries that were not independently checked>
- Coverage confidence: <High/Medium/Low> — <one sentence explaining representativeness of the inspected evidence>
```

## Evidence vs. assumptions

Keep inspected evidence separate from inherited or unverified context:

- Put only files/docs actually opened and commands actually run under `Evidence inspected`.
- Put user statements, orchestrator summaries, cartography hints not rechecked by the specialist, and inferred project intent under `Inherited or unverified context`.
- If a finding depends on inherited context, keep the finding confidence appropriate and say so in its evidence or uncertainty notes.

## Cartography subagent subset

A cartography subagent should return the normal cartography summary plus this manifest or a compact subset with at least:

- assigned scope and exclusions;
- review lens `cartography`;
- files/docs and commands actually inspected;
- discovered artifacts written, if any;
- findings/observations emitted, or none;
- uncertainties and out-of-scope areas;
- inherited/unverified context;
- coverage confidence.

Cartography may list likely review slices, but it should not classify specialist findings unless it directly inspected enough evidence to emit an observation.

## Specialist subagent subset

A specialist subagent should return its slice summary plus this manifest or a compact subset with at least:

- assigned scope and approved lens;
- files/docs and commands actually inspected;
- findings/observations it emitted using the existing severity/confidence/evidence-strength fields;
- uncertainties, out-of-scope areas, and inherited/unverified context;
- coverage confidence.

Specialist outputs should feed the report's findings/observations and decision queue. They do not approve decisions, create tasks, or replace the user's post-discovery decision phase.
