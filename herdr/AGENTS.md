# AGENTS.md — Multi-Agent Project Operating Rules

> Reusable starter template. Customize the **Project profile** before use. These instructions apply to Codex, Claude Code (when `CLAUDE.md` imports this file), and specialist agents. They do **not** grant permission to perform external actions.

## Project profile (complete during onboarding)

- Project: `[PROJECT_NAME]`
- Purpose and scope: `[ONE_PARAGRAPH]`
- Primary repository: `[ORG/REPO or LOCAL_ONLY]`
- Additional affected repositories: `[NONE or LIST]`
- Tech stack and supported versions: `[STACK]`
- Canonical requirements: `[PATH/URL; e.g., docs/ARCHITECTURE.md, Jira ticket]`
- Primary work tracker: `[GitHub Issues | Jira | Both | None]`
- Delivery process: `[PR + CI | existing team policy]`
- High-risk environments or data: `[NONE | staging | production | customer data | etc.]`
- Project-specific validation commands: `[MAKE TARGETS / TERRAFORM COMMANDS / TESTS]`

For existing repositories, **discover and preserve** existing conventions, CI, branching and deployment policies. Do not overwrite established `AGENTS.md`, `CLAUDE.md`, workflow files, or project configurations without proposing a merge. For new repositories, propose a minimal baseline and obtain approval before creating remote resources.

## Read first; source-of-truth hierarchy

1. Read this file, README, relevant architecture/runbooks, tests, and the active work item(s).
2. Follow the user's current explicit directions, organizational safety/change-control requirements, and repository policy. Escalate conflicts rather than silently overriding one source.
3. Prefer live repository and tracker state over stale exports, historic handoffs, or assumptions. Reconfirm external dependencies before implementing.
4. Use the repository's language-specific style, tooling, dependency/version policy, and testing conventions. Do not impose Go-specific rules on Terraform, Ruby, etc.
5. Keep long-term architecture decisions in maintained documentation. Treat an initial handoff as historical once superseded.

## Orchestrator and specialist roles

- **Lead/orchestrator:** Owns planning, integration, changes, approvals, progress reporting, and final handoff to the human.
- **Architecture specialist:** Read-only review of design, dependencies, interfaces, migration and rollback implications.
- **Testing specialist:** Read-only test strategy, fixtures, coverage, determinism, and negative-case review.
- **Review/security specialist:** Read-only correctness, security, privilege, secrets, blast radius, and regression review.
- **Domain specialist (optional):** Terraform/IaC, CI, performance, deployment, database, or application-specific expertise as warranted.
- **Shell/validation pane:** Ordinary terminal; not necessarily another model or agent.

By default the lead is the **only agent permitted to edit shared files or create commits**. Specialists provide findings with file paths and evidence, not independent code changes. Enable concurrent writing only after explicit agreement and isolated Git worktrees/branches with defined ownership. Never let agents independently push, merge, deploy, or approve their own work. Verify Herdr CLI syntax locally; pane labels and agent names may differ.

## Standard work cycle

1. **Discover:** Check worktree, branch, remote, active issues/tickets, CI, dependencies and environments; flag dirty state and incomplete context.
2. **Plan:** State scope, acceptance criteria, dependencies, design choices, risk level, validation, rollback and approval gates. For material changes, get approval before editing.
3. **Delegate:** Ask relevant specialists in parallel for read-only design and test reviews; reconcile conflicting findings.
4. **Implement:** Small changes on an approved feature branch; do not disturb unrelated changes. Use project's formatting, tests, lint, security and CI standards.
5. **Review:** Recheck diff, tests, secret exposure, risk, docs, and specialist findings. Document deviations.
6. **Deliver:** Prepare a PR linked to work items, obtain permission for pushing/creating PR, confirm green CI and human approval before merge.
7. **Close:** Verify merge and tracker status, document outcomes, and perform approved safe branch cleanup. Do not declare success from local tests alone when CI is required.

## GitHub Issues, milestones and Jira tracking

- **Choose one primary planning system per project.** Inspect what the team already uses. Do not create GitHub issues/milestones simply because Jira exists.
- **GitHub-first projects:** Use existing GitHub issues and milestones where useful. Read their acceptance criteria, link feature branches/PRs, and propose issues/milestones only for meaningful work. Avoid duplicating completed work or assigning arbitrary due dates.
- **Jira-first projects:** Jira tickets and their parent/epic remain authoritative. Use GitHub PRs linked to Jira keys; create GitHub issues/milestones **only if the team expressly wants mirrored execution tracking**, otherwise avoid two backlogs.
- **If both are used:** Maintain a mapping of Jira key ↔ GitHub issue ↔ PR ↔ milestone (if any). Never create more than one GitHub issue for the same Jira work item without approval. Preserve Jira acceptance criteria and dependency status. Respect company policy on which comments/statuses may be changed.
- **Progress comments:** At meaningful checkpoints (start, design decision/blocker, PR ready, merged), propose concise factual updates. When dual tracking is approved, post corresponding updates to **both Jira and GitHub issue** with reciprocal links and the same factual state. Do not spam routine tool outputs. Ask approval before posting externally unless permission has already been explicitly granted.
- **PRs:** Include summary, linked work items, test evidence, risk, rollback (when relevant), and relevant specialist findings. Use `Closes #N` only if merging fully resolves that issue; otherwise `Refs #N`. Reference Jira ticket keys in branch/PR where repository conventions allow.
- **Milestones:** Use only for coherent multi-issue work phases; they do not substitute for semantic software release versions. Close a milestone only when acceptance criteria, merged work, and required CI/deployment validation are verified. Do not auto-close issues or Jira tickets prematurely.
- **Existing-team workflows win:** Honor branch protection, reviews, Jira transitions, release/change windows, and incident procedures.

## Branch and worktree hygiene

- Never commit directly to a protected/default branch unless repo policy explicitly authorizes it. Prefer an issue-linked feature branch and PR.
- Before switching branches, pulling, rebasing or cleaning, inspect `git status` and preserve all unrelated user work. Do not use forced resets, cleans, or pushes as shortcuts.
- After a PR is confirmed merged into the intended base branch and CI is green, propose removing the **merged local feature branch** and any associated worktree; request approval first (or rely on an explicit standing approval for this specific cleanup).
- Before deletion verify: exact branch and PR, merge state, base branch, no uncommitted/untracked work in its worktree, and no unmerged unique commits. Never delete the current branch, default branch, shared branch, or a branch containing work not merged. Prefer `git branch -d` (not `-D`). Remove worktrees through `git worktree remove` only when safe. Do not delete remote branches without separate approval.
- Leave clean repos and provide precise branch/PR links in closeout reports.

## Safety and approval gates

**Pause and ask for explicit approval before:**

- Any destructive or potentially irreversible command (`rm -rf`, `git clean`, `git reset --hard`, force push, branch/worktree deletion, data removal, state manipulation, resource destroy/recreate).
- Any Terraform/OpenTofu apply/destroy/import/state mutation, cloud infrastructure change, deployment, traffic change, database migration, production operation, or restart affecting users.
- Changing secrets, IAM permissions, GitHub/Jira settings, remote repositories, protected branches, external tickets/comments, releases or published artifacts, unless specifically preapproved.
- Commands that would write outside the assigned repository or isolated temp fixtures, or any operation with unclear blast radius.

Read-only commands and local edits **within the approved scope** may proceed under normal project permissions. A dry-run/plan is not permission to apply. A successful plan is not proof of zero risk. Obtain **fresh, environment-specific** approval for each production change, respect maintenance windows, and document rollback/verification. If a required approval is unavailable, stop safely and report the blocker. Do not expose secrets in prompts, logs, artifacts or comments.

## Quality, documentation and reporting

- Add or update automated tests for behavior changes. Use isolated fixtures; never use real home, production, or account resources in tests without explicit approval.
- Run only the checks relevant to the project's stack; e.g., `make check`, linters, formatting, tests, `terraform fmt -check -recursive`, `terraform validate`, or plan review. Record exactly which passed, failed, or could not run.
- Keep documentation, changelog, runbooks, and migration notes in sync. Respect Conventional Commits **if adopted by the repository**.
- Follow the repository's CI requirements; do not bypass failing checks or dismiss specialist findings without explaining why.
- Final report: scope completed, changed files, branch/PR, linked issues/Jira, tests and CI, approvals received, risks, verification results, cleanup status, and next steps.
- Distinguish **implemented**, **locally validated**, **CI validated**, **merged**, and **deployed/verified**. They are not interchangeable.

## Project-specific additions

Add language standards, deployment runbooks, repository paths, tool commands, and risk controls here after discovery. Keep this template generally reusable; don't paste stale milestone instructions into permanent rules.
