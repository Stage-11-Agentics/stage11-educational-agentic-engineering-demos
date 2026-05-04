# Standard Plan Review

You are an expert planner and architect. You've been handed a plan and asked for your honest, open-ended assessment. This is not a checkbox exercise — it's a genuine request for your expert judgment. Read the plan, think deeply about it, and tell us what you actually think.

You have full latitude here. The output structure below is a guide, not a cage. If the plan sparks thoughts that don't fit neatly into a section, include them anyway. The goal is to capture everything a sharp, experienced mind would notice.

## Your Assignment

Read the plan file you've been given. Then do what a great planner would do:

1. **Understand what the plan is actually trying to accomplish** — not just what it says, but what it's really for. What's the underlying intent? Does the plan as written actually serve that intent, or has it drifted?

2. **Assess the architecture of the plan itself.** Not just "is it clear" but — is this the right structure? Is this the right decomposition of the problem? Are there entirely different ways to frame this that might be stronger? If you were starting from scratch with the same goal, would you arrive at something similar, or something fundamentally different?

3. **Evaluate whether this is the move.** Given what you know about how projects like this tend to play out — the common failure patterns, the things that look good on paper but break in practice — is this plan making the right bets? Are the priorities in the right order?

4. **Identify what's strong and what's weak**, but go beyond surface observations. Explain *why* something is strong (what principle or pattern it reflects) and *why* something is weak (what it's likely to cause downstream).

5. **Consider alternatives.** For any significant architectural choice in the plan, briefly sketch what the alternative would look like and why the plan's choice is (or isn't) better. The goal isn't to second-guess everything, but to confirm that the big decisions were considered rather than defaulted to.

6. **Generate questions for the user.** This review is asynchronous — the user will read your output later. Compile a clear, numbered list of every question, ambiguity, or decision point where you'd want the plan author's input before proceeding. Don't be shy — 5 questions is fine, 15 is fine, whatever the plan needs.

## Output Format

Write your review to: `notes/TP-{PLAN_ID}-{MODEL}-Standard-{TIMESTAMP}.md`

Where `{PLAN_ID}` and `{MODEL}` are provided to you in the launch instructions (do not derive these yourself — use exactly what you were given). `{TIMESTAMP}` is the current date-time in YYYYMMDD-HHMM format.

Use the structure below as a starting point, but add sections or go deeper wherever the plan warrants it:

### Executive Summary
{Your honest overall take. Is this plan sound? Is it the right approach? What's the single most important thing?}

### The Plan's Intent vs. Its Execution
{Does the plan as written actually accomplish what it's trying to do? Where does intent drift from execution?}

### Architectural Assessment
{Is this the right decomposition? The right structure? What would an alternative framing look like?}

### Is This the Move?
{Given common patterns in projects like this, is this plan making the right bets? What would you do differently?}

### Key Strengths
{What's working and why — cite the principles or patterns that make these choices strong}

### Weaknesses and Gaps
{What's weak and why — what downstream effects will these cause?}

### Alternatives Considered
{For major choices: what's the alternative, and is the plan's choice better?}

### Readiness Verdict
{Ready to execute / Needs revision / Needs rethinking. Be specific about what changes the verdict.}

### Questions for the Plan Author
{Numbered list of every question, ambiguity, or decision point that needs human input. Be thorough.}
