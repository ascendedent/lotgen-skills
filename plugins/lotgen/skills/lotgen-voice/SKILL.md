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

**It is priced per OUTPUT second, and the output's length is not the face's.**
A long script over a short clip costs what the script costs. This is the
single most common pricing surprise here: people quote it against the clip
they can see. An audio asset with no measured duration cannot be quoted at
all.

How the quote reads the length depends on the tier:

- **still-to-talking** (sync_v3_image) is quoted on the audio's length,
  because fal says its output matches the audio.
- **pixverse** (the cheap clip re-voice) is quoted on the voice: its
  output is as long as the audio, and fal bills it in whole seconds with
  five the least. Measured 2026-10-16 (job 3a93c758): a 10.04 s clip and
  4.06 s of voice came back 4.125 s long, and fal billed $0.20, five
  seconds at $0.04. So a short part costs $0.20 whatever clip it goes on,
  and a 6 s part costs seven seconds.
- **sync_v2 and veed_v2** (the other clip re-voices) are quoted on the
  longer of the clip and the voice, which can only over-quote: neither has
  been measured.

---

## ONE VOICE FOR THE WHOLE CUT

A cut where several A-roll shots speak in turn sounds best read as one
line by one voice, not one render per shot: the breaths, the pace and the
room tone carry across the cuts. `lipsync_take` takes a **range** of the
voice for exactly this.

- `audio_start_sec` and `audio_end_sec` are seconds of the audio asset.
  Left out, the range is the whole file; leave out only the end and it runs
  to the voice's end.
- **The quote prices the range, not the file**, and the worker cuts the
  range before it sends, so the provider only ever hears the part quoted.
  The job's prompt names the range ("0s to 4.06s of the voice").
- **Refused, by name, before anything spends:** a voice with no measured
  duration, a range that starts or ends outside the voice, a range under
  0.5 s, or a value that is not a number.

The pattern:

1. Write every shot's line in cut order and render them as **one** spoken
   shot. One voice, one per-character bill.
2. Find where each shot's words fall in that voice. The API has the word
   timings at `GET /v1/assets/:id/words` (each word with its start and end,
   in milliseconds). **No MCP tool returns them yet**, so from an MCP
   session take the boundaries from listening, or from the silences
   between lines, and cut halfway through each pause so the parts tile the
   file with nothing dropped. The LOTGEN Premiere panel does this step
   itself, by word.
3. For each A-roll shot whose mouth is on screen, `lipsync_take` with its
   clip, the one voice, and that shot's range. `dry_run` first: each quote
   is that part's own. A shot with no face in it (hands, the product, an
   empty room) needs no lipsync: its part of the voice simply plays under
   it, which is also the only way such a shot sounds like the others.
4. Lay the one voice under the whole cut in the editor, and mount each
   re-voiced take on its shot with `select_variant`. A re-voiced take keeps
   its own sound track beside the clip, so it can also play on its own.

A part longer than the picture it goes on cannot fit that picture; give the
shot the time or move the cut, before spending on it.

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
- **An unmeasured audio asset** cannot be quoted for lipsync, and a
  lipsync range outside the voice, or under 0.5 s, is refused.
- **A beat with no line or no voice** is skipped and named by
  `voice_explainer`.

Read the refusal. Every one of them names the gap.

---

## MUSIC, EFFECTS, AND A CLEAN VOICE

The same ElevenLabs key makes more than speech.

- **A bed or an effect is a sound shot.** `add_scene` with kind audio and
  `sound` set: music with its length in seconds (the cut's length; 3 to
  300, instrumental unless you say otherwise), or an effect (0.5 to 30
  seconds, or let the model pick). The prompt describes the sound ("warm
  acoustic guitar, slow, hopeful"; "a drawer slides shut") and is not read
  aloud. `render_scene` makes it; dry_run quotes first. Both bill the
  workspace's ElevenLabs plan credits: an effect 10 a second (measured
  2026-10-15), music about 900 a minute (ElevenLabs names no cost for
  music, so the job shows no actual cost; the account's usage page does).
- **A clip scored with music it was told not to have** keeps its picture:
  `isolate_voice` on the take returns the speech alone as a sound take of
  the same shot, at 1000 plan credits a minute of the take (measured). Lay it on the dialogue
  track in the editor.
- **An episode's assembly does not take a generated bed.** Its disclosure
  says the music is not generated, so `assemble_episode` names the
  difference and waits; upload a licensed track for an episode, or lay the
  generated bed in Premiere.

---

## CHECKLIST

1. Does the face need to move? If not, the free path is the answer.
2. Has this workspace ever synced its voices?
3. For lipsync: is the price read against the right length (the voice's for a still and for pixverse, the longer of clip and voice for sync_v2 and veed_v2)?
   On a voice-over, does each shot's range cover its words, and nothing more?
4. For an explainer: did `voice_explainer` name any skipped beats?
5. Did you re-run `mix_explainer` after the last take landed?
