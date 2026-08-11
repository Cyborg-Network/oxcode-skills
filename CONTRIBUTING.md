# Contributing a skill

One pull request, one plugin. That keeps review fast and lets us take yours
without waiting on anything else in the same branch.

## What review means here

Read this part first, because it is what makes this repository different from a
docs repository.

A skill is a system prompt that runs on someone else's machine with file and
command tools. So **a pull request here is a security review, not a docs
review**, and these are what a reviewer checks:

- **The body instructs rather than describes.** A skill that restates the docs
  changes no answer and costs tokens on every request.
- **Nothing needs a secret.** If it only works with your API key, it is not a
  skill.
- **No model or provider is named anywhere.** Model ids change under us and a
  skill naming one goes stale for everybody at once. Use `capability` and let
  OxCode route. CI rejects this, so you will see it before we do.
- **The skill does not try to widen its own tools.** `tools` is a request that
  gets clamped to whatever the session already allows. It is never a grant.
- **No `oxcode:final-override` block.** An installed skill never receives that
  slot, so writing one ships something that silently does nothing. CI rejects it.

Expect questions. We would rather ask than merge something that adds tokens to
every request and nothing to any answer.

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
- **A restatement of the docs.** Link the docs. Put the things the docs get
  wrong or bury in the skill.
- Anything failing the review checks above.

## The layout

The installable unit is a **plugin**. A plugin is a directory that may carry
several skills, which is why the ML pipeline ships as one plugin with seven.

```
plugins/
  your-plugin/
    .oxcode-plugin/
      plugin.json
    skills/
      your-skill/
        SKILL.md
```

Start from [`template/SKILL.md`](template/SKILL.md).

The skill's directory name is its id and should match `name` in the frontmatter.
Lowercase with hyphens. A plugin carrying one skill usually gives them the same
name.

`plugin.json`:

```json
{
  "name": "your-plugin",
  "version": "1.0.0",
  "description": "Same one line as the marketplace entry.",
  "author": { "name": "Your name or handle" },
  "license": "MIT",
  "requires": { "oxcode": ">=0.4.0" }
}
```

`requires.oxcode` is the lowest version your plugin works on. Leave it at
`>=0.4.0` unless you use something newer, and raise it if you do: a user on an
older extension is then told what they need instead of installing something that
half works.

## Writing it

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

You do not need to install anything or wait for us. Copy the skill into your own
skills directory:

```bash
cp -r plugins/your-plugin/skills/your-skill ~/.oxcode/skills/
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
  "name": "your-plugin",
  "description": "Same one line as the manifest",
  "category": "development",
  "author": "Your name or handle",
  "source": "./plugins/your-plugin"
}
```

CI checks that every plugin on disk is listed and every listing exists, so a
missing entry fails before a human looks at it.

In the description, tell us:

- What task you used it on, and what changed in the answer.
- What you deliberately left out, and why.

That second one is the part we read first. It tells us you drew a boundary.
