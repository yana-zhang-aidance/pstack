# Codex adapter

Read this reference before applying Cursor-specific instructions in pstack.
The active tool schema and repository instructions take precedence over upstream examples.

## Agent lifecycle

Discover the native subagent tools exposed by this session. Some Codex sessions expose `multi_agent_v1` with `spawn_agent`, `wait_agent`, `send_input`, and `close_agent`. Availability can change between sessions.
Use `spawn_agent` for internal delegates. Do not use `create_thread`, `fork_thread`, or `send_message_to_thread` to implement internal delegation; those manage user chats and require their own authorization.
For that tool, the observed spawn schema accepts `message` or `items`, optional `fork_context`, `model`, and `reasoning_effort`. It does not accept `subagent_type`, `readonly`, `environment`, `cloud_base_branch`, or `run_in_background`. Inspect the live schema before calling.
Omit `model` by default. Follow any tool restriction on explicit model overrides. Read `~/.agents/pstack-models.md` for roles. Unsupported effort or model selection is a limitation, not proof that the requested model ran.
Creation returns an agent ID and sometimes an automatic nickname. Record the semantic role separately from the nickname.
Wait for actual completion before aggregating. Close completed agents when their state is no longer needed. A missing agent or missing result is a gap, never a pass.
Without subagent tools, execute serially and explicitly report that independent review and parallel execution were not exercised.

## Scope and isolation

Give each delegate the goal, role, allowed paths, prohibited changes, input revision or snapshot, verification method, and expected evidence. For poteto-agent delegates, include the absolute path to poteto-mode/SKILL.md and require reading it first. Read-only tasks say not to edit; this is a prompt constraint, not a filesystem sandbox.
Read-only explorers may share a checkout. Concurrent writers need separate worktrees or disjoint isolated outputs. In Codex desktop use list_artifacts and reuse suitable worktrees; use create_worktree for new managed worktrees and archive_worktree for cleanup. Do not reset an unrelated checkout or assume cloud VM isolation.
A worktree starts from a commit and does not include uncommitted changes. Explicitly transfer only in-scope edits when the worker must inspect a dirty candidate. Record both the base SHA and candidate diff or snapshot. A verifier checks that exact candidate.

## Model panels

Preserve panel size even when all entries inherit the parent model. Two inherited seats are two fresh agents. This is independent-context review, not cross-model review. If a skill requires a different model family and the session cannot provide one, report that gate as unmet. Never count a same-family judge as a cross-family judge. Arena may return candidates with selection explicitly marked provisional when its required cross-family judge is unavailable.

## State and permissions

Use the repository's existing state and evidence paths first. Do not create a competing status file. For projects without a state convention, notes.md is an optional fallback. Keep local run evidence outside tracked product code unless the task requires committing it.
Existing user and repository instructions take precedence over pstack defaults. A skill does not grant authorization for external actions.

## Waiting and integrations

For a current run or CI job, keep checking in the current task until its requested terminal state. Do not create an automation to monitor that run. Use Codex automation only for user-requested future reminders or recurring work. No automatic equivalence exists between Cursor /loop and a persistent Codex scheduler.
Cursor cloud machines and Project store are unavailable here. Use local isolation and repository state. Treat existing Bugbot comments as normal PR evidence; do not claim a Bugbot run occurred. Use available browser, terminal, or project verification tools instead of assuming control-ui/control-cli are installed.

## Evidence

The parent inspects the actual diff and saved artifacts. A worker summary alone is insufficient. Bind each verdict to the checked revision and dirty diff if present. After a material change, obtain fresh verification.
Prefer native current-thread history tools when available. For local session fallback, inspect metadata first and require exact workspace cwd and the intended session ID; cwd alone does not identify this chat. Never bulk-read unrelated conversation bodies or credentials.

## Setup

Use the live tool schema to detect model and effort controls. Start with inherited models when explicit model selection is unavailable. Keep two entries for two-seat panels. See the repository example at docs/harness/pstack-models.codex.example.md when installing the complete repository. Copy it to ~/.agents/pstack-models.md only when no configuration exists or the user requests replacement. Never overwrite existing role choices during installation. A budget label cannot enforce reasoning effort when the tool does not expose that control.

An upstream disable-model-invocation flag is represented in Codex by agents/openai.yaml with policy.allow_implicit_invocation set to false. Routed workflows still read the Skill file explicitly by path.
