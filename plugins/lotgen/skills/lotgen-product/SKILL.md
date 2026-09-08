---
name: lotgen-product
description: >
  Making a physical product look like itself on screen — hero angle, material, reflection,
  scale, label text, surface, and the liquid and steam that sell freshness. Use when the
  user says "it does not look like our product", "the label came out as gibberish", "the
  bottle looks like plastic", "the tin is floating", "make it look premium", "shoot the
  packshot", "the colour is wrong", "our red went orange", "the label changed between
  shots", "how do I get a good hero shot", or when a product clip came back looking like a
  render of a different object. Read before writing any prompt whose subject is a thing you
  can hold.
---

# Making a product look like itself

**A video model has never seen your product. It has seen ten million products.**
Left alone it renders the average one: injection-moulded, slightly glossy,
uniform in colour, standing on nothing, wearing a label it invented. Every
rule below closes some part of the gap between that average object and yours.

Nothing in this file spends. The words are free; `render_scene` and
`render_project` are where the money moves, and a shot rewritten ten times
before the first render costs the same as one rewritten none.

---

## THE REFERENCE DOES MOST OF THE WORK

A saved product carries its own photography, and every lot built from it
mounts that photography on every shot. That single fact outranks everything
else in this document: **a described product renders a plausible object, a
referenced product renders yours.** `save_product` then `add_product_images`,
or `extract_product` to read the page first.

Three things about how the pack reaches the model, because they decide what
your words should and should not say:

**The pack sorts itself, and it is not insertion order.** A creator plate
leads, then the product map, then the packshot, then everything else, and the
brand mark goes dead last — it is the one reference that is not a picture of
the thing being filmed, so on a model that takes exactly one reference it is
the first thing dropped rather than the only thing sent. Pass
`product_asset_ids` to overrule the default selection; pass `image_asset_ids`
for stills already in the workspace. Never a signed URL — the renderer cannot
fetch one.

**The model is told what each picture is.** A legend is appended naming the
references in send order — "References, in order: 1. product map; 2.
packshot." — but only for the ones carrying a role. An untagged image is
called a reference and nothing more, and where nothing can be named the legend
is dropped entirely. So tagging photography in the library is not filing: it
is the caption the model reads.

**Therefore: stop describing what the picture already shows.** Every sentence
of geometry you write is a second opinion the model has to average against the
reference, and the average of two descriptions of one bottle is a third bottle.

> **Weak.** *A tall slim matte black bottle with a wide silver cap and a small
> square label two thirds of the way up, on a table.*
>
> **Strong.** *The bottle from the packshot stands on a dark walnut counter,
> turned a little off square. One large soft source high and behind it.*
>
> **Why.** The first prompt is competing with the photograph. Proportion,
> finish, cap and label position are all in the reference already, stated more
> precisely than words can state them; repeating them in prose gives the model
> two sources that disagree in the third decimal place and lets it split the
> difference. The prompt's job is everything the reference cannot carry: where
> the object is, what it is standing on, where the light comes from, and what
> happens in the next four seconds.

The corollary is a hard one. **A prompt that contradicts the reference does
not overrule it and does not lose to it — it averages with it.** A matte black
bottle in the pack and "glossy white bottle" in the prompt gives you a
semi-gloss grey one, in a take nobody can explain. If the object must change,
change the reference or use `edit_still` on a still of it; do not argue in
prose.

---

## THE HERO ANGLE

**Three-quarter, slightly above.** Turn the object roughly thirty to forty
degrees off square to the lens and put the camera a little above its top edge.

Straight-on shows one face and no depth, so the model has no cue for the sides
and renders what amounts to a flat decal standing up — the specific
uncanniness of "it looks like a photograph of a picture of our product". A
three-quarter view shows two faces and the top: the shape becomes readable,
the label curves away, and that curve is the thing that tells a viewer the
bottle is round. It also gives the light somewhere to fall off to, which is
where form comes from.

Camera height does its own work. At the object's own mid-height a jar becomes
monumental and the surface it stands on disappears; a little above reads as a
table you are standing at. Below it reads as advertising from 1994.

**When straight-on is right instead:** a genuinely flat product (a card, a
phone face, a book cover, a garment laid flat), where the front IS the object;
and an end card, where the vertical is already asking for the label facing
camera, unobstructed, and the whole job of the frame is the mark.

> **Weak.** *Product shot of the bottle on a table, nice lighting, premium
> look.*
>
> **Strong.** *The bottle stands on a dark walnut counter, turned about thirty
> degrees off square so the front face and the left shoulder both show, camera
> slightly above the cap looking down onto it. One large soft source high and
> behind, a dark card just off frame to the left holding a black edge down the
> silhouette.*
>
> **Why.** Two faces, a top, a named surface, one light with a direction and
> one negative fill that draws the edge. "Premium look" corresponds to no
> pixel; the black edge down the silhouette is what people are actually
> looking at when they say a shot looks expensive.

Set the glass in the shot's `camera` rather than typing it into the sentence.
Ids come from `cinema_vocabulary`, and the choices matter more here than
anywhere else in the product:

- **Long, 85mm** appends *"85mm lens, compressed background"* and **Telephoto,
  135mm** appends *"135mm telephoto, heavy compression"*. These are hero
  lenses: compression keeps the object's sides parallel and keeps the
  background from crowding it.
- **Wide, 18mm** appends *"18mm wide lens, deep space"*. On a small object
  that reads as a phone snapshot — enlarged front, receding sides, curved
  edges. Correct for a phone capture style, wrong for a hero.
- **Macro** appends *"macro lens, extreme close focus"*. That is the detail
  beat, not the hero beat.
- Aperture is the one people get backwards. **f/1.4, shallow** appends *"f/1.4,
  very shallow depth of field"*, which throws the far edge of a curved label
  out of focus — and "the label is unreadable" is usually this, not the model.
  **f/5.6** appends *"f/5.6, balanced focus"* and **f/11, deep** appends
  *"f/11, deep focus throughout"*. A product that must be legible end to end is
  shot stopped down, exactly as it would be on a real table.

---

## MATERIAL IS THE WORD THAT MUST BE THERE

The default surface of a generative object is plastic: smooth, uniform,
faintly glossy, no grain, no wear. Naming a material is how you leave that
default, and the naming only works if you also say **what the finish does to
light** — because that, and not the noun, is what the model renders.

| the material | the clause that makes it read |
|--|--|
| brushed metal | the grain running vertically, breaking the highlight into a soft streak rather than a mirror |
| polished chrome | a mirror carrying the room, one hard bright band and a dark edge beside it |
| matte soft-touch | a wide dull sheen with no hot point, edges fading rather than catching |
| unglazed ceramic | a chalky micro-texture, the light sitting on the surface instead of in it |
| transparent glass with liquid | the liquid darker at the base, the back wall of the glass visible through the front, a bright line where the meniscus meets the side |
| frosted glass | light diffusing a few millimetres into the wall, the contents readable as shape and not as detail |
| coated card | a flat even face with a faint sheen along one edge only, the printed area slightly deader than the board |
| leather | grain visible under raking light, the highlight broken up rather than continuous |

> **Weak.** *A premium metal water bottle, high quality, luxury finish.*
>
> **Strong.** *A brushed aluminium bottle, the grain running vertically from
> base to shoulder so the highlight breaks into a soft vertical streak rather
> than a mirror. The cap is matte soft-touch and takes a wide dull sheen with
> no hot point.*
>
> **Why.** "Metal" resolves to plastic with a shine on it, because that is
> what the average of ten million product images looks like. The grain
> direction is what makes it aluminium; the difference between the streak on
> the body and the sheen on the cap is what makes it two materials rather than
> one moulded object.

Note what the capture style has already said before you write any of this. The
cinema style splices *"a commercial grade: deep blacks, controlled highlights,
specular sheen held on the product"* into every shot — so on a cinema lot,
**do not type "specular highlights on the product"**; type where the highlight
sits and how the material breaks it. The phone style splices *"phone HDR tone
mapping, low contrast, lifted blacks"*, which flattens exactly the specular
detail this section is about: a surface story shot on a phone is a surface
story you have thrown away on purpose. `list_capture_styles` carries the exact
clauses, and `capture_overrides` on the create response says which look axes
the style took.

---

## REFLECTION NEEDS SOMETHING TO REFLECT

**A reflective object is a picture of its environment.** Chrome has no colour
of its own; glass has almost none. Describe a mirror-finish bottle in an
unspecified room and the model has nothing to put in the mirror, so it renders
grey — the flat, dead, slightly plastic grey that people describe as "it looks
fake and I do not know why".

The fix is to describe the environment as things with shape, position and
value, because that is what will appear in the surface:

- **A large bright source**, and where it lands: *"reflected as a single tall
  band down the right shoulder"*.
- **A dark card**, which is the half people miss. The black edge along the
  opposite side is what separates the object from a pale background. Without
  it a white bottle on a white sweep has no silhouette.
- **A ground return** — the surface it stands on, showing as a band across the
  bottom of the object.

> **Weak.** *A chrome perfume bottle on white, studio lighting, reflective.*
>
> **Strong.** *A chrome bottle on a white paper sweep, a tall soft source to
> the right reflected in it as one bright vertical band, a black card to the
> left drawing a dark edge down the opposite side, the pale floor showing as a
> soft grey band across the bottom third of the glass.*
>
> **Why.** Three named things in three named places, so the mirror has content
> and the shape reads. "Reflective" is a property the model already assumed
> and can do nothing further with.

If the lot's look already sets lighting to **Soft box**, the phrase *"large
soft source, gentle wrap"* is appended for you — so **do not spend a sentence
building the softbox**; spend it on the reflection the softbox makes. Same for
**Hard key**, which appends *"single hard key, deep falloff"*: that is a
texture light and a black-side light at once, excellent on brushed metal and
leather and bad on any label that ends up on the dark side.

**Overcast is the one to think twice about.** It appends *"flat overcast
light, no shadow"* — even, shadowless, and it removes both the modelling that
shows form and the contact shadow that gives scale. If you want a shadowless
frame, describe the condition that produces one: an even white environment
lit from every side. Naming the absence of a shadow is not a reliable way to
remove one.

---

## SCALE: WHY A PRODUCT FLOATS

A model has no units. A bottle alone on a plain ground is any size at all, and
the frame reads as a render rather than a photograph. Three cues fix it, and
the first is nearly free:

**1. The contact shadow.** The single most recognisable artefact in generated
product imagery is an object hovering a couple of millimetres above the
surface it is supposedly standing on. Write the contact: *"a tight dark
contact shadow directly under the base, softening outward over about a
centimetre"*. That one clause does more for realism than any amount of
lighting vocabulary.

**2. A neighbour of known size.** Hands are the strongest and the most
expensive — a hand is unambiguous scale and it is also five fingers the model
must get right. The physical product vertical already writes *"the product in
use, hands in frame, the label still readable"* onto the demo beat, so on
those shots the hand is coming whether or not you asked. Where you want scale
without hands, use a surface with a known texture instead: a marble worktop
with visible veining, linen with a readable weave, a plaster block.

**3. Focus behaviour.** Extreme close focus tells the viewer the object is
small; deep focus across a whole table tells them it is not. This is the
`camera` field doing scale work, not the prose.

> **Weak.** *A jar of moisturiser floating on a pastel background, soft and
> dreamy.*
>
> **Strong.** *A 50ml glass jar sitting on a pale plaster block, a tight dark
> contact shadow directly under the base softening outward over about a
> centimetre, a folded linen napkin behind it with the weave visible.*
>
> **Why.** "Floating" was granted. The named volume, the contact shadow and
> the weave give three independent size cues, and the weave also gives the
> softness that "dreamy" was reaching for.

---

## LABEL AND TEXT: THE THING MODELS CANNOT DO

**Video models do not spell. They render letterforms as texture.** Anything
longer than a word, at any size where you could read it, comes back wrong —
and it comes back wrong *differently on every take*, which is why a four-shot
lot with a legible label has four different labels and a viewer notices at the
cut.

There are two honest strategies and no third one.

**Frame so it is legible.** Which means: give it the best possible conditions
and expect to fix it after. One frame, held still, square-ish to the lens,
stopped down, with the packshot mounted as the reference. Make it a still —
`kind` set to image — because a still has to spell the label right once, where
a clip has to spell it right in every frame in sequence. Then use that
approved still as the clip's opening frame with `set_start_frame`, so the
clips inherit an object somebody has already looked at. The built-in ad
formats already do half of this on their own: the end card of a TV spot and of
a hyper-motion cut is `kind` image on purpose, because paying video rates to
hold a frame is money for nothing — and the same choice is what gives the type
a chance.

**Frame so it is not present.** Turn it away, crop past it, throw it soft, or
put it in shadow — and say so positively, because naming the thing you want
excluded is how it arrives.

> **Weak.** *A tube of cream on a bathroom shelf, no text visible, no
> branding.*
>
> **Strong.** *A tube of cream lying on its side with the crimped seam towards
> the lens and the printed face turned away to the left, the cap sharp and the
> body falling soft behind it.*
>
> **Why.** The weak version puts "text" and "branding" into a description of
> what is on screen, which is a good way to get a busy invented label. The
> strong version never mentions type at all: it just describes an object
> oriented so the printing is on the far side.

> **Weak.** *The can with the label facing camera, brand text readable and
> correct, in a kitchen, camera orbiting.*
>
> **Strong.** *(as a still, with the packshot as the reference)* *The can
> straight on and still, filling the middle third of the frame, the printed
> face square to the lens and evenly lit, a pale worktop running out of focus
> behind it.*
>
> **Why.** "Correct" is an instruction to a person, not a description of a
> picture. And the orbit is the real culprit: **a camera move decides how much
> unseen geometry the model must invent.** Push in and pull out reveal nothing
> new. Orbit — *"camera orbits the subject"* — demands the entire back of the
> object, which no reference shows, and that is where a label migrates,
> duplicates or grows a second set of words halfway through the clip. Tilt up
> demands the top. If the label matters, move the camera along the axis it is
> already on.

Two more facts worth holding:

- The **physical product** vertical writes *label legible* into the hook,
  product, solution and cta beats and *the label still readable* into demo.
  That is the right instruction to a renderer that has a photograph to work
  from, and it is a liability on a lot with none. The vertical enforces this
  itself: it refuses a lot with no product image, by name, before anything is
  created. **Software** goes the other way for the same reason — *no invented
  UI copy*, *no invented labels* — because an interface is nothing but text
  and a model will happily fill a screen with convincing nonsense.
- After the fact, `edit_still` can change a label's colour or finish
  (*"make the label deep green"*) and `erase_from_take` can take a wrong mark
  off a clip by naming it. Neither can spell. On a restyle, `note` is where
  you say *"keep the product label legible"* — a qualifier on the preset, not
  a promise.

---

## SURFACE AND GROUND

"On a table" is not a surface. Half the light in a tabletop shot is bounced off
whatever the object is standing on, and all of the reflection under it is.

Name three things: **the material, its value, and its finish.** Honed grey
limestone, warm walnut, brushed steel, raw plaster, unglazed terracotta —
each returns a different colour into the shadow side of the product, and that
returned colour is most of what makes a shot look shot rather than composited.

**Say where the background break falls.** In a tabletop frame there is a line
where the surface meets whatever is behind it. Leave it unsaid and the model
tends to put it across the middle of the object, cutting it in half with a
tonal edge. Put it deliberately: above the lid, or below the shoulder.

**A plain white sweep is a decision, not a default.** It removes the ground
cue entirely, which is why it needs the contact shadow more than any other
setting, and it gives a white or chrome product nothing to separate against —
so it also needs the dark card from the reflection section.

> **Weak.** *Product on a table with a blurred background.*
>
> **Strong.** *The tin sits on honed grey limestone, the stone running out of
> focus behind it and meeting a warm plaster wall high in the frame, above the
> lid.*
>
> **Why.** A named stone with a named finish, a named wall with a warmth that
> will show up in the tin's shadow side, and the break placed above the object
> instead of through it.

---

## CATALOGUE SHOT OR IN-USE SHOT

These are two different pictures, people ask for both in one sentence, and a
model given both renders the average — which is a bottle of ambiguous shape
with a hand growing out of it.

| | catalogue | in-use |
|--|--|--|
| the object | whole, unoccluded, centred | partly hidden, held, at an angle |
| hands | none | the point of the shot |
| ground | chosen, controlled, subordinate | a real room that goes on past the frame |
| light | one clear logic | whatever the room does |
| camera | still, or one slow move | follows the action |
| what sells it | the surface and the shape | the occlusion — being covered is what makes it look real |

The trap is that occlusion is exactly what a catalogue shot must avoid and
exactly what an in-use shot needs. That is why they cannot be one prompt.

> **Weak.** *The bottle on a marble counter while someone pours a glass,
> cinematic and premium.*
>
> **Strong — two shots.** *(1) The bottle alone on honed marble, three-quarter,
> stopped down, one large soft source high and behind, a tight contact shadow
> under the base. (2) A hand lifts the bottle out of frame left and pours into
> a short glass until it is two thirds full, then the pour stops. Same counter,
> same light.*
>
> **Why.** A lot exists so that two beats can be two shots. The averaged
> version costs one render and answers neither question; the split version
> costs two and answers both — and each one can be re-rendered on its own when
> only one of them is wrong.

The built-in formats already alternate the two: a TV spot runs wide, then
macro across the surface, then the product in a styled environment, then a
held end card. `list_ad_formats` shows the pattern. Trust the alternation
rather than packing it into a single shot.

---

## LIQUID, STEAM AND CONDENSATION

These are the cues that sell freshness, and they are also the only reason
several product shots are clips at all. **If nothing in a product clip moves,
you have paid video rates for a still** — and a still is both cheaper and
better at holding a label.

**One event, and it ends.** *"A single drop breaks away at the shoulder and
tracks down to the label edge, where it stops."* A drip described without an
end runs for the whole clip and multiplies.

**Condensation is a temperature statement, not an adjective.** Say the glass
came out of the fridge and say what the fog looks like: grey, even, with clear
tracks where fingers have been. "Refreshing" renders nothing.

**Steam and fog are lighting instructions.** They are only visible against a
darker background and lit from behind or the side; front-lit against a pale
wall they vanish, which is why "steam rising from the cup" so often returns a
cup with nothing above it. Write the backlight into the same sentence.

**Pours are short.** Continuous liquid volume is one of the least stable
things a video model does, and it degrades with length. Keep the pour inside
the shortest duration the model will actually make — see lotgen-prompt for
how a requested length gets clamped and snapped — and where you can, open with
the liquid already moving instead of asking the model to start a pour, since
the first moments are where the stream detaches from the bottle.

> **Weak.** *Refreshing ice cold drink with condensation, fresh and crisp,
> beautiful.*
>
> **Strong.** *The bottle straight out of the fridge, the glass fogged grey
> with condensation and clear tracks where fingers have been. One drop breaks
> away at the shoulder and runs down to the label edge, then stops. Lit from
> behind the bottle so the fog reads bright against a dark kitchen.*
>
> **Why.** A cause for the fog, a shape for the fog, one motion that finishes,
> and the backlight without which none of it is visible.

---

## COLOUR ACCURACY AGAINST THE GRADE

The brand colour is a fact about the object. The grade is a fact about the
picture. **They fight, and the grade tends to win, because the look's phrase is
appended after your words and applies to the whole frame.**

What the palette ids actually do to a product:

- **Teal & orange** appends *"teal and orange grade"*: a neutral white pack
  goes cyan in the shadows and a red cap goes orange.
- **Warm** appends *"warm amber and cream palette"*: cool grey packaging goes
  beige.
- **Monochrome** appends *"black and white"*: there is no brand colour left.
- **High contrast** appends *"crushed blacks, clipped highlights"*: the
  clipping erases the specular detail that makes chrome read as chrome, and
  the crush erases the shape of a black product against a dark ground.
- **Pastel** appends *"desaturated pastel palette"*, which is a saturation cut
  applied to your saturated hero colour.

So on a lot where the pack colour has to be right, **leave the palette axis
unset** and put the colour where it belongs: on the material, under a named
light, in the prompt. The brand kit's palette rides as a sentence in the
look's note rather than on the palette axis, and a note is never suppressed by
a capture style — which is why a lot shot on a security camera keeps its brand
sentence and loses its palette id, reported back as `capture_overrides`.

Two capture styles take the palette axis away outright: **security camera**
and **home camera**. Both also splice *desaturated* and *muted colour* into
every shot. A colour story cannot be told through either of them, and that is
the honest reason not to shoot one that way rather than something to work
around.

Film stock matters here too. **35mm film** appends *"halation, organic
grain"*: halation blooms the highlight, which softens the exact specular edge
that makes metal read as metal, and grain lands on top of the fine texture you
were selling. **16mm** is more of both. **VHS** appends *"soft chroma"*, which
is a brand colour dissolving. **Digital cinema** appends *"clean highlights,
wide latitude"* and is the stock that leaves a surface intact — which is why
the TV spot format sets it by default.

> **Weak.** *Brand red packaging, accurate colour, cinematic teal and orange
> grade.*
>
> **Strong.** *(palette left unset)* *The carton in a deep warm red, matte
> board, lit by one large soft source so the red stays even across the front
> face and darkens only at the right edge.*
>
> **Why.** The two halves of the weak version cancel: a teal and orange grade
> is a specific instruction to shift reds, and "accurate" is not an
> instruction at all. The strong version names the red as a property of a
> material under a light, which is the only form a model can act on — and
> then, if the delivered red is still off, `edit_still` changing a label
> colour on one approved still is cheaper and far more exact than another
> round of adjectives.

---

## WHAT A MODEL DOES NOTHING WITH

| what gets typed | what it does |
|--|--|
| "photorealistic 8K product photography" | nothing. And on a still, `resolution` is a token budget of 1K/2K/4K — the delivered frame is whatever the model chooses |
| "accurate label", "correct spelling", "brand-compliant" | nothing. These are instructions to a person |
| "shot on a 100MP medium format back" | close to nothing. The lens and aperture ids do the work a camera name is reaching for |
| "keep the proportions of the reference" | nothing — no model verifies its output against its input |
| "three bottles in a row" | a count it cannot check. Expect two, or four |
| "no reflections", "no distortion", "no text" | names the thing, which is how it arrives. `negative_prompt` is dropped by most adapters here and appended as "Avoid: …" on stills |
| "luxury", "premium", "high-end" | nothing. Black edge, controlled highlight, deep contact shadow — those are what people mean |
| "the same bottle as the last shot" | nothing. Sameness is a reference or a start frame |

---

## THE CHEAPEST ORDER FOR A PRODUCT LOT

Everything before the last line is free.

1. `save_product` and `add_product_images`, with roles tagged, so the pack
   sorts itself and the legend can name what it sends.
2. `run_marketing_recipe` under the physical product or apparel vertical —
   both refuse a lot with no product image before anything is written.
3. `get_brief`, and read the prompts the scaffold and the vertical actually
   composed. The label clause is in there; so is the capture style's grade.
4. Fix them with `update_scene`, passing the `updated_at` you read as
   `if_unmodified_since`. Edit around the spliced clauses rather than
   replacing the whole field — see lotgen-prompt for what a wholesale rewrite
   deletes.
5. Render **one still first**: the hero, `kind` image, with the packshot
   mounted. Look at the object. `edit_still` if the colour or the label finish
   is wrong. `select_variant` when you have two.
6. `set_start_frame` on the clips from that approved still, so four shots
   inherit one object instead of drifting down a continuity chain where each
   shot is a copy of a copy.
7. `estimate_render`, then `render_scene`.

Fixing the object on one still costs one still. Fixing it after four clips
costs four clips, and `upscale_take` will only make a wrong label bigger.

---

## CHECKLIST

1. Is the real product on the shot as a reference, with its roles tagged —
   `ref_count` checked rather than assumed?
2. Does the prompt describe anything the reference already carries, or
   contradict it anywhere?
3. Three-quarter and slightly above, unless the object is genuinely flat or
   this is the end card?
4. Is the material named, **with what its finish does to light**?
5. If anything is reflective, is there something specific in the environment
   for it to reflect — including a dark side?
6. Is there a contact shadow, and one other scale cue?
7. Is the label either given its best chance on a still, or positively turned
   away? And does the camera move reveal geometry no reference shows?
8. Is the ground a named material with a named finish, and is the background
   break placed away from the middle of the object?
9. One shot, one job: catalogue or in-use, never both?
10. If it is a clip, does something move — and does that motion end?
11. Does the look's palette or film stock argue with the colour the pack has
    to be, and has `capture_overrides` been read rather than guessed?
12. Priced with `estimate_render` or a `dry_run`, and said out loud that
    nothing has rendered and nothing has been spent?
