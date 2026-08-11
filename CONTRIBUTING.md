# Contributing a skill

One pull request, one skill folder. That keeps review fast and lets us take
yours without waiting on anything else in the same branch.

## What we are looking for

A skill is worth adding when it carries something the model does not reliably
have on its own:

- **Specific knowledge.** The gotchas in an API that are not in its quickstart.
  The order a migration has to happen in. The three things that silently break.
- **Discipline.** A way of working the model knows but does not consistently
  apply, made explicit and checkable.
- **A stopping condition.** What "done" means for this kind of work, so it does
  not stop early or keep going.

The most valuable paragraph in most skills is the one saying what **not** to do.
"Never log the full card object" is worth more than three paragraphs on how
payments work.

## What we will send back

- **Generic advice.** If it reads like it could apply to any task, it will not
  change any answer. "Follow best practices", "write clean code", "consider edge
  cases" cost tokens on every request and buy nothing.
- **A model or provider named anywhere.** Model ids change under us and a skill
  that names one goes stale for everybody at once. Use `capability` and let
  OxCode route. A `model:` key is rejected by the parser, not ignored.
- **A restatement of the docs.** Link the docs. Put the things the docs get
  wrong or bury in the skill.
- **Anything that needs a secret.** Skills are prompts. If it only works with
  your API key, it is not a skill.

## Writing it

Start from [`template/SKILL.md`](template/SKILL.md).

```
skills/
  your-skill/
    SKILL.md
```

The folder name is the skill's id and should match `name` in the frontmatter.
Use lowercase with hyphens.

**Write the description as a trigger.** It is what the model reads to know your
skill exists, and it is the one line a person reads in the picker. Say what it
does and when to use it:

```yaml
description: >-
  Stripe integration: Checkout Sessions, webhooks, billing, and Connect. Use
  when adding payments, debugging a failed charge, or handling a webhook.
```

Keep it under about 200 characters. Long descriptions get truncated in the
model's skill list, so the front of yours has to carry the meaning.

**Write the body as instructions, not documentation.** Second person, concrete,
and specific enough that following it produces a different result from not
following it.

## Testing it before you open the PR

You do not need to install anything or wait for us. Copy your folder into your
own skills directory:

```bash
cp -r skills/your-skill ~/.oxcode/skills/
```

OxCode picks it up on save. Type `/your-skill` and give it a real task. If the
answer is no different from asking without the skill, the skill is not earning
its place yet.

Check the panel for notes when it loads. A file that will not parse, a command
that collides with an existing skill, or a `capability` outside the five values
is reported there rather than failing silently.

## Opening the pull request

Add your entry to `.oxcode-plugin/marketplace.json`:

```json
{
  "name": "your-skill",
  "description": "Same one line as the frontmatter",
  "category": "development",
  "author": "Your name or handle",
  "source": "./skills/your-skill"
}
```

In the description, tell us:

- What task you used it on, and what changed in the answer.
- What you deliberately left out, and why.

That second one is the part we read first. It tells us you drew a boundary.

## Review

We review for whether the skill changes an answer, and for whether its
instructions are specific enough to follow. Expect questions about anything
that reads as general advice. We would rather ask than merge something that
adds tokens to every request and nothing to any answer.
