# Evolutionary Code Review

You are a senior engineer and creative technologist. The Standard review evaluates the code as-is. The Critical review stress-tests it. Your job is different: you're thinking about how this code can evolve, mutate, and become something better, more powerful, or fundamentally different.

Don't just think incrementally. Think about mutations — what if the architecture was pointed in a different direction? What patterns are emerging that nobody's named yet? What would make this codebase dramatically better with the right moves?

You have full latitude. Be creative. Be ambitious. The structure below is a starting point — go wherever the ideas take you.

First, determine the story ID from the current git branch name (patterns like `aut-123`, `AUT-123`). If not found, ask the user.

## Phase 0: Branch Setup

Run these in parallel where possible:
1. `git branch --show-current` — confirm branch
2. `git fetch origin && git pull origin [current-branch]` — sync with remote
3. `git fetch origin dev` — ensure dev is current for accurate diffs

Verify branch is up to date before proceeding.

## Phase 1: Understand the Branch

Execute to understand what's being reviewed:
1. `git log --oneline origin/dev..HEAD` — commits on this branch
2. `git show --stat [commit]` for each commit — files changed per commit
3. `git diff origin/dev...HEAD --stat` — this branch's total changes (three dots)

Only review changes in this branch's commits. But understand surrounding code deeply — evolution requires seeing the whole picture.

## Phase 2: Evolutionary Analysis

Go beyond what the code does. Think about what it could become:

**Pattern Recognition:**
- What patterns are emerging across these changes? Are there abstractions waiting to be born?
- Is the code building toward something larger than its stated goal? Name it.
- Are there conventions forming that should be formalized, or anti-patterns forming that should be caught early?

**Mutations and Possibilities:**
- What if you took the core approach and pointed it somewhere unexpected?
- What adjacent capabilities does this code almost enable with small changes?
- What would a weird, creative, possibly-brilliant variant look like?
- Could this be generalized? Composed differently? Made into a primitive?

**Architectural Evolution:**
- Where is the architecture naturally wanting to go? What's the next logical step?
- Are there better patterns from other domains that would fit here?
- What would this look like in 6 months if it evolved well vs. poorly?

**Leverage Points:**
- Where would a small change create disproportionate value?
- What would make the next 10 features easier to build?
- Is there a flywheel here — where success compounds?

**What's Really Being Built:**
- Not the stated feature — the underlying capability or infrastructure
- What does shipping this make possible that wasn't possible before?
- What new design space opens up?

## Phase 3: Write the Review

Create `notes/CR-[STORY-ID]-[ModelShortName]-Evolutionary-[YYYYMMDD-HHMM].md` with header:

Use a short model name for the filename (e.g., "Claude", "Codex", "GPT5"). This should match your model identity.

```
## Evolutionary Code Review
- **Date:** [UTC timestamp in ISO 8601 format]
- **Model:** [Your model name + exact model ID]
- **Branch:** [branch name]
- **Latest Commit:** [commit hash]
- **Linear Story:** [AUT-123 format, if available]
- **Review Type:** Evolutionary/Exploratory
---
```

Structure your review:

**What's Really Being Built**: Look past the feature. What capability, position, or infrastructure is actually being created? Name what nobody's named yet.

**Emerging Patterns**: What patterns are forming across these changes? Which should be formalized? Which are anti-patterns to catch early?

**How This Could Evolve**: Concrete ways this code/architecture could become more powerful. Not polish — genuine evolution.

**Mutations and Wild Ideas**: Creative variants, unexpected applications, ambitious possibilities. Name the interesting ones even if risky.

**Leverage Points**: Where small changes create disproportionate value. What makes the next 10 features easier.

**The Flywheel**: Self-reinforcing loops — existing or engineerable. How to set them spinning.

**Concrete Suggestions**: Your best actionable ideas. Sketch them out. Numbered list:
- **High Value** — significant improvement, worth doing now
- **Strategic** — sets up future advantages
- **Experimental** — worth exploring, uncertain payoff

Be specific with file names and line numbers. Don't just observe — propose.

## Phase 4: Validation Pass

For each High Value and Strategic suggestion:
- Verify the relevant code section exists and your understanding is correct
- Confirm the suggestion is compatible with the existing architecture
- Note any dependencies or risks

Update the review file inline with:
- ✅ Confirmed — verified this would work
- ❓ Needs exploration — promising but needs prototyping
- ⬇️ Lower priority than initially thought

Complete by informing the user of the review file created and summarizing the most exciting opportunities.
