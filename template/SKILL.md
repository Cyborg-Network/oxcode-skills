---
name: my-skill
description: >-
  What this skill does and WHEN to use it. Both halves matter: the model reads
  this to know the skill exists and what it is for.
# Everything below is optional. OxCode fills in a sensible default for each.
command: my-skill
label: My Skill
hint: One line shown under the name in the picker
category: development
order: 100
icon: sparkle
capability: Coding
workspace: required
tools: full
---

Everything below the frontmatter is the prompt. This is where the skill lives.

Write it as instructions to someone competent who has not done this particular
job before. Be specific. A skill that says "follow best practices" adds nothing;
a skill that says "always verify the webhook signature before trusting the
event, and never log the full card object" adds everything.

## What makes a skill worth installing

Knowledge the model does not reliably have, or discipline it does not reliably
apply. Concretely:

- The five gotchas in an API that are not in its quickstart.
- The order a multi-step process has to happen in, and what breaks otherwise.
- What "done" means for this kind of work, so it stops at the right place.
- What NOT to do, which is usually the most valuable part.

## What to leave out

Do not restate general good practice. Do not name a model, a provider, or a
version of anything that changes. Do not describe the tools, OxCode already
tells the model what it has.
