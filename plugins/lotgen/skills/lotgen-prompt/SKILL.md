---
name: lotgen-prompt
description: >
  The craft of writing ONE shot so the model renders what you meant, and of revising it
  after a bad take instead of re-rolling blindly. Use when the user says "fix this
  prompt", "write the prompt for this shot", "why did it render that", "it ignored what I
  asked for", "how do I stop it putting a person in frame", "the clip runs on after the
  action", "the light is wrong", "make it more cinematic", or when a take came back wrong
  and nobody has worked out which layer is at fault. Read before editing any prompt: what
  the provider receives is not what was typed into the box.
---

# Writing a shot

**Nothing in this document spends.** A prompt can be rewritten a hundred times
for nothing; the money moves at `render_scene` and `render_project` and the
paid post passes, and nowhere else. So the cheapest sequence is always: read
the shot back, change one thing, price it, render once.

The expensive mistake is not a bad prompt. It is a bad prompt rendered three
times because nobody worked out which of the four layers was actually wrong.

---

## WHAT THE PROVIDER ACTUALLY RECEIVES

**The prompt in the box is not the prompt that is sent**, and confusing the two
mechanisms below is the most common reason a careful edit changes nothing.

Two different things happen to a shot's words, at two different times.

**Spliced in at create time, and stored.** When a recipe planned the lot, the
vertical's clause and the capture style's clauses were written *into* the
prompt text. They are literally in the field `get_project` shows you. Up to six
of them per shot — optics, motion, lighting, grade, an ambient audio line, and
what the frame must show.

**Appended at render time, and never stored.** This shot's camera, then the
lot's look, then a legend naming the references in send order:

```
your words          the stored prompt, clauses already spliced in
  + camera          lens, aperture, move       (update_scene camera)
  + look            genre, film stock, lighting, palette, move-set, note
  + legend          References, in order: 1. hero; 2. packshot.
```

Four consequences worth having in hand before you touch anything:

**No tool here returns the composed string.** You reconstruct it: `get_project`
for the words and the camera, `cinema_vocabulary` for the phrase each look id
appends, `list_capture_styles` for the clauses the recipe put in. Nothing is
ever silently rewritten between the box and the provider — the composition is
additive on purpose, because a prompt that is edited on the way out is a prompt
nobody can debug — but nothing displays the result over MCP either.

**Camera lands before the look**, so the specific qualifies the general: a shot
that asked for a macro lens has said something the lot-wide move-set should not
argue with.

**Punctuation decides how your last clause reads.** A prompt ending in a full
stop gets the appended vocabulary as its own sentence; one that does not gets
it comma-spliced onto whatever you ended with. End on a full stop unless you
want *"…the label edge catching a thin white line, 85mm lens, compressed
background"*.

**The kind of shot changes what is appended at all.** A still drops every
motion phrase, because asking an image model for "smooth dolly moves" can only
be answered as blur. A spoken shot gets nothing appended whatsoever: its prompt
*is* the script, read aloud word for word, so a look composed into it would
have the voice say "teal and orange grade".

### The trap

**Rewriting a prompt wholesale with `update_scene` deletes the clauses that
were spliced into it.** The field is replaced, not merged. The vertical's
clause and the capture style's six go with it; the look survives, because the
look was never in the text.

A "tidied up" UGC prompt therefore renders pristine footage — the one thing
real UGC never is — and the lot still reports the capture style it no longer
obeys. Edit around the clauses, or read them back from `list_capture_styles`
and put them in yourself.

---

## THE FOUR THINGS THAT MUST BE SAID

Subject, action, camera, light. Anything you leave out the model fills in, and
it does not fill it in the way you meant. These are four facts, not four
adjectives.

**Subject — a noun with a boundary, and colour attached to a material.**
"Crimson silk catching the cold spill from the corridor", not "red and blue".
Words that name a feeling rather than a surface — premium, clean, modern,
elevated — correspond to no pixel and are simply noise in the description.

**Action — one verb that ends.** A clip has to finish something in the seconds
it has. *"She turns until she is square to camera and stops"* ends; *"she is
energetic"* never does, and a verb with no stopping point is exactly where the
extra gesture at the tail of a clip comes from.

**Camera — where it is, on what, and whether it moves.** Say nothing and the
model invents a move. Prefer the shot's structured `camera` over typing "85mm"
into the sentence: the structured field composes as a phrase in a fixed
position, and can be changed on one shot without disturbing a word of the
prose. Ids come from `cinema_vocabulary`, and an id the vocabulary does not
know is **dropped rather than pasted in as literal text** — which is silent, so
a look full of invented ids composes to nothing and the shot renders ungraded
with no error anywhere.

**Light — source, direction, hardness.** Along with eyeline, the gap a model is
most confident about filling on its own.

### Where the light rule bites

A capture style overrules the lot's look on the axes it names — and it does
that **by removing look fields, never by editing a sentence anybody typed.**

So a body-cam lot whose prompt says "one hard key from the left" sends the
style's available-light clause *and* your key light, in one prompt, and the
model averages them into neither. The style protects you from your look.
**Nothing protects you from your own words.** Read `capture_overrides` on the
response, then check your own sentences on those same axes and delete the ones
the camera has already decided.

---

## WHAT IT DOES NOT ANSWER TO

| what gets typed | what the model can do with it |
|--|--|
| "premium feel", "elevated" | nothing — no pixel corresponds |
| "energetic" | nothing, until it names a movement that ends |
| "make sure the logo stays readable" | nothing — that is an instruction to a person |
| "no people in frame" | reliably puts people in frame |
| "four bottles" | a count it cannot verify; expect three, or five |
| "the same woman as the last shot" | nothing — identity is a reference, not a sentence |

---

## WHY NEGATIONS FAIL, AND THE ONE CHANNEL THAT EXISTS

A prompt is a description, not a filter. There is no token meaning NOT, so
naming a thing in order to exclude it only puts that thing in the description.
Every capture clause in this product is written positive, and a test holds the
line, for exactly this reason.

`negative_prompt` is a real field on a shot. **It is not a universal channel,
and what happens to it depends entirely on where the shot renders:**

- Kling and Alibaba send it as its own field. It does what you expect.
- The Veo, Seedance, Omni and Runway video adapters never read it. It is
  stored on the shot, shown in the editor, and dropped on the way out.
- On a marketplace row it is sent only if that row maps it — and a row can
  disagree with itself per route. Veo 3.1 Fast on fal takes it for
  text-to-video and drops both it and the seed on the reference-to-video
  route, measured 2026-08-25.
- On **stills it is worse than dropped.** The nano, OpenAI, BFL and Runway
  image adapters append it to the prompt as "Avoid: …", so the noun you did
  not want is now in the prompt text.

State the requirement positively in `prompt`, which every provider reads. Use
`negative_prompt` as a second belt on a provider that has the field, and never
as the only place a requirement is written down.

---

## ONE SHOT, ONE INTENT

A prompt asking for two beats gets the model's average of them, and the average
of two beats is a shot of neither. This is not a style preference: a lot exists
precisely so two beats can be two shots, and a second shot is one render where
an averaged one is three.

Two specifics that are not obvious:

**Never describe the edit.** "Then cut to the packshot" asks one continuous
camera to perform a cut. One clip, one camera, one continuous piece of time.

**On a shot carrying a spoken line, put your change before the quote.** A UGC
prompt is two sentences fused: what is on screen, then the introduction and the
line in quotation marks. **A native-audio model given words after the closing
quote will say them.** That is why every clause this product splices goes into
the framing half rather than being appended to the end — and it is why a stage
direction you append with `update_scene` is a stage direction the voice reads
out.

---

## DURATION: THE ASK IS NOT THE RENDER

A requested length is clamped to the model's floor and ceiling, rounded to a
whole second, and — where a model publishes a range but honours a list —
snapped to the nearest of the lengths it really makes, **upward on a tie**,
because a clip shorter than asked for has to be rendered again.

- Veo 3.1 refuses five seconds with a message describing a range it does not
  honour. It takes 4, 6 and 8. A 5s shot renders and bills as 6.
- An absent `duration_sec` is neither zero nor a provider default: it becomes
  the floor, the shortest thing anybody could have meant.
- `estimate_render` names every one of these before the money moves, and
  `get_brief` prints the line per shot. The price quoted is always the price of
  what will actually be made, so a 7s shot on a 4/6/8 model is quoted for
  eight.
- `render_scene` refuses a shot whose quote named a degradation until
  `accept_degradations` says otherwise — and refuses a *blocking* one whatever
  you send, because a talking-head shot whose creator reference would be
  dropped is the wrong model rather than a compromise somebody consents to.
- Resolution behaves differently and more quietly: an unsupported tier is
  downgraded and billed at the tier that ran.

**This changes the writing, not only the invoice.** Write the action to the
length that will actually render. Four seconds of beat inside an eight-second
clip is four seconds of a model deciding what happens next, which is where the
drift, the second gesture and the wandering camera come from.

---

## REVISING AFTER A BAD TAKE

**Re-rolling the same prompt with a new seed is the most expensive way to learn
nothing.** Name the fault on one axis before changing a word.

| what came back wrong | where the fault actually lives | the move |
|--|--|--|
| the grade, on every shot | the lot's look | one change to the look, not six prompts |
| the light, on a lot with a capture style | the style already owns that axis | read `capture_overrides`, then delete your own light sentence rather than adding another |
| the wrong person, or a different one each shot | references, not words | a face is a reference; a sentence describing one renders a new person every time |
| too much happening | the beat | split it into two shots |
| clip runs on past the action | the length the model really makes | write the action to that length |
| one object wrong, the rest right | not a prompt problem at all | a post pass keeps the take you already paid for — see lotgen-post |

Then, in order:

1. **Change one axis per round, and hold the seed.** Variant batches differ by
   seed alone, and a shot with a seed set gives a reproducible spread rather
   than a random one, so the edit is the only variable. Three video providers
   refuse a seed outright; on those no two takes are ever strictly comparable,
   so change more per round rather than pretending the comparison is
   controlled.
2. **Re-read before you write.** `get_brief` gives the shot's settings and its
   `updated_at`; pass that as `if_unmodified_since` on `update_scene`. A 409
   means somebody moved the row — re-read and decide again, never retry the
   same write. A take mounting itself bumps the shot, which is the usual reason
   a timestamp you were holding is stale.
3. **Keep what you have.** The bad take does not go anywhere, and
   `select_variant` is how you mount whichever one wins once both exist.
4. **Price the retry.** `estimate_render` to compare across models, `dry_run`
   on `render_scene` for the exact number on this one. Then spend.

---

## WHERE A FACT BELONGS

Most bad prompts are prompts carrying a fact that belongs one layer up, where
it would have been typed once instead of six times.

| the fact | where it goes | why not in the prompt |
|--|--|--|
| the grade of the whole piece | the lot's look, ids from `cinema_vocabulary` | typed once, and it cannot drift between shots |
| this shot's glass | `camera` on the shot | composed ahead of the look, so the specific qualifies the general |
| how the thing was shot | the capture style, `list_capture_styles` | it splices its clauses into every shot and overrules the look where they collide |
| what the piece is FOR | the vertical, `list_verticals` | it refuses inputs it cannot work with before anything is created |
| the beat pattern | the format or structure, `list_ad_formats` and `list_ugc_structures` | it decides how many shots there are and what each one does |
| what happens in THIS shot | the prompt | nothing else knows |

---

## CHECKLIST

1. Does the shot say all four — subject, action, camera, light — and is the
   action one verb that ends?
2. Positive throughout: no "without", no "avoid", no "no ...", and no
   requirement that exists only in `negative_prompt`?
3. Does anything you typed argue with a clause the capture style already
   spliced in — light above all? `capture_overrides` read rather than guessed?
4. Will the appended camera and look phrases land as their own sentence, or be
   comma-spliced onto your last clause?
5. If the shot carries a spoken line, is every change of yours before the
   quote?
6. Is the duration one this model actually makes, and is the action written to
   that length rather than to the one you asked for?
7. Identity carried by a reference rather than by a description?
8. Priced with `estimate_render` or a `dry_run`, and said out loud that nothing
   has rendered and nothing has been spent?
