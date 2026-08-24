# OxCode Skill Style Guide

What makes an OxCode skill different from everyone else's. Read this before
writing a skill or opening a pull request.

---

## The Point of View

Most skill marketplaces ship generic best-practice documents: long lists of
guidelines, summarized documentation, and advice to "write clean code" or "be
thorough." They add tokens to every request and change no answers.

The single biggest complaint developers have about AI tooling is output that is
**almost right**. A model's default instinct is to sound confident, guess past
information gaps, and list plausible-sounding issues to look thorough.

An OxCode skill is defined by the opposite instinct: **refusing to claim what it
did not check**.

> "Your job is to find defects that would reach a user, and to be trusted when
> you say there are none."
>
> "Cannot verify from this diff is a useful review line. A confident wrong
> finding costs more than a missing one."

A skill earns its place by changing the answer on a real task. It does this not
by lecturing the model, but by imposing a **discipline**—a set of checkable
constraints that prevent the confident wrong conclusion.

---

## The Six Rules

Every OxCode skill enforces these six rules. When writing your skill body, use
these before-and-after patterns as your standard.

### 1. Say what you could not verify

"I could not check X because Y" is a finding, not a failure. A skill that never
says this is a skill that guesses. When context is missing from a diff, log, or
file, state the boundary explicitly instead of reasoning past it.

- ❌ **Before (guesses past missing context):**
  > "Ensure all caller functions across the codebase handle null values returned
  > by this updated method."

- ✅ **After (states the boundary as a finding):**
  > "When a change touches something you cannot see in the diff, say so
  > explicitly rather than guessing. 'Cannot verify caller handling from this
  > diff because \`user_service.py\` is outside the changeset' is a useful review
  > line." *(from `code-review`)*

### 2. No claim without evidence

If a skill tells the agent to assert something (a version exists, a test
passes, an invariant holds, a root cause is found), it must also specify what
would prove it.

- ❌ **Before (asserts without proof):**
  > "Verify that test coverage is adequate and all existing unit tests pass."

- ✅ **After (specifies the test of truth):**
  > "Passing is the null result. Never claim a test is good simply because it is
  > green; a passing test proves only that the code satisfies the assertion, not
  > that either is correct. Every assertion must be tested by deliberate mutation:
  > name the change, invert the conditional, run the suite, and report the
  > failure by name." *(from `test-hardening`)*

### 3. One grounded finding beats five plausible ones

The default behavior of a model is to pad its response with speculative
critiques, stylistic nits, and hypothetical edge cases to appear thorough. Prohibit
this explicitly.

- ❌ **Before (invites padding):**
  > "List all possible bugs, style improvements, performance optimizations, and
  > edge cases you can identify in the code."

- ✅ **After (demands grounded selectivity):**
  > "Do not pad a review to look thorough. Three real findings beat ten where
  > seven are style. If the change is good, say it is good and say what you
  > checked, so the author knows the review had a shape." *(from `code-review`)*

### 4. Name the input that reproduces it

Every reported defect or failure must specify the concrete input, sequence, or
state that triggers it. If the agent cannot construct the reproducing case, it
has a suspicion, not a finding.

- ❌ **Before (abstract suspicion):**
  > "This calculation might fail or throw an exception if given invalid or
  > empty input."

- ✅ **After (concrete reproducing input):**
  > "State the concrete input, sequence, or state that produces the wrong result.
  > Not 'this could fail with bad input' but 'called with an empty array, this
  > returns undefined and the caller dereferences it'. If you cannot construct
  > that input, you have a suspicion rather than a finding. Say which it is."
  > *(from `code-review`)*

### 5. Never make the skill a reason to do less

A skill shapes *how* work is done; it must never become an excuse to refuse work,
punt decisions back to the user, or ask unnecessary permission. When information
is incomplete, evaluate the visible scope and name the decisive next step.

- ❌ **Before (stalls or asks permission):**
  > "If log output is incomplete or ambiguous, ask the user if they would like
  > you to investigate further or provide more logs."

- ✅ **After (decisive progress within boundaries):**
  > "When the log cannot settle which hypothesis is correct, do not suggest
  > open-ended troubleshooting steps. Name the single most decisive next action:
  > a specific file path to read, a diagnostic command to run, or a specific log
  > level to raise." *(from `triage`)*

### 6. Write to the person, not the machine

No "As an AI assistant...", no conversational filler, no hedging, and no
apologising for being uncertain. State findings, evidence, and uncertainties
plainly and move directly to the work.

- ❌ **Before (hedging and AI boilerplate):**
  > "I apologize, but as an AI I cannot be entirely certain. However, it seems
  > that there might possibly be an issue with the database connection pool."

- ✅ **After (direct, plain statement):**
  > "1,500 occurrences of ECONNREFUSED 127.0.0.1:5432. The log buffer was
  > truncated at the start, so whether the database crashed or never started
  > cannot be determined from this log. Check \`pg_isready\` to settle it."
  > *(from `triage`)*

---

## Discipline vs. Advice

When drafting a skill body, distinguish between **advice** and **discipline**:

| Advice (Rejected) | Discipline (Accepted) |
|---|---|
| "Check system health and analyze logs carefully." | "Quote the exact log line verbatim before making any inference." |
| "Write comprehensive test assertions." | "Name the mutation hypothesis: *I will change X to Y in file:line, and expect test Z to fail.*" |
| "Look for UI defects." | "Check body text contrast against its actual background in both light and dark themes." |
| "Consider potential race conditions." | "Two concurrent errors are a lead, not a cause; evaluate whether an error is the initiator or a downstream casualty." |

**The test of a discipline:** Can the model (or a reviewer) look at the output
and objectively verify whether the rule was followed? If compliance cannot be
checked, the rule is advice. Delete it or make it concrete.

---

## Frontmatter Reference

Every skill is a directory containing a `SKILL.md` file with YAML frontmatter
enclosed between `---` fences.

```yaml
---
name: code-review
command: review
label: Code Review
hint: Review a change for defects that would reach a user
description: >-
  Review a diff or a set of changes for defects that would reach a user. Use
  when asked to review code, check a pull request, or look over a change before
  it ships. Reports each finding with the input that reproduces it.
category: development
order: 10
icon: eye
capability: Reasoning
workspace: required
tools: chat
---
```

### Fields

| Field | Required | Default | What it controls |
|---|---|---|---|
| `name` | **Yes** | — | Skill identifier. Lowercase with hyphens (kebab-case). Must match directory name. |
| `description` | **Yes** | — | Trigger text evaluated by OxCode to route tasks, and shown in search. Keep under ~200 characters. |
| `command` | No | `name` | Slash command trigger (e.g. `command: review` -> `/review`). |
| `label` | No | Title-cased `name` | Display name in pickers and menus. |
| `hint` | No | `description` | One-line subtitle displayed below the label in pickers. |
| `category` | No | none | Grouping in the marketplace (e.g. `development`, `design`). |
| `order` | No | `100` | Position in picker lists. Lower numbers sort first. |
| `icon` | No | `sparkle` | UI icon (e.g. `eye`, `shield`, `list-ordered`, `sparkle`). |
| `capability` | No | `Coding` | Cognitive routing mode: `Coding`, `Teaching`, `Deep analysis`, `Reasoning`, `Auto`. |
| `workspace` | No | `required` | Set to `optional` if the skill can function with no folder or workspace open. |
| `tools` | No | inherit | Set to `chat` for read-only skills. Omit or set to `full` if the skill mutates files or runs shell commands. |

> **Never name a model or provider.** OxCode routes dynamically based on `capability`.
> Setting `model:` or mentioning specific model IDs (`claude-*`, `gpt-*`, `gemini`, `deepseek`)
> is an error that CI will reject.

### Description vs. Body

- **`description` is the trigger:** It decides *whether* the skill gets selected.
  It must contain two things: **what** the skill does and **when** to invoke it.
  Keep it concise (under 200 characters); long descriptions get truncated in model
  indexes.
- **The body is the method:** Once activated, the body instructs the model on
  *how* to conduct the work, what disciplines to apply, and what structure to
  output.

---

## Choosing `tools` and `workspace`

### Match `tools` to what the method actually does

The `tools` setting controls the capabilities available to the model during the
skill execution:

- **`tools: chat` (Read-only):** The model cannot edit files or execute terminal
  commands. It reads diffs, open files, or prompt text and reasons over them.
  **Judgement and review skills must use `tools: chat` by default.**
- **`tools: full` (Active):** The model can create files, edit files, and run
  shell commands.

#### The Hallucination Trap: Why matching matters

A skill's `tools` value **must** match what its own method requires.

Consider `test-hardening`: its core loop is *mutate the code on disk, run the
test suite via terminal, observe the failure, restore the code*. If configured
with `tools: chat`, the model has no tools to edit files or run tests. It will
proceed to imagine the mutation, imagine the test suite output, and fabricate a
convincing report of "tests verified with deliberate mutations."

That is the worst failure a skill can have: **output that looks exactly like
success while doing zero actual work.**

- If the skill's instructions tell the agent to run commands, edit files, or
  mutate code: use `tools: full`.
- If the skill's instructions evaluate existing diffs, review UI designs, or
  triage logs: use `tools: chat`.

### Choosing `workspace`

- **`workspace: required` (Default):** The skill operates on a codebase and
  requires an open workspace folder (e.g. `code-review`, `ui-review`, `test-hardening`).
- **`workspace: optional`:** The skill operates purely on text, logs, or snippets
  provided directly in chat without needing an open repository (e.g. `triage`).

---

## How to Test Before Opening a PR

A skill earns its place by **changing the answer**. If a task produces the same
output with and without your skill, the skill is not ready.

Before opening a pull request:

1. **Test locally:**
   Copy your skill into your local skills directory:
   ```bash
   cp -r plugins/your-plugin/skills/your-skill ~/.oxcode/skills/
   ```
2. **Run a before-and-after comparison on a real task:**
   - Run the task in OxCode *without* the skill active. Note where the default
     output hedges, guesses, pads with style nits, or makes claims without evidence.
   - Run the task *with* `/your-skill`. Verify that the output enforces the
     discipline, states boundaries, cites evidence, and eliminates false claims.
3. **Document the difference in your PR description:**
   Every skill PR must state:
   - What real task you tested it on.
   - What the answer looked like before vs. after.
   - What you deliberately left out of the skill, and why.
