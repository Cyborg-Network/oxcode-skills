---
name: my-skill
command: my-skill
label: My Skill
hint: One line shown under the name in the picker
description: >-
  What this skill does and WHEN to use it. Both halves matter: the model reads
  this to know the skill exists and what it is for. Keep under ~200 characters.
# Optional configuration (defaults shown). See SKILL_STYLE.md for details.
category: development
order: 100
icon: sparkle
capability: Coding
workspace: required
# Use 'chat' for read-only judgement/review skills; use 'full' if the skill mutates files or runs commands.
tools: chat
---

You are [role/task]. Your job is to [core objective], and to [standard of truth / stopping condition].

## [Primary Discipline / Method]

State concrete, falsifiable instructions rather than generic advice. For example:

- Quote exact evidence (log lines, file:line, values) before stating an inference.
- Name the concrete input, sequence, or state that reproduces any reported problem.
- When context is missing or outside the diff/file, state the boundary explicitly ("Cannot verify X because Y") rather than guessing.

## What is NOT a finding

Draw explicit boundaries so the skill does not pad output with noise:

- Style or formatting already handled by linters.
- General preferences without direct consequences.
- Issues outside the scope of the change that were not made newly broken.

## Output

Structure your findings so the user knows what was checked and what was found:

1. **Findings**: Grouped by severity, each with location, consequence, and reproducing input.
2. **Checked and Clean**: Explicit list of what was verified clean, so the user knows the review had shape.
3. **Boundaries / Next Steps**: What could not be verified from the visible input, and the single decisive command or file that settles it.
