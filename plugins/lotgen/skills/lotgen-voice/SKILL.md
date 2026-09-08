---
name: lotgen-voice
description: >
  Anything spoken in LOTGEN — choosing a voice, putting narration over a silent clip,
  making a face say something, and voicing a whole explainer. Use when the user says
  "add a voiceover", "make her say this", "narrate it", "which voices do we have",
  "dub this", "lip sync", "read the script over the b-roll", or asks why a spoken shot
  was refused. Two of these passes are free ffmpeg and one is priced by the length of
  the AUDIO rather than the clip; knowing which is the whole skill.
---

# Voice

There are two ways to get a voice onto a picture in this product and they cost
very different amounts. **Most of the time the free one is the right one.**

---

## THE FREE PATH, AND WHY IT EXISTS

`narrate_take` lays a voice track over a rendered clip. It is an **ffmpeg mix,
not a render** — nothing is sent to a provider and nothing is billed.

That is what makes a narrated explainer or a voice-over b-roll shot possible
**without paying a native-audio model to speak**. The sequence:

1. Render the line as a **spoken shot**, on one of the workspace's own voices.
2. Render the picture **silent**, as an ordinary shot.
3. `narrate_take` to mix them. Free.

**Neither side is trimmed.** When the line outlasts the picture the last frame
is held; when the picture outlasts the line it runs to its end and the tail is
silent. The silent take survives, and the export carries both.

`mix_explainer` is the same idea across a whole lot: lay each finished voice
over its finished picture, free, and **idempotent** — re-running replaces
rather than accumulates, so call it again as more takes land. Beats still
missing a half are named rather than silently skipped, so read what comes back
instead of assuming a quiet run means a complete one.

---

## THE PAID PATH, AND WHAT IT IS FOR

`lipsync_take` is for when the **face has to move**. Narration over a shot of
a product does not need it. A person on camera saying the line does.

Two inputs: a face — a still, or a rendered clip from `list_assets` — and a
voice, which is an audio asset, usually a spoken shot rendered in the same lot.

**Two products share one endpoint and the tier decides which:**

| tier kind | what it does | what it leaves behind |
|--|--|--|
| still-to-talking | makes a **still** talk | a **new shot** beside the still — a clip cannot be a take of a photograph, and the still stays a still |
| clip re-voice | re-voices an existing **clip** in place | **another take** of the same shot — this is localisation: same shot, new words |

Those are not interchangeable and they do different things to the lot's
shape. Read which one the tier is before calling, and use `dry_run` — it says
the quote **and whether a shot is about to be added**.

**It is priced per OUTPUT second, and the output's length is the AUDIO's, not
the source's.** A long script over a short clip costs what the script costs.
This is the single most common pricing surprise here: people quote it against
the clip they can see. An audio asset with no measured duration cannot be
quoted at all.

---

## VOICES: CHECK THE WORKSPACE HAS ANY

`list_voices` groups by whose they are — the customer's own cloned voices,
ones added from the provider's library, and the ones that ship as standard.

**Read the source field before trusting the list.** If it says the workspace
is seeing the shipped list only, that workspace has **never synced** and the
customer's own voices are not in what you just read. Call `refresh_voices`,
then ask again. Choosing a voice from an unsynced list means telling a
customer they do not have the voice they cloned last week.

---

## AN EXPLAINER'S VOICE

`voice_explainer` renders **every beat's narration line in one call**, as
ordinary spoken renders — quoted, capped and provenanced like anything else.
Priced **per character**, so the script length is the bill and it can be known
exactly before spending.

It **skips beats with no line or no voice chosen, and names them.** That list
is the thing to read: a silent beat is usually a script gap rather than a
choice, and it is cheaper to notice before the mix than after.

Then `mix_explainer`, which is free. Voice first, mix second, and the mix can
be run again as later takes land.

---

## THE ORDER, FOR A TALKING SHOT

1. `list_voices` — and `refresh_voices` first if the workspace has never
   synced.
2. Write the line. A spoken shot's script is the thing being billed on the
   per-character passes, so it is worth tightening before rendering, not after.
3. Render the spoken shot with `render_scene`. This spends.
4. Render the picture, silent.
5. Free mix with `narrate_take`, **or** paid `lipsync_take` if the face has to
   move.
6. `get_job` to follow either. `select_variant` if the new take should be the
   shot's answer.

---

## WHAT REFUSES, AND WHY IT IS RIGHT TO

- **A capture style that records nobody speaking** refuses a talking-head
  script by name, before anything is created. Six of them do — three record
  nobody speaking and three have the operator's face behind the lens. That is
  a real conflict between what the camera IS and what the shot is doing, not a
  quirk to work around: pick a different capture style or a different shot.
- **An unmeasured audio asset** cannot be quoted for lipsync.
- **A beat with no line or no voice** is skipped and named by
  `voice_explainer`.

Read the refusal. Every one of them names the gap.

---

## CHECKLIST

1. Does the face need to move? If not, the free path is the answer.
2. Has this workspace ever synced its voices?
3. For lipsync: is the price quoted against the AUDIO's length, not the clip's?
4. For an explainer: did `voice_explainer` name any skipped beats?
5. Did you re-run `mix_explainer` after the last take landed?
