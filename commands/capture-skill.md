---
name: capture-skill
description: Capture learnings, patterns, or workflows from the current conversation into CLAUDE.md or a skill. Use when the user wants to save what was learned, discovered, or built during a conversation for future sessions.
---

# Capture Learnings from Conversation

Extract knowledge, patterns, and workflows from the current conversation and persist them for future sessions.

## When to Use

- User says "capture this" or "save this for next time"
- A useful workflow, pattern, or piece of domain knowledge emerged
- The conversation uncovered non-obvious steps, gotchas, or best practices
- User wants to update an existing skill with new learnings

## Phase 1: Identify What to Capture

Review the conversation for:

1. **Workflows**: Multi-step processes figured out through trial and error
2. **Domain knowledge**: Non-obvious facts, configurations, or constraints
3. **Gotchas and fixes**: Problems encountered and their solutions
4. **Patterns**: Code patterns, command sequences, or templates that worked
5. **Decision rationale**: Why certain approaches were chosen

**Critical:** If the conversation didn't surface anything clearly worth preserving, say so and stop. Not every conversation produces reusable knowledge. "Nothing worth capturing here" is a valid outcome.

Summarize what you plan to capture and confirm with the user before proceeding.

## Phase 2: Decide Destination

**Default: Repo CLAUDE.md** — Most learnings belong in the project's CLAUDE.md where future sessions will automatically see them.

**Alternatives:**
- **Update existing skill** — If the learning directly relates to an existing command in `~/.claude/commands/` or `.claude/commands/`
- **Suggest new skill** — If the learning is substantial, reusable across projects, and warrants its own invocable command

If the destination isn't obvious, ask the user. Don't overthink it—CLAUDE.md is almost always right.

## Phase 3: Draft the Content

### Distillation Guidelines

The goal is to transform a messy conversation into clean, reusable instructions.

**Do:**
- Extract the final working approach, not failed attempts (unless gotchas are instructive)
- Generalize from the specific case (replace hardcoded values with placeholders)
- Include the "why" behind non-obvious steps
- Add context the agent wouldn't know without this conversation
- Keep it concise

**Don't:**
- Include conversation artifacts ("as we discussed", "you mentioned")
- Repeat information the agent already knows
- Include overly specific details that won't transfer
- Add verbose explanations where a code example suffices

### For CLAUDE.md Updates

- Find the appropriate section or create one
- Match the existing style and structure
- Keep entries concise—a few lines is often enough

### For Skill Updates/Creation

When updating an existing skill:
1. Read the existing file
2. Integrate new learnings without duplicating content
3. Preserve existing structure and voice

When creating a new skill:
1. Choose a descriptive name (lowercase, hyphens)
2. Write a specific description including WHAT and WHEN
3. Include concrete examples from the conversation
4. Place in `~/.claude/commands/` (personal) or `.claude/commands/` (project)

## Phase 4: Write and Verify

1. Create/update the file
2. Show the user what was added
3. Confirm the captured content is accurate

## Example: Capturing a Debugging Discovery

If a conversation involved debugging a tricky iOS simulator issue, the CLAUDE.md addition might be:

```markdown
## iOS Simulator Gotchas

- **Stale builds**: If UI changes don't appear, run `xcrun simctl shutdown all && xcrun simctl erase all` before rebuilding
- **Deep links**: Test with `xcrun simctl openurl booted "myapp://path"` not Safari
```

Note how this captures the solution without conversation artifacts—just the actionable knowledge.

## Edge Cases

**Conversation had multiple unrelated topics**: Ask which to capture, or suggest separate entries.

**Learning is too small**: A single line in CLAUDE.md is fine. Not everything needs to be a skill.

**Existing skill needs major rewrite**: Confirm whether to restructure or create a new one that supersedes it.
