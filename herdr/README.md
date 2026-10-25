# Herdr Multi-Agent Orchestration Templates

Reusable guidance for setting up a lead orchestrator and specialist coding agents in **new or existing projects**. These templates grew out of the `dots` Go project, but are intended to work with Terraform, Ruby, Python, Go, and other repositories.

The goal is straightforward: **one orchestrator responsible for changes, specialists providing independent expertise, durable tracking in GitHub and/or Jira, and human approval for consequential actions.**

## 1. What's in `AGENTS.md`?

`AGENTS.md` is the **permanent project rulebook** for Codex and other coding agents. It describes how agents should discover a repository, work together, make changes safely, validate results, and communicate progress.

| Area | Included behavior |
| --- | --- |
| Project discovery | Adapt to an existing repository or bootstrap a new one without overwriting established conventions. |
| Herdr orchestration | Define the lead's responsibilities and delegate targeted, read-only work to specialist agents by default. |
| GitHub Issues | Use existing issues and acceptance criteria; report meaningful progress without generating noise. |
| GitHub Milestones | Group related deliverables when appropriate; close only after acceptance criteria are met. |
| Jira integration | Respect existing Jira stories, dependencies, decisions, and company workflows. |
| Dual tracking | When the team explicitly wants both systems, link them and post meaningful updates to both. |
| Git workflow | Feature branches, pull requests, CI validation, reviews, and approved merges. |
| Branch cleanup | After merging, verify worktrees and merge status before safely removing local feature branches. |
| Safety | Ask before destructive or irreversible commands, modifications to real user data, and consequential remote actions. |
| Production | Separate approval gates for infrastructure changes, deployments, and each environment. |
| Quality | Run repository-appropriate formatting, tests, linting, security checks, and documentation updates. |
| Reporting | Summarize changed files, checks, review findings, blockers, next steps, and tracking links. |

### Branch cleanup policy

After a pull request is merged, the orchestrator should verify that the branch is fully merged, inspect the working tree, and check whether any linked Git worktree still uses the branch. **Propose cleanup and ask for approval before deleting the local branch.** Prefer normal deletion (for example, `git branch -d`) to force deletion. Never discard uncommitted work or remove an active worktree just to make cleanup succeed.

## 2. What's in `PROMPTS.md`?

`PROMPTS.md` is a **reusable playbook**, not a mandatory agent instruction file. Choose and adapt the prompt appropriate to the current phase of the project.

The template contains **11 reusable prompts** spanning these activities:

1. **Project discovery and setup:** inspect an existing repository, or bootstrap a new one without overriding its conventions.
2. **Agent orchestration:** verify Herdr delegation and coordinate architecture, testing, and review specialists.
3. **Implementation and GitHub workflow:** plan bounded work, implement it, validate changes, prepare PRs, inspect CI, and clean up branches safely.
4. **Production infrastructure workflow:** read-only discovery, Terraform plans, risk and rollback assessments, and explicit deployment approvals by environment.
5. **Resuming and improving work:** recover context after a break and refine the process following a milestone.

These are **categories**, not a suggestion to run every prompt for every task. Start with discovery and delegation, then use the prompts relevant to the work.

## 3. GitHub vs. Jira: which should track the work?

Don't automatically create GitHub Issues and Milestones for every Jira task. Use the system the team already relies on, and avoid duplicate project-management work.

| Project situation | Preferred tracking |
| --- | --- |
| New personal project | GitHub Issues and Milestones, when the project is large enough to benefit. |
| Existing GitHub project | Existing GitHub issues and milestones. |
| Work project with Jira | Jira as primary tracking; GitHub pull requests link to the relevant Jira ticket. |
| Team explicitly wants both | Jira plus linked GitHub Issues, with meaningful updates synchronized across both. |
| Small one-off task | Existing ticket plus PR; no unnecessary milestone. |

For example, **TBDEV-5616** should remain the primary work item for the CodeDeploy/Terraform migration unless the team explicitly wants a mirrored GitHub issue.

If both trackers are used, record reciprocal links, avoid duplicating requirements unnecessarily, and post substantive status updates or blockers to both **only when authorized**. Do not treat a GitHub milestone as a replacement for a Jira epic or team change-management process.

## 4. Recommended local template organization

Keep reusable originals in a central folder, then adapt a copy for each repository:

```text
~/code/
├── dots/
├── aws-iac/
└── project-templates/
    └── herdr/
        ├── AGENTS.md
        ├── PROMPTS.md
        └── README.md
```

For a **new** project, copy `AGENTS.md` into the repository root and use the project-discovery prompt to customize language-specific tools, CI requirements, milestones, and guardrails. You may keep `PROMPTS.md` only in the central template directory and copy individual prompts into your agent sessions as needed.

For an **existing** project, first inspect the repository's `AGENTS.md`, `CLAUDE.md`, README, CI, Jira practices, branch policies, and security requirements. Merge only compatible instructions; never overwrite established team rules blindly.

If Claude Code is used alongside Codex, a root `CLAUDE.md` can contain the following import:

```markdown
@AGENTS.md
```

Commit the project-specific `AGENTS.md` when the team agrees with its policies. Keep personal preferences and sensitive work details out of shared templates.

## 5. Keep prompts separate from permanent rules

**`AGENTS.md` is the permanent rulebook.** It tells all agents how to work, where approval is required, what validations to run, and how to collaborate safely. It belongs in the repository when appropriate.

**`PROMPTS.md` is your personal playbook.** It contains optional prompts you choose for discovery, delegation, implementation, review, PR creation, operations, and cleanup. It can live centrally; it does not have to be committed into each project.

This separation keeps repository instructions concise and avoids forcing agents to treat every example prompt as a standing order.

### Recommended starting workflow

1. Leave any existing Herdr project workspace running; create a **separate Herdr workspace** for the new project.
2. Start a lead agent and one read-only testing or architecture specialist.
3. Run project discovery first. Confirm the repository, tracking system, branch policies, CI, and existing instructions.
4. Test delegation on a harmless read-only task before allowing implementation.
5. Define a small first deliverable, ask the lead to delegate reviews, and approve the plan.
6. Use a feature branch and appropriate tracking links; validate locally and in CI before merging.
7. After an approved merge, close tracking items only when their criteria are satisfied, and safely clean up the feature branch with approval.
8. For production or infrastructure work, require separate explicit approval for each change window and environment; read-only planning never implies approval to apply.

**Core principle:** one lead owns integration and code changes; specialists independently review; GitHub/Jira preserve the history; you retain control over destructive, external, and production actions.
