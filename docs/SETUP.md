# One-Time Harness Setup

This repository supports two independent coding-agent harnesses that share the same durable `docs/` project record.

## Codex

Expected topology:

- **Primary/orchestrator:** native Codex `gpt-6-astra`.
- **Children:** only `chatgpt-web/high` through `miuuyy/codex-chatgpt-web` Full Harness.
- **Methodology:** use Superpowers when its planning, TDD, debugging, review, verification, or branch-finishing skills add value.

### 1. Install Superpowers in Codex

1. Open `/plugins` in Codex.
2. Find and install `Superpowers`.
3. Start a fresh Codex task/session.

Project: https://github.com/obra/superpowers

### 2. Install and connect codex-chatgpt-web

Project: https://github.com/miuuyy/codex-chatgpt-web

Use the latest release for your OS. In the launcher:

1. Sign into your ChatGPT account and run its browser smoke test.
2. Install the ChatGPT Web model entries into Codex, then restart Codex.
3. Complete **Full harness (MCP)** setup.
4. Create the ChatGPT Developer Mode Tunnel connector named exactly `Codex Native2`, with Authentication `None` and the permissions required by the harness.
5. Run the launcher's runtime/doctor verification.
6. Confirm `ChatGPT Web — High` is present and working from Codex.

### 3. Use Compatibility V1 for the mixed topology

```bash
codex-chatgpt-web subagents compatibility-v1
codex-chatgpt-web subagents status
```

Restart Codex and start a **new task** after changing protocol mode. Do not duplicate bridge-owned protocol flags in `.codex/config.toml`.

### 4. Trust the project config

Codex loads project-local `.codex/config.toml` and agent files for trusted projects. Start from the repository root.

Expected configuration:

- model: `gpt-6-astra`
- reasoning: `max`
- subagent default: `chatgpt-web/high`
- subagent reasoning: `high`

If your installed Codex build rejects `model_reasoning_effort = "max"`, update Codex; as a temporary compatibility fallback, use the highest effort your installed build supports.

### 5. Smoke-test isolation

Before a long run:

1. Confirm the orchestrator reports `gpt-6-astra`.
2. Spawn `explorer` on a harmless read-only task and confirm the child uses `chatgpt-web/high`.
3. Run one read-only reviewer task.
4. Run one implementer task on a disposable change and verify its route.

If `chatgpt-web/high` is unavailable, fix the bridge/account/runtime setup. Do not silently allow another child route.

---

## Claude Code — Opus 5.5 + Ultracode

Expected topology:

- **Main session:** `claude-opus-5-5`.
- **Baseline effort:** `xhigh` via `.claude/settings.json`.
- **Preferred mode for substantive work:** **Ultracode**.
- **Project subagents:** `.claude/agents/*.md`, pinned to Opus 5.5 at `xhigh`.
- **Durable state:** the same `docs/` files used by Codex.

### 1. Use a current Claude Code build

The harness relies on project `CLAUDE.md`, `.claude/settings.json`, custom project subagents, and current subagent frontmatter such as `effort` / `omitClaudeMd`. Update Claude Code before using the harness if these fields are not recognized.

Run from the repository root:

```bash
claude
```

Then use `/status` to confirm the project settings file loaded and `/context` to confirm `CLAUDE.md` is present.

### 2. Confirm the model and effort

`.claude/settings.json` pins:

```json
{
  "model": "claude-opus-5-5",
  "effortLevel": "xhigh",
  "autoMemoryEnabled": false
}
```

Project auto memory is disabled deliberately: long-lived product/project facts belong in `docs/`, where both harnesses can see and review them.

### 3. Enable Ultracode for substantive sessions

Ultracode is a **session setting**, not a model ID and not a persistent project JSON key. In the Claude Code effort menu, select **Ultracode** when you want Opus 5.5 at `xhigh` plus automatic dynamic-workflow orchestration.

Use it for large migrations, repo-wide audits, broad debugging, or work that benefits from independent fan-out and cross-checking. Leave it off for small mechanical changes where a dynamic workflow would only add overhead.

If Ultracode is unavailable on your account/build, remain on Opus 5.5 `xhigh` and use the project subagents directly.

### 4. Smoke-test project subagents

Ask Claude to run small tasks through:

- `explorer`
- `planner`
- `implementer`
- `spec-reviewer`
- `quality-reviewer`
- `verifier`

Confirm the agents are discovered from `.claude/agents/`, use Opus 5.5, and respect their tool boundaries. The custom agents set `omitClaudeMd: true`, so parent prompts must include the relevant task constraints and context paths explicitly.

### 5. Keep harness boundaries clean

Claude Code reads `CLAUDE.md` instead of `AGENTS.md` by default when both are present. That is intentional. Do not import `AGENTS.md` into `CLAUDE.md`, because the Codex-only model routing and child-provider rules would conflict with Claude Code.

---

## Start a real project

For either harness, first establish only what the repository supports with evidence, then identify the smallest MVP milestone and keep `docs/PROJECT_STATE.md` current.

Codex starter prompt:

> Initialize this repository using AGENTS.md. Establish the baseline, fill only project context you can support with evidence, identify the smallest MVP milestone, and execute it with independent review and verification gates.

Claude Code starter prompt:

> Initialize this repository using CLAUDE.md. Establish the baseline, fill only project context you can support with evidence, identify the smallest MVP milestone, and use the lightest appropriate combination of direct work, project subagents, and Ultracode/dynamic workflows with independent review and verification gates.
