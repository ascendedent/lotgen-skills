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
                    (on Seedance: "@Image1 is the person: take ...", one
                    tagged sentence a picture; lotgen-refs)
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

**The exception: sound, on a native-audio model.** Seedance adds a score and
voices whoever is on screen unless told otherwise, and a positive sound
description alone did not stop it. What held on one product ad run (2026-10-27,
Seedance 2.5 in Higgsfield) was a closed list that names the silence and
then everything that IS heard:

> *There is no music at all, no score and no soundtrack, only these sound
> effects, in order: the fridge hum, a click, the drawer sliding open,
> rain on the window.*

and, for a person on screen who must not speak, *"her lips stay closed
throughout"*. The list is exhaustive and in beat order, because anything not
on it is a gap the model fills. It still sometimes scores the clip anyway;
`isolate_voice` (lotgen-voice) is the fix after the fact, and a clip whose
sound is replaced in the edit does not need the fight at all.

---

## ONE SHOT, ONE INTENT

A prompt asking for two beats gets the model's average of them, and the average
of two beats is a shot of neither. This is not a style preference: a lot exists
precisely so two beats can be two shots, and a second shot is one render where
an averaged one is three.

Two specifics that are not obvious:

**Never describe the edit, in single-shot mode.** "Then cut to the packshot"
asks one continuous camera to perform a cut. One clip, one camera, one
continuous piece of time. That is the mode for every product hero and demo
shot, and the only mode on the routes where LOTGEN leads the prompt with the
vendor's own single-shot words (Omni, Wan, Runway). The other mode is below:
a clip that is several setups on purpose, written as such.

**On a shot carrying a spoken line, put your change before the quote.** A UGC
prompt is two sentences fused: what is on screen, then the introduction and the
line in quotation marks. **A native-audio model given words after the closing
quote will say them.** That is why every clause this product splices goes into
the framing half rather than being appended to the end — and it is why a stage
direction you append with `update_scene` is a stage direction the voice reads
out.

---

## THE OTHER MODE: SEVERAL SETUPS IN ONE CLIP

Seedance 2.x can cut inside one clip when the prompt says where. On one
product ad run (2026-10-27, Seedance 2.5 in Higgsfield) every comedy and performance
scene was one 5 to 12 second clip of three to eight setups joined by hard
cuts, under one continuous voice-over added in the edit, and it was the best
ad made with this stack. Use it for **performance and comedy beats under a
single VO**: reactions, gags, a run of quick angles on one moment. Keep
single-shot mode for the product hero and the demo, where the product has
to hold still long enough to be the same product.

The anatomy, in this order:

1. **Header:** the shape, the genre, the finish, the length and the count:
   *"Vertical 9:16 cinematic comedy, photoreal, shot like a premium film on
   macro and wide-angle lenses, 10 seconds, 8 setups joined by hard cuts."*
2. **Cast:** each person tied to their reference, with the full description
   repeating the wardrobe anchors. On Seedance LOTGEN's legend tags each
   reference and says what it supplies ("`@Image1` is the person's character
   sheet: take the face, hair, build and wardrobe from it"), so name the
   person by that tag and add only what the legend cannot know (which person
   is which, in a scene with two); a motion clip is `@Video1`, a track
   `@Audio1` (lotgen-refs).
3. **Setting:** tied to its reference, with the weather and the power state.
4. **Product block,** word for word, when the product is in the scene
   (lotgen-product).
5. **Light, shot by shot:** *"Shots 1 and 2 are warmly lit by the dome
   light; from Shot 3 on, the power is out and the only light is the
   lightning through the window."* Never once for the whole clip: darkness
   and light bleed across setups otherwise.
6. **Performance:** *"a real, expressive person ... tiny natural movements:
   weight shifts, blinks, breaths, small twitches of her mouth and brows"*,
   plus the beat's specific business (lotgen-casting).
7. **Sound:** the closed list above, matched to the beats.
8. **Shots:** `Shot N (a–b s):` shot size, angle, lens, camera move, one
   specific action, then *"Hard cut."* Take the time windows from the pauses
   in the recorded VO, so each cut lands on a breath; one hook cut at
   about 3.7 s and 5.7 s because that is where the voice paused.

Two things carry over from single-shot mode unchanged: one action per setup,
and the line is never in the prompt when the voice is added in the edit (no
one speaks to camera; *"her lips stay closed throughout"*).

**Every setup in the clip has to land in the same take.** That is the price
of the mode, and it is why the product's proof never rides inside it. A
ten-second shower ad (2026-11-20, Seedance 2.5, three setups: his feet on
the tub floor, his head under the spray, the floor again) came back four
times, each take wrong in a different setup, at the full clip's price every
time. Split it when:

- a setup carries the **proof** (the product visibly working, the before and the
  after): it is its own shot, at 720p or higher, so a miss is re-rolled
  alone, and fine detail such as thin water does not read at 480p;
- the shot has a person **and** the product: at most two setups.

---

## SEVEN TRAPS THAT RUIN A TAKE

The first four were each in one prompt on 2026-11-20 and each showed up in
the takes; the fifth was in every shot of a whole lot the same week. None is the model being random; each is the prompt asking for it.

1. **A sound a face makes moves the mouth.** *"His mouth stays closed, lips
   sealed"* and, in the sound line, *"one long soft breath out through the
   nose"*: with sound on, Seedance animated the breath with his mouth in every
   take. On any model that makes its own sound, a shot whose mouth must stay
   closed lists no breath, sigh, hum, gulp or swallow. Put those in the edit.
2. **A device named as how it was filmed shows up in the frame.** *"Filmed
   on a phone"* in the first line drew a phone in front of his face. On a
   shot looking at a person, from outside or through someone's eyes, write
   the look rather than the device: *"raw handheld point-of-view footage,
   flat, slight compression"*. A selfie or propped style is different: the
   phone IS the camera there, so say where it is held and put it behind the
   frame, the way the capture styles do (*"the lens of a phone propped up on
   a counter, the phone itself behind the camera"*).
3. **A reference's outfit rides into every shot.** His character sheet shows
   him in a white T-shirt and jeans, and one take put the jeans on him in the
   shower. When a scene changes what the person wears (shower, bath, bed,
   pool, gym), say it as something a camera records (*"bare legs and bare
   feet, wet skin"*) or use a scene outfit made as an edit of the sheet
   (lotgen-casting, lotgen-map).
4. **Undressed scenes trip moderation.** One of the five takes was blocked by
   Seedance's content moderation: a bare-shouldered man in a shower. Before
   the first render of a shower, bath, bed or swimwear scene, ask whether to
   turn the lot's content filter off (lotgen-cost: `update_lot` with
   `content_filter_off`, EvoLink Seedance 2.0 and 2.5 only, at 1.1x). Every
   other model keeps its own filter, so there the move is the scene itself: a
   towel, steam, a frame that stops at the shoulders.
5. **Never say who is filming.** A board that says *"her grandson films
   her on his phone"* is describing the footage, not a person to draw.
   Written into a frame as *"the grandson's phone in the foreground filming
   Maria"*, it became an over-the-shoulder commercial on every row of a UGC
   lot (2026-11-18 to 20): a hand, a shoulder, her picture on a screen, a
   third camera nobody in the story is holding. That is the look people call
   cinematic AI, and it is the opposite of UGC. All that matters is that it
   is phone footage: *"raw handheld phone footage from the rear camera, the
   frame drifting a little and re-centering, flat, slight compression"*, and
   her eyes on a spot just beside the lens. No grandson, no friend, no "the
   one filming", no "the person holding the phone", in the shot or in the
   opening still, because image-to-video continues the frame it is given. An
   off-screen laugh is a sound, not a person. On a `mobile` lot it is
   `capture_mount: "handheld_rear"`, chosen when the lot is made
   (lotgen-lot). When someone asks to *"show the one filming her"*, say once
   that a visible filmer makes it read as a staged ad, and ask before you
   make it.
6. **A fixture with no mount floats.** *"Looking up at his face under the
   shower head"* gave a round rain head hanging from a ceiling under a
   skylight, in a tub-shower whose head is on an arm out of the end wall
   (a tub-shower lot, 2026-11-20). The model puts a named thing
   where the frame has room for it. Say where it is mounted, as the
   location map's `layout` does: *"the shower head on its chrome arm out of
   the end wall, above and behind his head"*. When the camera stands where
   the fixture is (the view from the shower head down onto his face), the
   fixture is not in the frame: say what is behind him instead (*"the far
   end of the tub and the curtain on the open side"*).
7. **The map is a layout reference, not a background: tag the angle and
   write the view.** On a Seedance clip with no panel named and no view in
   words, the map's front panel came back as a flat photo behind him: he
   stood in front of the tub instead of in it, and a second shower head
   appeared where the prompt put one *"at the right edge of frame"*
   (a tub-shower lot, 2026-11-20). LOTGEN now tells the model the
   map is the layout to build the room from, never a picture to put behind
   the person, and names the shot's closest panel. Your half, on every
   shot, still or clip: set `location_angle` to the panel closest to the
   camera, and write the view in words: where the camera is, what it faces,
   and what is behind the subject (*"the phone propped on the window sill
   at shoulder height, facing him across the tub; behind him the left end
   wall with the shower head on its chrome arm above his head, the valve
   below it"*). A camera no panel stands at still names the closest one and
   says where it really is. And place a fixture by its mount, never by the
   frame (*"at the right edge of frame"* adds a second one there).

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

**Every revision is a whole prompt.** Write the shot's prompt out in full
each round, never as a diff against the last one, and say in a line what
changed and why. A patched prompt carries a stale instruction nobody can see;
a full one is the only version that explains the next render.

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
9. Clear of the seven traps: no sound a closed mouth would have to make, no
   "filmed on a phone" on a shot looking at a person (a selfie or propped
   phone stays behind the frame), the scene's own wardrobe stated, a
   moderation-prone scene asked about before it renders, and nobody named as
   the one filming, in the shot or its opening still? Every fixture in the
   frame named with its mount, and none in the frame the camera stands in?
   Every shot's `location_angle` set and its view written out: where the
   camera is, what it faces, what is behind the subject?
   Is the product's proof its own shot?
