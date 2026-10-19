---
name: lotgen-platform
description: >
  Framing for the surface the piece will actually be watched on, and why the shape of the
  frame is a decision taken at plan time rather than a crop taken at export time. Use when
  the user says "make it vertical", "we need this for TikTok and for YouTube", "reframe it
  to 9:16", "will this work on Reels", "the subject is cut off in the crop", "it has to
  work on mute", "put captions on it", "how long should this be for Shorts", "same ad, two
  placements", "deliver it 4:5 for the feed", or when a lot is about to be planned and
  nobody has yet said where it is going. Read before choosing an aspect, and before
  agreeing to deliver one lot to two placements.
---

# Framing for where it is watched

**The shape of the frame is the one decision on a lot that no later call can
properly repair.** A prompt can be rewritten for nothing, a look changed in one
call, a bad take re-rendered. A shot composed for a wide frame and delivered
tall has already thrown away the pixels the tall frame needed, and every remedy
after that point either subtracts more or pays a model to invent what was never
photographed.

Three ways to end up in a vertical placement, cheapest first:

| | what it is | what it costs |
|--|--|--|
| framed for the aspect at plan time | the subject staged inside the shape it will be watched in | nothing |
| `reframe_project` | ffmpeg, a centre crop with a chosen focus | nothing, and stored, so a second delivery of the same aspect is free too |
| `reframe_take` | a model paints the newly exposed edges | $0.10 per second of the source at 720p on LTX; $0.12 per **started** source second on Ray |

The order is also the quality order. The paid one is third for a reason: it is
the only one that can keep the whole picture, and it is still guessing at
everything outside the frame somebody actually shot.

---

## THE ARITHMETIC OF A CROP

`reframe_project` crops **one axis and keeps the other whole**. Measured off
the crop the product actually performs, for a 1920x1080 source and a 1080x1920
source:

| source | delivered | kept | what leaves |
|--|--|--|--|
| 1920x1080 | 9:16 | 606x1080 — **31.6% of the width** | 657 pixels off each side |
| 1920x1080 | 4:5 | 864x1080 — 45.0% | 528 off each side |
| 1920x1080 | 1:1 | 1080x1080 — 56.3% | 420 off each side |
| 1920x1080 | 4:3 | 1440x1080 — 75.0% | 240 off each side |
| 1080x1920 | 4:5 | 1080x1350 — **70.3% of the height** | 285 off the top and the bottom |
| 1080x1920 | 1:1 | 1080x1080 — 56.3% | 420 off the top and the bottom |
| 1080x1920 | 4:3 | 1080x810 — 42.2% | 555 off the top and the bottom |
| 1080x1920 | 16:9 | 1080x606 — 31.6% | 657 off the top and the bottom |

Two things follow, and both are worth saying to whoever asked for the crop.

**A wide shot taken to 9:16 keeps under a third of its width.** A two-person
frame becomes a one-person frame. A product placed left of centre for a
composition leaves the delivery entirely. Nobody chose that; the geometry did.

**`focus` moves along the axis with spare picture, and only that one.** On a
16:9 source going to 9:16 the spare is horizontal, so start keeps the **left**
of the frame and end keeps the **right** — there is no "top". On a 9:16 source
going to 16:9 the spare is vertical, so start keeps the **top** and end the
**bottom**. Asking for start expecting headroom, on a landscape source, quietly
gives you the left-hand side instead.

And the thing a crop cannot do at all: it cannot invent headroom, it cannot
follow a subject, and it cannot recover a two-shot. It is a centre crop with a
chosen focus, not subject tracking, and the tool says so on purpose.

---

## WHERE THE SHAPE ACTUALLY LIVES IN THIS PRODUCT

**The lot has no shape. Its shots do.** `create_project` takes no aspect at
all; `aspect_ratio` is a field on the shot, set by `add_scene` and changed by
`update_scene`. "Make the lot vertical" is therefore a change to every shot, and
a lot half-converted renders half its shots in the wrong shape with no error
anywhere.

**In UGC Factory, `platform` decides both the runtime and the shape.** It is
one field on `run_ugc_recipe` and `plan_ugc`, and the four placements it accepts
carry their own numbers:

| platform | runtime | rendered | delivered | the row's own note |
|--|--|--|--|--|
| tiktok | 24s | 9:16 | 9:16 | the hook has to land in the first second, not the third |
| reels | 30s | 9:16 | 9:16 | tolerates a slightly more polished tone than TikTok |
| meta | 30s | 9:16 | **4:5** | 4:5 takes more of the feed than 1:1; assume it is watched on mute |
| shorts | 45s | 9:16 | 9:16 | the only placement with room for a real demonstration |

`aspect_ratio` on those calls is an override, and omitting it is the point of
naming a platform.

**Meta is the case everyone gets wrong.** The shots are rendered 9:16 because
nothing renders 4:5 — no video adapter in this product declares it, and Kling
was probed on 2026-08-25 and rejects 4:3, 3:4, 4:5 and 21:9 outright. The 4:5
is a delivery crop, and by the table above it takes 285 pixels off the top and
285 off the bottom of a 1080x1920 shot. **Anything staged in the top or bottom
eighth of a Meta shot is not in the file the client receives.**

In Marketing Studio there is no platform field. `aspect_ratio` on
`run_marketing_recipe` offers 9:16, 16:9 and 1:1, and nothing else: 4:5 and 4:3
exist in this product as deliveries and never as renders. A vertical may supply
the shape you did not name — apparel defaults to 9:16 — and the talking-head
vertical deliberately declares none, because the platform already decided.

**`get_brief` does not print the shape.** Its settings line carries provider,
model, duration, resolution, audio, continuity and seed. The aspect appears
there only when the provider is about to change it. A lot that is quietly 16:9
throughout reads as perfectly clean in the brief; `get_project` is where the
shape is.

---

## THE MODEL HAS ITS OWN SHAPES, AND IT WILL PICK ONE

An aspect a model does not offer is not refused. It is **degraded, orientation
first**: a portrait ask falls to the model's nearest portrait shape, a landscape
ask to its nearest landscape, a square ask to 1:1 where that exists — and where
the model offers nothing in that orientation, to the first shape it declares.

**So a vertical shot on a landscape-only model renders landscape.** MiniMax has
no aspect parameter of any kind: its text-to-video renders 16:9 and its
image-to-video follows the frame it was given. Ask it for 9:16 and you are
billed for a wide clip. On that model the opening frame is the aspect control —
`set_start_frame` with a vertical plate is what makes a vertical clip there,
not the `aspect_ratio` field.

Others to have in hand: Kling renders 16:9, 9:16 and 1:1. Omni renders 16:9 and
9:16 only. Several marketplace rows carry shapes as pixel pairs rather than as
aspect names, and declare no aspect list at all — which means "pass it through",
not "accepts anything".

Two calls close this before the money moves:

- `estimate_render` takes `aspect_ratio` and reports what the provider will
  change. The quote is the contract: no degradation is applied that a quote did
  not name.
- On a model that uses a reference as the literal opening frame, a plate that
  is not the shot's own shape **ships letterboxed**. The incompatibility says
  so and says to crop the plate first. A letterbox is burned into the render,
  and no crop, upscale or restyle takes it back out.

---

## FRAMING FOR A VERTICAL THIRD

A 9:16 frame is a column. The subject occupies a vertical third of it, the
camera is closer than it would be in a wide frame, and depth is staged above
and below rather than left and right. That is a writing decision, and it goes
in the words.

**Rewrite 1 — the product hero.**

- Weak: *The bottle on a marble counter, kitchen behind, soft window light.*
- Strong: *The bottle stood upright on a marble counter, filling the middle
  third of a tall frame from just above the cap to the base, the kitchen
  falling out of focus above and below it, soft window light from the left.*
- Why: "on a counter" is a horizontal statement — a counter runs across a wide
  frame. The strong line states how much of the height the bottle occupies,
  which is the only measurement a vertical frame cares about, and puts the
  background on the axis that still exists.

**Rewrite 2 — two people.**

- Weak: *Two people at a table comparing the old kit and the new one.*
- Strong: *One person at a table holding the old kit in the near hand and the
  new one up beside it, both inside the same tall frame, the second person's
  shoulder entering at the edge.*
- Why: a two-shot is a wide idea. It survives a 16:9 render and dies in a 9:16
  crop, which keeps 31.6% of the width. Written for the column, the comparison
  happens in one pair of hands and the frame holds it.

**Rewrite 3 — the move.**

- Weak: *camera pans right across the shelf* typed into the prose.
- Strong: leave the prose to the shelf — *three tins stacked one above another,
  the top one at eye level* — and set the shot's camera move to tilt up.
- Why: a pan travels along the axis a vertical frame has least of; the appended
  phrase is literally "pan right", and the model has 606 pixels of width to
  perform it in. Tilt up appends "tilt up" and push in appends "slow push in",
  both of which travel along the axis you have. Lot-wide, the crane move-set
  appends "crane moves, vertical reveals" — the one move-set written for height.

**Rewrite 4 — the safe area, on an apparel CTA.**

The apparel vertical appends to its CTA beat: *the whole outfit in frame, no
crop above the knee*. In a vertical placement that puts the shoes on the bottom
edge, under the caption strip and the handle.

- Weak: *Full length, the whole outfit in frame.*
- Strong: *Full length with a clear margin of floor below the shoes and a hand's
  width of air above the head, the figure standing in the upper two thirds of
  the frame.*
- Why: platform chrome **overlays** the picture rather than cropping it — the
  caption, the handle and the description sit along the bottom of a vertical
  player and the button column runs up its right-hand edge. Nothing in this
  product removes chrome. The only defence is composing the payload away from
  those edges, and the words above are the composition.

Two habits that follow from that, in every vertical shot: keep the thing that
must be read in the **upper middle** of the frame, and when the frame has a
side bias, put it on the **left**. Both cost nothing to write and neither can
be added afterwards.

**Rewrite 5 — a screen in a column.**

- Weak: *A screen recording of the dashboard.*
- Strong: *A phone held upright in one hand, the app's own list view filling the
  screen top to bottom, a thumb pulling one row at a time, the desk out of focus
  behind.*
- Why: an interface is a landscape object, and a 16:9 screen inside a 9:16 frame
  is a band across the middle with two thirds of the picture doing nothing. A
  portrait device is the honest way to fill a portrait frame. The software
  vertical's own clause already says *no UI text beyond what the brief supplies*
  — the same rule as the on-screen text section below.

---

## CAPTURE STYLES CARRY A SHAPE WHETHER OR NOT YOU ASKED

A capture style splices its clauses into the prompt text at create time. Several
of those clauses assume a frame shape, and **a crop cannot take a spliced clause
back out** — the words went to the model and the model answered them.

| style | the words that assume a shape | in 9:16 |
|--|--|--|
| mobile, selfie sub-mode | *the front camera at arm's length … the room behind still readable* | written for vertical; the natural choice for a 9:16 lot |
| webcam | *a webcam above a screen at desk height … chest-up and close enough to fill the frame* | a landscape device. In a column it reads as a phone video of a laptop |
| dash_cam | *the bonnet along the bottom edge and the rear-view mirror at the top* | both markers land exactly where the chrome sits |
| security_cam | *the whole room laid out in frame — the walls, the doorways, the furniture* | a room laid out is a horizontal fact; a column gives a slice, and the corner-mount reading weakens |
| drone | *an aerial view from altitude, wide and rectilinear* | the same problem: altitude reads as breadth |
| body_cam, helmet_cam | *a wide fisheye … curving the straight lines at the edges* | the long edges of a column are its sides, so the curve is more visible, not less |

None of this makes a style wrong in a vertical lot. It means the framing half of
the prompt has to answer the shape the style implies — a security camera in 9:16
needs a corner of a room named rather than a whole room, or the model averages a
demand it cannot satisfy.

---

## LOOK PHRASES THAT ARGUE WITH THE ASPECT

The look is appended at render time and never stored, so it is one call to
change and worth checking against the shape.

- **vhs** appends *"VHS capture, tracking noise, soft chroma, 4:3 era
  artefacts"* — a shape word inside the prompt of a vertical shot. A model
  handed a named format can answer it with borders drawn into the picture, and a
  border in the pixels is not something a crop removes. **16mm** appends
  *"16mm film, heavy grain, slight gate weave"*, which names texture and no
  shape.
- **anamorphic** appends *"anamorphic lensing, oval bokeh, horizontal flares"*.
  A horizontal flare crosses the short axis of a column and has almost no room
  to travel.
- **comedy** appends *"bright, snappy, generous framing"*. Generous framing costs
  subject size, and subject size is what a column is short of.
- **epic** appends *"vast scale, deep staging, slow moves"*. Scale in 9:16 reads
  as height; say what is tall rather than what is broad.
- Lenses: **wide** appends *"18mm wide lens, deep space"*, which gives back the
  sides you cropped to. **long** appends *"85mm lens, compressed background"* and
  fills a column cleanly. **macro** appends *"macro lens, extreme close focus"*
  and is the safest glass for a detail beat in any shape.

---

## SOUND OFF

Two of this product's own platform rows say it outright: Meta's is *assume it is
watched on mute*, TikTok's is *the hook has to land in the first second, not the
third*.

**The first shot has to state the subject as a picture.** A UGC hook beat fuses
what is on screen with the spoken line in quotation marks, and a native-audio
model speaks that line — but on mute the frame is the whole message. So the
visual event goes into the framing half of the prompt, before the quote, where
every clause this product splices already goes. Put it after the closing quote
and the voice reads your stage direction aloud.

**Rewrite 6 — the muted hook.**

- Weak: the planned beat, unchanged — *a person talking straight to camera in a
  real home, holding the bottle, natural window light*, then the line.
- Strong: *a person in a real home holding the bottle up beside their face and
  turning the label to the lens as they begin to speak, natural window light*,
  then the line.
- Why: on mute the weak version is a person moving their mouth. The strong one
  states the product, the brand and the gesture in the first frames, and the
  audio still does its job for whoever has it on.

**On-screen text is the trap.** A video model spells unreliably: ask for a
printed word and expect a shape that resembles letters. This product is built
around that — the software vertical writes *no invented UI copy*, the marketing
brief has a switch that keeps the price out of the frame, and a caption track
ships as a sidecar file beside the clips rather than being drawn into the
picture.

Burning captions in is a paid pass, not a prompt. The bold_caption preset in
`list_short_presets` exists for exactly this and describes itself as *assume it
is watched on mute*; `restyle_take` runs it. Read `list_video_edit_tiers` first:
the default tier returns at most about 5.06 seconds whatever it is handed, so a
24-second cut cannot be captioned in one pass there, and splitting a clip and
restyling the halves is measured dead — adjacent halves come back as different
films at the cut.

The reliable move is to write the sentence into the world. A label turned to the
lens, a hand pointing at the result, the finished thing on the table beside the
product: those are pixels a model can render and a viewer can read with the
sound off.

---

## WHAT A FEED REWARDS AND WHAT A PRE-ROLL REWARDS

The same shot list, ordered two ways, because the viewer arrives two ways.

**A feed.** The piece autoplays, silent, against a thumb that is already
moving. Every second is a fresh decision to stay. The payload goes first — the
product, the result, the person — and the brand rides with it rather than
waiting for a card at the end. The last shot is where the CTA lands for the
people still there, and it is written for a minority audience on purpose.

**A pre-roll.** The viewer came for something else and a skip control is coming.
Everything you need to be sure was seen belongs before it. A thirty-second story
whose product appears at twenty-two seconds is a story most viewers never reach
the end of. The opening does not have to stop a scroll — nobody is scrolling —
but it does have to be worth not skipping, which is a different requirement and
usually a calmer one.

That difference is a **plan-time** decision: it changes the beat order, the
first shot's content and where the CTA sits. Re-ordering after the fact is
`reorder_scenes` and free, but the first shot written to stop a thumb is rarely
the right first shot for a pre-roll, and vice versa. Two audiences that far
apart usually want two plans.

---

## DURATION BY SURFACE

The platform's seconds are a budget, not a per-shot length. `plan_ugc` shares
that budget out by beat weight and clamps each shot to between 4 and 15 seconds.
Marketing does the same with `target_duration_sec` (4 to 120), stills take no
share of it, and the real total comes back on the plan because the clamp moves
it.

So the number you actually control is the **beat count**:

- Four beats into TikTok's 24 seconds is about six seconds each. Comfortable.
- Six beats into the same 24 is four seconds each, which is the floor. The plan
  is at its limit and any additional beat lengthens the piece rather than
  dividing it.
- Shorts' 45 seconds is the only placement with room for a demonstration that
  needs more than one continuous action.

Then the provider moves it again: a shot asked for 5 seconds on a model that
makes 4, 6 and 8 renders and bills 6. Write each action to the length that will
actually render — see lotgen-prompt, which covers that clamp in full.

---

## ONE LOT, TWO PLACEMENTS

**Delivering the same lot to two platforms is a framing decision made at plan
time, not a crop made at export time.** The ladder from a 9:16 render, by the
free crop's own arithmetic:

- to 4:5 — 70.3% of the height survives. Good. This is the Meta path and it is
  designed for.
- to 1:1 — 56.3%. Workable if the subject was staged in the middle band.
- to 4:3 — 42.2%. Only for a subject that was already framed tight and central.
- to 16:9 — 31.6%. Almost never acceptable: two thirds of your composition is
  gone and the remaining band was never framed as a landscape shot.

The rule that falls out: **crop towards the squarer shape, never across the
orientation.** Two orientations are two renders. If a client wants 9:16 for
Reels and 16:9 for a site hero, that is either two lots or one lot duplicated
with `duplicate_project` and each shot's `aspect_ratio` changed — decided
before anybody renders, because the second render costs the same as the first
and the crop that would have avoided it costs a third of the picture.

The crops themselves are cheap and durable: `reframe_project` stores them, so a
second delivery of the same aspect costs nothing at all, and spoken shots have
no picture and are skipped and named rather than silently missed.

---

## THE TWO REFRAMES, SIDE BY SIDE

| | `reframe_project` | `reframe_take` |
|--|--|--|
| what it does | centre crop with a chosen `focus` | a model paints the newly exposed edges |
| scope | every shot in the lot | one rendered clip, by `asset_id` |
| price | free, and stored | LTX $0.10 per source second at 720p, $0.20 at 1080p; Ray $0.12 per started source second at 720p, $0.36 at 1080p, $0.06 at 540p |
| shapes | 16:9, 9:16, 1:1, 4:5, 4:3 | LTX reaches 4:5 and not 4:3; Ray reaches 4:3 and not 4:5. Neither reaches both — check `list_video_region_tiers` |
| needs | nothing | Ray paints from the shot's own prompt and refuses a shot that has none. LTX needs no prompt and takes a source up to 60 seconds |
| where it lands | a delivery format on the lot | a delivery format too — the shot's own takes are untouched |

Both land as deliveries rather than as takes, which is why neither disturbs a
mounted winner and why the export in that aspect picks them up.

Worked sum, so the trade is concrete: eight six-second shots taken to 4:5 on
LTX at 720p is **$4.80** and several minutes of provider time. The same eight
shots through the free crop is **$0.00** and instant, at the cost of 30% of the
height. Framing 9:16 for a 4:5 delivery in the first place is free, instant and
better than either — and Ray's rounding is worth knowing before you choose it: a
6.2-second take is billed as seven started seconds.

---

## WHAT THE MODEL IGNORES

| what gets typed | what the model can do with it |
|--|--|
| "leave room at the bottom for captions" | nothing — an instruction to a designer |
| "keep it in the safe area", "title safe" | nothing; no pixel corresponds |
| "9:16" written into the prompt | nothing reliable — the shape is a field, not a sentence |
| "shot vertically" in the prose | nothing you can count on, and on a model with no aspect field the opening frame is what decides |
| "add the words HALF PRICE" | a shape that resembles letters |
| "don't crop the shoes" | a negation, so expect cropped shoes. Say where the shoes are instead |
| "make it work on both" | nothing — two shapes are two decisions, taken before the render |

---

## CHECKLIST

1. Has anybody said where this will be watched? If not, ask that one question
   before planning — it sets the shape, the runtime and the beat count at once.
2. If it is UGC, is the placement carried by `platform` rather than by an
   `aspect_ratio` you typed over the top of it?
3. Is the delivery shape the same as the render shape, and if not, is the gap
   the designed one (rendered 9:16, delivered 4:5) rather than an accident?
4. Does every shot carry the intended `aspect_ratio` — read from `get_project`,
   since `get_brief` prints the shape only when a provider is about to change it?
5. Will the chosen model actually render that shape, or will it degrade to its
   nearest orientation? Checked with `estimate_render`, not assumed.
6. On a model with no aspect field, is a correctly shaped opening frame doing
   the job instead?
7. Does each prompt say how much of the frame's **height** the subject occupies,
   rather than describing a horizontal arrangement?
8. Is the thing that must be read staged in the upper middle, clear of the
   bottom and right edges where the chrome sits?
9. Does the capture style's spliced framing agree with the shape — a room, a
   windscreen, an aerial view given something a column can actually hold?
10. Does the look append a shape word (the vhs film stock) or a horizontal
    figure (anamorphic flares, generous framing) into a vertical shot?
11. Does the first shot state the subject as a picture, with any change of yours
    written before the quoted line?
12. Are the beats within the platform's budget at four seconds or more each, and
    is each action written to the length the model really renders?
13. If two placements were asked for, has it been said out loud that a crop
    across the orientation keeps under a third of the picture, and that the
    honest answer is two renders?
