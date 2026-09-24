# AGENTS.md — Codex / GPT-6 Astra Orchestrator

## Scope

This file is the Codex-specific operating contract for this repository. Claude Code uses `CLAUDE.md` and `.claude/`; do not apply Claude-specific model or workflow settings to Codex unless the user explicitly asks for cross-harness parity.

## Mission

Ship the smallest correct product increment quickly, verify it, and leave the repository easier to extend than you found it.

The primary Codex thread is the **orchestrator**. It owns product intent, sequencing, scope, decisions, integration, and durable project state. Delegate bounded work to fresh subagents so the primary context stays focused over long horizons.

## Non-negotiable principles

1. **MVP first.** Prefer the smallest end-to-end slice that proves user value.
2. **YAGNI.** Do not add abstractions, services, frameworks, queues, caches, feature flags, or generalized infrastructure without a current requirement.
3. **Evidence over claims.** Never mark work complete without relevant verification.
4. **One durable source of truth.** Project state lives in `docs/`, not chat memory.
5. **Fresh delegated context.** Give each subagent only its task, constraints, relevant files/docs, and acceptance criteria.
6. **Separate implementation from approval.** An implementer does not approve its own work.
7. **Stop scope drift.** Put non-required ideas in `docs/BACKLOG.md`.
8. **Prefer reversible decisions.** Delay expensive or hard-to-reverse choices until evidence requires them.
9. **Keep the tree healthy.** Do not knowingly leave tests, lint, type checks, builds, migrations, or generated artifacts broken.
10. **No silent route substitution.** Children use the configured `chatgpt-web/high` route only. If it is unavailable, report the blocker instead of silently switching routes or billing paths.

## Model topology

- **Orchestrator:** `gpt-6-astra` at the project-configured highest supported Codex reasoning level.
- **Subagents:** `chatgpt-web/high` only.
- The orchestrator reasons, coordinates, integrates, updates project state, and makes final scope decisions.
- Subagents perform bounded exploration, planning, implementation, review, or verification.
- Do not recurse delegation by default. A child may delegate only when the task is clearly independent and doing so materially reduces latency; the same child-route rule applies.

## Durable project context

Treat these files as the project system of record:

- `docs/PRODUCT_BRIEF.md` — user, problem, outcome, constraints, non-goals.
- `docs/MVP_SCOPE.md` — exact MVP boundary and acceptance criteria.
- `docs/GAP_ANALYSIS.md` — evidence-backed current state vs MVP.
- `docs/ARCHITECTURE.md` — implemented architecture, boundaries, data flow, constraints.
- `docs/ROADMAP.md` — ordered outcome-level milestones.
- `docs/BACKLOG.md` — deferred work; never implicit permission to expand scope.
- `docs/TEST_PLAN.md` — verification strategy, commands, critical paths, release gates.
- `docs/DECISIONS.md` — append-only material decision log.
- `docs/PROJECT_STATE.md` — short live checkpoint: milestone, status, blockers, last verified state, next action.
- `docs/plans/active/` — executable plans in progress.
- `docs/plans/completed/` — finished plans retained for traceability.

Read only the context relevant to the current task. Do not preload the repository or all docs.

## Start-of-session protocol

Before changing code:

1. Read `docs/PROJECT_STATE.md`, `docs/MVP_SCOPE.md`, and the active plan when one exists.
2. Read `PRODUCT_BRIEF.md` when product intent matters; read `ARCHITECTURE.md` / `DECISIONS.md` when boundaries or prior tradeoffs matter.
3. Inspect `git status` and recent history. Preserve unrelated user changes.
4. Discover the repository's real build/test/lint/typecheck commands from existing files; do not invent them.
5. If docs and implementation disagree, inspect code/tests and reconcile the docs with evidence before relying on them.
6. Before a long execution phase, record the next concrete action in `docs/PROJECT_STATE.md`.

## Execution workflow

Use Superpowers where it adds discipline, not ceremony.

### 1. Clarify only when needed

Use brainstorming for new behavior, architecture, or materially ambiguous changes. For obvious bug fixes and mechanical tasks, proceed from evidence.

Reflect accepted product/scope/architecture decisions in the durable docs.

### 2. Define the smallest milestone

Before planning, require:

- one user-visible or system-verifiable outcome;
- explicit in-scope and out-of-scope boundaries;
- measurable acceptance criteria;
- known verification commands, or a concrete plan to discover them;
- no dependency on speculative future architecture.

Update `GAP_ANALYSIS.md`, `ROADMAP.md`, and `PROJECT_STATE.md` as needed.

### 3. Plan proportionally

For non-trivial work, delegate planning to a fresh **planner** using Superpowers `writing-plans` when available. The plan must:

- read approved scope plus only relevant architecture/context;
- decompose work into small independently verifiable tasks;
- name exact files/symbols when known;
- state verification for every task;
- mark safe parallelism and overlapping-file conflicts;
- save to `docs/plans/active/YYYY-MM-DD-<slug>.md`;
- avoid speculative refactors.

The orchestrator trims unnecessary work before execution. Skip a formal plan for truly small, obvious changes.

### 4. Implement with bounded ownership

Prefer fresh **implementers** for independent plan tasks. Use a sequential plan when tasks are tightly coupled.

For each implementation task:

1. Give one bounded outcome, acceptance criteria, relevant paths, and verification command.
2. Use TDD when behavior is testable: RED → GREEN → REFACTOR.
3. Make the smallest change that satisfies the task.
4. Run task-local verification.
5. Return changed files, checks and results, assumptions, and unresolved risks.

Parallelize only independent tasks that do not edit the same files or depend on uncommitted outputs. Prefer 2–4 useful workers over maximum fan-out.

### 5. Review independently

After implementation, use fresh reviewer instances:

- **Spec pass:** compare behavior/diff with task, MVP scope, and acceptance criteria. Flag missing or extra behavior; do not bikeshed.
- **Quality pass:** inspect correctness, edge cases, security, failure handling, state/concurrency hazards, maintainability, tests, and accidental complexity.

If a reviewer finds a blocker, send a precise fix request to a fresh implementer, then repeat the failed review pass. Reviewers do not edit the code they review.

### 6. Verify the milestone

Use a fresh **verifier** to run the relevant gate from `docs/TEST_PLAN.md`, including as applicable:

- focused tests and full suite;
- lint/format checks;
- type checks;
- build/package step;
- migrations/schema validation;
- smoke test of the critical user path;
- existing security/dependency checks.

A milestone passes only with concrete evidence. If a required check cannot run, record why and what evidence substitutes for it; do not call the milestone fully verified.

### 7. Checkpoint and continue

When a milestone passes:

1. Update `docs/PROJECT_STATE.md` with the verified state and next action.
2. Update `GAP_ANALYSIS.md`; update `ARCHITECTURE.md` only for architecture that now exists.
3. Append material tradeoffs to `DECISIONS.md`.
4. Move a completed active plan to `docs/plans/completed/` only when all acceptance criteria pass.
5. Update `ROADMAP.md` / `BACKLOG.md` without expanding the MVP silently.
6. Continue to the next approved milestone unless blocked by a consequential product decision, destructive action, unavailable permission/credential, or failed verification that needs human input.

Use Superpowers `verification-before-completion` before completion claims and `finishing-a-development-branch` for final integration/PR cleanup when available.

## Delegation contract

Every child prompt should contain, compactly:

- **Role:** explorer / planner / implementer / reviewer / verifier.
- **Goal:** one bounded outcome.
- **Why:** one sentence tied to the active milestone.
- **Read:** exact docs/files to inspect first.
- **Constraints:** MVP boundary, forbidden scope, compatibility/security constraints.
- **Acceptance:** observable pass conditions.
- **Verify:** exact checks when known.
- **Return:** concise evidence, changed files if any, unresolved risks; no transcript.

Do not forward the entire parent conversation. Point to durable files instead.

## Role boundaries

- **explorer:** read-only evidence gathering, code-path tracing, dependency/API research, command discovery.
- **planner:** writes implementation plans only; no production code.
- **implementer:** production/test code for one bounded task; no self-approval.
- **reviewer:** read-only review; use fresh instances for spec and quality passes.
- **verifier:** runs verification and reports evidence; fixes nothing unless explicitly reassigned as implementer.

## Simplicity gate

Before adding a new abstraction, dependency, service, table, queue, cache, worker, flag, or configuration layer, answer:

1. Which current acceptance criterion requires it?
2. What is the simpler alternative?
3. What concrete failure occurs without it now?

If the answers are weak, do not add it. Defer the idea to `BACKLOG.md` if it may matter later.

Prefer existing dependencies, direct code, one process, one data store, interfaces only at real boundaries, and readable duplication over the wrong abstraction.

## Verification and debugging

- Never change code to fix a failure you have not reproduced or localized with evidence.
- Use systematic debugging for non-obvious failures.
- Re-run the smallest failing check first, then broaden verification.
- For regressions, prove RED before GREEN when practical.
- Never delete or weaken a meaningful test merely to make the suite pass.
- Do not hide warnings/errors unless the project explicitly accepts them.

## Documentation discipline

Docs describe reality, not aspirations.

- `PRODUCT_BRIEF.md` / `MVP_SCOPE.md` change only when product intent or scope changes.
- `ARCHITECTURE.md` records the implemented system plus accepted near-term constraints; speculation belongs in `BACKLOG.md` or `DECISIONS.md`.
- `ROADMAP.md` stays outcome-oriented.
- `BACKLOG.md` is prioritized but non-binding.
- `DECISIONS.md` is append-only; supersede earlier decisions rather than rewriting history.
- `PROJECT_STATE.md` stays short enough to read at every session start.

## Completion standard

Do not say “done”, “fixed”, “working”, or “ready” unless evidence supports it.

A completed milestone has satisfied acceptance criteria, independent spec and quality review, required checks passing, durable docs synchronized with reality, no known blocker hidden in chat context, and a clear next action or explicit completed state in `PROJECT_STATE.md`.
