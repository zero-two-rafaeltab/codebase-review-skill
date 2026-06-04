# Post-Discovery Decision Phase

Use this reference after the report artifact and executive summary are delivered. Findings remain decision candidates until the user chooses follow-up.

## Post-Discovery Decision Phase

The post-discovery decision phase begins only after the report artifact is written and the executive summary is delivered. Its purpose is to help the user interpret and triage findings; it is not part of discovery and should not be treated as approval to mutate the repository or project systems.

In this phase, keep each finding as a decision candidate until the user chooses what to do. Useful user choices include:

| Decision | Meaning |
| --- | --- |
| Accept | The user agrees the finding is valid enough to track or act on later. |
| Reject / ignore | The user disagrees with the finding, considers it invalid, or chooses no follow-up now. |
| Create an issue | Select a finding for issue creation; open or draft the tracking issue only in a separate workflow after explicit user approval. |
| Document an ADR / decision | Capture context or rationale in docs or a decision record only in a separate workflow after explicit user approval. |
| Launch deeper research | Run a separate investigation when discovery evidence is incomplete or context-sensitive. |
| Revise severity | Adjust decision-phase severity using user-provided business, roadmap, ownership, migration, or operational context that was not available during discovery. Keep this separate from the original discovery severity. |
| Plan / implement separately | Turn a selected finding into a plan or implementation task only in a separate workflow after explicit user approval. |

During decision discussion:

- do not automatically create GitHub issues, ADRs, docs, commits, PR comments, or code changes from findings;
- keep discovery assessment separate from the user's decision context;
- if the user adds business, roadmap, ownership, migration, or operational context, record it as user-provided decision context rather than rewriting the original discovery evidence;
- if the user adjusts priority or severity, including after adding future-work or business context, label it as user-adjusted decision severity/context, distinct from the discovery assessment;
- when an action is chosen, start or hand off to the appropriate separate workflow for that selected action.
