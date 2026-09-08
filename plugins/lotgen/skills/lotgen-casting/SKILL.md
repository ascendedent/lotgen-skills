---
name: lotgen-casting
description: >
  How to describe a person so the same person comes back in every frame. Use when the
  user says "it is a different woman in each shot", "the face keeps changing", "make her
  look consistent", "describe the model", "write the character", "invent a creator",
  "what should I put in distinguishing features", "her hair changed", "the face drifted",
  "this shot does not look like the plate", "should I say she is 34", "make him look like
  [a famous actor]", or hands you a photo and asks what to write around it. Identity here
  is a reference photo carried on every shot — a likeness held, not a model trained — so
  the words you put beside that photo decide how well it holds. Read before writing a
  persona, a plate, or any prompt whose subject is a human being.
---

# Casting

There is no trained model of anybody in this product. A person is **one
reference photograph, mounted on every shot**, plus a sentence telling the
model what that photograph is. The photograph is not the whole mechanism. The
sentence is half of it, and the sentence is the half you write.

Everything here is free. Nothing on this page renders; the money moves at
`render_scene` and `render_project`, and re-rolling a face is the second most
expensive way to fix a description that was wrong the first time.

---

## THE TRAP, FIRST

**A photodump prompt carries the identity sentence inside its own prompt text,
and rewriting the prompt with `update_scene` deletes it.** The composed shot
ends:

> The exact same person as in the reference photo, identical face, hair and
> build.

That line is not attached to the reference. It is not part of the reference
pack, it is not restored by re-mounting the plate, and nothing warns you. A
"tidied up" prompt therefore still shows the person's photograph mounted, still
reports the reference count you expected, and has stopped telling the model
that the photograph is the subject. The face drifts across the whole roll and
every diagnostic says the references are fine.

It is worse than it looks, and here is why. A shot whose only reference is a
creator plate emits **no reference legend at all** — the legend names references
only when at least one of them has a recorded role, and a creator has none, on
purpose, because captioning a photograph of a person as "product photo" is a
lie the model acts on. So on a photodump or a talking-head shot, that one
sentence in the prompt is the *only* thing in the whole payload that says the
attached picture is the person.

Edit around it. If you have already replaced a prompt, put the sentence back
verbatim before you spend anything.

---

## WHAT HOLDS, IN ORDER

A reference holds a likeness; it does not pin one. Write so that the things at
the top of this list carry the most information, because they are the things
that survive a re-render.

1. **Anchors** — small, unique, fixed marks. Freckles, a scar, a mole, a gap
   tooth, a widow's peak, an asymmetric brow.
2. **Silhouette and wardrobe** — what a person is at twenty metres.
3. **Hair state** — length, parting and how it is *worn*, not its colour.
4. **Face proportion** — holds only while the lens and the distance stay near
   the plate's. A wide lens at close range renders a different geometry and no
   reference argues with optics.
5. **Skin tone** — the first thing a grade takes off you.
6. **Apparent age** — drifts by a decade in both directions and is the least
   worth spending words on.

A reference plate has held a face across a six-shot dump when it was measured.
Expect a roll to hold across most of its frames and to miss some. The answer to
a miss is `render_scene` on that shot and `select_variant` to mount the keeper,
not a rewritten persona.

---

## ANCHORS, AND WHY A FACE WITH NONE DRIFTS

`distinguishing` is the field that does the most work of any input on the
character sheet, and it does the most work for a mechanical reason: everything
else you can type about a face describes a *category* of face. Warm olive skin,
dark brown hair, an average build and hazel eyes name a very large set of
people, and a model asked for a member of a large set returns the centre of it.
The centre is different every time it is drawn. That is the drift.

An anchor is a fact that belongs to one person. It gives the render something
to check itself against, and it gives *you* something to check it against,
which matters just as much — "is this the same person" is an unanswerable
question about a generic face and a five-second question about a face with a
scar through one eyebrow.

Three tests before you commit an anchor:

**Does it survive the grade?** Colour anchors die in a monochrome set. If the
lot's palette is **mono** — appended phrase, "black and white" — or the dump's
aesthetic is **editorial_bw** — "High-contrast black and white editorial
photograph, deep blacks, visible grain" — then auburn hair and green eyes are
not anchors, they are noise. Shape survives a grade; hue does not.

**Does it survive the resolution?** A gap tooth is a superb anchor in a
beige-studio portrait and invisible through the **security_cam** capture style,
whose grade clause is "desaturated, low contrast, low-bitrate smearing wherever
something moves", or **home_cam**, "low resolution, soft detail, visible
compression blocking". Grain does the same thing: **16mm** appends "heavy
grain" and **vhs** appends "soft chroma". Pick anchors at the scale the camera
you chose can actually resolve — a hairline and a jaw survive a compressed
frame; freckles do not.

**Does it survive the expression?** The lower face moves. In a set that
includes **laugh** ("head tilted back slightly") or **spin** ("half-smile"),
the mouth and jaw are in a different configuration in every frame, so anchors
above the mouth carry the identity: brow, hairline, nose, the placement of a
mole. One anchor below the mouth is fine. Three is a face you have described
only in its least stable region.

**Rewrite 1 — the demographic**

> Weak: *"A beautiful 28-year-old Latina influencer, flawless skin, perfect
> smile."*

> Strong, as fields: `presentation` woman · `age` late 20s · `heritage`
> "Mexican-American" · `skin` "warm olive skin, two small dark moles on the left
> cheek" · `hair` "black hair, blunt shoulder-length cut, centre parting" ·
> `eyes` "dark brown eyes, thick straight brows that almost meet" · `build`
> average · `distinguishing` "a narrow gap between the front teeth, a small
> raised scar at the point of the chin, three piercings in the left ear" ·
> `energy` warm

Why: *beautiful*, *flawless* and *perfect* are instructions to move towards the
mean, and the mean face is exactly the face that comes back different. There is
not one checkable fact in the weak line. There are five in the strong one. The
number 28 became a band because the picture cannot show a number and the free
text cannot hold one — see below.

---

## THE ORDER TO DESCRIBE A PERSON IN

Write general to specific, and stop at the person. This is the order the plate
is composed in, so it is the order your free text lands in:

```
age band + presentation      A late 20s woman
heritage                     of Portuguese heritage.
skin, hair, eyes, build      They have warm olive skin, dark brown hair
                             in a low ponytail, hazel eyes and an athletic build.
distinguishing               Distinguishing features: freckles across the nose
                             and cheeks, a small scar through the left eyebrow.
wardrobe                     Wearing a plain black crew-neck.
framing, expression, light   Photorealistic chest-up portrait facing the camera,
                             a warm, open expression with an easy natural smile,
                             soft even studio light, plain warm-grey background,
                             sharp focus on the face.
```

The order is not decoration. General to specific means each clause qualifies
the one before rather than arguing with it — the same reason a shot's own glass
composes ahead of the lot look. Put an anchor first and it reads as the
headline fact about a person you have not yet introduced.

**The noun rule.** The composer appends the noun — "skin", "hair", "eyes" —
only when your text names none of it, so `hair` set to "dark brown, loose bun
with strands falling out" composes as "…with strands falling out hair". This
was measured on the first live persona, whose plate read "…beard to match
hair".

**Rewrite 2 — put the noun where it reads**

> Weak: `hair` = *"dark brown, loose bun with strands falling out"*

> Strong: `hair` = *"dark brown hair in a loose bun, loose strands at the
> temples"*

Why: the sentence the model reads is the composed one, not the field. Naming
the noun yourself puts it in the middle of the phrase where it belongs, and
"at the temples" turns a mood ("falling out") into a location a render can put
strands in. The same applies to `skin` and `eyes`.

---

## AGE AND BUILD: OBSERVABLE, NOT NUMERIC

`age` is a band — early 20s, late 20s, 30s, 40s, 50s, 60s or older — and
`build` is one of five words: slight, average, athletic, curvy, broad. Both are
enums on purpose. A number is not a pixel. Nothing in a photograph is 34, and
"34 years old" in free text is read as the region of the training set where
that phrase appears, which is a wide region.

If age matters, spend the words on what age *looks* like, in the anchor field
where it will do work: "fine lines at the outer corners of the eyes, grey
coming in at both temples" is checkable in a way that "early 50s" is not. Same
for build: "athletic" plus "square shoulders, long neck" beats any measurement
you could type, and a measurement in centimetres is simply ignored.

**Rewrite 3 — the shot that re-describes the person**

> Weak: *"A 30-something blonde woman in a red coat walks through the old town
> of Lisbon, smiling."*

> Strong: *"Walking mid-stride through the old town of Lisbon, late afternoon,
> looking away to one side, caught rather than posed. Wearing the red wool coat.
> The exact same person as in the reference photo, identical face, hair and
> build."*

Why: **the reference is the person, so the prompt should not contain a second
one.** Age, gender and hair colour are already in the photograph. Saying them
again gives the model two sources for one face, and where two sources disagree
a model averages them — which is the drift you were trying to fix, caused by
the fix. Notice what the strong version keeps: the outfit, because at
full-body distance the outfit is the identity, and the identity sentence,
because a reference the model is never told about is decoration.

---

## HAIR IS THE MOST VOLATILE THING ON A HEAD

Hair is the one attribute that changes legitimately between two photographs of
the same person on the same day, so a model has learned that it may. It also
moves with every action you write: the archetypes in a roll include **stretch**
("fixing hair"), **motion_blur** ("clothes and hair moving") and **spin**
("coat and hair in motion"), and the identity clause names hair explicitly for
this reason — "identical face, hair and build".

So describe hair as a **state**, not as a colour, and repeat the state wherever
the action would disturb it.

**Rewrite 4 — hair as a state**

> Weak: `hair` = *"blonde, long"*

> Strong: `hair` = *"ash-blonde hair to the collarbone, worn down, parted on the
> left, tucked behind the right ear"*

Why: "long blonde" is satisfied by a thousand heads. A parting is a line on a
scalp that either is or is not on the left, and tucking one ear clear fixes the
hairline — the most reliable anchor on the head — where the render can see it.
On a shot whose action moves the hair, carry the state forward in the shot's
own words: *"hair lifting off the shoulders as they turn, still parted on the
left"*, not *"hair blowing"*.

**Also:** do not put a hat, hood or helmet on a plate. It hides the hairline
and the ears, and the shots that use it will invent both.

---

## WARDROBE IS IDENTITY BELOW THE NECK

This is the part people skip, and it is the part that decides half a roll.

**In a 26-shot dump, five frames contain no face.** **detail_hands** and
**detail_shoes** both say "no face in frame" in their own text, **detail_outfit**
is a crop from shoulders to hips, **over_shoulder** is shot from behind and
**back_view** is a full back view. Two of those five — **detail_hands** and
**over_shoulder** — are inside the first six archetypes, so even a six-shot
roll has two frames where the face is not carrying anything. In those frames
the wardrobe *is* the person.

Consequences you can act on:

- **One outfit for the whole set.** The `wardrobe` field is appended to every
  shot as "Wearing …", which is what makes a roll read as one day photographed
  rather than a week of them. Changing clothes between shots removes the only
  identity signal the faceless frames have, and removes it from the wide shots
  too, where a face is a few dozen pixels.
- **Adornment is specified in one place and that place is `wardrobe`.** The
  hands archetype used to hardcode "rings, watch" into a prompt the wardrobe
  text was then appended to, so a wardrobe saying no jewellery argued with the
  shot it was attached to. That is fixed; keep it fixed by never naming
  jewellery, watches or glasses in a shot prompt.
- **Wardrobe is where texture goes.** "Oatmeal" and "ribbed" and "oversized"
  are things a camera records. "Chic" and "elevated" are not.

**Rewrite 5 — the negation in the wardrobe**

> Weak: `wardrobe` = *"casual, no jewellery, nothing branded"*

> Strong: `wardrobe` = *"an oversized oatmeal wool knit, straight indigo jeans,
> plain white leather trainers, bare hands, bare neck"*

Why: there is no token meaning NOT, so "no jewellery" is a description
containing jewellery, and this text is appended to **every** shot in the roll —
including the one cropped tight on the hands. "Bare hands" is the same
requirement written as something a camera can record, and it lands exactly
where it is needed.

---

## EXPRESSION, AND WHERE IT BELONGS

Three different places, and putting it in the wrong one is a doubled
instruction.

**On the plate: the `energy` field, and nowhere else.** Six ids, each of which
composes a fixed phrase into the plate — warm gives "a warm, open expression
with an easy natural smile", deadpan gives "a level, deadpan expression with a
hint of dry humour", polished gives "a composed, editorial expression, chin
level, unhurried". Typing "smiling warmly" into `distinguishing` or `style` on
top of one of those puts two expression clauses in one sentence, and a model
handed both averages them into neither.

**On a still: a state is fine.** A photograph has no time, so "shoulders
loose", "chin slightly up", "eyes just off camera" are all things a single
frame can be.

**On a clip: a state is not an instruction.** "She looks happy and confident
throughout" describes no event, so the model invents events to fill the
seconds — which is where the second gesture at the tail of a clip comes from.

**Rewrite 6 — expression on a clip**

> Weak: *"She looks happy and confident throughout, smiling warmly at the
> camera."*

> Strong: *"The level expression breaks into a laugh as they look up, and
> settles."*

Why: one change, and it ends. Note the missing pronoun — deliberate, and the
UGC planner does the same thing throughout for the same reason: the reference
image decides who is on camera, and a sentence guessing "she" overrides it in
the one place a model will listen.

And note the cost of the expression you chose. The wider the expression, the
more of the lower face is deformed, so a set built on big expressions needs its
anchors above the mouth.

---

## A CELEBRITY OR A DEMOGRAPHIC BOTH RETURN AN AVERAGE

They fail the same way, for the same reason, and neither is a shortcut.

**A celebrity name** returns the mean of every image associated with that name
across twenty years, several hair colours and a lot of retouching. It is not
one face, it is a cloud, and a cloud is what drifts. It is also somebody's
likeness, which is a separate and larger problem than a bad render.

**A demographic phrase** — "a 30-something Latina woman", "a typical British
mum", "a Gen-Z creator" — names a distribution rather than a person. The model
draws from the middle of it, and the middle moves every time. This is precisely
the failure the reference mechanism exists to fix: an adjective in a prompt
renders a different woman in every shot.

**Rewrite 7 — the look-alike request**

> Weak: *"Cast someone who looks like [a well-known actor], same vibe."*

> Strong: build the persona from what you actually wanted off that face —
> `heritage`, `hair` as a state, `build`, and above all two or three anchors —
> render the plate, and `cast_creator` on the take you like. From then on the
> person is a photograph, and photographs do not drift towards anybody.

Why: the thing you wanted was a specific face. Naming a famous one asks for a
cloud; casting a plate gives you a fixed image that every later shot is held
to, reusable for nothing, forever.

---

## THE PLATE IS NOT A PICTURE

The plate is the reference every later shot is measured against. It is a
document photo and it should be dull. Everything that makes a good photograph
makes a bad plate.

| the plate | the shots that use it |
|--|--|
| chest-up, frontal, square to camera | any framing, any distance, any angle |
| soft even light, plain warm-grey ground | the lot's own light and setting |
| sharp focus on the face | whatever depth the shot wants |
| the full attribute list, in composer order | **no attributes at all** |
| expression set once, by `energy` | expression as an event in the action |
| no setting, no verb, no story | one verb that ends |
| no hat, no sunglasses, no hair across the face | whatever the scene needs |

**Rewrite 8 — the plate written as content**

> Weak: *"Stunning portrait of Maya at golden hour on a Lisbon rooftop, wind in
> her hair, laughing, shot on 35mm."*

> Strong: the composed plate — *"A late 20s woman of Portuguese heritage. They
> have warm olive skin, dark brown hair in a low ponytail and hazel eyes and an
> athletic build. Distinguishing features: freckles across the nose and cheeks,
> a small scar through the left eyebrow. Wearing a plain black crew-neck.
> Photorealistic chest-up portrait facing the camera, a warm, open expression
> with an easy natural smile, soft even studio light, plain warm-grey
> background, sharp focus on the face."*

Why, item by item: golden hour puts half the face in shadow and bakes an amber
cast into a skin tone twenty-six later shots will inherit. Wind hides the
hairline. Laughing closes the eyes and moves the jaw. 35mm film grain eats
freckles — the anchors this persona was built on. And a rooftop is a setting,
which a reference must not have, because everything in the plate is something
later shots may copy.

**Judge the plate as a reference, not as a photograph.** Re-roll it until the
eyes are open, both ears are visible, the hairline is clear and the anchors are
legible at a glance — a plate is one still, and it is the cheapest render in
the whole workflow to repeat. Then `cast_creator`, which is free and makes the
person reusable across dumps, ads and lipsync clips.

---

## WHERE THE CAMERA FIGHTS THE LIKENESS

The words a lot appends are not neutral about faces. Check these before
blaming the description.

- **Lens.** The **wide** id appends "18mm wide lens, deep space". A face at
  18mm has a larger nose and smaller ears than the same face at 85mm, and no
  reference overrules optics. On any shot that has to carry the person, set the
  shot's `camera` `lens` to **portrait** ("50mm lens") or **long** ("85mm lens,
  compressed background").
- **Selfie beats already ask for the distortion.** The **mobile** capture
  style's on-camera clauses include "the face close enough that the perspective
  enlarges it slightly". Do not add a wide lens on top of that; two wide
  statements make a caricature.
- **Aperture.** **wide_open** appends "f/1.4, very shallow depth of field",
  which softens ears, hairline and jaw — three of the places identity lives.
  **f56** ("f/5.6, balanced focus") is the safer default for a face you need to
  hold.
- **Lighting.** **hard** appends "single hard key, deep falloff" and **neon**
  appends "neon spill, saturated coloured light". Both are legitimate looks and
  both remove information from a face: one by hiding half of it, the other by
  recolouring skin so a skin-tone anchor stops meaning anything. The **noir**
  genre goes further — "hard shadows, venetian light, moral fog" puts stripes
  across a face.
- **Capture style overrules the lot's look on the axes it names**, and the
  response says which. So if you set a soft key to protect a face and then
  chose a capture style, read the overrides back before assuming the face is
  lit the way you asked.
- **Two cameras have no face at all.** **body_cam** and **helmet_cam** put the
  lens on the person, so their own face is behind it; a talking-head structure
  on either is refused by name before anything is created. That is not an
  identity problem to solve, it is the wrong camera.
- **A one-reference model sends the face and nothing else.** Where a model
  takes a single reference, the creator leads the pack and the product stills
  stay behind. The face holding while the product does not is that, not a
  casting fault.

---

## WHAT A MODEL DOES NOT ANSWER TO, ABOUT A PERSON

| what gets typed | what happens |
|--|--|
| "the same woman as the last shot" | nothing — a sentence has no memory of a render |
| "34 years old", "five foot nine" | nothing a frame can show |
| "looks like [a famous actor]" | the average of a cloud of images |
| "beautiful", "flawless", "perfect skin" | a push towards the mean face, which is the drifting face |
| "no jewellery", "without glasses" | jewellery, and glasses |
| "consistent across all shots" | an instruction to a person, not a render |
| "she", "her" on a shot with a reference | a guess that can overrule the photograph |
| "freckles across the nose, a scar through the left eyebrow" | two facts the next render can be checked against |
| "the exact same person as in the reference photo" | the sentence that makes the mounted picture do work |

---

## FIXING ONE DRIFTED FRAME WITHOUT RE-RENDERING

A single bad face in an otherwise good roll is not a re-plan.

- `render_scene` on that shot alone, then `select_variant` to mount the keeper.
  The bad take survives either way.
- Or `edit_still` on the take you already paid for, with the instruction
  written the way you would say it to a retoucher: *"replace the face with the
  attached reference, keep the lighting and the hair"*. The shot's own
  references are carried in after the source, so a face already mounted on that
  shot is available to swap in — on the **firered** and **kontext_multi** tiers.
  **kontext_max** takes the source alone and ignores references, so a face swap
  asked for on that tier has nothing to swap in.
- Either way, price it before you spend: `estimate_render`, or `dry_run`.

---

## CHECKLIST

1. Does the persona carry at least two anchors that are unique to one person,
   or is every attribute a category?
2. Do those anchors survive the grade, the resolution and the expressions this
   set actually contains — colour anchors out of a monochrome or grainy set,
   fine detail out of a low-bitrate camera, lower-face anchors out of a set
   full of laughter?
3. Is the description in composer order — age and presentation, heritage, then
   skin, hair, eyes, build, then `distinguishing`, then wardrobe — and does
   each free-text field name its own noun?
4. Is age a band and build a word, with the observable evidence for either put
   in `distinguishing` rather than as a number?
5. Is hair written as a state — length, parting, worn how — and carried forward
   in any shot whose action disturbs it?
6. One outfit for the whole set, all adornment in `wardrobe`, and stated
   positively?
7. Expression set once: `energy` on the plate, a state on a still, one change
   that settles on a clip — and no pronoun anywhere the reference decides who
   it is?
8. Does the plate have plain ground, even light, open eyes, a visible hairline
   and nothing it would be bad to inherit — and was it judged as a reference
   rather than as a photograph?
9. Was the winning plate actually passed to `cast_creator`, so the same person
   costs no upload next time?
10. Do the shot prompts re-describe the person anywhere, and does every one of
    them still contain the identity sentence after any edit with
    `update_scene`?
11. Does the lens, aperture or lighting you chose remove the information the
    likeness needs, and have the capture style's overrides been read rather
    than assumed?
12. Said out loud: nothing on this page has rendered and nothing has been
    spent?
