---
name: lotgen-faceless
description: >
  Build a faceless episode in LOTGEN: one 9:16 Short for a brand's organic series on TikTok,
  Reels and Shorts with nobody on camera, a narrator voice over stills the assembly animates
  (or silent clips), captions burned from the voice's own timing. Use when the user says
  "faceless", "a listicle short", "5 things about", "myth vs fact", "story time", "POV",
  "a carousel", "an episode a week", "organic series", or wants a brand channel without a
  presenter. The planner does NOT research the topic and says so; the lines are yours to
  write or to draft with a text model on the workspace's key and approve, and this explains
  where they go, what a claim needs behind it, and what to tell the user before they post.
---

# Faceless

A faceless episode is **one finished vertical file**: a hook, a body at a
steady cadence, a call to action, one voice, one look, captions on the voice's
own clock. Every other planner in this product hands back a lot of shots; this
one hands back a lot the assembly turns into an episode. Nobody is on camera,
synthetic or real, and that is a rule, not a default.

---

## THE ONE RULE THAT MATTERS MOST

**Nothing here researches the topic.** The scaffolds fill deterministically
from the brief. A listicle shipped as it comes out says "one thing about cast
iron pans that changes how you use it" five times, and the five proving
episodes measured on 2026-09-17 were one flat declarative per beat, read
straight through with no pause anywhere: that is what a scaffold sounds like.

**So the lines are the work, and there are two ways to do it:**

1. Call `run_faceless_recipe` with `dry_run`.
2. **Read the beats back**, and read `word_budget` and `hook_word_budget`.
3. Research the topic yourself. Keep the pages.
4. Write one line per beat, inside its budget, **or** have a text model
   draft them: pass `writer` on the create (or call `draft_lines` on any
   lot that exists), read the job's `result.lines` with `get_job`, and apply
   the ones you approve with `update_scene`. A draft is marked as a draft
   until you do, and a line flagged `needs_fact` states something the brief
   did not give it: find the page or cut the line.
5. Call it again for real, passing your lines as `narration` **by beat
   index** and the pages as `sources`.

**Planning is deterministic.** The same brief gives the same beats, so the
indexes you read in the dry run are the indexes your lines land on. Change the
structure, the preset or the count between the two calls and that is gone.

**What a good line is shaped like.** The writer reads the same reference
maps you should: one idea per beat in spoken words; the hook opens on the
subject, a number, a question or a contradiction in its first three words;
at least one beat ends on something the next beat has to finish; a number
only when the brief gives one; sentences of four to fourteen words; the
delivery written into the words where the voice reads it (an ellipsis for a
beat of air, ONE capitalised word for weight, a full stop to land) and never
as a stage direction; the last beat asks for one thing or lands the point,
never both. Per structure: a listicle numbers its items in the spoken words
and puts the promised item last; myth_fact quotes the belief as its holders
say it and lands the fact on a full stop before its reason; story_time makes
every turn the cause of the next and cuts the cliffhanger one sentence
early; a hands_demo step names the verb and the object or stays silent.
Eight reference Shorts were measured on 2026-09-17 and three habits held
across them: five of eight open on "you" (a command, a conditional, or what
the narrator knows about the listener), a number is spoken alone as its own
beat after a pause, and the close is a question to the listener or a
landing, never "follow".

---

## THE SHAPE

`list_faceless_structures` carries everything the planner accepts: ten
structures, ten looks, three presets, the tiers and their defaults.

| structure | shape | spoken |
|--|--|--|
| listicle | hook, N items (3 to 10), CTA | yes |
| myth_fact | hook, myth and fact in pairs, takeaway | yes |
| story_time | hook, setup, turns, cliffhanger | yes |
| three_tips | title over beat 1, exactly three tips, the end card | yes |
| faq | the question over beat 1, answers, CTA | yes |
| founder_story | hook, low point, turns, where we are, CTA | yes |
| pov_card | POV line over beat 1, one to three pictures | no, on-screen text |
| hands_demo | hook, steps, result, as clips of hands and product | yes, lines may be empty |
| screen_tutorial | hook, steps on screen, result, as screen captures | yes, lines may be empty |
| carousel | five to seven stills, each with its line | no, on-screen text |

**No structure opens on a static card.** A title, a question or a POV line is
a text overlay the studio writes over the first beat's moving picture; the
image model is asked for a clean picture and the words are data you can change
with `set_text_overlay`. The `count` is the number of the growing beat; each
row's range is in the listing and outside it is refused by name.

**Duration is a preset.** short_30, short_60 or short_90 at 9:16, never free
seconds. The preset is the `word_budget`: about 150 words for a minute. The
hook is three seconds, which in stills mode is `hook_word_budget`, about seven
words. Every beat carries its own budget in the plan; a line that runs long
holds its picture longer, and the finished length is the voice's.

**Two structures are not spoken.** pov_card and carousel put their lines on
the picture. Pass them as `narration` all the same, one overlay line per
newline; they are never voiced and never captioned as speech, and
`voice_faceless` refuses them by name. A carousel's lines ship as `slides` on
the export and the review payload, and no caption file is written for it.

---

## PICTURES: STILLS FIRST

`visual_mode` is stills for eight structures and clips for hands_demo and
screen_tutorial, the two built for the brand's OWN footage: attach the clip as
a shot reference (`import_from_drive` with `destination` scene_ref, or the
resumable put in the web app), then `import_take` with `scene_id` and
`ref_asset_id` makes it the beat's take at $0 under the import provider. The
assembly, the export and provenance find it like any render and say it was
not generated (the disclosure's synthetic_visuals reads some). A 16:9 capture
in a 9:16 episode is refused by the assembly by name; `reframe_project` to
9:16 first.

**Stills** go to `image_provider`, gpt-image-2 by default at about half a cent
a picture. It renders a 9:16 ask at 1024x1536, a 2:3 frame; the plan shows
that frame on every picture line and the assembly's cover absorbs it. The
assembly animates each still (a slow zoom or pan, rotated by beat) and holds
it for its line. A flat episode of stills is named in the assembly's warnings
and in the disclosure, in YouTube's own words; that is the exposure of this
whole genre, and the answer is distinct storylines, real insight per episode
and pictures that move, not a bigger model.

**Clips** go to `provider`, fal's Kling 3.0 Standard by default, silent, at
the structure's cadence snapped to the provider's grid. `billed_seconds` in
the plan is what the grid will charge for; it can exceed the preset. Never
omni: it bills ten seconds whatever is asked.

**The look** is one of ten. Eight are 2D (animated, illustrated, doodle,
papercraft, whiteboard, isometric, blueprint, chalkboard) and the disclosure
says they are not photographs; clean_product and warm_editorial are. **Prefer
2D for evergreen series.** For a product brand, owned footage of hands and
product is the honest picture: hands_demo or screen_tutorial with the brand's
clips through `import_take`, and generated clips only where there is no
footage, said so.

**Words are data.** Every still is `text_mode` editable unless the user asks
for baked: the prompt asks for no lettering and the copy is an overlay. Read
`lettering_detected` on a still's asset from `list_assets` before laying words
over it; if the model painted words anyway, re-render or move the overlay.

---

## THE MONEY

`run_faceless_recipe` answers with `bill_of_materials`: the picture lines
(provider, model, delivered frame, price), the narration lines (per character
on ElevenLabs), the assembly at zero, and a rejection multiplier marked as an
assumption on the picture lines only. `estimate_usd` is one take of
everything; it is null if any line could not be priced, and then the plan says
which. Read it to the user before anything renders. `get_usage` afterwards is
what was actually spent.

---

## THE CALLS, IN ORDER

1. `list_faceless_structures`, once, to choose the shape and the look.
2. `list_brand_kits`: the kit's voice, palette and default look apply where
   the brief names none, and its name fills the CTA.
3. `list_voices`, and `refresh_voices` if the workspace has never synced. A
   cloned voice is the user's to name; never suggest cloning.
4. `run_faceless_recipe` with `dry_run`. Read the beats, the budgets and the
   bill of materials.
5. Research. Write the lines. Every health, finance or efficacy claim has a
   page behind it or is cut.
6. `run_faceless_recipe` for real with `narration`, `sources`, `voice_id`
   and `caption_style` (from `list_caption_styles`). The lot is stamped
   faceless; `get_project` shows each beat with its overlay state.
7. Fix a line with `update_scene` before rendering. Text is free; a picture
   is not. A draft costs one text call (cents; the plan's `writer.estimate_usd`
   or `draft_lines` with `dry_run` says how many): read `get_job`'s
   `result.lines`, apply what you approve, and say to the user which lines a
   model drafted.
8. `render_project`. **This spends.**
9. `voice_faceless`. Per character. It names every beat it skipped.
10. `assemble_episode` with `captions` (required, no default). Pass
    `end_card` with the series name and episode number when the plan said
    the structure closes on the card (three_tips); the three structures that
    speak a CTA get no card CTA, one CTA not two. Do not also pass
    `hook_card` when beat 1 already carries the studio's title overlay.
    `bed_asset_id` adds a music master beside the voice-only one from a track
    the user uploaded; the uploader warrants the rights and no generated
    music is offered. Read `assembly_id`.
11. `get_assembly` until the episode appears or `waiting` names a beat. Read
    `warnings` and `platforms`.
12. `create_review_link`. The episode plays first.
13. `export_to_drive` or `export_project`: the episode, the music master if
    any, the stills with their composited copies, and the disclosure.
14. `get_usage`: what it cost, beside what was quoted.

`plan_faceless` and `create_faceless_lot` are the same job in two calls, for
when something has to happen between planning and building. `mix_faceless`
is the per-shot mix for a clips episode only; a still gets its voice in the
assembly.

## THE SERIES

"An episode a week" is a series, and a series is a row (decision 25): the
recipe, the kit, the voice, the look and the caption style that never change,
the structures that rotate, a ledger of every topic covered, and its own cap.
Nothing is scheduled and nothing is posted; cadence is a sentence for your
clock, and every episode is a call you make when it is time.

1. `create_series` once: name it (the name is every episode's client_tag),
   pick the frame and `structure_rotation`. Or pass `from_project_id` with a
   Faceless lot the user liked: it becomes episode 1 and seeds the rest.
2. `get_series` before every episode. Read `topic_ledger` and `spent_usd`.
   The ledger is the memory: a topic already there is refused, and the tail
   shows which structures ran last.
3. `next_episode` with `dry_run` and the topic. It answers the plan, the bill
   of materials, `ledger_tail`, `structure_repeats` and the series' spend
   against its cap, and writes nothing. If a repeat is refused, the sentence
   names the earlier episode; pick another topic, or say what is new about
   this one in the topic itself.
4. Write the lines, then `next_episode` for real with `narration` and
   `sources`. The lot lands stamped with the series and its episode number;
   steps 8 to 14 above follow unchanged. `update_series` moves the rotation,
   the cap or the frame between episodes; the ledger and the count are the
   record and cannot be edited. `delete_series` removes the series; its
   episodes stay as lots.

---

## THE RULES

- **Never a first-person testimonial.** No "I tried it and", no invented
  customer, no rating. The FTC's Rule on the Use of Consumer Reviews and
  Testimonials (16 CFR Part 465, in force since 2024-10-21) reaches fake or
  AI-generated reviews and testimonials; the per-violation ceiling is set
  separately (16 CFR 1.98, 90 FR 5581, 2025-01-17). A faceless brand voice
  states; it does not pretend to have been a customer.
- **A claim needs a source or is cut.** Health, finance and efficacy claims
  have to be substantiated before they are made (FTC substantiation policy,
  1984-11-23; FINRA Rule 2210(d)(6) for anything financial). `sources` is
  where the pages go, and a line you cannot source is a line you do not
  write.
- **Distinct storyline per episode.** YouTube's monetization policy names
  "image slideshows, templated storylines" and templated AI content without
  the creator's own insight as ineligible, and allows a series in which each
  video has a distinct storyline, focus or concept. Read the structure's
  YouTube note in the listing. Same structure, different insight, every week;
  `get_series`'s ledger is where you check that the insight IS different.
- **2D for evergreen; owned footage first for product brands.**
- **Hook formulas are `lotgen-hook`'s.** This skill gives the hook its three
  seconds; that one says what goes in them.
- **Repeat the disclosure block to the user.** Every finished episode carries
  it on `get_assembly`, in the export and on the review payload: what was
  synthetic, whether it reads as a photograph, the field or label each
  platform uses to mark synthetic media, YouTube's dated sentence on what a
  label does and does not do, and the line naming the brand posting to an EU
  audience as the deployer who marks the post. LOTGEN is not the poster. It
  also says, measured and dated, that the episode carries no embedded Content
  Credentials (every assembly is a re-encode) and lists per source provider
  whether that provider's own files carried one: say so plainly, and never
  claim the file is signed.
- **Never promise reach.** Nothing here knows what a platform will do with a
  post, and a label alone changes neither recommendation nor monetization,
  in YouTube's words.

---

## CHECKLIST

1. Did you dry-run first and actually read the beats and the budgets?
2. Did you research the topic and write real lines, inside the budgets, with
   the pages in `sources`? If a model drafted them, did you read every line
   before applying it, and cut or source every `needs_fact`?
3. Do your `narration` indexes match the beats you read, and did the inputs
   stay identical between the two calls?
4. No testimonial, no unsourced claim, nobody on camera?
5. Did you read the bill of materials to the user before `render_project`?
6. `captions` passed, `end_card` only where the plan asked, no `hook_card`
   over the studio's title?
7. Did you repeat the disclosure block, and say nothing about reach?
