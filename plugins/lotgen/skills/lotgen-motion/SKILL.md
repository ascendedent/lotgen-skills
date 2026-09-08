---
name: lotgen-motion
description: >
  How to make a clip MOVE — the camera and the subject, and the fact that a shot always
  has both. Use when the user says "add a camera move", "make it more dynamic", "the
  camera drifts on its own", "it just sits there", "the push is too fast", "the shot ends
  mid-move", "it looks like a photo with a wobble", "make it feel handheld", "why did it
  orbit when I asked for a pan", "the movement doesn't read on a phone", "add motion
  blur", or when a take came back where nothing happens, or where everything happens at
  once. Read before setting a shot's move, a lot's move-set, or writing any sentence with
  a verb of movement in it. Deciding movement is free; only rendering spends.
---

# Movement

A clip is a picture with time in it. Time is the only thing a still cannot do,
and it is the thing most prompts leave to the model. **A shot with no movement
written into it is not a still shot — it is a shot whose movement was chosen by
something that has never seen your product.**

**Nothing in this document spends.** Move ids, move-sets, durations and prose
can all be rewritten for free. Money moves at `render_scene`,
`render_project` and the paid post passes.

---

## THE TWO MOTIONS

Every shot has two, always, whether or not you named them:

| | what it is | what happens if you leave it out |
|--|--|--|
| **camera motion** | what the frame does — where it starts, where it ends | the model invents a drift, usually a slow push, usually into nothing |
| **subject motion** | what moves inside the frame | the model invents a gesture, and it does not end where your clip does |

**Naming only one is the single most common reason a take is "fine but
nothing".** A prompt that says *slow push in* and nothing else is a prompt with
a camera and no event; the model fills the event half with the most average
thing that fits, which is a person shifting their weight or a product sitting
still while dust floats. A prompt that says *she unscrews the lid* and nothing
about the camera hands the framing to the same average.

The rewrite is never "add more words". It is "say the other half".

> **Before** — camera only
> *The bottle on brushed steel. Slow push in.*
>
> **After**
> *The bottle stands on brushed steel. A bead of condensation gathers at the
> shoulder, runs down to the base and stops there. The push ends with the label
> filling the centre third of the frame.*
>
> **Why** — the push now has an event to arrive at and a place to stop. Before,
> "push in" described three seconds of nothing approaching slightly closer
> nothing.

> **Before** — subject only
> *She lifts the jar and turns it to read the back.*
>
> **After**
> *She lifts the jar to eye height and turns it until the back label faces the
> lens, then stops. The camera stays level at chest height and holds.*
>
> **Why** — "the camera holds" is a real instruction and costs six words. Say
> nothing and you get the invented drift, which is what people are describing
> when they say a shot feels *unsteady* on a lot with no move-set at all.

---

## WHAT THIS PRODUCT ALREADY SAYS ABOUT MOVEMENT

Motion reaches a provider from three places, and they are all additive.

```
the prompt text        your verbs, plus a capture style's motion clause
                       spliced in at create time and STORED
  + the shot's move    camera.move, appended at render      (update_scene)
  + the lot's move-set look.moveset, appended after that    (set_lot_look)
```

Read the exact appended phrase from `cinema_vocabulary` before you type
anything near it. The whole per-shot move vocabulary is nine ids and their
phrases are short:

| id | the phrase it appends, verbatim |
|--|--|
| none | *(nothing at all)* |
| push in | slow push in |
| pull out | slow pull out |
| pan left / pan right | pan left / pan right |
| tilt up / tilt down | tilt up / tilt down |
| orbit | camera orbits the subject |
| track | tracking alongside the subject |

And the lot-wide move-sets: locked-off appends *locked-off camera, no
movement*; handheld appends *handheld, slight float*; dolly *smooth dolly
moves*; crane *crane moves, vertical reveals*; steadicam *steadicam, gliding
follow*; snap *whip pans and snap zooms*.

Four consequences worth holding before you write.

**The word "slow" is already in the push and the pull.** Type *slow push in*
in your prose on a shot whose move is push and the provider gets *"…slow push
in, slow push in"*. Duplication is not neutral: a repeated instruction is a
weighted one, and the second copy is competing with your subject sentence for
the model's attention. If you want a push that is not slow, set the move to
none and write the push yourself.

**A move id the vocabulary does not know is dropped in silence.** The field
takes free text up to forty characters, so `camera` with a move of *dolly
zoom*, *crash zoom*, *rack focus* or *whip pan* is accepted, stored, echoed
back, and composes to nothing. Those four are not in the vocabulary. Write them
in the prose instead, where they at least reach the provider.

**none is silence, not a lock.** Setting the move to none appends no phrase; it
does not say the camera is still. It leaves the camera unspecified, which is
the state the model fills in for you.

**The one negation in the vocabulary is the locked-off move-set.** Its phrase
is *locked-off camera, no movement*, and a video model has no token meaning
NOT — so *locked-off camera* is the half doing the work and *no movement* is,
at best, inert. This is the reason to say what the frame IS doing (*the frame
is still, fixed on a tripod, and only the steam moves*) rather than what it is
not.

### The trap

**A capture style takes the lot's move-set away and never touches the shot's
own move.** Nine of the ten styles list camera moves among the axes they
overrule — every one except cinema. So on a body-cam lot the move-set is
removed, reported back in the plan response, and the shot's *camera orbits the
subject* sails straight through into a prompt that already says *the wearer's
stride and breathing carried straight into the frame*.

Nothing warns you, because nothing is wrong: two motion instructions were both
honoured and the model averaged them into a chest-mounted camera performing an
impossible arc. **When a lot has a capture style, the shot's move is your
responsibility alone.** Read the style's motion clause from
`list_capture_styles` first.

---

## A MOVE NEEDS A REASON, AND THE REASON IS THE DESTINATION

A camera move is a sentence about attention. Each one means something, and the
meaning is what makes it read as intentional rather than as drift:

| move | what it means to a viewer | so the shot must supply |
|--|--|--|
| push in | *look at this* | the thing it ends on, and how much of the frame that thing then fills |
| pull out | *there is more than you thought* | what is revealed, and that it was outside the opening frame |
| tilt up | *this is bigger than the frame* | where the top ends — a roofline, a face, a sky |
| tilt down | *this leads somewhere* | what is at the bottom of the move |
| pan left / right | *something over there matters* | what leaves frame, on which side, and what arrives |
| orbit | *this object has volume* | a subject with a back and sides worth seeing |
| track | *this is going somewhere* | a subject already in motion, and where it is heading |

**The appended phrase never carries the destination.** *pan left* is two words.
The model is being told the direction and nothing else, so it picks the extent,
the speed and the resting frame — three decisions, made blind, in a clip that
has about four seconds to spend.

> **Before**
> *slow pull out, wide shot of the kitchen*
>
> **After**
> *The pull ends wide enough that the whole counter is in frame — the kettle,
> the open jar and the second mug she has already set down — with her still
> centred.*
>
> **Why** — a pull-out with no named reveal is a shot that gets smaller for no
> reason. Naming the three things that arrive gives the model both the extent
> of the move and a reason for the wide frame to exist.

> **Before**
> *camera orbits the subject, 135mm telephoto*
>
> **After**
> *35mm lens. The camera arcs a quarter turn to the left, bringing the pump on
> the side of the bottle into view, and stops with it centred.*
>
> **Why** — a long lens compresses depth, which is exactly the cue an orbit
> exists to deliver. On a 135mm the arc costs a full clip and looks like a
> slightly wobbling still. Orbits want the wide end. So does the push: on
> 18mm a short push feels like a lunge, on 135mm a long one barely registers.

---

## SPEED IS WHAT IT DOES, NOT WHAT IT IS

*Fast*, *slow*, *dynamic*, *smooth*, *snappy* and *energetic* are all
descriptions of a feeling. They correspond to no measurable thing in the frame,
which is why two takes with the same adjective come back at different speeds.

Describe a speed as **a distance and an ending**:

> **Before**
> *fast whip pan to the door, energetic, high energy*
>
> **After**
> *The frame crosses the room in about a second, the shelves smearing past, and
> lands square on the door, holding it for the rest of the clip.*
>
> **Why** — *crosses the room*, *smearing* and *holding for the rest of the
> clip* are three things a model can render. *Energetic* is three words of
> nothing, and *high energy* is the same nothing again.

> **Before**
> *very slow, gradual, subtle camera movement*
>
> **After**
> *The frame moves less than its own width over the whole clip, and stops
> before the last second.*
>
> **Why** — "less than its own width" is a distance. "Subtle" is a hope.

The same rule applies to a subject: not *she moves quickly*, but *her hand
crosses the frame and is gone before the mug settles*.

---

## DURATION AND MOVEMENT ARE ONE DECISION

**Write the move to the length that will actually render, not the length you
asked for.** A requested duration is clamped, rounded and snapped to what the
model really makes — Veo 3.1 makes 4, 6 and 8 seconds and nothing between;
Minimax makes 6 or 10; some Runway rows take anything from 2 to 30; Alibaba
rows sit around 2 to 15. `get_brief` prints the real length per shot and
`estimate_render` names it before the money moves. An absent `duration_sec` is
not a provider default — it becomes the floor, the shortest thing anyone could
have meant, which is where four-second clips full of eight seconds of intention
come from.

Three working rules that follow from that:

**A move should settle before the clip ends.** Give it roughly the first two
thirds and let the frame hold. A move still travelling at the last frame reads
as an accident, and the last frame is not only aesthetic — a shot chained with
**last_frame_i2v** hands its final frame to the next shot as an opening image.
Chain from a frame taken mid-pan and the next clip starts on a smeared,
motion-blurred picture and spends its own first second recovering.

**A four-second clip holds one move or one event, not both, unless one of them
is small.** Two beats in four seconds is the same fault as two beats in one
prompt: you get the average.

**A slow push has no room in a short clip.** This is the mismatch that produces
the "nothing happened" take.

> **Before** — a 4-second Veo shot
> *A very slow, gradual push in across the room, from the doorway to the desk,
> settling on the laptop.*
>
> **After**
> *The camera is already at the desk. The push travels about the width of the
> mug and settles by the third second, the laptop screen filling the left half
> of the frame.*
>
> **Why** — a room-length traverse in four seconds is either a lurch or, more
> often, a model quietly rendering a tenth of it. Start where the move should
> have ended and give it a small distance it can actually cover.

---

## HANDHELD IS A TEXTURE, NOT A MOVE

Handheld describes how the frame behaves; it does not get the camera anywhere.
The move-set phrase is *handheld, slight float* — a quality, applied
everywhere, with no start and no end. A shot whose only motion instruction is
"handheld" still has an unspecified camera, and you will get the invented
drift on top of the float.

The two compose properly: handheld plus a push is a hand-held push, and that is
a real and useful thing. Handheld alone is a wobble.

> **Before**
> *handheld camera movement, dynamic and energetic, feels real*
>
> **After**
> *The frame floats and re-aims by a few degrees throughout. She pours, the
> stream stops, and the cup lands on the counter.* — with the lot's move-set
> set to handheld, which carries the float itself.
>
> **Why** — *feels real* is not a picture. Two things changed: the texture
> moved up to the lot where it is typed once, and the shot got an event that
> ends.

The same distinction applies to the phone capture style, whose motion clause
adds *handheld micro-drift, digital stabilisation breathing at the edges of the
frame, the arm correcting its own framing, an occasional autofocus hunt*. That
is a texture too, and a very specific one — it is already doing the job that
made you reach for the word "handheld", and typing the word again on top of it
buys nothing.

---

## WHEN THE LOCKED FRAME IS STRONGER

A still frame is a choice, and in five cases it is the better one:

- **The subject already moves.** A person walking through a fixed frame is more
  legible than a camera chasing a person. Motion against a stable reference
  reads; motion against motion cancels.
- **The shot carries a spoken line.** A face that stays put is a face a viewer
  can read, and it is the face that survives a lipsync pass afterwards.
- **The clip is four seconds.** There is time for one thing, and an event beats
  a traverse.
- **You are going to cut fast.** Three moving shots cut together fight each
  other; one moving shot between two locked ones is the move anybody notices.
- **The take will be reused.** A frame you will pull as a still, hand to
  `set_start_frame` on the next shot, or send through a reframe pass wants to
  be sharp and level.

**There is no per-shot lock in this product.** The move vocabulary has no
locked-off id — locked-off exists only as a lot-wide move-set. So on a lot
whose move-set is handheld, one shot cannot be locked by choosing an id. Your
three honest options: change the lot's move-set, pick a capture style that
overrules move-sets (nine of ten do — a fixed camera is what webcam, security
camera and home camera each already are), or write the stillness into the shot
and accept that *handheld, slight float* is still being appended behind it.

> **Before**
> *locked off, no camera movement, static shot, tripod*
>
> **After**
> *The frame is fixed on a tripod, level with the counter. Only the steam moves,
> rising and bending left.*
>
> **Why** — the first line is three near-synonyms and a negation. The second
> names a mount, a height, and the one thing in the frame that is allowed to
> move, which is what makes stillness read as deliberate.

---

## SUBJECT MOTION THAT READS AT FEED SIZE

Most of this work is watched at 9:16 on a phone, at a size where the whole
frame is about as tall as a hand. **A movement that does not cross a boundary
does not exist at that size.**

Boundaries a viewer can actually see: an edge of frame, the horizon, the line
between the subject and the background, another object, a shadow. Movements
that read: something enters or leaves, something crosses in front of something
else, something falls, something changes shape or state — steam, liquid, fabric,
a lid coming off.

Movements that do not read at feed size: micro-expressions, an eyebrow, a
finger, a small tilt of the head, anything happening entirely inside a face.

> **Before**
> *She raises an eyebrow and smiles slightly, subtle and understated.*
>
> **After**
> *She lifts the jar into the top third of the frame, her hand crossing in
> front of her shoulder, and grins.*
>
> **Why** — the grin still happens; it is just no longer carrying the shot. The
> jar crossing the shoulder is a boundary crossing, which survives being watched
> at thumb size with the sound off.

This is also the reason a 16:9 shot reframed later needs its motion near the
centre: a 9:16 crop keeps under a third of the frame, so a gesture at the
left edge is a gesture that no longer exists.

---

## MOTION BLUR, AND WHAT ASKS FOR IT

There is **no shutter axis in this product**. The lens and aperture
vocabularies govern field of view and depth of field; neither has an exposure
time in it, so a shutter is something you type or something a capture style
brings. The one place shutter language exists ready-made is the cinema capture
style, whose motion clause reads *deliberate operated movement on a dolly or a
fluid head — a slow push in, a slow reveal — 24fps motion blur, every move
settling on a frame*.

Ask for blur when the movement is the point: a hand crossing frame, a car
passing, a whip pan. Ask for sharpness when the object is the point: a packshot,
a label, a face on a spoken line.

> **Before**
> *cinematic motion blur, high shutter speed, crisp and sharp*
>
> **After**
> *24fps motion blur — her hand smears slightly as it crosses the frame while
> the mug's rim stays sharp.*
>
> **Why** — the first line asks for blur and for the setting that removes it, in
> six words, and gets neither. The second says which thing smears and which
> thing does not, which is the only form of this request a model can act on.

Two related cautions. A wide-open aperture and a moving subject do not mix:
*f/1.4, very shallow depth of field* means a subject who takes one step towards
camera has walked out of focus, and there is no focus-pull axis here to save
them. If the subject moves in depth, use the balanced or deep aperture. And a
macro lens — *macro lens, extreme close focus* — has a focal plane a few
millimetres deep, so any move at all is a subject leaving focus. Macro wants a
locked frame and a moving subject, or nothing.

---

## CAMERAS THAT BRING THEIR OWN MOTION

A capture style splices a motion clause into every shot's stored prompt at
create time. Adding a shot move on top of it is adding a second, contradicting
motion instruction — the mount has already decided.

| capture style | the motion it already asserts | what a shot move does to it |
|--|--|--|
| phone | micro-drift, stabilisation breathing, the arm re-aiming, autofocus hunts | a push or a pan is plausible and legitimate; keep it small, because an arm is not a dolly |
| webcam | fixed to a screen, perfectly still, the subject leaning in and out | any move contradicts the mount. Move the subject instead |
| cinema | dolly or fluid head, slow push, every move settling on a frame | the one style that agrees with the lot's move-set. Moves compose cleanly here |
| body cam | the wearer's stride and breathing, the view swinging as the torso turns | an orbit or a track becomes the wearer walking. Say so in words, not with a move id |
| helmet cam | shake keyed to footfalls, whip-fast pans, the horizon rolling | a slow push is not survivable. Write where the wearer looks |
| security camera | a fixed mount, the room moving through the frame | the frame does not move, ever. Everything happens in the room |
| home camera | fixed on a stand, with a slow motorised pan when it follows movement | its own pan exists — trigger it by giving it something to follow |
| dash cam | locked to the vehicle, road vibration, the world sliding past | the vehicle is the dolly. Movement is what the road does |
| drone | gimbal glide, a steady climb or a slow orbit, horizon held level | it already owns the orbit and the climb. A second one is duplication |
| news | steady and level, a slow pan or a locked tripod, reframing between beats | a slow pan is in character; a push is not what a shoulder mount does |

> **Before** — on a lot whose capture style is dash cam, with the shot's move
> set to pan right
> *Pan right across the row of shopfronts until the shop is centred.*
>
> **After** — move set to none
> *The vehicle passes the row of shopfronts, each one sliding out of frame to
> the left. The target shop arrives centre-frame and the vehicle slows.*
>
> **Why** — a camera bolted behind a windscreen cannot pan. The world moving
> past is the only motion this mount has, and describing it that way gets the
> exact picture the pan was reaching for, with one instruction instead of two
> that argue.

> **Before** — on a body-cam lot, shot move set to orbit
> *The camera circles the machine.*
>
> **After** — move set to none
> *The wearer walks around the machine, keeping it centred as the frame swings
> with each step, and stops facing its display.*
>
> **Why** — the arc survives; it now belongs to a person with legs, which is
> what the mount already asserted. *camera orbits the subject* was fighting *the
> wearer's stride carried straight into the frame*, and the average of those two
> is a drifting, floating shot that belongs to no camera at all.

---

## WHAT A VIDEO MODEL IGNORES

| what gets typed | what the model does with it |
|--|--|
| "at 2 seconds the hand enters, at 4 the lid comes off" | nothing — there is no clock; it renders both, in some order |
| "the camera circles twice" | a count it cannot verify. Expect one arc, or three quarters of one |
| "a 30-degree arc", "moving at 5mph", "a two-metre dolly" | units are read as words, not measurements |
| "then it cuts to the packshot" | one continuous camera performing a cut, which is a smear |
| "no camera movement" | naming a thing to exclude it puts it in the description |
| "24fps", "1/48 shutter", "120fps slow motion" | read as style words, not as settings. There is no frame-rate control |
| "on a Steadicam" / "on a gimbal" | a texture — a glide — never a specific path |
| "pan left" with nothing else | a direction, with the extent, speed and ending all chosen for you |
| "she moves gracefully" | nothing, until it names a body part and a distance |

The rig names are the interesting case, because this product uses them itself:
the steadicam move-set appends *steadicam, gliding follow* and the crane one
appends *crane moves, vertical reveals*. Those words buy a quality of movement.
They do not buy a trajectory, so a shot that needs a specific path still has to
describe the path.

---

## STILLS AND SPOKEN LINES

**Every motion phrase is dropped for a still.** A shot with kind image gets no
move and no move-set — asking an image model for *smooth dolly moves* can only
be answered as blur, so the composer removes both. That means a photodump or an
influencer plate with a carefully chosen orbit renders exactly as if the orbit
were never set, and no surface says so. Motion in a still has to be written into
the prose as a frozen state: *mid-pour, the stream unbroken*, not *pouring*.

**A spoken shot gets nothing appended at all.** Its prompt is the script, read
aloud word for word, so a move composed into it would have the voice say
"tracking alongside the subject". Movement for a talking shot belongs to the
picture shot the audio is mixed onto.

---

## WHEN THE MOTION IS RIGHT AND SOMETHING ELSE IS WRONG

Movement is the most expensive thing in a take to reproduce, because it is the
thing a re-roll changes most. Before re-rendering, check whether you are about
to throw away motion you already paid for.

- `edit_take_video` changes a named object inside a rendered clip and keeps the
  people, the setting, the motion and the timing. A take whose push, pace and
  performance are right and whose mug is wrong is an edit, not a re-render —
  and the tier decides how much of the clip comes back, so read
  `list_video_edit_tiers` against the take's actual length.
- `reframe_take` paints new edges rather than cropping to them, and one of its
  tiers paints from the shot's own prompt. A prompt that is mostly camera
  instructions gives it very little to paint with, which is one more reason the
  subject half has to be written.
- If you are re-rolling, hold the seed and change one motion axis per round.
  Three video providers refuse a seed outright; on those, no two takes are
  strictly comparable, so change more per round rather than pretending the
  comparison is controlled.
- Re-read with `get_brief` and pass the shot's stored timestamp as `if_unmodified_since`
  on `update_scene`. A take mounting itself bumps the shot, which is the usual
  reason a timestamp you were holding comes back 409.

---

## CHECKLIST

1. Does the shot name **both** motions — what the frame does and what moves
   inside it — or has one of them been left to the model?
2. Does the camera move have a **destination**: the thing it ends on, and how
   much of the frame that thing then fills?
3. Is every speed written as a distance and an ending rather than as an
   adjective — no *fast*, *slow*, *dynamic*, *smooth*, *energetic*?
4. Have you read the phrase the move id actually appends, and checked your prose
   does not repeat it? *slow push in* already contains "slow".
5. Is the move id one the vocabulary knows? Anything else — dolly zoom, crash
   zoom, rack focus, whip pan — is dropped in silence and belongs in the prose.
6. Does the lot have a capture style? If so, its motion clause is already in
   the prompt, the lot's move-set has been taken away, and **the shot's own
   move has not** — is yours arguing with the mount?
7. Will the move settle before the clip ends, at the duration the model really
   renders rather than the one that was asked for? And if the next shot chains
   from this one, is the final frame worth opening on?
8. Is the subject's movement one that crosses a boundary, so it survives being
   watched at feed size?
9. Positive throughout — the stillness stated as what the frame IS doing, never
   as *no movement*?
10. Priced with `estimate_render` or a `dry_run`, and said out loud that nothing
    has rendered and nothing has been spent?
