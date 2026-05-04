# Stage 11 — Educational Agentic Engineering Demos

Patterns from the working setup of a Stage 11 hyperengineer. Published as reference material for operators learning to build software with digital intelligences as first-class teammates.

This is what we actually run, every day. Not a sanitized demo. The files in this repo are pulled from a live operator's `~/.claude/` and the Stage 11 skills tree. Copy what fits. Replace what doesn't. The patterns transfer; the specifics will not.

────

## What's Here

| Artifact | What it is |
|---|---|
| [`atins-global-claude-md-example.md`](atins-global-claude-md-example.md) | One operator's global `~/.claude/CLAUDE.md`. Loads into every Claude Code session on the machine. The headline pattern: language overloading. |
| [`commands/trident-code-review.md`](commands/trident-code-review.md) | Nine-reviewer multi-model code review. Claude + Codex + Gemini, three lenses each, four parallel synthesizers, one validated machine-actionable fix plan. |
| [`commands/trident-plan-review.md`](commands/trident-plan-review.md) | Same shape as trident code review, pointed at a plan or design document instead of a diff. |
| [`commands/code_review.md`](commands/code_review.md), [`commands/code_review_critical.md`](commands/code_review_critical.md) | Single-agent review prompts the trident commands compose. |
| [`commands/capture-skill.md`](commands/capture-skill.md) | Extract reusable knowledge from a conversation into a `CLAUDE.md` file or a new slash command. The flywheel for a self-improving codebase. |
| [`agents/`](agents/) | Markdown prompt files that the trident commands invoke as sub-agent definitions. Just markdown, but Claude Code reads them from `~/.claude/agents/` when its Agent tool spawns sub-agents, so they live there once installed. |
| [`skills/lattice-delegate/SKILL.md`](skills/lattice-delegate/SKILL.md) | Orchestrator + delegator pattern for end-to-end ticket execution. Assumes [c11](https://github.com/Stage-11-Agentics/c11) and [Lattice](https://github.com/Stage-11-Agentics/lattice). |

────

## The CLAUDE.md File: Atin's Global Example

[`atins-global-claude-md-example.md`](atins-global-claude-md-example.md) is the most portable artifact in this repo. Read it first.

A global `CLAUDE.md` lives at `~/.claude/CLAUDE.md` and loads into every Claude Code session on the machine, before the operator types a word. It is the contract between the operator and the agents they work with: vocabulary, defaults, escalation rules, the patterns the operator wants the agents to apply by reflex.

The headline pattern is **language overloading**. Single-word triggers that invoke specific agent behaviors. Defined once, used everywhere.

| Trigger | Behavior |
|---|---|
| `clear` | Spawn a fresh, headless agent with isolated context |
| `loopy` | Drive a full validation loop. Implement, validate, iterate until actually done. |
| `dialogue` | Pause and ask every question needed before building |
| `full force` | Parallel Claude + Codex analysis, merged into one document |
| `triple force` | Parallel Claude + Codex + Gemini analysis, merged |
| `ultrathink` | Spend extra reasoning on edge cases and side effects before acting |
| `ralph` | Launch an autonomous loop that runs until the task is complete |

These are the most under-discussed leverage points in agentic engineering. Defined once in a global file, they collapse the cost of explaining what you want from a paragraph to a word. The agent enters a different mode. Misunderstanding drops. Throughput rises.

The full file goes deeper: bias-toward-action heuristics, the two patterns for spawning sub-agents (interactive vs. headless), parallelization defaults, the auto-commit and auto-push policy, dialogue-driven development, thrashing detection, AskUserQuestion best practices.

Treat it as a reference implementation. Steal the table. Steal the structure. Build your own vocabulary on top.

**Install:**

```bash
# Back up your existing global CLAUDE.md if you have one
cp ~/.claude/CLAUDE.md ~/.claude/CLAUDE.md.bak 2>/dev/null

# Copy this one in (or merge it with yours)
cp atins-global-claude-md-example.md ~/.claude/CLAUDE.md
```

────

## Trident Code Review

The flagship workflow. Spawns nine reviewers across three providers and three lenses, then four parallel synthesizers, in one slash command.

```
trident-code-review
├── Phase 1-2: Build context (branch info, commits, full diff, test results)
├── Phase 3-4: Stage prompt files in notes/.tmp/
├── Phase 5: Launch nine reviewers in parallel
│     ├── Claude:  Standard, Critical, Evolutionary
│     ├── Codex:   Standard, Critical, Evolutionary
│     └── Gemini:  Standard, Critical, Evolutionary
├── Phase 6: Quality gate (drop empty or failed reviews)
├── Phase 7: Launch four synthesizers in parallel
│     ├── Standard lens consolidator
│     ├── Critical lens consolidator
│     ├── Evolutionary lens consolidator
│     └── Action-ready synthesizer (validated, machine-actionable fix plan)
└── Phase 8-9: Open the four syntheses, then apply default fixes
```

The fourth synthesizer is the load-bearing innovation. It reads all nine raw reviews plus the diff and produces a `synthesis-action.md` with two sections: **Apply by default** (validated findings the next agent commits without asking) and **Surface to user** (findings that need human judgment). Downstream agents read that one file as their action contract instead of re-deriving the fix list from nine documents.

That separation is what makes nine reviewers productive instead of overwhelming.

### Install

You will need [Claude Code](https://docs.anthropic.com/en/docs/claude-code) (the orchestrator and the three Claude reviewers run here), [Codex CLI](https://github.com/openai/codex) (three Codex reviewers), and [Gemini CLI](https://github.com/google-gemini/gemini-cli) (three Gemini reviewers). If you skip Codex or Gemini, the workflow degrades gracefully to six or three reviewers and still produces a useful synthesis.

```bash
# Clone
git clone https://github.com/Stage-11-Agentics/stage11-educational-agentic-engineering-demos.git
cd stage11-educational-agentic-engineering-demos

# Slash commands. Trident composes code_review.md and code_review_critical.md
# into its prompt files, so all three must be in ~/.claude/commands/.
mkdir -p ~/.claude/commands
cp commands/trident-code-review.md ~/.claude/commands/
cp commands/code_review.md         ~/.claude/commands/
cp commands/code_review_critical.md ~/.claude/commands/

# Sub-agent prompt files. Trident reads CodeReview-Evolutionary.md from
# ~/.claude/agents/ when it spawns the evolutionary Claude reviewer via the
# Agent tool. Plain markdown, but the path matters.
mkdir -p ~/.claude/agents
cp agents/CodeReview-Evolutionary.md ~/.claude/agents/

# Optional: hardlink commands into Codex CLI's prompt directory so the same
# file backs both `/trident-code-review` in Claude Code and the equivalent
# Codex prompt. Edit one, both update.
mkdir -p ~/.codex/prompts
ln ~/.claude/commands/trident-code-review.md ~/.codex/prompts/trident-code-review.md 2>/dev/null
ln ~/.claude/commands/code_review.md         ~/.codex/prompts/code_review.md 2>/dev/null
ln ~/.claude/commands/code_review_critical.md ~/.codex/prompts/code_review_critical.md 2>/dev/null
```

### Run It

From any feature branch in any repo with diff against `dev`, `main`, or `master`:

```
/trident-code-review
```

Optional context goes after the command:

```
/trident-code-review pay particular attention to the new auth middleware
```

The orchestrator builds a context document with the diff, runs the project's test suite once, stages prompt files in `notes/.tmp/trident-<id>/`, spawns nine reviewers, gates on quality, runs four synthesizers in parallel, and opens all four syntheses. Total wall clock typically lands around five to ten minutes depending on diff size and the slowest reviewer.

The review pack lives at `notes/trident-review-<id>-pack-<timestamp>/`. Twelve files total: nine raw reviews, three lens syntheses, one action-ready synthesis. After the syntheses land, the orchestrator (you, the agent that ran the command) is expected to apply the default fixes and surface the rest.

────

## Trident Plan Review

Same shape as trident code review, pointed at a plan or design document instead of a diff. Same nine reviewers, same three lenses, same four synthesizers. Output is a validated revision plan instead of a fix plan.

```
/trident-plan-review path/to/PLAN.md
/trident-plan-review path/to/PLAN.md focus on assumptions about scale
```

Use it before implementation starts, when the cost of building the wrong thing is higher than the cost of one more review pass.

**Install** (in addition to the trident code review install above):

```bash
cp commands/trident-plan-review.md ~/.claude/commands/

# Three plan-review lens prompts the trident plan command spawns
cp agents/PlanReview-Standard.md      ~/.claude/agents/
cp agents/PlanReview-Adversarial.md   ~/.claude/agents/
cp agents/PlanReview-Evolutionary.md  ~/.claude/agents/

# Optional Codex hardlinks
ln ~/.claude/commands/trident-plan-review.md ~/.codex/prompts/trident-plan-review.md 2>/dev/null
```

────

## Capture-Skill

After a conversation that uncovered a useful workflow, a non-obvious gotcha, or a pattern worth keeping, run:

```
/capture-skill
```

The agent extracts what it learned and writes it into either the project's `CLAUDE.md` or a new slash command. The next session starts with what this one figured out. Used regularly, it compounds. The codebase becomes a memory the team writes to and reads from.

**Install:**

```bash
cp commands/capture-skill.md ~/.claude/commands/
```

────

## Lattice-Delegate

The pattern for taking a single ticket from "this needs to ship" to "this is shipped" without the operator in the loop for every step.

```
Operator pane (Orchestrator)
└── spawns ── Delegator pane
              ├── Plan sub-agent
              ├── Impl sub-agent
              ├── Translator sub-agent (when localized strings change)
              ├── Review sub-agent (typically invokes trident)
              ├── Validate sub-agent
              └── PR
```

The orchestrator stays with the operator and watches for status transitions. The delegator drives the work and is the operator's interface to the ticket. Each phase runs in its own sibling pane so the operator can scrub any phase from one vertical slice. All code work happens inside an isolated git worktree. All coordination flows through Lattice as the comms bus.

The failure modes of long-running autonomous work are mostly coordination failures. Sub-agents go silent. Status doesn't update. The operator doesn't know the work needs them until they ask. The delegator pattern bakes the discipline into the structure: status transitions are mandatory, the orchestrator polls actively, every phase ends with a Lattice comment.

**Prerequisites:** [c11](https://github.com/Stage-11-Agentics/c11) and [Lattice](https://github.com/Stage-11-Agentics/lattice). Both are open-source Stage 11 projects. Without that stack the SKILL.md is still useful as a reference for structuring an orchestrator + delegator pattern in your own environment.

**Install** (only meaningful with c11 + Lattice running):

```bash
mkdir -p ~/.claude/skills/lattice-delegate
cp skills/lattice-delegate/SKILL.md ~/.claude/skills/lattice-delegate/
```

────

## Adapt, Don't Copy Wholesale

The patterns transfer. The specifics often don't.

The global `CLAUDE.md` was shaped by one operator's actual work. Some triggers will land in your context. Others won't. The trident review prompts assume a particular review aesthetic, terse, lens-aware, action-synthesizing. Your team might want a different cut. The lattice-delegate skill assumes c11 panes and Lattice tickets. The underlying pattern (orchestrator + delegator + isolated worktree + shared comms bus) holds without that stack. The implementation will look different.

Read the files. Understand the shape. Build your own.

────

## About Stage 11

[Stage 11](https://stage11.ai) builds digital intelligences and the infrastructure they need to operate as first-class engineering peers. The work in this repository is part of how we build, every day. We publish it because the patterns are too useful to keep private and because the next generation of operators is going to need them.

The Stage 11 projects most relevant to this repo:

- [**c11**](https://github.com/Stage-11-Agentics/c11). Native macOS terminal multiplexer; the workspace environment the lattice-delegate skill assumes.
- [**Lattice**](https://github.com/Stage-11-Agentics/lattice). File-based agent-native task tracker; the coordination substrate the lattice-delegate skill writes to.

────

## License

MIT. Use these patterns however they serve you.
