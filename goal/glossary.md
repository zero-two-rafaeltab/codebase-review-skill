# Glossary

## Codebase health review

An explicit review workflow focused on architecture, maintainability, refactoring opportunities, pain spots, testing gaps, documentation gaps, and future-risk prevention.

## Discovery phase

The phase where the review observes, analyzes, and reports findings. It is read-only except for report artifacts.

## Decision phase

The phase after discovery where the user decides what to do with findings, such as creating issues, rejecting findings, writing ADRs, launching research, or planning refactors.

## Finding

An evidence-backed or explicitly confidence-labeled review result. A finding is not automatically an accepted task.

## Observation

A weak or speculative signal that may deserve attention but is not yet strong enough to treat as a finding.

## Severity

How much a finding can hurt the project if left alone. Severity includes maintainability and architecture pain, not only production incidents.

## Confidence

How sure the reviewer is that the finding is real and accurately characterized.

## Evidence strength

The kind of support behind a finding, such as source evidence, tool output, runtime/test evidence, Git history, documentation mismatch, or heuristic pattern.

## Churn signal

A cheap prioritization signal based on how often files or components change in Git history.

## Cartography / sizing

A preliminary subagent pass that maps the approved review scope, estimates its size and shape, discovers domains/components, collects cheap metrics, and proposes a full review agent plan.

## Progressive disclosure

A reporting approach where chat shows only concise summaries and next options, while deeper details are available in linked files.
