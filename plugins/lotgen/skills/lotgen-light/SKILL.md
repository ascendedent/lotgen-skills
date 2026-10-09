---
name: lotgen-light
description: >
  How to describe light so a generative video model actually renders it, given that
  LOTGEN already appends a lighting clause of its own. Use when the user says "the
  lighting is wrong", "it came back flat", "make it moody", "make it look expensive",
  "add dramatic lighting", "why is everything lit from the front", "the product looks
  cheap", "I set golden hour and it did nothing", "how do I get shadows", "it looks
  like a stock photo", "the subject disappears into the background", or when a take is
  right in every way except the light. Read before writing the light half of any prompt,
  before setting a lighting id on a lot, and before re-rendering a take whose only fault
  is the light. Describing light spends nothing; re-rendering it does.
---

# Lighting a shot

**A re-render costs the full price of the shot, and light is the commonest
reason anyone pays it twice.** Nothing in this document spends. The money moves
at `render_scene` and `render_project` and the paid post passes, and nowhere
else — so the light is settled in words first, priced with `estimate_render` or
a `dry_run`, and rendered once.

Two facts to hold before rewriting anything:

- **A still that is right apart from its light is an `edit_still`, not a
  re-roll.** "Relight as harsh midday sun from the left" keeps the frame you
  already paid for and lands as a new take beside it. A fresh render throws
  away the composition as well as the light.
- **A clip's light is decided before its first frame.** `set_start_frame` hands
  the model a picture, and a picture states direction, hardness, colour and
  level at once, in a way no sentence reliably does.

---

## THE EIGHT PHRASES THE PRODUCT ALREADY SENDS

A lot's look carries one lighting id. At render time its phrase is appended to
every video and every still in the lot — verbatim, never edited into your
sentence, and never shown back to you by any tool here. These are the exact
words the provider receives:

| id | the phrase it appends | what it never says | so your sentence's job is |
|--|--|--|--|
| none | *(nothing)* | everything | all of it |
| natural | available light, soft key | where the light gets in, what time it is | name the opening — a window at the left edge, a doorway behind him |
| golden | low golden sun, long shadows | which side the sun is on, what the shadows fall across | put the sun behind one shoulder and say where its shadow lands |
| hard | single hard key, deep falloff | direction, height, what stays lit at the edge of the falloff | the side, the height, and the one surface still catching light |
| soft | large soft source, gentle wrap | how big and how close, and which side it is on | the source as an object: a window a step to her left, filling that edge of frame |
| practical | lit by practicals in frame | which fitting, where it stands, how far its light reaches | name the lamp, place it, say where its glow dies |
| neon | neon spill, saturated coloured light | the colour, the surface it lands on, whether the sign is in shot | one colour on one surface — magenta across wet tarmac |
| overcast | flat overcast light, no shadow | see the trap below | the soft shadow you DO want, stated positively |

Three things are true of that whole column, and each one is a job that falls to
you:

**Not one of the eight names a direction.** "Single hard key" has no side.
"Large soft source" has no position. Golden hour comes closest — a low sun
implies height, and nothing more. **Say nothing about direction and the model
picks, and the thing it picks is flat and frontal**, because that is the average
of every lit picture ever made.

**Only two carry colour** — golden and neon. Every other id is colourless, and
the lot's palette is a *grade*, not a light: a teal and orange grade is applied
across the whole frame, so it cannot put a warm lamp on one side of a face and
a cold window on the other. Only your words can.

**One of the eight is a negation.** Overcast appends *"flat overcast light, no
shadow"*, and a prompt is a description, not a filter — there is no token
meaning NOT, so "no shadow" puts shadow into the description. Every capture
clause in this product is written positive and a test holds that line; this
phrase predates the rule. Pick overcast and you send it whatever you type, so
spend your own sentence on the shadow you want to see rather than on agreeing
with it.

---

## DIRECTION: THE FACT WITH NO OTHER HOME

Direction cannot be set anywhere in the product. It is not in the eight, it is
not in a palette, and no capture style states a side. It exists only in the
words you write, and it is the single change that most reliably stops a frame
looking like stock photography.

State direction as a **consequence**, not as an angle. "Key at 45 degrees
camera left" is crew shorthand for a lighting plan; the model draws pictures,
not plans. Which side is bright, which side goes dark, and where the shadow
lands — those are pixels.

**Weak** — *"well-lit product shot of the bottle, cinematic lighting"*

**Strong** — *"The bottle on brushed steel. The light comes from the left, so
the right side of the label falls into shade and one shadow runs to the right
of the base, out of frame."*

**Why** — the rewrite names a side and its two visible consequences. It also
gives the appended clause something to attach to: with the look's lighting set
to hard, *"single hard key, deep falloff"* now has a side, a subject and a
shadow to be hard about. Alone, it had none of them.

---

## HARD AND SOFT LIVE IN TWO PLACES

Hardness is a property of the source's size relative to the subject, and it is
visible in exactly two places. Describe those two and the model has something to
draw. Say "harsh" or "flattering" and it has a mood word.

**The edge of a shadow.** A hard source draws a shadow with a crisp edge that
stays crisp across the wall. A soft source draws one that fades out across its
own width and has no findable edge at all.

**The size of the specular.** A hard source puts a small bright highlight — a
coin on a forehead, a dot on the shoulder of a bottle. A soft source puts a long
strip down a whole side, and that strip is what makes glass read as glass and
brushed metal read as metal.

**Weak** — *"harsh dramatic lighting on her face"*

**Strong** — *"One small source high and to her right. The shadow of her nose
has a crisp edge across her cheek, and a coin-sized highlight sits on her
forehead."*

**Why** — two drawable facts instead of two adjectives, and neither of them
duplicates *"single hard key, deep falloff"*. The id supplies the falloff; the
sentence supplies the edge and the specular the id has no word for.

**Weak** — *"soft flattering beauty lighting"*

**Strong** — *"A window a step to her left fills the left edge of frame. The
shadow under her jaw fades out across its own width, and one long highlight
runs down that side of her cheek."*

**Why** — the soft box id already appends *"large soft source, gentle wrap"*, so
typing "soft" again buys nothing. The sentence adds what the phrase omits: the
source is an object, it has a position, and the shadow behaves in a stated way.

---

## THE PRACTICAL IS THE MOST RELIABLE INSTRUCTION YOU CAN GIVE

A practical is a lamp that is **in the frame**. It is the one lighting
instruction that names an object rather than a quality, and objects are what a
generative model is best at. Give it a lamp and the light follows the lamp,
because a picture with a visible source and light that disagrees with it is a
picture the model has almost never seen.

It is also the only lighting instruction that survives a cut. "One hard key from
the left" is re-invented in every shot; "the amber lamp on the bar top" is the
same lamp in shot two, and three clips start looking like one room.

**Weak** — *"moody lighting in a bar"*

**Strong** — *"A low amber lamp stands on the bar top just inside the right
edge of frame. Its glow reaches the glass in front of it and dies about a metre
past it, and the far wall sits almost black."*

**Why** — a source, a position, a reach and a consequence. With the look's
lighting set to practicals, *"lit by practicals in frame"* has already asserted
the arrangement, so the prompt's whole job is which lamp, where it stands, and
where its light stops. Type "moody" and none of those exist.

Two more uses of the same trick:

- **A screen is a practical.** A phone or a monitor lighting a face from below
  is a specific, recognisable picture, and it names its own colour.
- **A source just outside the frame still works** if you say what it lands on:
  *"a doorway out of frame left throwing one bright rectangle across the
  floorboards"*. The rectangle is the drawable part.

---

## TIME OF DAY IS A LIGHTING INSTRUCTION

Models carry a strong prior for time, weather and place — far stronger than for
lighting vocabulary. "An hour before sunset in a side street after rain" fixes
colour, height, hardness and contrast in one clause, and every part of it is
something the model has seen thousands of times.

**Weak** — *"golden hour vibes, warm cinematic glow"*

**Strong** — *"Late afternoon, an hour before sunset. The sun sits low behind
her right shoulder and her shadow runs most of the way across the pavement
towards camera."*

**Why** — time plus direction plus what the shadow does. With golden on the
look, *"low golden sun, long shadows"* is already going out; repeating "long
shadows" adds nothing, while *whose* shadow and *where it lands* adds the two
facts the phrase cannot carry.

Weather is the cheapest hardness control there is: **an overcast sky is a soft
source the size of the sky, and clear midday sun is the hardest source
outdoors**. Name the sky and you have named the shadow.

---

## COLOUR TEMPERATURE, DESCRIBED AS A SOURCE

Numbers are invisible. "5600K" and "3200K" correspond to no pixel, and a mixed
white balance is a fact about a camera setting rather than about a picture. A
named source, on the other hand, carries a colour, a position and a believable
falloff all at once.

**Weak** — *"5600K key with 3200K practicals, mixed white balance"*

**Strong** — *"Blue evening light through the window on one side of his face;
an orange desk lamp on the other. The two meet down the middle of his nose."*

**Why** — two sources, two colours, two sides, and one visible line where they
meet. That line is the whole point of mixed colour, and it is the part a number
never states.

Sources worth naming by object, each of which brings its own colour: a sodium
street lamp, a fluorescent tube in a ceiling grid, a phone screen, a fridge
door standing open, a television two rooms away, headlights through glass, a
candle. **Colour belongs to a material and a light** — "amber lamplight on a
brass tap" beats "warm tones", which names a grade the palette already owns.

Where a colour must hold across a whole lot, it is the palette's job, not the
prompt's — see *Where a light fact belongs* below.

---

## BACKLIGHT AND RIM: THE CHEAPEST SEPARATION THERE IS

**No lighting id in the vocabulary appends a backlight.** Check the eight: key,
source, sun, spill, flat. The only rim light stated anywhere in this product is
inside the Cinema capture style's clause — *"key, fill and rim placed
deliberately"* — and that style is one of ten. So unless you type it, there is
no light behind the subject, and a dark subject on a dark background merges into
it. That merge is what people mean when they say a shot looks cheap, and it is
worse on a phone screen and worse again at a low bitrate.

**Weak** — *"make the subject stand out from the background"*

**Strong** — *"A hard light behind her, just out of frame, draws one bright line
along her shoulder and through the loose hair at the back of her head. The wall
behind her stays dark."*

**Why** — the weak line is an instruction to a person; a model has nothing to
draw for it. The strong line is a description of the picture that separation
actually looks like, and it costs one clause.

The same move on a product: *"one bright line down the back edge of the bottle,
the background falling off behind it"*. On a bottle the rim also does the second
job — it tells you the glass is glass.

---

## WHY "CINEMATIC LIGHTING" IS A WASTED PHRASE

It names a category, not a picture. The model answers it with the average of
everything labelled cinematic, which is a soft frontal key and a shallow
background — the same frame you would have got by saying nothing at all. It
also spends the position in the sentence where the actual light would have gone.

This product makes the point structurally: the Cinema capture style says the
same thing in physical terms — *"a lit set — key, fill and rim placed
deliberately, practicals dressed into the background"* — and it splices that
into every shot. **If you want cinematic, choose it as a capture style and spend
your prompt on the specifics.** The word in a sentence buys strictly less than
the clause the style already sends.

### What a video model does nothing with

| typed into the prompt | what it does |
|--|--|
| "5600K", "3200K", "daylight balanced" | nothing — no pixel corresponds |
| "f/8 for exposure" | nothing. The aperture ids only ever speak about depth of field, and a rendered frame has no exposure to set |
| "three-point lighting", "2:1 ratio", "backlit 70%" | names a crew practice or a meter reading; type the rim and the shadow instead |
| "expose for the highlights", "protect the shadows" | instructions to a person, not descriptions of a frame |
| a light named by brand, fixture or modifier | risks drawing the stand and the softbox into your frame — a prompt is a description, not a call sheet |
| "no harsh shadows", "avoid blown highlights" | a reliable way to get harsh shadows and blown highlights |
| "same lighting as the last shot" | nothing. That is the look, or an opening frame |
| "well lit", "perfect lighting", "professional lighting" | nothing measurable |

### What it answers to, every time

A named source that is in or just outside the frame. A time of day, a season and
a weather. Which side is bright and which side is dark. Where a shadow falls,
what it falls across, and whether its edge is crisp. The size and position of a
highlight. A colour attached to an object. And, above all of them, a picture.

---

## WHEN YOUR PROMPT FIGHTS THE ID

The composition is additive and deliberately never edits the words you typed.
Your sentence goes out whole, then this shot's camera, then the lot's look:

```
your sentence.                       untouched, in full
  + camera        50mm lens          this shot's own glass
  + look          single hard key, deep falloff
```

So a prompt that describes a soft north window and a look set to hard sends
**two independent descriptions of the light in one prompt**, and the model
averages them the way it averages any two beats: into a third thing nobody asked
for, usually the flatter of the two. Nothing errors, nothing warns, and the take
looks merely disappointing rather than wrong.

**Weak** (with the lot's lighting set to overcast) — *"one hard key from camera
left, deep shadow on the far wall"*

**Strong** — set the lot's lighting to hard with `set_lot_look`, then write
*"One hard source from camera left. The bottle's shadow is thrown long across
the far wall with a crisp edge."*

**Why** — the composed prompt otherwise carries *"flat overcast light, no
shadow"* and your hard key at once. One of them has to go, and the id is the
one that costs a single call to change for the whole lot.

**Weak** — *"flat light, no shadows anywhere"*

**Strong** — *"A white overcast sky is the only source. The shadow under the
bottle sits directly beneath it, soft, and barely darker than the ground."*

**Why** — a described soft shadow lands; a negated one arrives as a shadow. This
is also the honest way to use overcast: you cannot take *"no shadow"* out of the
appended phrase, so put a positive account of the shadow in front of it.

### Nine of the ten capture styles overrule the lighting id

A capture style says what the camera IS, and it **removes look fields rather
than editing anybody's sentence**. Cinema is the only one of the ten that leaves
the lighting axis alone; the other nine replace it with their own clause —
*"the screen itself as the key"*, *"the room's own overhead fittings alone, flat
and even across the whole space"*, *"a hot centre where the on-body lamp reaches
and steep falloff past it"*. Read `capture_overrides` on the response rather
than guessing, and check `list_capture_styles` for the exact clause.

**The style protects you from your look. Nothing protects you from your own
words.** A body cam lot whose prompt says "one hard key from the left" sends
the style's available-light clause and your key light together.

**And nothing protects you from the look's note either.** The note is appended
verbatim and is deliberately outside the set of axes a capture style may
suppress — so a light sentence parked in the note survives a camera that has
already decided the light, on every shot in the lot, silently. A brand kit's
palette rides in that same note, so it may already hold words you did not type:
read it with `get_project` before adding to it.

---

## WHERE A LIGHT FACT BELONGS

Most bad light is a fact written in the wrong layer — six times where once would
have done, or once where it needed to be six.

| the fact | where it goes | why not in the shot's prompt |
|--|--|--|
| the whole piece is lit hard, or by practicals | the look's lighting id, via `set_lot_look` | typed once, and it cannot drift between shots |
| the key stays camera left throughout | the look's note, since no id says direction | one sentence, appended to every shot; remember no capture style suppresses it |
| the grade is warm, or teal and orange | the look's palette | a grade is applied to the whole frame; it cannot light one side of a face |
| this camera is a webcam, a dash cam, a drone | the capture style, `list_capture_styles` | it splices a whole lighting clause and overrules the id |
| the exact source, side, shadow and highlight in THIS frame | the prompt | nothing else knows |
| the light this clip opens on | `set_start_frame` | a picture states four things at once that four sentences state badly |
| the light in a still that is otherwise finished | `edit_still` | it keeps the frame you paid for |

Two mechanical notes that bite specifically on light:

**A still keeps the light and drops the motion.** Composing for an image drops
every movement phrase, because a single frame can only answer "smooth dolly
moves" with blur — but the lighting phrase still goes out. Light is the one axis
that reads the same on a still and on a clip, which is what makes a lot's stills
and clips look like one shoot.

**A spoken shot gets nothing appended at all.** Its prompt is the script, read
aloud, so a lighting sentence written there is a sentence the voice says.

**End your prompt on a full stop.** Without one, the appended clause is
comma-spliced onto whatever you finished with, so *"…the label edge catching a
thin white line, low golden sun, long shadows"* reads as a property of the
label.

**Keep light out of `negative_prompt`.** Several video adapters never read it,
and the stills adapters append it to the prompt as "Avoid: …", which puts the
harsh shadow you excluded straight into the description. See lotgen-prompt for
which providers read that field at all.

---

## A PICTURE OUTRANKS A SENTENCE

The most reliable lighting instruction in this product is not a sentence. It is
`set_start_frame`: the shot renders image-to-video from a picture you already
hold — a still take, a product photograph, a creator plate — and the clip
inherits that frame's direction, hardness, colour and level rather than
re-deriving them from prose.

Three consequences worth having:

1. **Once the frame is set, stop describing the light.** A clip whose prompt
   describes light that differs from its own opening frame is a clip that
   relights itself somewhere in the middle. Write the movement and leave the
   light to the picture.
2. **A frame you can generate cheaply is a light test.** Render the still,
   relight it with `edit_still` until it is right, then mount it as the opening
   frame. Stills are the cheap end of this product and clips are the expensive
   end.
3. **References carry light too.** A product shot on a white sweep under a big
   soft source pulls a reference-driven render towards a white sweep under a big
   soft source. If the lot is meant to be a dim bar, say what the light does to
   the product in words the reference cannot contradict — the bottle's rim
   catching the lamp — or supply a reference shot in that light.

---

## WHEN THE LIGHT COMES BACK WRONG

Name the layer before changing a word. Re-rolling the same prompt against a new
seed is the most expensive way to learn nothing.

| what came back | where the fault lives | the move |
|--|--|--|
| flat and frontal, every shot | nobody stated a direction | one sentence per shot, or a direction in the look's note |
| the subject merges into the background | there is no backlight in the vocabulary | add the rim as a described bright line |
| light drifts between shots of one lot | the fact is in six prompts instead of the look | `set_lot_look`, then delete the repeated sentences |
| the id did nothing at all | the id may not be one `cinema_vocabulary` knows — an unknown id is dropped silently, not injected as text | re-read the ids in the same session you use them |
| the id did nothing, and it is spelt right | a capture style is suppressing that axis | read `capture_overrides`; the camera owns the light here |
| lighting is neither what you typed nor what you set | the prompt and the id are both describing the light | delete one of them; keep the one that is true of the whole lot in the look |
| light relights itself mid-clip | the prompt argues with the opening frame | describe the movement, not the light |
| one frame, right except the light | not a prompt problem | `edit_still`, which keeps the take |
| a clip whose grade is wrong but whose motion is right | not a prompt problem | `restyle_take`, which transforms every frame and leaves the motion alone |

Then change one axis, hold the seed where the provider accepts one, re-read with
`get_brief` before writing, and price the retry with `estimate_render` or a
`dry_run` on `render_scene` before anybody says yes. `select_variant` decides
which of the two takes is the shot's answer once both exist; the loser costs
nothing to keep.

---

## CHECKLIST

1. Does the prompt name a **direction** — which side is bright, which side goes
   dark — given that not one of the eight lighting phrases does?
2. Is hardness stated where it is visible: the **edge of a shadow** and the
   **size of a highlight**, rather than as "harsh" or "soft"?
3. Is there a **source you could point at** — a lamp, a window, a screen, a sky
   — rather than a quality?
4. Have you said what the light **lands on** and where it **stops**?
5. Is the subject separated from the background by a described **rim or
   backlight**, since no id supplies one?
6. Is colour carried by a **named source** rather than by a number, and is any
   whole-lot colour in the palette instead?
7. Have you deleted every word that duplicates the appended phrase — no second
   "soft wrap" under soft box, no second "long shadows" under golden hour?
8. Does anything you typed **argue with the lighting id**, and if the lot uses a
   capture style, have you read `capture_overrides` rather than assumed it?
9. Is the look's **note** free of a light sentence the camera has already
   decided — remembering that no capture style suppresses the note?
10. Everything positive: no "no shadows", no "avoid", and no light requirement
    living only in `negative_prompt`?
11. If the clip has an opening frame, does the prompt leave the light to it?
12. Priced with `estimate_render` or a `dry_run`, and said out loud that nothing
    has rendered and nothing has been spent?
