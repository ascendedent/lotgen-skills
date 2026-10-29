---
name: lotgen-hook
description: >
  The craft of the first second: what has to be legible in frame one, why a slow
  establishing shot loses a feed, and how that becomes the words of one shot prompt.
  Use when the user says "write the hook", "the opening shot", "this doesn't stop the
  scroll", "test three hooks", "what should the first shot be", "the thumbnail looks
  wrong", "the card picture is black", "it takes too long to get going", "nobody watches
  past the first second", "should the product be in the opening shot", or when a lot has
  been planned and nobody has looked at shot one yet. Read before rendering the first
  shot of anything, and before re-rolling it after the rest of the lot has landed.
---

# The first second

**A lot's first shot is the hook, and it is the only shot in the lot worth
rendering three times.** Everything below is about making that spend buy
something.

Nothing in this document spends. Rewriting a hook is free; the money moves at
`render_scene` and `render_project`.

The reason to concentrate here is arithmetic, not taste. A five-second hook
re-rolled six times costs about the same as one take of a six-shot lot, and the
hook is the shot that decides whether the other five are ever seen. Every other
shot in a planned lot is priced against a viewer who is already watching.

---

## THE HOOK SHOT IS SHOT ONE, AND THE PRODUCT KNOWS IT

Three facts about this codebase that are worth having before you write a word.

**Shot one is the only shot with nothing behind it.** In every marketing
format and in the UGC planner, the opening shot carries `continuity_mode` of
none and every shot after it carries last-frame continuity. So the hook does
not merely open the ad — **the rest of the lot chains off its final frame.**
The room, the light and the wardrobe of shots two through five are inherited
from whichever take of the hook rendered first.

**The hook shot is longer than the hook.** Every UGC structure weights its hook
beat 2 against a total of nine or ten, and the planner shares the platform's
runtime out by weight against a four-second floor. That is a five-second
opening shot on **tiktok** and about ten on **shorts**. The hook is the first
second of it. The other four or nine seconds are yours to write, or they are
the model's to invent.

**The opening frame is the card picture whether or not anyone chose it.**
Nothing in this product writes a separate poster image — the kind exists in the
schema with zero rows. The browse grid paints the take itself: a mounted take
beats an unmounted one, and among mounted takes the earliest shot wins. For a
finished lot that is the hook's own first frame, painted by the browser from
the video. You do not get to pick a frame later. You get to write one.

---

## WHAT HAS TO BE LEGIBLE IN FRAME ONE

Five things stop a scroll. A hook needs one of them, sharp, at t=0:

- **a face**, close enough to read an expression, with an eyeline
- **a hand**, already doing something to something
- **the product**, mid-use rather than at rest
- **motion across the frame**, big enough to register in a thumbnail
- **a question**, posed by an object rather than by an expression

That is a list of things a camera could record. "Intrigue", "energy" and
"scroll-stopping" are not on it, and a video model can do nothing with any of
them — the wild card format's own scaffold says "something that makes a viewer
stop scrolling", which is an instruction addressed to a person and the first
thing to delete when you rewrite it.

**Frame one has to work paused.** It is a still twice over: once as the card
picture, once as the frame a muted feed shows before the video decodes. Ask of
your opening sentence, "if this were a photograph, what is in it?" If the
answer is "a room", the hook has not started.

### Why the establishing shot loses

A wide that resolves into a subject spends its whole hook on the resolution. At
t=0 it is the emptiest and least legible it will ever be, and the payoff lands
at three seconds, which is after the decision. The same is true of every
opening whose verb is *reveals*, *pans across to find*, *fades up on*,
*slowly pushes in to*. They are all promises to be legible later.

A hook does not establish. **It arrives.** Write the frame you would have cut
to, and let shot two do the room.

---

## NINE REWRITES

### 1. The establishing shot

Weak: *Wide establishing shot of a modern kitchen, morning light, slowly
revealing the coffee machine on the counter.*

Strong: *Tight on the machine, steam rising off a full cup, a hand lifting the
cup up out of frame as the crema settles. Morning window light from the left.*

Why: the wide gives its first two seconds to a room nobody is buying.
*Revealing* is a verb whose payoff is at the end of the clip, and the end of
the clip is after the scroll. The rewrite puts the product, motion (steam) and
a person (a hand) into frame one, and the light is named as a source and a
direction rather than as an hour.

### 2. Motion the viewer has to wait for

Weak: *The bottle sits on brushed steel, camera slowly pushes in.*

Strong: *The bottle already filling the frame, condensation running down the
glass and pooling on the steel. The camera holds.*

Why: the **push** move appends the literal phrase "slow push in" to the prompt
at render time, so typing it as well says it twice — and a slow push means the
frame is at its widest and emptiest exactly when it matters. Move the motion
into the subject and let the camera hold. A locked frame with a moving subject
is legible in a 200-pixel card; a push is not.

### 3. The face with nothing on it

Weak: *A woman in her 30s talks to camera about the product in a bright
kitchen.*

Strong: *Chest-up, the creator already mid-sentence, eyes on the lens, brows
raised, holding the tin at chin height so it reads beside her face. Available
light from a window behind camera.*

Why: three separate faults. *Talks about* has no first frame, so the model
picks one, and it often picks a closed mouth and lowered eyes. *Already
mid-sentence* buys an expression at t=0. And **a described person is a
different person in every take** — pass `creator_image_url` or
`creator_asset_id` and let the reference hold the face, which is what the UGC
planner mounts on every talking beat for exactly this reason.

### 4. The question

Weak: *Someone looks confused at a pile of laundry.*

Strong: *A single sock held up to camera, a hole straight through the heel, the
rest of the wash heaped out of focus behind it.*

Why: *confused* is a feeling and corresponds to no pixel. The question a viewer
answers for themselves — what happened to that sock — is posed by an object,
and an object survives a thumbnail where a facial micro-expression does not.
One legible thing, held near the centre, which also means it survives a crop.

### 5. The hook that spoils itself

Weak, and this is the wild card scaffold as planned: *An unexpected opening
image involving the Ridge Kettle, something that makes a viewer stop scrolling
before they know what is being sold, warm and dry.*

Strong: *A kitchen worktop at six in the morning, one mug, and a rolling boil
already going somewhere just out of frame, steam crossing the low light. The
worktop otherwise bare.*

Why: the scaffold names the product, so the model puts the kettle in frame one
and the reveal in shot two has nothing left to reveal. The **wild_card** format
exists precisely to withhold — its own description calls it "an opening that
hides what is being sold until the reveal" — and a template cannot know what
your unexpected image is. Note the last clause: *the worktop otherwise bare*,
never *no logo in shot*. The blank-lot preset that starts a three-shot hook lot
ships with the words "no logo yet" in its opening prompt; that is a scaffold to
rewrite, not a phrasing to copy, because naming a thing to exclude it puts it
in the description.

### 6. Text on screen

Weak: *Text overlay reading "I stopped buying these in 2023" appears over the
shot.*

Strong: *A hand writing four words on a sticky note in black marker — I STOPPED
BUYING THESE — and pressing it onto the cupboard door, the note square to the
lens and filling the middle of the frame.*

Why: **nothing in this product composites text.** There is no overlay layer:
the only way words reach a frame is the video model drawing them, which is why
the ad brief's price arrives as the clause "price on screen" inside a prompt
rather than as a graphic. So give the words a physical surface, a plain
background and a reason to be square to camera, keep them to four or five, and
read the take. A misspelling is a re-roll of a five-second shot, which is the
cheapest re-roll in the lot.

### 7. The verb that ends too soon

Weak: *She holds up the jar and smiles.*

Strong: *She lifts the jar into frame beside her cheek, turns the label square
to the lens, and holds it there.*

Why: *smiles* is over in a beat, and on a five-second hook — which becomes six
on a model that only makes 4, 6 and 8 — that leaves four or five seconds of a
model deciding what happens next. That is where the second gesture, the extra
blink and the drift into a different room come from. Give the clip one verb
that takes the whole length and a final state that holds. **The final state
matters twice**, because it is the frame the next shot chains from.

### 8. Saying what the capture style has already said

Weak, on a lot with the **mobile** capture style: *Handheld phone footage,
shaky, natural light from the window, she opens the box on the kitchen floor.*

Strong: *She drops to the kitchen floor with the box already half open, tears
the last of the tape and tips the contents out onto the tiles.*

Why: the style has already spliced "shot on a phone's main camera on a
24mm-equivalent wide lens, deep focus", "handheld micro-drift, digital
stabilisation breathing at the edges of the frame" and "available light alone"
into that prompt at create time. Typing handheld, shaky and window light again
gives the model the same instruction twice in different words and it averages
them. **A capture style overrules the lot's look. It does not overrule your
sentence.** Delete the camera and the light, and spend the words on the action
— which is the only thing the style has not decided.

### 9. The black thumbnail

Weak: *Fade up from black onto the product on a plinth.*

Strong: *The product already lit on the plinth, a highlight travelling along its
edge as the turntable carries it a few degrees.*

Why: the card picture is the take's own first frame. A clip that fades up from
black has a black card. So does an opening whip pan, whose first frame is a
smear. If the opening frame genuinely has to be a particular picture, put it
there on purpose with `set_start_frame` — pass an asset id, one per shot,
replaced rather than added to — and note that a supplied frame overrides a
last-frame chain, so a hook with a start frame is answering to you and not to
the shot before it. `clear_start_frame` puts it back.

---

## HOOK SHOT VERSUS BEAUTY SHOT

They are written by opposite rules, and most bad hooks are beauty shots in
position one.

| | hook shot | beauty shot |
|--|--|--|
| starts | mid-event | at rest |
| frame one | the subject, already there, large | the composition, complete |
| the verb | one that has already begun | one that resolves at the end |
| the camera | holding, or already moving at speed | slow push, slow orbit |
| what it offers | a reason to stay | a reason to want the thing |
| where it belongs | shot one | shot two, three, the end card |
| worth rendering | three to six times | once |

The vocabulary tells you which one you are writing. **push** appends "slow push
in"; **orbit** appends "camera orbits the subject"; both are beauty-shot
grammar and both hand the viewer their emptiest frame first. **locked** appends
"locked-off camera, no movement", which is a fine hook only when the subject
supplies the motion. The **snap** move-set appends "whip pans and snap zooms" —
hook grammar, but it is a *lot-wide* setting, so choosing it for the opening
puts whip pans on the end card too. The hyper motion format does exactly that
on purpose; if you only want it on shot one, put it on the shot's own `camera`
instead.

---

## WHAT IS ALREADY BEING SAID, SO DO NOT TYPE IT

These phrases are appended at render time, verbatim, after your words. Typing a
synonym of one is how a hook ends up asking for the same thing twice.

| id | the exact phrase appended | what a hook should therefore not type |
|--|--|--|
| **push** | slow push in | "slowly zooms in", "camera creeps forward" |
| **orbit** | camera orbits the subject | "circles around it" |
| **locked** | locked-off camera, no movement | "static", "on a tripod" |
| **handheld** | handheld, slight float | "shaky", "handheld" |
| **snap** | whip pans and snap zooms | "whip pan", "fast" |
| **macro** | macro lens, extreme close focus | "extreme close-up" |
| **wide** | 18mm wide lens, deep space | "wide shot", "everything sharp" |
| **long** | 85mm lens, compressed background | "compressed background" |
| **hard** | single hard key, deep falloff | "hard light" — say the direction, which the phrase leaves out |
| **golden** | low golden sun, long shadows | "golden hour", "sunset" |
| **action** | kinetic action framing, urgent | "energetic", "fast-paced" |

Two of these are traps rather than duplications:

**horror appends "dread, negative space, held too long".** That is a pacing
instruction, and it is the exact opposite of a hook. A lot graded for horror
has a look arguing with its own first shot. Either write the hook against it
deliberately, or leave genre off and put the dread in the shot.

**macro is a distance, not an interest.** "Macro lens, extreme close focus" on
frame one gives you a texture nobody can identify. It is a superb shot two.

And two things the look does at the wrong moment:

**vhs appends "VHS capture, tracking noise, soft chroma, 4:3 era artefacts".**
A degraded frame one is a degraded card picture. Worth it sometimes; worth
knowing always.

**high_contrast appends "crushed blacks, clipped highlights".** A hook whose
subject sits in the shadows vanishes at card size.

### The capture styles that decide your hook for you

- **security_cam** splices a framing demand into every shot: the whole room in
  frame, "with people crossing it at an ordinary unhurried pace". An unhurried
  pace is now written into your hook. It also takes the lot's palette, and it
  refuses both direct address and dialogue.
- **home_cam** likewise takes the palette, and switches to "flat grey infrared
  night mode" once the room is dark — a colourless card picture.
- **drone** opens "from altitude". There is no face-forward hook under it.
- **mobile** on an on-camera beat swaps in the front-camera clauses, including
  "the face close enough that the perspective enlarges it slightly". That is a
  hook asset you get for nothing; do not fight it with a wide framing.
- **cinematic** suppresses nothing, so the lot's look reaches the hook whole.
  It is the one style where a horror genre really will hold your opening too
  long.

Read the styles back with `list_capture_styles` rather than recalling them, and
repeat the overrides to whoever is paying.

---

## PROMISE, DO NOT SPOIL

A hook that **promises** leaves one thing unresolved for the length of the
clip. A hook that **spoils** puts the answer, the logo and the price in frame
one, and the viewer has no reason for the next four seconds.

The distinction is testable. Cover the last frame of your hook: is there still
a question? If not, the hook is a compressed advert rather than an opening.

Three practical consequences in this product:

1. **The scaffolds name the product in shot one.** Every marketing hook
   template puts the product in the opening frame. That is right for a
   demonstration and wrong for a withheld reveal, and it is a one-line
   `update_scene` either way.
2. **The price does not reach the hook, and should not.** The brief's price
   clause is composed onto the closing shot, not the opening one. Leave it
   there. If you want a price hook, it belongs in the spoken line, not burned
   into the picture.
3. **A promise still has to be paid.** Shot two chains from the hook's last
   frame, so a promise made in a room the model has left cannot be kept.
   Finish the hook in the place the answer lives.

---

## TEXT, SPOKEN, OR IMPLIED

Three channels, and they are not interchangeable.

**Implied is the strongest and costs nothing.** The sock with the hole is a
sentence nobody has to read or hear. It cannot be misspelled by a model, it
survives mute, and it survives a crop.

**Spoken is what UGC is for.** The line goes into the prompt in quotation marks
as dialogue, and a native-audio model reads it. Two rules follow. Keep the line
to one breath — these are four-to-eight-second shots. And **put every edit of
yours before the closing quote**: words after it get spoken aloud, which is how
a stage direction becomes a line of the script.

**Text on screen is the weakest and the most expensive.** Nothing composites
it; a model draws it. Assume it will need a re-roll, keep it to four or five
words, and give it a surface.

Three facts that decide between them:

- **Meta is watched on mute**, by that platform's own note in this codebase. A
  hook carried only by a spoken line fails there, and the reframe to 4:5 crops
  it as well.
- **Reframing is a centre crop**, not subject tracking — `reframe_project`
  takes a `focus` of centre, start or end and keeps that part of the frame for
  the whole clip. A hook subject parked at the edge of a 9:16 frame does not
  survive delivery at 4:5. Compose the hook inside the smallest shape it will
  ever ship in.
- **Captions are not a hook device.** Their cue boundaries are estimated from
  character counts, not from forced alignment, and the file says so. They serve
  accessibility. They will not reliably put four words on screen at t=0.

---

## RENDER THE HOOK THREE TIMES, AND IN THE RIGHT ORDER

This is the part that costs money, so it has an order.

1. **Write the hook, then price it.** `estimate_render` compares providers;
   `dry_run` on `render_scene` gives the exact number for this shot on this
   model. Neither spends.
2. **Set a `seed` on the shot first.** `render_scene` with `variants` renders N
   takes differing only by seed, up to six — and with a seed already on the
   shot they are seed, seed+1, seed+2, so the spread is reproducible and a
   later edit is the only variable. With no seed they are random, and no two
   rounds are ever comparable.
3. **Render the hook alone. Three variants is the usual answer**; six is the
   ceiling and is rarely a better use of the money than three plus one rewrite.
4. **Look at frame one of each, paused, small.** That is the test the card
   picture will apply. Then watch them.
5. **Mount the winner with `select_variant`**, passing the shot's own
   `if_unmodified_since` — the shot's, never the job's. Name it with
   `label_take` so the export ships a file whose name says which hook it is.
6. **Only then render the rest**, with `render_project`. `get_brief` lists
   which shots are held back behind the shot they continue from.

**The trap, and it is expensive: re-rolling the hook after the body has
rendered.** Shots two onward chained from whichever hook take was finished when
they ran, and they do not re-render themselves when you mount a different one.
Mount a new hook after the fact and you have a lot whose opening frame no
longer matches the room, the wardrobe or the light of every shot after it —
silently, because nothing is wrong at the row level. If the hook has to change
late, re-render the chained shots too, and price that before you decide.

### Hook variants are a different instrument

`hook_variants` on the UGC planner builds up to five extra opening shots, each
on a different hook formula, sharing one body. They do not chain, they do not
lengthen the ad, they are excluded from the reported runtime, and only one ever
ships. Three is the number the platforms that sell this recommend testing.

**They change the words and not the picture.** A hook formula replaces the
opening *line* only; every variant is composed over the same shot scaffold from
the same structure. So a batch of five variants renders five near-identical
pictures with five different sentences over them — which tests one third of the
hook and pays for all of it.

The fix is one call each. After the lot exists, rewrite each variant's own
`prompt` with `update_scene` so the opening image differs too — a face for the
question hook, a hand and an object for the demonstration hook, the aftermath
for the result hook. Then you are testing a hook rather than a caption. Rewrite
the lines as well: pass `lines` on `run_ugc_recipe` or `plan_ugc` rather than
shipping a fill-in-the-blank scaffold, because you write better copy than a
template does.

---

## WHERE A HOOK FACT BELONGS

| the fact | where it goes | why not in the hook prompt |
|--|--|--|
| the grade of the whole piece | the lot's look, ids from `cinema_vocabulary` | typed once, and the hook cannot drift from shot two |
| this shot's glass and move | the shot's own `camera` | composed ahead of the look, and changeable without touching a word of prose |
| how the thing was shot | the capture style | it splices into every shot and overrules the look where they collide |
| who is on camera | a reference, via `creator_image_url` or `creator_asset_id` | a description renders a new person per take, hook variants included |
| what the opening frame is | `set_start_frame` | a sentence is a request; an asset is the frame |
| how many hooks to compare | `variants` on `render_scene`, or `hook_variants` at plan time | one is the same picture with new seeds, the other is new words |
| what happens in the first second | the prompt | nothing else knows |

---

## CHECKLIST

1. Is there a face, a hand, the product in use, motion, or a question **in
   frame one** — and would it read as a photograph at card size?
2. Does the opening verb start already begun, rather than reveal, fade up, pan
   across to find, or slowly push in?
3. Is frame one something you would be content to see as the lot's card picture
   and the muted feed still — not black, not a smear, not an empty room? If it
   must be something specific, is it set with `set_start_frame`?
4. Does the action fill the whole clip the model will actually make, with a
   final state that holds — remembering the hook shot is about five seconds on
   **tiktok**, ten on **shorts**, and that a five-second ask becomes six on a
   4/6/8 model?
5. Positive throughout — a bare worktop rather than "no logo", and no
   requirement living only in `negative_prompt`?
6. Have you deleted every camera and lighting word the capture style or the
   look already appends, and checked the phrase list rather than recalling it?
7. Does the hook still leave one thing unresolved at its last frame, and does
   that last frame sit in the place shot two needs to inherit?
8. If words have to be on screen, are they four or five, on a physical surface,
   square to the lens — and has the take actually been read?
9. Does the hook survive the shape it ships in: subject inside a 4:5 crop of a
   9:16 frame, legible with the sound off?
10. Is a `seed` set on the shot before you render `variants`, so the spread is
    reproducible?
11. Was the hook rendered, judged and mounted with `select_variant` **before**
    anything that chains from it, and if it is being changed late, has the
    re-render of the chained shots been priced?
12. Said out loud: what has been spent so far, and that a rewrite spends
    nothing.
