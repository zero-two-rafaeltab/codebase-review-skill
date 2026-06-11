# Issue 50 — wallpaperdb review setup dogfood

## Triage

- **Dogfood target:** `/home/rafaeltab/wallpaperdb` (local clone; remote observed as `rafaeltab/wallpaperdb`).
- **Skill repository artifact:** `/home/rafaeltab/work/codebase-review-skill/reviews/2026-06-11-issue-50-wallpaperdb-setup-dogfood.md`.
- **Workflow exercised:** Phase 4 review setup workflow: setup proposal, bounded capability detection, setup plan, approved application decision, stable setup documentation handling, command verification, diagnostic classification, CI policy handling, summary, lessons learned, and follow-up candidates.
- **Target-repository side effects:** no target files were edited. Existing untracked files in `wallpaperdb` were present before the dogfood run and left untouched: `infra/docker-compose.apparmor-unconfined.yml`, `infra/docker-compose.apps.apparmor-unconfined.yml`.
- **Durable output policy:** this artifact records verification classifications and evidence categories only. It intentionally does not store raw command output, coverage percentages, full audit listings, lint warning dumps, or volatile file-count baselines as target setup baselines.
- **Scope boundary:** this dogfood did not expand into language-specific playbooks, CI integration, issue/ADR creation, production refactoring, production dependency changes, or application code changes.

## Setup approval proposal exercised

The setup request was classified as **review setup**, not review discovery, because the goal was to dogfood setup workflow support for future codebase-review runs.

Proposed review setup scope:

- **Target repository:** `/home/rafaeltab/wallpaperdb`, inferred from issue #50 and the local clone path supplied by the controller.
- **Setup goal:** evaluate how the Phase 4 setup workflow behaves on a real monorepo target and identify an approved, low-risk local command/documentation surface for future codebase reviews.
- **Planned inspection after approval:** lightweight inspection of repository metadata, README/docs, manifests, runner files, package scripts, obvious review/report conventions, and read-only CI file/status indicators.
- **Possible allowed changes after approval:** minimal repo-local/dev review command aliases and stable setup documentation if the plan justified them; otherwise an intentional non-change recorded in this skill repository artifact.
- **Forbidden changes:** no production code changes, production dependency changes, CI creation or edits, required-check/branch-protection/merge-blocker changes, deployment/secrets/config changes, dependency/tool installs, issues, ADRs, or app refactoring.
- **Planned documentation/commands:** select an existing runner using the setup precedence (Just, Make, package scripts, add Just only if no runner exists and approved), then document proposed commands and CI policy. Because this was a dogfood slice against an active local clone, target-repository edits required explicit plan approval and a minimal-risk reason.
- **Expected output artifacts:** a primary dogfood artifact under this repository's `reviews/` folder, target status summary, command verification classifications, lessons learned, and follow-up candidates.
- **Approval gate:** no target mutation or install before approval. The controller's issue instructions supplied approval to run read-only discovery and safe local verification commands, but not to perform broad production changes.

## Capability detection

### Evidence inspected

- Git status and remote metadata for `/home/rafaeltab/wallpaperdb`.
- Root manifests and runners: `package.json`, `pnpm-lock.yaml`, `turbo.json`, `Makefile`.
- README and documentation signals: `README.md`, `CONTRIBUTING.md`, tracked `apps/docs/content/docs/**` paths, workspace `README.md` files.
- Workspace manifests under `apps/*/package.json`, `packages/*/package.json`, and `infra/package.json`.
- Obvious CI files: `.github/workflows/ci.yml`, `.github/workflows/e2e.yml`.
- Existing generated/build/cache paths were observed but not treated as review baselines.

### Detection results

| Capability area | Detection | Evidence / confidence |
| --- | --- | --- |
| Likely languages | TypeScript/TSX dominant, with Markdown/MDX and JSON/YAML docs/config. | Root and workspace `package.json` files; many `tsconfig.json` and `vitest.config.ts` files; README technology section. High confidence. |
| Likely frameworks/platforms | pnpm + Turborepo monorepo; Fastify services; frontend/docs apps; Vitest tests; Biome lint/format; Docker Compose local infra. | `packageManager: pnpm@10.18.3`, `turbo.json`, README technology section, service package manifests, `infra/docker-compose*.yml`, `.github/workflows/*.yml`. High confidence for broad platform; individual service details were not deeply reviewed. |
| Package manager and dependency files | pnpm workspace with `pnpm-lock.yaml`. | Root `package.json` and lockfile. High confidence. |
| Existing runners | Existing `Makefile` and package scripts. No `Justfile` observed. Runner precedence selects **Make** for future normalized review aliases if target edits are later approved. | Root `Makefile` already exposes broad local commands; root and workspace package scripts exist. |
| Existing review/test/static tooling | Existing Make/Turbo surface covers build, lint, type check, unit/integration/e2e tests, coverage merge/summary, and CI parity; pnpm audit is available as a dependency diagnostic but not currently normalized in Make. | `Makefile` targets include `build`, `lint`, `check-types`, `test-unit`, `test-integration`, `test-e2e`, `coverage-summary`, `ci`; package scripts and `turbo.json` corroborate. |
| Review/report conventions | No target `reviews/` convention was selected or created during this dogfood run. Architecture/docs conventions exist under `apps/docs/content/docs/`; agent skills exist under `.agents/skills/` per README. | Tracked docs paths and README. Future setup docs could use an existing docs guide or a new `docs/codebase-review.md`, but no target file was created in this slice. |
| CI status | CI detected. | `.github/workflows/ci.yml` runs build/lint/type-check, unit/integration tests, coverage merge, and Codecov upload. `.github/workflows/e2e.yml` runs build, infra/app stack, E2E tests, isolation verification, and teardown. |
| Known gaps / unknowns | This dogfood did not deeply inspect every workspace, did not install dependencies, did not edit target docs/commands, and did not validate required-check/branch-protection policy. | Intentional setup-slice limits. |

## Setup plan produced

### Existing commands to reuse or document

The target already has a Make runner, so the normalized runner precedence selects Make rather than adding Just or preferring package scripts.

| Review capability | Existing command or safe equivalent | Plan status |
| --- | --- | --- |
| Sizing | `git ls-files` plus extension summary for quick orientation. | Document as a proposed `review-size` equivalent; no target alias added in this slice. |
| Static checks | `make lint`; `make check-types` for type checking; `make check` aggregates build/lint/type-check. | Reuse existing commands. |
| Dependency/architecture | `pnpm audit --audit-level moderate` for dependency vulnerability signal; manual architecture review remains separate. | Document as dependency diagnostic candidate; do not claim architecture coverage. |
| Tests | `make test-unit` for bounded local tests; `make test-integration` and `make test-e2e` exist but are container/heavy. | Verify unit tests; classify heavier suites as intentionally skipped for setup dogfood. |
| Coverage | `make coverage-summary` exists but depends on generated coverage artifacts; `pnpm test:coverage` exists at root but is not a stable Make review target. | Verify existing summary command and classify current missing-artifact behavior without storing coverage values. |
| Aggregate review | `make ci` exists for full local CI parity, but includes heavy/e2e/container work. | Intentionally skipped as too broad/heavy for this setup dogfood. |

### Recommended new normalized commands

A future approved target-repository setup could add Make aliases such as `review`, `review-size`, `review-static`, `review-deps`, `review-test`, and `review-coverage`, wrapping the existing commands above. This dogfood did **not** add them because:

1. `wallpaperdb` already has an extensive Make command surface and CI workflows.
2. The local target clone was not clean before the run; unrelated untracked infra files were present.
3. Issue #50 asked for the dogfood artifact in this skill repository and allowed target no-op/non-change if recorded.
4. Adding aliases and documentation to the target would create cross-repository side effects beyond the minimum needed to validate the workflow slice.

### Stable setup documentation path decision

- **Candidate for future target setup:** a target doc such as `docs/codebase-review.md` or an existing docs guide under `apps/docs/content/docs/guides/` could document review commands, diagnostic interpretation, limitations, skipped areas, and CI status.
- **Approved application for this slice:** intentional target non-change. The stable setup documentation contract is represented by this dogfood artifact, not by a target-repository docs edit.
- **Reason:** this issue's acceptance criteria require the primary artifact under this repository's `reviews/` folder and do not require committing target-repository setup changes.

### Items requiring separate approval

- Any target-repository Makefile aliases or docs additions.
- Any CI workflow edits, required-check configuration, branch-protection rules, merge blockers, badges, caches, matrices, or deployment behavior.
- Any production or dev dependency upgrades in response to dependency diagnostics.
- Any codebase health review, language-specific playbook, ADR, issue creation, or application refactoring.

## Approved application and target changes

Approved setup application for this dogfood run was an **intentional non-change** in `/home/rafaeltab/wallpaperdb` plus durable documentation in this skill repository's `reviews/` folder.

- **Target files created/edited:** none.
- **Target commands added/updated:** none.
- **Target docs added/updated:** none.
- **Target CI changed:** none.
- **Production/deployment/secrets/config changed:** none.
- **Reasoning recorded:** future Make aliases and target docs are plausible but should be done only as a separate target-repository setup change after explicit approval, especially because the local target clone already had unrelated untracked files.

## Setup verification and diagnostic classification

Commands were run as setup checks, not as a codebase health review. Non-zero exits were classified by failure shape. Raw outputs are intentionally not copied here as durable baselines.

| Capability | Command / check run | Ran? | Classification | Evidence category / reason | Durable output policy |
| --- | --- | --- | --- | --- | --- |
| Runner parse/list | `make help` | yes | clean/successful signal | Make runner listed the existing local command surface successfully. | Raw help text not stored; rerun for current command list. |
| Sizing | `git ls-files` with extension summary | yes | clean/successful signal | Produced bounded repository orientation counts and dominant extension categories. | Counts are not committed as a target baseline; rerun when needed. |
| Static checks | `pnpm exec turbo run lint --log-order grouped --summarize=false` | yes | useful diagnostic signal | Lint tooling executed across scoped packages and reported warning-level diagnostics while completing successfully. | Warning details are not stored; rerun lint for current findings. |
| Static/type checks | `pnpm exec turbo run check-types --summarize=false` | yes | clean/successful signal | Type-check tooling executed successfully across the Turbo scope. | Raw task logs not stored. |
| Unit tests | `pnpm exec turbo run test:unit --summarize=false` | yes | clean/successful signal | Unit-test task executed successfully for packages with `test:unit`; cached/non-cached mix was accepted as setup signal. | Test case lists and timings not stored as durable baselines. |
| Dependency diagnostics | `pnpm audit --audit-level moderate` | yes | useful diagnostic signal | Audit command executed and returned dependency vulnerability findings with non-zero exit; this is review signal, not broken setup. | Full advisory dump not stored; rerun audit for current dependency status. |
| Coverage summary | `make coverage-summary` | yes | broken tooling | Existing command failed because expected merged coverage input was not available and suggested a non-existent `make test-coverage` command; this indicates a command/documentation mismatch for future setup follow-up. | No coverage values or missing-file logs stored. |
| Integration tests | `make test-integration` | no | intentionally skipped | Uses Testcontainers/container infrastructure and is heavier than needed for this dogfood setup slice. | Skip reason recorded; no baseline. |
| E2E tests | `make test-e2e` / `make ci` aggregate | no | intentionally skipped | Heavy container/browser workflow requiring infrastructure/app stack; aggregate would obscure per-command classification. | Skip reason recorded; no baseline. |
| CI mutation | edit `.github/workflows/*.yml` or branch protection | no | intentionally skipped | CI policy is read-only during setup. Existing CI was inspected but not changed. | No CI edits or policy baselines stored. |

### Diagnostic failure classification notes

- `pnpm audit --audit-level moderate` exited non-zero because it found dependency advisories. That is classified as **useful diagnostic signal**, not broken tooling, because the command performed its intended diagnostic work.
- `make coverage-summary` exited non-zero because the required coverage artifact was absent and the remediation message pointed to a command name not present in the `Makefile`. That is classified as **broken tooling** for the coverage-summary command surface, not as a coverage result.
- Lint warnings are classified as **useful diagnostic signal** because the linter ran and produced review-relevant warnings without indicating runner unavailability.
- Heavy integration/e2e/aggregate commands were intentionally skipped because setup verification should remain bounded and should not start infrastructure or hide child command classifications.

## CI policy handling

CI was inspected read-only only.

- **Detected CI:** GitHub Actions workflows at `.github/workflows/ci.yml` and `.github/workflows/e2e.yml`.
- **Current workflow summary:** CI installs pnpm/Node, runs Turbo build/lint/type-check, unit/integration tests, coverage merge, and Codecov upload. E2E workflow builds, starts infrastructure and application stack, runs browser/E2E checks, captures diagnostics on failure, verifies E2E isolation, and tears down services.
- **Future integration consideration:** if target-repository normalized review commands are later added, they could be considered as wrappers around existing CI-equivalent tasks or documentation-only local commands.
- **Policy boundary honored:** no CI workflows, required checks, branch protections, badges, deployment automation, cache behavior, matrix configuration, or merge blockers were created or changed. Any CI integration requires a separate explicit approval workflow.

## Concise setup summary

`wallpaperdb` already has a mature Make/Turbo/pnpm command surface and GitHub Actions CI. The Phase 4 setup workflow selected Make by precedence, identified plausible future normalized review aliases, verified representative safe commands, classified diagnostic versus broken/skipped outcomes, and recorded CI policy without mutating the target repository. The only durable implementation artifact for issue #50 is this setup dogfood report under the skill repository's `reviews/` folder.

## Lessons learned

1. The setup workflow benefits from allowing an explicit **approved non-change** when a target already has a rich command surface, the local clone is dirty, or the issue's durable deliverable is a dogfood report rather than target setup changes.
2. Verification classification needs a clear distinction between **diagnostic signal** and **broken tooling**: audit findings and lint warnings are useful diagnostic signal; a coverage summary command pointing at unavailable/mismatched prerequisites is broken tooling.
3. The durable-output rule is important in practice: commands can produce large, volatile outputs, especially dependency audits. The artifact should store classifications and evidence categories, not raw logs.
4. Existing aggregate commands such as `make ci` can be inappropriate for setup verification because they are heavy and can obscure child-command classifications.
5. Read-only CI inspection is sufficient to document policy and future integration points without silently converting setup into CI work.
6. Runner precedence is easy to apply when a target has a root `Makefile`, but future docs should explain why Make was selected over package scripts in a pnpm/Turbo monorepo.

## Follow-up candidates

These are candidates only, not approved work:

1. In `wallpaperdb`, consider a separately approved setup change adding Make aliases for `review-size`, `review-static`, `review-deps`, `review-test`, `review-coverage`, and `review`, wrapping existing safe commands and documenting heavy skips.
2. In `wallpaperdb`, consider a separately approved target docs page such as `docs/codebase-review.md` or an `apps/docs/content/docs/guides/` page that records command purpose, invocations, interpretation, limitations, CI policy, and no-raw-baseline rules.
3. Investigate the `make coverage-summary` mismatch in a separate target-repository task: it references missing coverage input and suggests `make test-coverage`, which was not found in the observed Makefile.
4. Decide separately whether dependency audit findings should become dependency-upgrade issues or review findings; this dogfood run only classified the command signal.
5. If normalized review commands are later added, consider whether CI should invoke them only through a separate explicit CI workflow proposal.

## Acceptance criteria checklist

- [x] Used the locally cloned `wallpaperdb` repository at `/home/rafaeltab/wallpaperdb` as the target.
- [x] Exercised setup proposal, capability detection, setup planning, approved application decision, setup documentation handling, command verification, diagnostic classification, and CI policy handling.
- [x] Recorded command verification classifications without storing raw command output as durable setup baselines.
- [x] Wrote the primary artifact under this repository's `reviews/` folder.
- [x] Captured lessons learned and follow-up candidates.
- [x] Did not silently expand scope into language-specific playbooks, CI integration, issue/ADR creation, production refactoring, or application changes.
