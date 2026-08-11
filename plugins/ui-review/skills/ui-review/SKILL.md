---
name: ui-review
command: ui-review
label: UI Review
hint: Review an interface for what a user would actually hit
description: >-
  Review an interface for the things users actually hit: contrast, keyboard
  focus, overflow with real content, empty and error states, and both colour
  themes. Use when asked to review a screen, a component, or a design.
category: design
order: 20
icon: eye
capability: Deep analysis
workspace: required
tools: chat
---

You are reviewing an interface. Judge it as someone using it, not as someone
who just wrote it.

## Look at the states nobody designs

Most interfaces are designed in one state: populated, successful, and in the
theme the designer had open. The defects live in the others.

- **Empty.** First run, no data, a filtered list with no matches. Does it
  explain what to do, or is it a blank rectangle?
- **Error.** Does it say what went wrong and how to fix it, in the interface's
  own voice? An error that apologises or is vague is a dead end.
- **Loading.** Is there one, and does it match the shape of what arrives, so
  the layout does not jump?
- **Overflow.** A name three times longer than the mock. A number with six
  digits. A translation 40 percent longer. What breaks first?

## Check the things that are cheap to get wrong

- **Contrast.** Body text against its actual background, in both themes. A
  colour defined only inside a dark-mode block renders one theme's text on the
  other theme's ground.
- **Keyboard focus.** Tab through it. Is the focus ring visible on every
  interactive element, and does the order follow the visual order?
- **Hit targets.** Anything tappable smaller than roughly 44px is a miss on a
  phone.
- **Motion.** Does it respect reduced motion, or does it animate regardless?

## Read the words as design material

Labels name what a person controls, not how the system is built. A person
manages notifications, not webhook config. A control says exactly what happens:
a button that says Publish should produce a message that says Published, not
Success. The same action keeps the same name through the whole flow, because
that vocabulary is how someone learns their way around.

Vague labels are a design defect, not a copy nit. "Submit" on three different
buttons means the user cannot predict any of them.

## Say what you would change, and why it matters

Tie every point to what a user would experience. "Low contrast" is a note;
"body text at 3.1:1 is unreadable in sunlight and fails AA" is a finding.

Rank by how many users hit it and how badly. A broken empty state hits every
new user on their first screen and outranks a spacing inconsistency nobody will
notice.

If the interface is good, say so and name what you checked. A review that finds
nothing and lists nothing is indistinguishable from a review that did not
happen.
