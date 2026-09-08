---
name: lotgen-explainer
description: >
  Build a narrated explainer in LOTGEN — how something works, why it matters, its history,
  two things compared, or a myth taken apart. Use when the user says "explain X", "make an
  explainer", "a video about how this works", "a 5-minute piece on", "debunk", "compare
  these two", or wants narration-first video rather than an ad. The planner does NOT
  research the topic and says so; writing the real lines is your job and this explains
  exactly where they go.
---

# Explainer

An explainer is **narration-first**, where every other planner in this product
is shot-first. The script is the spine; each beat's picture illustrates a
sentence. So every shot carries two things: what the picture is, and what is
said over it.

---

## THE ONE RULE THAT MATTERS MOST

**Nothing here researches the topic.** The scaffolds fill deterministically
from what you pass. No model writes them.

A scaffolded explainer, narrated as it comes out, says almost nothing. It is
a shape with placeholder sentences in it. That is deliberate — a tool that
invented facts about a topic and narrated them confidently would be worse than
one that says nothing at all.

**So the work is yours, and it goes in a specific place:**

1. Call `run_explainer_recipe` with `dry_run`.
2. **Read the beats back.**
3. Research the topic yourself.
4. Write the real lines.
5. Call it again for real, passing your lines as `narration` **by shot index**.

**Planning is deterministic.** The same inputs give the same shot list, which
is the whole reason step 5 works: the indexes you saw in the dry run are the
indexes your lines land on. Change the inputs between the two calls and that
guarantee is gone.

---

## THE SHAPE

`list_explainer_structures` carries everything the planner accepts: five
narrative structures and eight 2D visual styles.

**A structure is a rhetorical shape, not a fixed count.** It grows with the
target runtime by repeating the beat its argument expands by — more mechanism
stages, more eras, more points of divergence — while the hook and the takeaway
stay put. Each row says which beat it grows by.

So **a longer target duration grows the beat COUNT, not the beat length.** A
ten-minute piece is around forty shots, and **the middle beats are where your
research goes**. If you have nothing to put in forty beats, ask for a shorter
piece rather than stretching five ideas across it.

**The styles are deliberately 2D** — animated, illustrated, doodle,
papercraft, whiteboard, isometric, blueprint, chalkboard — and not the
live-action vocabulary the ad side uses. That is a decision, not an omission:
an explainer that looks like footage invites the viewer to believe it *is*
footage, and for a diagram that is a lie.

---

## THE TWO DURATIONS THAT ARE NOT THE SAME

This confuses everybody once.

| | |
|--|--|
| the pictures' runtime | what the planner prices and plans |
| the finished length | what the narration decides |

The voice is mixed on **afterwards**. A line longer than its picture **holds
the last frame**, so the narration is what actually sets the running time.
Quote the pictures, but never tell a user the finished piece is that long.

The price covers **pictures only**. Voicing is billed per character when
rendered; mixing is free.

---

## THE CALLS, IN ORDER

`run_explainer_recipe` is the way to build one. It plans the beats, creates
the lot with each line carried on its shot, and prices the pictures — in a
single call. **It does not render, voice or mix.**

`plan_explainer` and `create_explainer_lot` are the same job split in two.
Reach for them only when you need to do something between planning and
building; otherwise the one call is the right one, and it takes `narration` on
that same call.

After the lot exists:

1. `render_project` — the pictures. **This spends.**
2. `voice_explainer` — every beat's line, per character.
3. `mix_explainer` — free, and idempotent, so run it again as more takes land.

**The shots are SILENT on purpose.** Asking a video model to speak every beat
would cost more than the pictures and hand back a voice nobody chose. The
separation is what makes the voice yours.

---

## BEFORE YOU RENDER

- `list_voices` first, and `refresh_voices` if the workspace has never synced —
  otherwise you are choosing from a list that does not include the customer's
  own voices.
- `estimate_render` prices the pictures without spending.
- Read what `voice_explainer` names as skipped. A beat with no line or no voice
  chosen is passed over and reported; that is nearly always a gap in the script
  rather than an intention.
- Fix a wrong line with `update_scene` before rendering, not after. Text is
  free to change; a rendered picture is not.

---

## CHECKLIST

1. Did you dry-run first and actually read the beats?
2. Did you research the topic and write real lines — or are you about to ship
   scaffolding?
3. Do your `narration` indexes match the beats you read?
4. Did the inputs stay identical between the dry run and the real call?
5. Have you told the user the quote covers pictures only?
6. Voices synced, and no beat silently skipped?
7. `mix_explainer` re-run after the last take landed?
