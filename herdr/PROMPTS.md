# PROMPTS.md — Reusable Herdr / Codex Orchestration Playbook

> Companion to `AGENTS.md`. These are **prompts for a human to paste** into the lead agent (or adapt into a project brief), not automatic instructions to execute every step. Replace `[PLACEHOLDERS]`. Follow your company's approval and security policies.

## Choosing a workflow

| Situation | Recommended tracking | Agents | First move |
|---|---|---|---|
| New personal repository | GitHub issues/milestones if useful, otherwise simple roadmap | Lead + Test + Architecture + Review | Bootstrap and agree on first milestone |
| Existing GitHub repo | Existing issues/PRs/milestones | Lead + targeted specialists | Repo audit; no surprise restructuring |
| Jira-managed work repo | Jira + GitHub PRs, avoid duplicate backlog | Lead + domain/test/review | Read ticket and latest repo state |
| Jira + GitHub issues intentionally mirrored | Jira as agreed source of truth, reciprocal links | Lead + specialists | Agree mapping and comment rules |
| Production/IaC change | Existing change-management system + Jira; GitHub PRs | Lead + IaC + Deploy + Security/Test | Read-only discovery, staged plan, approvals |

## Workspace layout (suggestion, not a requirement)

- One Herdr **workspace per project**, each with its own working directory and appropriately named agents.
- Tab 1: lead/orchestrator. Tab 2: specialists (test, architecture, review/domain), plus optional ordinary shell.
- Do not share writable checkout paths between independent projects; keep `dots` and work infrastructure tasks in separate workspaces.
- Start with two agents to verify delegation, then add specialists as needed. Four simultaneous paid model sessions may have meaningful usage costs.
- Open Codex manually in panes if convenient; use Herdr detection, `agent list`, and descriptive agent names. Validate installed CLI syntax with `herdr --help` and the relevant subcommand help rather than assuming fixed pane IDs.
- Explicitly assign specialist read-only tasks. Agent-to-agent communication requires lead instructions and supported Herdr commands; multiple panes alone are not orchestration.

## Prompt A — First-time project discovery (NEW or EXISTING)

```text
You are the lead orchestrator for [PROJECT] in [REPO_PATH].
Read AGENTS.md, README, current architecture/runbooks, tests, CI configuration and open work items. If AGENTS.md does not yet exist, use the provided reusable template as a proposal; don't overwrite an existing file.
Determine whether this is a new or existing repo. Report stack, branch/remotes, dirty working-tree state, quality commands, relevant deployment policies, and available Herdr agents. Identify the primary tracker: [GitHub | Jira | Both | Unclear].
For Jira work, read the current ticket(s), comments, dependencies and acceptance criteria; distinguish recent corrections from stale exports. Check repository state before trusting assumptions.
Propose: project-specific AGENTS.md edits, initial/active scope, optional issues/milestones, agent roles, risk assessment, test plan and approval gates. Do not edit files, create tickets, change settings, push or deploy yet. Ask for my approval of the plan.
```

## Prompt B — Bootstrap a new repository (only after approving discovery)

```text
Initialize the minimal approved structure for [PROJECT], using [LANGUAGE/STACK]. Keep one repo and established simplicity. Add README, AGENTS.md, optional CLAUDE.md importing AGENTS.md, .gitignore, tests, CI, project-formatting/linting tools, changelog and license if approved.
Use existing templates only where appropriate. Create a small first milestone/issue backlog ONLY if we chose GitHub tracking. Avoid invented deadlines and redundant GitHub issues if Jira is primary.
Delegate read-only architecture and test-design reviews in parallel. Lead is sole writer. Run applicable local checks and report changes. No remote creation, push, PR, or release without approval.
```

## Prompt C — First orchestration smoke test (no edits)

```text
Read AGENTS.md and the active work item. Do not modify files or external trackers.
Use the installed Herdr workflow to delegate one read-only task to [TEST_AGENT_NAME]: identify five critical tests and open design questions for [FEATURE]. Wait for it, read its findings, and summarize them to me, including any disagreements with the specification. Confirm the specialist received the prompt automatically. Stop before implementation.
```

## Prompt D — Plan a feature / Jira story

```text
Plan work for [GITHUB_ISSUE_URL and/or JIRA_KEY] in [REPO]. Read the latest issue/ticket, comments, linked blockers, architecture, CI and code. Confirm requirements against current state; do not assume old notes are accurate.
Delegate architecture and testing/domain review tasks in parallel, read their reports, and propose a narrowly scoped implementation plan with acceptance criteria, file map, test matrix, risk, dependencies, rollback, and approval gates.
Tracking: [GitHub-first | Jira-first | Approved dual tracking]. Propose any ticket/issue/milestone updates rather than creating duplicates. No edits or external mutations until I approve.
```

## Prompt E — Implement approved scope

```text
Implement the approved plan for [ISSUE/JIRA KEY] on [FEATURE_BRANCH]. First confirm clean worktree, base branch and current CI. Do not discard unrelated work. Keep changes small and compatible with existing project conventions.
Lead is sole writer; delegate read-only architecture, test, and safety reviews. Add tests, update docs and run [PROJECT_CHECKS]. Resolve findings before completion.
Prepare a summary, diff and proposed Conventional Commit messages if the repo uses them. Do not push, open PR, merge, deploy, create releases, or post tracker comments without approval. For production/IaC, do not apply a plan or change cloud resources.
```

## Prompt F — GitHub + Jira progress update

```text
Prepare a concise factual progress update for [JIRA_KEY] and [GITHUB_ISSUE]. Include completed work, validation, open risks, next steps and cross-links. Verify which tracker is authoritative and avoid duplicates.
Show me the exact comment text and destinations before posting. If I approve both, post the corresponding update to both systems and verify success; never claim it was posted before checking. Don't transition tickets or close issues unless separately approved.
```

## Prompt G — Milestone closeout audit BEFORE commits and CI

```text
We have finished the local implementation and specialist reviews for [PROJECT] / [MILESTONE_OR_TICKET]. We are preparing the changes for commits, a pull request, and CI validation. This is a PRE-COMMIT AUDIT, not permission to commit, push, open a PR, deploy, or publish a release.

1. Establish the baseline: identify the repository, expected base branch, active feature branch, working-tree state, changes since base, relevant issues/Jira tickets, approved acceptance criteria, and whether this is a new or existing repository. Do not overwrite unrelated changes or assume the worktree is clean.
2. Review all changed and untracked files, including ignored/generated artifacts when relevant. Verify .gitignore, staged versus unstaged differences, binaries, coverage, temp/audit files, dependency lockfiles, and unexpected large files. Preserve any intentionally tracked lockfiles.
3. Review project metadata and reproducibility: module/package identity and supported runtime/tool versions (e.g., go.mod, package.json, Terraform required_version/providers), dependency changes, licensing, and platform compatibility where applicable. Identify placeholders, but do not change unrelated settings.
4. Check CI definitions and triggers for push/PR, platform matrix, formatting, linting, static checks, tests, build, and existing repo-specific hooks. Do not invent tools or require a CI platform that the repository does not use.
5. Check README, AGENTS.md, optional CLAUDE.md, architecture/handoff/migration docs, CHANGELOG, runbooks, and license for inconsistencies or stale milestone instructions. Require only documents relevant to this repository; do not create boilerplate just to satisfy a checklist.
6. Check diffs for secrets, tokens, credentials, private endpoints, internal-only data in public repositories, personal paths, environment-specific values, Terraform state/plan files, and accidental destructive operations. Stop and report any suspected exposure without copying secret values into reports or tracker comments.
7. Delegate a final, explicitly READ-ONLY review to [REVIEW_AGENT] and targeted testing/architecture reviews if warranted. Specialists must not edit, commit, change branches, or post external comments.
8. Run the repository's documented checks (e.g., [PROJECT_CHECKS]), including tests, formatting/lint, security/static analysis, build, and applicable pre-commit hooks. Record commands, results, skipped checks, and why. Local passing results do not count as remote CI success.
9. Compare the implementation and tests with each active GitHub issue/Jira ticket's acceptance criteria. Report incomplete items, unresolved safety risks, deployment or rollout dependencies, and any changes that need explicit approval.
10. Propose a SMALL set of logically grouped commits using the repository's established commit conventions. Provide exact suggested messages and the files in each group. Draft a PR summary and issue/Jira references, but do not create it yet.
11. Present a go/no-go recommendation: ready for commit, ready with caveats, or blocked. List outstanding approvals and next steps. Keep milestone/ticket state unchanged until the required review, remote CI, merge, and any separately required deploy validation have completed.

For GitHub-first projects, use the existing milestone/issues; do not create duplicates. For Jira-first projects, treat Jira as primary and link the GitHub PR. If approved dual tracking exists, prepare concise synchronized status comments for both trackers, but do not post either without authorization.

Do not stage, commit, push, open/merge PRs, close issues, change Jira status, delete branches/worktrees, run Terraform apply/destroy, deploy, or release. Wait for explicit approval of the proposed next action.
```

## Prompt H — Final review and PR preparation

```text
Review [FEATURE_BRANCH] against [BASE_BRANCH] and [ISSUE/JIRA KEY]. Ask testing, architecture and review specialists for independent read-only assessments. Check implementation, failure modes, tests, formatting/lint/security, docs, breaking changes, and secrets.
Run project-defined checks and document exact results. Draft PR title/body containing scope, linked items, validation, risk/rollback, and reviewer findings. Use Closes #N only if all criteria are met; otherwise Refs #N. Associate milestone only if appropriate.
Do not push or create PR until I approve. No merges or releases.
```

## Prompt I — Post-PR merge closeout and local branch cleanup

```text
Verify PR [PR_URL] is merged into the intended default/base branch, required CI passed, tracker acceptance criteria are met, and the local working tree/worktree(s) have no uncommitted or untracked user work. Confirm any deployment validation separately.
Propose issue/Jira updates, closing the relevant GitHub milestone only when all work is complete. Show exact external changes before making them and request approval.
Identify merged local feature branches and associated worktrees eligible for cleanup. Propose the exact safe commands. Do not delete anything without my approval. After approval, use safe worktree removal and git branch -d only; never force delete. Do not delete remote branches unless separately approved. Report the final state.
```

## Prompt J — Infrastructure / production change (strict two-phase gate)

```text
For [CHANGE/JIRA KEY] perform READ-ONLY discovery and planning only. Confirm current Terraform state/workspaces/providers, drift, affected environments, plans, CI, workflows, IAM, secrets dependencies, maintenance windows, change-management approvals, and rollback. Never run apply, destroy, import, state mutation, deploy, or edit GitHub variables in this phase.
Delegate IaC architecture, deployment risk, and test/review analysis. Build a precise environment-by-environment procedure with preflight gates, deployment seeding where required, application-health verification, pause conditions, rollback and audit evidence.
Stop and present the proposed sequence for human approval. Obtain fresh permission for each environment before any change. A plan or prior lower-environment approval does not authorize production.
```

## Prompt K — Resume another day or after context loss

```text
We are resuming [PROJECT] / [ISSUE OR MILESTONE]. Read AGENTS.md, relevant docs, latest tracker comments and current git/CI state. Reconcile with the last reported checkpoint without assuming prior tasks completed. Inspect active Herdr agents and branches before starting new sessions or recreating work.
Summarize completed, pending, blocked, approvals needed, and next safest action. Do not modify anything until I approve the continuation plan.
```

## Prompt L — Retrospective / improve the workflow

```text
Review the completed [MILESTONE/PR] and summarize what agent delegation saved, duplicated, or missed; recurring test failures; CI gaps; overbroad permissions; and useful changes to AGENTS.md or the prompts. Suggest at most three targeted improvements, without rewriting the project conventions or executing changes. Ask permission before updating templates.
```

## Operating guidance

1. **Don't over-orchestrate:** A tiny docs fix may need only the lead and one reviewer. Add specialists for independent value, not just to fill panes.
2. **Tracker selection is intentional:** For a single Jira story, use Jira + GitHub PR references by default; don't create an extra GitHub milestone without a coherent multi-issue GitHub roadmap.
3. **Comments in two systems require consent:** When the team explicitly wants mirrors, link Jira and GitHub issues and post meaningful updates to both, not every model step.
4. **Clean up after merge, carefully:** Verify merged state, protect work, get approval, then delete the merged *local* feature branch; remote deletion is a separate decision.
5. **Never equate local tests, CI, merge, deployment and health verification.** Each is a separate gate.
6. **Keep private work separate:** Do not put internal Jira exports, credentials or customer details in public repositories or agent logs.
7. **Update your project profile:** Set project paths, tracker, environment gates and concrete check commands before giving an agent write permission.
