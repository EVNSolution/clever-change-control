# AGENTS.md

## Scope

This file defines the startup contract for agents working from `clever-change-control`.

This repository is part of the CLEVER three-repository local control plane.
It is the **approval and traceability repository**, not the default intake entrypoint.

## Core Rule

Use this distinction first:

- If this session is **generic CLEVER work startup**, do not start here. Move startup to `clever-agent-project`.
- If this session explicitly **edits `clever-change-control` itself**, stay here and treat this repository as the current target.

Short version:

- generic startup: redirect to `clever-agent-project`
- repo-local maintenance: stay in `clever-change-control`

## Workspace Contract

CLEVER expects a local three-repository workspace:

1. `clever-agent-project`
2. `clever-context-monorepo`
3. `clever-change-control`

Before doing real work, run the automatic workspace check from the current session:

```bash
python3 ../clever-agent-project/scripts/bootstrap_clever_work.py --cwd "$PWD" --workspace-check --json
```

If the session is explicitly about editing this repository itself, run:

```bash
python3 ../clever-agent-project/scripts/bootstrap_clever_work.py --cwd "$PWD" --workspace-check --current-repo-maintenance --json
```

Interpret `agent_action` like this:

- `proceed-with-hard-gate`: the workspace is complete and startup may continue
- `current-repo-maintenance`: this repository itself is the target, so stay here
- `switch-to-clever-agent-project`: this is generic startup work, so move to `clever-agent-project`
- `stop-and-fix-workspace`: the local workspace is incomplete, so do not continue as if startup were healthy

## What This Repository Owns

Use this repository for:

- `project-start` root records
- scoped change requests
- rollout and rollback trace
- release evidence linkage

## Main Branch Contract

This repository is public and its `main` branch is protected by the GitHub
ruleset `CLEVER protect main`.

- Do not push directly to `main`.
- Use a role-prefixed branch for repository changes.
- Open a PR into `main`.
- `main` PRs require at least one approval.
- Admin bypass is allowed only in `pull_request` mode.

Do not treat this repository as:

- the default generic startup surface
- the canonical interpretation repository
- the implementation repository for product code

## Startup Behavior

If the user is starting a new CLEVER task and the target repo is not yet fixed:

1. Run the workspace check
2. If the workspace is healthy, move startup to `clever-agent-project`
3. Apply the first-response hard gate there

If the user is directly changing this repository:

1. Stay in the current repo
2. Treat `clever-change-control` as the target repo for this session
3. Keep `project-start issue #` as the root canonical start identifier and avoid forcing `change_id` early

## Target Repository Traceability Gate

This repository is the required trace anchor for target-repository development.
When any target project is opened with the CLEVER three-repository control plane,
the agent must establish the issue-to-branch trace chain here before
implementation begins.

Required behavior:

- Use `project-start` issues for root start records.
- Use `change-request` issues for scoped execution.
- Cross-mention the root context issue, such as
  `<context-owner>/<root-context-repo>#<issue>`, when one exists.
- Cross-mention the target repository issue from the relevant
  `clever-change-control` issue.
- Ensure the target repository issue mentions the relevant
  `clever-change-control` issue.
- Create or confirm an issue-based branch before implementation.

Do not hard-code a GitHub organization, root context repository, or target
repository name in this rule. Resolve repository identifiers from the current
workspace, `git remote -v`, issue URLs, or explicit user instructions.

One parent issue may have many child branches. Track that one-to-many
relationship on the relevant `clever-change-control` issue by listing the active
branch names, related commits, PR links, current status, and next action.

Do not treat GitHub automatic references as sufficient traceability. GitHub may
link plain issue mentions, but the agent must maintain the bidirectional working
context explicitly.

Use `fixes`, `closes`, or similar GitHub keywords only when the PR merge is
intended to close the referenced issue. Use plain issue mentions for context
links.

## Read Order For Repo-Local Work

When the task is specifically about this repository, read in this order:

1. `README.md`
2. `.github/ISSUE_TEMPLATE/project-start.yml`
3. `.github/ISSUE_TEMPLATE/change-request.yml`
4. `../clever-agent-project/AGENTS.md`
5. `../clever-context-monorepo/docs/root/authority-boundaries.md`

## Expected Behavior

Do not automatically redirect every session away from this repository.

Redirect only when the session is generic startup.
Stay here when the work is explicitly about modifying `clever-change-control` itself.
