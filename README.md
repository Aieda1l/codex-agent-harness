# Codex Agent Harness

A long-horizon software-development harness for Codex that combines **Superpowers**, durable project context, specialized subagents, milestone-based verification, and explicit model routing.

The goal is simple:

> **Give one strong orchestrator enough structure to plan, delegate, implement, verify, recover context, and keep shipping without turning the repository into an over-engineered agent framework.**

The harness is designed around four principles:

- Ship the first working MVP quickly.
- Avoid unnecessary complexity.
- Keep the system easy to extend later.
- Verify progress after every meaningful milestone.

It uses **GPT-6 Astra** as the primary orchestrator and delegates bounded work to **GPT-5.6 Sol High** subagents through [`codex-chatgpt-web`](https://github.com/miuuyy/codex-chatgpt-web), allowing ChatGPT Web models available through your ChatGPT plan to participate in Codex workflows.

---

## Why this exists

Long-running coding agents tend to fail in predictable ways.

They lose the original product intent.

They accumulate enormous conversational context.

They prematurely build abstractions that the MVP does not need.

They make several changes at once and discover integration problems too late.

They confuse "the code looks right" with "the milestone is actually verified."

They repeatedly rediscover architectural decisions that should have been written down once.

This harness treats those as **context and orchestration problems**, not prompt-engineering problems.

Instead of putting everything into one enormous `AGENTS.md`, it divides responsibility between:

1. a small orchestration contract;
2. persistent project documentation;
3. executable implementation plans;
4. fresh specialist subagents;
5. explicit verification gates.

The repository becomes the agent's long-term memory.

---

# Architecture

The intended topology is:

```text
                        ┌─────────────────────┐
                        │    GPT-6 Astra      │
                        │    Orchestrator     │
                        └─────────┬───────────┘
                                  │
                    owns product state + routing
                                  │
              ┌───────────────────┼────────────────────┐
              │                   │                    │
              ▼                   ▼                    ▼
        ┌───────────┐       ┌───────────┐       ┌───────────┐
        │ Explorer  │       │  Planner  │       │ Reviewer  │
        │ Sol High  │       │ Sol High  │       │ Sol High  │
        └───────────┘       └───────────┘       └───────────┘
                                  │
                                  ▼
                            ┌─────────────┐
                            │ Implementer │
                            │  Sol High   │
                            └──────┬──────┘
                                   │
                     ┌─────────────┴─────────────┐
                     ▼                           ▼
               Spec review                Quality review
                Sol High                    Sol High
                     │                           │
                     └─────────────┬─────────────┘
                                   ▼
                             ┌──────────┐
                             │ Verifier │
                             │ Sol High │
                             └────┬─────┘
                                  │
                                  ▼
                         verified milestone
```

The orchestrator keeps the strategic context.

Subagents receive bounded tasks with fresh context.

The repository stores everything that must survive conversation compaction, restarts, or new Codex tasks.

---

# Core workflow

The default development loop is:

```text
Understand
    ↓
Brainstorm
    ↓
Update durable project context
    ↓
Define smallest useful milestone
    ↓
Write implementation plan
    ↓
Trim plan to MVP
    ↓
Implement
    ↓
Spec review
    ↓
Quality review
    ↓
Verification
    ↓
Checkpoint project state
    ↓
Next milestone
```

The orchestrator should resist skipping directly from an idea to implementation.

Likewise, it should resist endlessly planning something that can already be tested with a small vertical slice.

The target is:

> **the smallest verified implementation that meaningfully advances the product.**

---

# Superpowers

This harness is designed to work with the excellent [Superpowers](https://github.com/obra/superpowers) workflow.

Superpowers supplies disciplined development behaviors such as:

- brainstorming;
- writing specifications;
- writing implementation plans;
- subagent-driven development;
- executing plans;
- test-driven development;
- systematic debugging;
- verification before completion;
- reviewing implementation against requirements;
- finishing development branches.

This repository does **not** copy Superpowers skills.

Instead, `AGENTS.md` instructs the orchestrator when to use them.

That keeps the harness small while allowing Superpowers itself to evolve independently.

---

# Persistent project context

Long-horizon work should not depend on remembering an old chat.

The following files form the durable project record:

```text
docs/
├── PRODUCT_BRIEF.md
├── MVP_SCOPE.md
├── GAP_ANALYSIS.md
├── ARCHITECTURE.md
├── ROADMAP.md
├── BACKLOG.md
├── TEST_PLAN.md
├── DECISIONS.md
├── PROJECT_STATE.md
└── plans/
    ├── active/
    └── completed/
```

Each document has a specific job.

## `PRODUCT_BRIEF.md`

Defines:

- the problem;
- target users;
- product promise;
- desired outcomes;
- major constraints;
- non-goals.

This should change slowly.

---

## `MVP_SCOPE.md`

Defines what must exist before the first meaningful release.

It explicitly separates:

```text
MUST HAVE
SHOULD HAVE
LATER
NOT IN MVP
```

If an implementation task does not help satisfy the MVP, it should require a clear reason to exist.

---

## `GAP_ANALYSIS.md`

Describes the difference between:

```text
current state
    ↓
required state
```

This prevents agents from repeatedly analyzing the entire repository from scratch.

---

## `ARCHITECTURE.md`

Captures the architecture that actually exists or has been intentionally selected.

It should document things such as:

- components;
- boundaries;
- data flow;
- external dependencies;
- interfaces;
- persistence;
- deployment assumptions;
- important invariants.

Do not turn it into a speculative architecture wishlist.

---

## `ROADMAP.md`

Contains coarse project sequencing.

Example:

```text
M0 — repository baseline
M1 — smallest end-to-end MVP
M2 — core usability
M3 — production hardening
M4 — extensibility
```

Milestones should describe outcomes, not arbitrary collections of tickets.

---

## `BACKLOG.md`

Contains executable future work.

Tasks should be concrete enough that an agent could eventually select one without reinterpreting the entire product.

---

## `TEST_PLAN.md`

Defines how the system proves that it works.

It may contain:

- unit tests;
- integration tests;
- end-to-end tests;
- manual acceptance checks;
- performance checks;
- security checks;
- regression scenarios.

Tests should correspond to actual product risks.

---

## `DECISIONS.md`

A lightweight architectural decision log.

Record decisions that future agents might otherwise reopen repeatedly.

Typical entries include:

```text
Date
Decision
Context
Alternatives considered
Reason
Consequences
```

Do not record trivial implementation details.

---

# `PROJECT_STATE.md`

`PROJECT_STATE.md` is the recovery checkpoint for the orchestration loop.

It should stay deliberately small.

It records things such as:

```text
Current milestone
Current objective
Active implementation plan
Last verified state
Verification evidence
Known blockers
Important risks
Next action
```

When a new task starts, context is compacted, or execution resumes after a long pause, the orchestrator should be able to read this file and quickly answer:

> Where are we, what has actually been verified, and what should happen next?

The rest of `docs/` provides depth.

`PROJECT_STATE.md` provides orientation.

---

# Plans

Implementation plans live under:

```text
docs/plans/active/
```

Completed plans move to:

```text
docs/plans/completed/
```

A useful plan should include:

- objective;
- scope;
- affected files;
- implementation steps;
- tests;
- verification commands;
- completion criteria.

Plans should be detailed enough for a fresh implementation agent to execute without inventing product requirements.

They should not become miniature design documents for hypothetical future systems.

---

# Agent roles

The harness intentionally uses a small number of roles.

More agents do not automatically produce better software.

## Orchestrator

**Model:** GPT-6 Astra

Owns:

- product intent;
- task decomposition;
- milestone selection;
- agent routing;
- scope control;
- durable context;
- integration decisions;
- final milestone acceptance.

The orchestrator should delegate execution detail whenever doing so preserves its context for higher-level reasoning.

---

## Explorer

**Model:** ChatGPT Web — GPT-5.6 Sol High

Used for:

- repository reconnaissance;
- tracing unfamiliar systems;
- finding relevant code;
- investigating dependencies;
- collecting evidence before planning.

The explorer should return evidence, not implement speculative fixes.

---

## Planner

**Model:** ChatGPT Web — GPT-5.6 Sol High

Used for:

- turning specifications into executable implementation plans;
- identifying affected files;
- defining test strategy;
- spotting dependency ordering.

A planner does not get permission to expand MVP scope simply because a larger architecture looks cleaner.

---

## Implementer

**Model:** ChatGPT Web — GPT-5.6 Sol High

Used for:

- writing code;
- writing tests;
- executing individual plan tasks;
- resolving implementation-local failures.

Multiple implementers may work concurrently only when their work is genuinely independent.

Parallelism is a tool, not a goal.

---

## Reviewer

**Model:** ChatGPT Web — GPT-5.6 Sol High

The same reviewer role is used in **fresh independent contexts** for two different reviews.

### Specification review

Checks:

> Did we implement what the milestone actually required?

### Quality review

Checks:

> Is the resulting implementation maintainable, correct, and proportionate?

Keeping those reviews separate reduces the chance that elegant-but-wrong implementations pass because the code itself looks good.

---

## Verifier

**Model:** ChatGPT Web — GPT-5.6 Sol High

The verifier independently executes the milestone's acceptance checks.

Its job is not to assume previous agents ran the tests correctly.

Its job is to produce evidence.

A milestone is not complete merely because implementation finished.

It is complete when its defined acceptance conditions pass.

---

# Model routing

The intended model configuration is:

```text
Orchestrator
└── GPT-6 Astra

Subagents
└── ChatGPT Web — GPT-5.6 Sol High
```

The project-local Codex configuration lives in:

```text
.codex/config.toml
```

Role-specific configuration lives in:

```text
.codex/agents/
├── explorer.toml
├── planner.toml
├── implementer.toml
├── reviewer.toml
└── verifier.toml
```

Subagents are intentionally pinned to the Web High route.

The harness should fail visibly if that route is unavailable rather than quietly spending another model quota or changing the intended reasoning tier.

---

# codex-chatgpt-web

This harness uses:

https://github.com/miuuyy/codex-chatgpt-web

`codex-chatgpt-web` makes ChatGPT Web model modes available from the Codex environment and can connect them to the active task's tools through its Full Harness setup.

Follow that project's current installation and MCP instructions before relying on Web-routed subagents.

The recommended topology for this harness is:

```text
Native Codex Astra orchestrator
        │
        ▼
codex-chatgpt-web
        │
        ▼
ChatGPT Web Sol High subagents
```

---

# Subagent protocol

For a mixed native-parent / Web-child topology, this repository is designed around the compatibility protocol:

```bash
codex-chatgpt-web subagents compatibility-v1
```

Inspect the active configuration with:

```bash
codex-chatgpt-web subagents status
```

After changing the subagent protocol, restart Codex and start a new task so the new task begins with the expected delegation protocol.

If `codex-chatgpt-web` changes its protocol behavior in a future release, follow its current documentation rather than blindly preserving this setting.

---

# Installation

## 1. Clone this repository

Use it directly:

```bash
git clone https://github.com/Aieda1l/codex-agent-harness.git
cd codex-agent-harness
```

Or copy the harness files into an existing project:

```text
AGENTS.md
.codex/
docs/
```

---

## 2. Install Superpowers

Install the Superpowers plugin for your Codex environment.

Project:

https://github.com/obra/superpowers

After installation, verify its skills are available before beginning a long-running project.

---

## 3. Install `codex-chatgpt-web`

Project:

https://github.com/miuuyy/codex-chatgpt-web

Complete its launcher setup, sign in with your own ChatGPT account, install the Web model entries, and configure the Full Harness/MCP integration if you want Web models to use Codex tools.

Follow the upstream documentation because browser integration details may change over time.

---

## 4. Configure subagent compatibility

```bash
codex-chatgpt-web subagents compatibility-v1
codex-chatgpt-web subagents status
```

Then restart Codex.

---

## 5. Verify model availability

Confirm that your model picker exposes the intended modes.

The desired configuration for this harness is:

```text
Primary:
GPT-6 Astra

Subagents:
ChatGPT Web — GPT-5.6 Sol High
```

Model availability depends on the account, plan, launcher version, and current ChatGPT model availability.

---

## 6. Initialize project context

Open the target repository in Codex and start with:

```text
Initialize this repository using AGENTS.md.

Inspect the repository before making assumptions.

Use Superpowers where appropriate.

Populate only project documentation that can be supported by repository
evidence or explicit product requirements.

Determine the smallest useful MVP milestone, write an implementation plan,
execute it with bounded subagents, and stop only after its verification gate
has passed or a genuine blocker has been documented.
```

The orchestrator should now establish the baseline documentation and begin the first milestone.

---

# Operating principles

## 1. Working software beats speculative architecture

Prefer:

```text
small vertical slice
→ verify
→ learn
→ extend
```

over:

```text
design enormous framework
→ build abstractions
→ eventually attempt product behavior
```

---

## 2. Complexity must earn its place

Do not introduce:

- abstraction layers;
- services;
- frameworks;
- queues;
- databases;
- caches;
- generalized plugin systems;
- distributed infrastructure;

unless current requirements justify them.

"Useful later" is not sufficient by itself.

---

## 3. Preserve extension paths, not unused implementations

There is a major difference between:

> making future change possible

and:

> implementing future change now.

Prefer clean interfaces and sensible boundaries over premature features.

---

## 4. Build the narrowest complete slice

The preferred first milestone usually crosses the entire required path.

For example:

```text
input
→ business logic
→ persistence/API
→ output
→ test
```

A thin end-to-end feature teaches more than five disconnected architectural components.

---

## 5. Verification is part of implementation

A task is not complete because code was written.

A milestone should have explicit verification such as:

```bash
npm test
npm run lint
npm run typecheck
npm run build
```

or equivalent project-specific commands.

Where automated verification is impossible, define an explicit manual acceptance test.

---

## 6. Evidence beats confidence

Agents should communicate:

```text
Command executed
Observed result
Relevant test output
Remaining limitations
```

not:

```text
This should work.
```

---

## 7. Fresh contexts are valuable

Implementation agents should receive the information needed for their bounded task rather than inheriting every exploratory conversation that happened before them.

Fresh contexts improve:

- focus;
- independent reasoning;
- review quality;
- context efficiency.

The repository is the shared memory layer.

---

## 8. The orchestrator protects context

The primary agent should avoid personally doing every repository search, implementation step, and review when a bounded subagent can perform the work.

Its scarce resource is not typing speed.

It is **coherent long-horizon understanding**.

---

# Milestone lifecycle

Every meaningful milestone should roughly follow this sequence.

### 1. Select outcome

Choose one concrete, user-visible or architecture-enabling outcome.

### 2. Establish acceptance criteria

Define how success will be demonstrated.

### 3. Explore

Investigate only enough of the repository to plan safely.

### 4. Plan

Write the executable implementation plan.

### 5. Scope check

Remove unnecessary complexity.

Ask:

> Is every proposed component required to reach the milestone?

### 6. Implement

Delegate bounded tasks.

### 7. Spec review

Verify requirements were actually satisfied.

### 8. Quality review

Inspect maintainability, correctness, and avoidable complexity.

### 9. Verify

Execute the milestone gate independently.

### 10. Checkpoint

Update:

```text
PROJECT_STATE.md
ROADMAP.md
BACKLOG.md
DECISIONS.md
```

only where appropriate.

### 11. Continue

Select the next smallest useful milestone.

---

# Concurrency

The harness supports concurrent subagents but intentionally does not maximize fan-out.

Good parallel work:

```text
Agent A → frontend component
Agent B → independent backend endpoint
Agent C → documentation/research
```

Poor parallel work:

```text
Agent A → edits auth.ts
Agent B → redesigns auth.ts
Agent C → refactors auth.ts
Agent D → writes tests against a moving auth.ts
```

As a default, use roughly **2–4 concurrent implementation agents** only when file ownership and dependencies are sufficiently independent.

Integration cost should remain lower than the time saved by parallelism.

---

# When the orchestrator should stop

The orchestrator should generally continue autonomously between milestones.

It should stop for human input when it encounters something genuinely consequential, such as:

- ambiguous product requirements with materially different outcomes;
- credentials or permissions it cannot obtain;
- destructive operations;
- external costs;
- legal or security-sensitive choices;
- irreversible migrations;
- verification failures that expose a product decision rather than an implementation bug.

It should not stop merely because:

- multiple reasonable code implementations exist;
- a file needs refactoring;
- a test fails;
- documentation needs updating;
- the next implementation step is obvious.

Those are ordinary engineering tasks.

---

# Example project lifecycle

A new project might evolve like this:

```text
M0 — Baseline
├── inspect repository
├── establish product brief
├── establish MVP scope
├── capture architecture
└── verify development environment

M1 — First vertical slice
├── implement smallest end-to-end behavior
├── add tests
├── run acceptance checks
└── checkpoint

M2 — Complete MVP
├── add remaining essential behavior
├── improve failure handling
├── complete acceptance coverage
└── checkpoint

M3 — Hardening
├── address reliability gaps
├── address security gaps
├── improve observability
└── verify production readiness

M4 — Extension
├── revisit deferred capabilities
├── expand architecture only where justified
└── continue milestone loop
```

The exact milestones belong in `ROADMAP.md`.

---

# Repository structure

```text
.
├── AGENTS.md
│
├── .codex/
│   ├── config.toml
│   └── agents/
│       ├── explorer.toml
│       ├── planner.toml
│       ├── implementer.toml
│       ├── reviewer.toml
│       └── verifier.toml
│
└── docs/
    ├── README.md
    ├── PRODUCT_BRIEF.md
    ├── MVP_SCOPE.md
    ├── GAP_ANALYSIS.md
    ├── ARCHITECTURE.md
    ├── ROADMAP.md
    ├── BACKLOG.md
    ├── TEST_PLAN.md
    ├── DECISIONS.md
    ├── PROJECT_STATE.md
    ├── SETUP.md
    │
    └── plans/
        ├── active/
        └── completed/
```

---

# What belongs in `AGENTS.md`?

`AGENTS.md` is the operating contract.

It should answer questions like:

- What should the orchestrator read first?
- What information is authoritative?
- When should work be delegated?
- Which model should perform which role?
- When should Superpowers be invoked?
- What counts as milestone completion?
- When should the agent ask a human?
- How should durable project state be maintained?

It should **not** contain the entire product specification.

That belongs in `docs/`.

This distinction is central to keeping the harness useful over long projects.

---

# Starting a fresh Codex session

For an existing project already using the harness, a minimal restart prompt should be enough:

```text
Resume this project using AGENTS.md and docs/PROJECT_STATE.md.

Confirm the current repository state against the recorded checkpoint.

Continue the active milestone if one exists. Otherwise select the next
smallest roadmap milestone.

Use fresh subagents for bounded work and do not mark the milestone complete
until its verification gate passes.
```

The project should not depend on reconstructing an old chat transcript.

---

# Adapting the harness

This repository is intentionally opinionated about process but not technology.

It can be used with:

- TypeScript;
- Python;
- Rust;
- Go;
- Swift;
- Java;
- mobile applications;
- web applications;
- CLIs;
- libraries;
- infrastructure repositories;
- monorepos.

Modify the project documents and verification commands.

Avoid changing the orchestration model unless the existing structure is genuinely preventing progress.

---

# Updating model names

Model names will change.

The orchestration pattern should not need to.

Today the intended arrangement is:

```text
best available reasoning model
        ↓
orchestrator

strong inexpensive/free Web reasoning model
        ↓
bounded subagents
```

If GPT-6 Astra or GPT-5.6 Sol High is superseded, update the `.codex` configuration while preserving the responsibility boundaries.

This is why the repository is named **Codex Agent Harness**, not after a specific model generation.

---

# Non-goals

This project is not trying to:

- create an autonomous software company;
- maximize the number of simultaneously running agents;
- replace product judgment;
- eliminate human review forever;
- construct an elaborate multi-agent message bus;
- maintain infinite conversational history;
- pre-build architecture for every possible future feature.

The aim is much narrower:

> **make a capable coding agent reliably useful over longer projects.**

---

# Security

Treat any agent with terminal and repository access as a powerful development tool.

Review the security model of every integration you enable.

In particular:

- protect browser session state;
- never commit credentials;
- use environment variables or secure secret stores;
- review destructive commands;
- inspect third-party MCP servers before granting tool access;
- keep dependencies and automation tools updated;
- use the `codex-chatgpt-web` integration only with accounts and machines you control;
- follow applicable OpenAI and workspace policies.

The harness deliberately separates product automation from authentication and provider configuration.

Secrets do not belong in this repository.

---

# Troubleshooting

## Subagents are using the wrong model

Check:

```bash
codex-chatgpt-web subagents status
```

Then inspect:

```text
.codex/config.toml
.codex/agents/*.toml
```

Restart Codex after changing protocol or model routing.

---

## A Web agent cannot use tools

Confirm that the `codex-chatgpt-web` Full Harness/MCP setup is connected and that the required tools are exposed to the Web model.

Follow the upstream project's current troubleshooting guide.

---

## The orchestrator keeps forgetting context

Check whether:

```text
docs/PROJECT_STATE.md
```

is actually being maintained.

Then inspect whether architectural or product knowledge is stuck only inside old implementation plans instead of the appropriate durable documentation file.

---

## The system is over-planning

Reduce the milestone.

Ask:

```text
What is the smallest implementation that can produce verified user value?
```

Move speculative features into `BACKLOG.md`.

---

## Too many subagents are colliding

Reduce concurrency.

Give agents explicit ownership of independent files or modules.

Parallel execution should only be used where merge and integration risk remain small.

---

# Philosophy

The most capable coding model still benefits from a good environment.

A reliable long-horizon agent does not need thousands of lines of prompt instructions.

It needs:

```text
clear intent
+
durable context
+
bounded delegation
+
small milestones
+
independent review
+
real verification
```

That is what this harness provides.

---

# Credits

This project builds on ideas and tooling from:

- [OpenAI Codex](https://github.com/openai/codex)
- [Superpowers](https://github.com/obra/superpowers)
- [`codex-chatgpt-web`](https://github.com/miuuyy/codex-chatgpt-web)

`codex-chatgpt-web` is independent software and is not affiliated with or endorsed by OpenAI.

---

# License

Choose a license appropriate for your project.

For a permissive open-source harness, MIT is a straightforward option:

```text
MIT License
```

Add a `LICENSE` file before publishing if you want others to reuse or modify the harness.

---

## Quick start

```bash
# Clone
git clone https://github.com/Aieda1l/codex-agent-harness.git
cd codex-agent-harness

# Configure mixed-backend subagents
codex-chatgpt-web subagents compatibility-v1
codex-chatgpt-web subagents status

# Restart Codex and start a new task
```

Then tell the orchestrator:

```text
Initialize this repository using AGENTS.md.

Establish the current project baseline, determine the smallest useful MVP
milestone, and execute it using the documented orchestration workflow.

Keep scope minimal, use fresh subagents for bounded work, and do not declare
the milestone complete until independent verification passes.
```

Then start shipping.
