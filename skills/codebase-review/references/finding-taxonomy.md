# Finding Taxonomy

Use this reference whenever classifying review signals. Severity, confidence, and evidence strength must remain separate.

## Finding Taxonomy

Use this taxonomy to make findings easier to compare and decide on. Keep the concepts separate:

- **Severity** is the project pain if the finding is left alone. It includes architecture and maintainability pain, delivery drag, operational risk, reliability risk, security/data-loss risk, and future-change cost. It is not a measure of reviewer certainty.
- **Confidence** is how sure the reviewer is that the finding is real and accurately characterized. Confidence is separate from severity: a potentially severe concern can still be low confidence if the review only saw partial evidence.
- **Evidence strength** is the kind and directness of support behind the finding. Evidence strength is separate from confidence: strong evidence can still be interpreted cautiously, and weak evidence should not be presented as proof.

### Severity levels

| Severity | Use when the pain if left alone is... |
| --- | --- |
| Critical | Likely to cause major project harm soon or block essential work, such as data loss/security exposure, severe production risk, or an architectural constraint that already prevents necessary delivery. |
| High | Likely to create serious reliability, maintainability, architecture, or delivery pain across an important path or team if not addressed. |
| Medium | Meaningful but bounded pain that will slow change, increase defects, or compound in a specific component, workflow, or concern. |
| Low | Minor, localized, or easily reversible pain that is worth noting but unlikely to drive near-term decisions by itself. |
| Observation | A weak, early, contextual, or informational signal that may deserve attention but is not strong enough to treat as a proven issue. |

Prefer the lowest severity that honestly describes the expected project pain. Do not raise severity because evidence is plentiful or because the reviewer feels certain; use confidence and evidence strength for that. Do not raise severity for speculative future work or roadmap assumptions unless that context was explicitly supplied by the user during intake or scope approval.

Use `Observation` when the item is primarily an early signal, useful context, a pattern worth watching, or a research prompt. Use Low/Critical severity levels only for findings that are supported strongly enough to describe actual project pain if left alone.

### Confidence levels

| Confidence | Use when... |
| --- | --- |
| High | Multiple signals or direct inspection make the finding very likely to be real and accurately scoped. |
| Medium | The finding is plausible and supported, but some scope, impact, or intent is uncertain. |
| Low | The signal may be real, but the review lacks enough context to claim it confidently. Low-confidence items should usually be phrased as candidates for research. |

### Evidence strength categories

List the strongest applicable evidence category or categories for each finding:

- **Source evidence:** direct code/configuration/schema references show the behavior, dependency, coupling, missing boundary, or maintainability problem.
- **Tool output:** safe inspection commands, linters, type checkers, dependency analyzers, test runners, or other existing project tools reported relevant signal.
- **Runtime/test evidence:** executed tests, examples, local runs, logs, fixtures, or reproducible behavior show the issue or gap.
- **Git history or churn:** recent commits, blame, file churn, or repeated changes suggest active pain or unstable ownership. Use only when cheap to inspect; do not require automated churn analysis. If cheap churn data is not available, the finding's churn signal may be `unknown` or `not checked`.
- **Documentation mismatch:** README, docs, comments, examples, or API contracts disagree with the implementation or with each other.
- **Heuristic pattern evidence:** reviewer-recognized maintainability or architecture pattern, such as duplication, hidden conventions, oversized modules, tangled ownership, or missing seams, without stronger direct proof yet.

Weak signals, especially heuristic-only signals, should be routed to Observation or labeled as low confidence unless supported by stronger evidence. Do not present a hunch, single ambiguous example, or unexplored pattern as a proven issue. When evidence is weak, say so directly in the Evidence field instead of implying there are references you did not inspect.
