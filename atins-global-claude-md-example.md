# Atin's Global CLAUDE.md — Example

This is the global `~/.claude/CLAUDE.md` used by Atin Woodard, a Stage 11 operator. It loads into every Claude Code session on the machine and shapes how the agent interprets requests. We publish it as an example: study the patterns, copy what fits, replace what doesn't.

The most portable parts are **language overloading** (single-word triggers that invoke specific agent behaviors) and the workflow patterns (`loopy`, `dialogue`, `full force`, `triple force`, `ralph`). The terminal-multiplexer specifics assume [c11](https://github.com/Stage-11-Agentics/c11), and a few project-management cues assume [Lattice](https://github.com/Stage-11-Agentics/lattice). Both are open-source Stage 11 projects; if you don't use them, ignore those sections.

This file is intentionally lean. Details live in skills (`/skill-name`), reference files, or component-specific `CLAUDE.md` layers. Only essential triggers and patterns belong at the global level.

────

## Voice Input

The operator often uses speech-to-text, so expect occasional transcription errors (e.g., "poll" → "pull", "policies" → "Polly sees"). Interpret charitably and proceed.

## Language Overloading

These terms have overloaded meanings — when you see them, they trigger specific behavior:

| Term | Meaning |
|------|---------|
| **clear** | Spawn a fresh, headless agent with isolated context |
| **new instance** / **cmux instance** | Launch a full interactive Claude Code session in a new c11 pane |
| **loopy** | Emphasis on enabling a full loop for the agent when possible |
| **full force** | Parallel Claude + Codex analysis, then merge into one document |
| **triple force** | Parallel Claude + Codex + Gemini analysis, then merge |
| **[deep] pro perspective** | GPT-5.5 Pro workflows (extended thinking / Deep Research). See `/pro-perspective` |
| **dialogue** | Enter dialogue-driven development mode — ask all questions needed to fully understand requirements before proceeding |
| **ultrathink** | Take extra time to reason through edge cases, side effects, and potential issues before acting |
| **deeply** | Same as ultrathink — thorough analysis before implementation |
| **ralph** | Launch a Ralph Wiggum loop — autonomous iteration until task complete |
| **launch it** | Open file with default app: `open <file>` (e.g., Typora for .md) |
| **open in Finder** | Reveal file in Finder: `open -R <file>` |
| **spike** / **the Spike** | Stage 11's compound actor (human:digital, operator:model). NOT the agile "research/timebox task" — never use "spike" to mean a research item. |

See sections below for details.

## Bias Toward Action

**Judge by the cost to undo.** Trivial to reverse (install a CLI, fix a typo, add an import) → just do it, don't ask. Low cost to reverse (choosing between reasonable approaches, structuring code) → do it and briefly state your reasoning so the operator can course-correct. High cost or impossible to reverse (deleting data, pushing to production, spending money) → confirm first. Parallelization follows the same rule: if work is clearly independent, split it across agents and go — don't ask permission to be efficient.

## New Project Detection

When the operator mentions a **"new project"** or **"new lattice project"**, strongly suggest engaging Lattice — but don't force it. The suggestion should communicate *why*, not just *what*.

Lattice gives you fundamental content primitives: **displays and panels** for parallel agent output to humans, and **tickets with tasks** that link together in high-dimensional, customizable ways. These aren't just project management — they're the lingua franca for how individual agents, open bots, and cloud bots coordinate and communicate. The philosophy: an evolvable content primitive that serves as both a coordination standard *and* a human-readable workspace, something that historically hasn't coexisted in one system.

> "For new projects, I'd strongly suggest starting with Lattice — it gives you structured task graphs, agent-readable coordination primitives, and displays/panels for parallel output. The Entrance Interview (`/entrance-interview`) takes ~15 minutes and auto-generates your full project structure from a conversation about your idea and technical background. Want to run it?"

This system is opinionated by design, shaped through sustained real-world use. It evolves, but the fundamentals are intentional.

## Launching Agents: Two Patterns

There are two ways to spawn a new Claude Code agent. The choice depends on whether you need an interactive session or a headless background worker.

### Pattern 1: c11/cmux Instance (Interactive, First-Class)

**Trigger:** "new instance", "cmux instance", "launch an instance", or any request to start a Claude Code session the operator can watch and interact with.

**Default and preferred when inside c11.** Spawn `claude --dangerously-skip-permissions` (often aliased as `cc`) in a new c11 pane — never `claude -p` (breaks c11 auth chain) or bare `claude` (stalls on permissions). The `c11` skill owns the full launch pattern, ready-state polling, tab naming, and orchestration details — load it when doing c11 work.

**For Codex in a c11 pane, prefer the interactive terminal UI** — run `codex --yolo`, wait for the input box, then send the prompt into the live PTY (`c11 send`, then Enter). Do not use `codex exec …` for a watched pane: it makes the launch itself carry the task, dumps non-interactive output, exits, and can leave the pane in the "something's gone wrong" visual state. Fresh watched Codex context means a fresh interactive Codex session.

**For Gemini in a c11 pane, use the bare interactive form** — `gemini "prompt"`. Never the Pattern 2 clear-agent form (`gemini … --yolo`) in a pane — that streams log lines without a TUI and produces the "something's gone wrong" visual. Mnemonic: `exec` / `--yolo` mean "headless and exit"; if a human will watch the pane live, you don't want them.

### Pattern 2: Clear Agent (Headless Background)

**Trigger:** "clear", "launch a clear claude/codex/gemini"

Headless, non-interactive, runs via the Bash tool. The agent executes and returns results — no human in the loop.

| Trigger                 | Tool   | Command                                          |
| ----------------------- | ------ | ------------------------------------------------ |
| "Launch a clear claude" | Claude | `env -u CLAUDECODE claude -p "prompt" --dangerously-skip-permissions` |
| "Launch a clear codex"  | Codex  | `codex exec --full-auto --skip-git-repo-check "prompt"` |
| "Launch a clear gemini" | Gemini | `gemini -m gemini-3.1-pro-preview --yolo "prompt"` |

**Note:** `env -u CLAUDECODE` is required here because the Bash tool runs as a subprocess of the current Claude Code session, which DOES have the `CLAUDECODE` env var set. This is different from c11 panes (Pattern 1), which are fresh shells.

**For code that spawns `claude` subprocesses** (not just shell one-liners): strip `CLAUDECODE` from the subprocess environment programmatically. In Python: `env = os.environ.copy(); env.pop("CLAUDECODE", None)` and pass `env=env` to `subprocess.run()`/`Popen()`.

All clear variants run in background (`run_in_background: true`) and are monitored via `TaskOutput`.

**Tip:** Use "clear codex" or "clear gemini" for code reviews to get perspectives from different models.

### Sub-Agent Permissions: Always Skip Permissions

Any Claude subprocess that runs without a human watching **must use `--dangerously-skip-permissions`** — never bare `claude`. Without it, the sub-agent stalls on every tool call waiting for permission approval that nobody will grant. This applies to: c11 panes running autonomously, Lattice review agents, any `claude -p` subprocess.

### Invoking Codex Reliably

**Codex ignores piped stdin.** Do not pipe content to Codex — it won't receive it. In a watched c11 pane, run `codex --yolo` interactively first and send text into the PTY after the TUI is ready.

**Use the file reference + interactive send approach:**
```bash
# 1. Write prompt/content to a file
cat <<'EOF' > /tmp/codex-prompt.md
[Your instructions here, can include complex markdown, backticks, etc.]
EOF

# 2. In the target pane, start the TUI with no prompt argument
codex --yolo

# 3. From the orchestrating pane, send the file-reference prompt after Codex is ready
c11 send --workspace "$WS" --surface "$SURF" "Read /tmp/codex-prompt.md and follow the instructions."
c11 send-key --workspace "$WS" --surface "$SURF" enter
```

**For simple prompts** (no special characters), send them into the live TUI:
```bash
c11 send --workspace "$WS" --surface "$SURF" "Review /path/to/file.ts for bugs."
c11 send-key --workspace "$WS" --surface "$SURF" enter
```

Do not use `codex exec` for watched workspace agents. It is a headless one-shot mode, not an interactive Codex session.

### Shell Escaping (Claude)

For Claude, prompts with backticks/quotes/markdown cause shell interpretation errors. Options:
- **File reference (recommended):** Write prompt to file, use `claude -p "Read /tmp/prompt.md and follow instructions"`
- **Piped stdin:** `cat file | claude -p -` (works for Claude, NOT Codex)

Avoid heredoc with `$(cat <<'EOF' ... EOF)` in nested bash contexts — can cause hangs.

## 'Loopy' Tasks (End-to-End Autonomy)

**"Loopy"** means: attempting the current item of discussion as close to a full loop yourself — implement, validate, iterate, report. A loopy task doesn't stop at "I've made the changes," it instead attempts to fully validate as best possible. However, there are times where human involvement is required: do not steamroll this. Not every task should be made loopy.

In practice being asked to make the task loopy tends to mean to use curl, iOS Simulator MCP, or Claude in Chrome to validate the work. It means to unblock and unleash the agent.

**Validation tools:** Use whatever makes sense — curl, test suites, log inspection, manual checks, etc. You're empowered to choose. That said, three tools are particularly powerful:
- **iOS Simulator MCP** (`mcp__ios-simulator-mcp__*`) — screenshots, tap flows, verify UI. Highly effective for iOS mobile validation.
- **Mobile MCP** (`mcp__mobile-mcp__*`) — Android emulator automation via UI Automator. Use for Android mobile validation. Note: Do NOT use for iOS — it misses interactive elements.
- **c11 embedded browser** (`c11 browser *` / `open <url>`) — **preferred for web validation when running inside c11.** Playwright-style automation integrated into the workspace. Use `open <url>` for quick previews (reuses the browser pane), `c11 browser snapshot/click/fill` for interaction. The `c11` skill's `references/browser.md` has the full API.
- **Claude-in-Chrome** (`mcp__claude-in-chrome__*`) — full Chrome browser automation. Use only when NOT in c11, or when Chrome-specific features are needed (extensions, saved sessions). **Do not reach for Chrome MCP inside c11** — the embedded browser is faster, lighter, and doesn't create stray windows.

**Stay in the loop.** If a fix doesn't work, try another approach. If tests fail, investigate why. Iterate until the task is actually complete or you hit a genuine blocker requiring human input.

## Token-Expensive Tools (Require Confirmation)

These tools consume significant tokens. **Always ask for user confirmation before using them:**

- **iOS Simulator MCP** (`mcp__ios-simulator-mcp__*`)
- **Claude-in-Chrome** (`mcp__claude-in-chrome__*`)

**Exception:** When the operator explicitly triggers a workflow that requires these tools (e.g., "loopy", "pro perspective", "deep pro perspective"), confirmation is implicit.

**Example prompt:** "I can validate this in the iOS Simulator / browser. Want me to proceed?"

## 'Dialogue' (Dialogue-Driven Development)

**"Dialogue"** means: pause execution and enter a collaborative conversation to fully understand requirements before building. The model is empowered to ask as many questions as necessary — ignore normal "be concise / move fast" defaults.

**Why this matters:** Models have broad knowledge of patterns, tradeoffs, edge cases, and potential pitfalls — but this intelligence is wasted if the model charges ahead with assumptions instead of surfacing what it knows. Dialogue-driven development creates space for the model's knowledge to combine with the operator's context and decision-making authority. The result is a shared understanding that produces more accurate, valid, and well-considered solutions than either could achieve alone.

**Core principle:** Use the `AskUserQuestion` tool liberally. Ask every question needed to eliminate ambiguity. There is no limit — 5 questions, 10 questions, 20 questions are all acceptable if that's what's needed to fully understand the task. Do not proceed with assumptions when you could ask instead.

**When to enter dialogue mode:**
- Operator explicitly invokes it ("dialogue", "let's discuss", "let's talk through this")
- Story or task lacks detail
- Model detects ambiguity or thinks the task deserves more discussion
- Scope is large enough that assumptions could lead to significant rework

**What good dialogue looks like:**
- Use `AskUserQuestion` repeatedly until you fully understand:
  - The operator's actual needs and goals
  - The existing system's constraints and patterns
  - The story's acceptance criteria (stated and implied)
- Surface tradeoffs, edge cases, and options the operator may not have considered
- Validate assumptions explicitly rather than guessing
- Ask follow-up questions when answers reveal new ambiguity

**How dialogue relates to planning:**
- **Dialogue** = "Do we fully understand what we're building?"
- **Planning** = "How do we build this thing we understand?"

Dialogue can come before planning, after planning (to refine), or standalone. It's a phase, not a replacement.

**Exit conditions:**
- Model feels confident it fully understands the requirements
- Operator signals completion ("good to go", "let's proceed", "do it")

**Anti-pattern this prevents:** Charging ahead with a plan based on incomplete information, forcing the model to guess at requirements and potentially building the wrong thing.

## 'Ralph' (Ralph Wiggum Loop)

**"Ralph"** means: launch an autonomous loop that keeps working until the task is complete. Loops until `RALPH_COMPLETE` or max iterations.

```bash
ralph "Fix all failing tests"
ralph --max 20 --file path/to/prompt.md
```

The full guide lives in a separate skill (`/ralph-guide`) that isn't bundled here; the trigger and minimal usage above are enough to get started.

## 'Full Force' and 'Triple Force' (Multi-Model Analysis)

**"Full force"** means: spawn parallel clear agents for diverse model perspectives, then merge into one document with Claude Opus. Use for reviews, plans, and analysis — **not for implementation**.

| Term | Models |
|------|--------|
| **full force** | Claude (Opus) + Codex |
| **triple force** | Claude (Opus) + Codex + Gemini |

Run agents in parallel with identical prompts. Each writes to `notes/`. After all complete, spawn Claude Opus to synthesize a merged document. If one agent fails, proceed with the others and note the gap.

**Example:** "Full force review of this PR" → Claude + Codex review in parallel → merged feedback document.

The trident commands shipped in this repo (`commands/trident-code-review.md`, `commands/trident-plan-review.md`) are the canonical, fully-developed version of this pattern — three providers, three lenses, nine reviewers, four synthesizers.

## Emphasis on Parallelizing Work

You can spawn up to 10 agents simultaneously. When a task is parallelizable, try divide it into up to 10 approximately equal **buckets of work** (by complexity/effort, not file count). Ensure that each bucket can be independent to avoid agents messing each other up: rather have too few buckets than too many.

**Proactively parallelize** when you recognize independent work. Don't ask — just split and go. Examples: fixing multiple failing tests, adding tests for components, updating independent files with similar changes, any batch operation on independent items.

**Example:** "Fix all 15 failing tests" → spawn 10 agents, each handling ~1-2 tests based on complexity

**When NOT to parallelize:** Tasks with shared mutable state, order-dependent operations, or where agents would edit the same files. When in doubt, fewer buckets is safer than too many.

## Keeping Claude & Codex Commands in Sync

Claude Code and Codex CLI use identical command formats, so commands are shared via **hardlinks**:

| Location | Purpose |
|----------|---------|
| `~/.claude/commands/*.md` | Claude Code slash commands |
| `~/.codex/prompts/*.md` | Codex CLI prompts (hardlinked) |

**How it works:** Both paths point to the same files on disk. Edit one, both update.

**Sync script** (run after adding new commands):
```bash
~/.claude/scripts/sync-commands-to-codex.sh
```

Creates hardlinks for any new `.md` files in `~/.claude/commands/`. Runs instantly.

**AGENTS.md symlinks:** Every directory with a `CLAUDE.md` must also have `AGENTS.md -> CLAUDE.md` (relative symlink). This ensures Codex reads the same instructions. When creating a new project or adding a `CLAUDE.md`, create the symlink: `ln -s CLAUDE.md AGENTS.md`.

## c11 (First-Class Environment)

[c11](https://github.com/Stage-11-Agentics/c11) is a native macOS terminal multiplexer built on Ghostty's renderer; primary workspace environment. **Assume you are running inside c11 unless detection says otherwise.**

**Load the `c11` skill whenever ANY of these is true:**
- `CMUX_SHELL_INTEGRATION=1` or any `CMUX_*` env var is set (you're running inside c11).
- The operator says "c11" or "cmux", or asks about panes, splits, workspaces, surfaces, tabs, or the embedded browser.
- The task touches terminal multiplexing, sub-agent orchestration in sibling panes, or multi-pane layout.

The skill owns splits, sends, sub-agent orchestration, the embedded browser, targeting rules, tab naming, pane resize, and sidebar reporting. **Do not improvise `c11` commands from memory** — the skill is kept current with binary quirks and gotchas the help output doesn't surface. Do not reach for Chrome MCP when you're in c11.

**Tab naming is mandatory and must happen first.** When running inside c11 (`CMUX_SHELL_INTEGRATION=1`), your very first batch of tool calls must include: `c11 rename-tab --surface "$CMUX_SURFACE_ID" "<role>"`. An unnamed tab gets Claude Code's auto-title from the first user message, which produces names like "✳ Read and follow implementation prompt instructions" — useless for navigation. For "read a file and follow instructions" type prompts: read the file in your first call, find your role, and rename the tab in that same batch before doing any other work. Key first, 2-4 words, under 25 chars.

## Git: Auto-Commit and Push Policy

**Auto-commit is ON by default.** When a discrete unit of work is complete (feature, fix, review pass, refactor), commit it immediately. Do not wait for the operator to say "commit." Group related changes into logical commits with good messages — the same quality as if asked explicitly.

**Get work to a finished state — push ahead when the path is clear.** The goal is to leave work landed, not pending approval. When you've completed a logical unit of work on a feature branch — committed, build green, tests where applicable — push it. Open the PR if one is warranted. Don't stop short at "changes are committed locally" when the obvious next step is `git push`.

**Still confirm when the path is genuinely ambiguous.** Force-pushing over shared history, pushing directly to `main`/`master` on a repo with collaborators, or pushing work that hasn't been validated — these warrant a check-in. The spirit is: act decisively on clear forward motion, pause when the action is hard to reverse or affects others' work.

## Environment Confirmation

Before running any command that affects data or server state, confirm the target environment:
- "This will run against [dev/prod/local]. Is that correct?"
- Never assume — explicit confirmation prevents costly mistakes

## AskUserQuestion Best Practices

When using `AskUserQuestion` for non-trivial decisions — anything beyond simple yes/no or cut-and-dried choices:

- **Include pros and cons** for each option. The operator benefits from seeing tradeoffs surfaced explicitly rather than having to reason through them independently.
- **State a recommendation** if the model has a clear preference. Lead with the recommended option (mark it with "(Recommended)") and explain *why* it's preferred. The operator can always override, but surfacing the model's informed opinion saves time and produces better outcomes.
- **Keep it proportional.** Simple factual questions ("Which file?", "TypeScript or Python?") don't need pros/cons — just present the options. Reserve the detailed treatment for architectural choices, tradeoff-heavy decisions, and anything where the "right" answer depends on context the operator holds.
- **Ask as many questions as needed.** You can ask up to 5 questions at a time and keep asking across multiple rounds — 10+ total questions is fine if the situation calls for it. Don't compress everything into one round if it sacrifices clarity.
- **Use rich descriptions.** For complex choices with multiple tradeoffs, several sentences per option is encouraged. Don't artificially compress option descriptions — give the operator enough context to make an informed decision. A well-explained option with 3-4 sentences beats a terse one-liner that leaves the operator guessing.

## Recognizing Thrashing

If you notice repeated failed attempts at the same bug (3+ tries), circular debugging, or operator frustration:

1. **Pause and acknowledge:** "This isn't going as intended. We may be thrashing."
2. **Generate a fresh handoff prompt** for a new agentic session containing:
   - **Actions taken:** What we tried and why it didn't work
   - **Current state:** Relevant file states, error messages, test output
   - **Observed behavior:** What's actually happening vs. expected
   - **Possible directions** (non-exhaustive): A few hypotheses to explore, framed openly — not as "the answer"

Fresh context without accumulated assumptions often breaks through stuck situations.

## Self-Improvement

If you notice a pattern, convention, or piece of knowledge that would help future sessions, suggest adding it to the relevant `CLAUDE.md` file. Every future session (yours and other agents) reads these files first. A 30-second addition here can save hours across the team of agents and humans who would otherwise rediscover the same thing.
