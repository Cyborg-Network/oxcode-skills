# OxCode Skills

Skills for [OxCode](https://oxcode.oxlo.ai), maintained by Oxlo.ai and open to
contributions.

A skill teaches OxCode how to do one kind of work well. It is a folder with a
`SKILL.md` file: some frontmatter, then a prompt. No code, no build step.

## Installing

In OxCode, open the command palette and choose **Manage Skills**, then paste
this repository's URL:

```
https://github.com/Cyborg-Network/oxcode-skills
```

Every skill in it appears under **Available**. Install one and it gets a slash
command:

```
/review     look over this change before I open the PR
/ui-review  check the settings screen
```

You can paste any repository, not only this one. OxCode reads three shapes:

| The repo has | What happens |
|---|---|
| `.oxcode-plugin/marketplace.json` | Read as a curated list |
| `.claude-plugin/marketplace.json` | Read as-is, so skills written for Claude Code work |
| No manifest | Every `SKILL.md` in the tree is offered |

## Writing your own, without publishing anything

Drop a folder into `~/.oxcode/skills/` and OxCode picks it up when you save:

```
~/.oxcode/skills/
  my-skill/
    SKILL.md
```

That is the whole loop. No install, no manifest, no restart. Start from
[`template/SKILL.md`](template/SKILL.md) and read [`SKILL_STYLE.md`](SKILL_STYLE.md).

## What is in here

| Skill | What it is for |
|---|---|
| [`code-review`](plugins/code-review) | Review a change for defects that would reach a user, with a reproducing input for each |
| [`ui-review`](plugins/ui-review) | Review an interface for contrast, focus, overflow, and the states nobody designs |
| [`triage`](plugins/triage) | Turn logs into ranked hypotheses with distinguishing tests without guessing |
| [`test-hardening`](plugins/test-hardening) | Prove a test can fail before trusting it by mutating code and watching for named failures |

## The format

Two keys are required. Everything else has a default. See [SKILL_STYLE.md](SKILL_STYLE.md) for full style conventions.

```yaml
---
name: stripe
description: >-
  Stripe integration: Checkout Sessions, webhooks, billing. Use when adding
  or debugging payments.
---

You are integrating Stripe.
Always verify the webhook signature before trusting an event.
Never log the full card object.
```

The optional keys:

| Key | Default | What it does |
|---|---|---|
| `command` | the name | What the user types after `/` |
| `label` | the name, title-cased | Shown in the picker |
| `hint` | the description | One line under the label |
| `category` | none | Groups it in the browser |
| `order` | 100 | Position in the picker, low first |
| `icon` | `sparkle` | |
| `capability` | `Coding` | `Coding`, `Teaching`, `Deep analysis`, `Reasoning`, `Auto` |
| `workspace` | `required` | `optional` if it works with no folder open |
| `tools` | inherit | `chat` to make a skill read-only. Omit to keep the session's tools |

A skill **must not name a model**. OxCode picks one from `capability`, so your
skill keeps working when the fleet changes. Writing `model:` is an error, not a
silent no-op.

`capability` says what kind of thinking the work needs, not what the skill does.
What it does belongs in `description` and in the body.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) and [SKILL_STYLE.md](SKILL_STYLE.md).
One pull request, one plugin.

## Licence

MIT. See [LICENSE](LICENSE).
