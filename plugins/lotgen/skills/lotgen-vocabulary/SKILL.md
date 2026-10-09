---
name: lotgen-vocabulary
description: >
  What LOTGEN's ids actually MEAN, so an agent picks the right vertical, capture style,
  format and look instead of the one whose name sounds closest. Use when the user asks
  "which vertical is this", "what capture style should it be", "what do these ids mean",
  "why was that combination refused", "why does my look do nothing", "make it look like
  security footage", "is this apparel or a physical product", or when a plan came back
  refused by name and you need to know which of the two things you chose is the problem.
  Read this before passing any id to a studio, a recipe, a look or a restyle. Choosing
  ids never renders and never spends.
---

# The vocabularies

Four ids decide what a lot is: a **format or structure** (what the ad IS), a
**vertical** (what it is FOR), a **capture style** (what the camera IS) and a
**look** (how it is GRADED). They are four separate lists doing four separate
jobs, and the usual failure is not a wrong call. It is the right call carrying
the id whose name sounded closest.

**Nothing here spends.** Every list in this skill is free to read, and all four
ids are chosen while a lot is being planned, before any row is written. Money
moves at `render_scene`, `render_project` and the paid post passes, and nowhere
else.

---

## THE TRAP: THREE VOCABULARIES, THREE ANSWERS TO A TYPO

| you misspell | what happens |
|--|--|
| a vertical id | refused by name — "unknown vertical *x* — see `list_verticals`". Nothing written |
| a capture style id | refused by name, pointing at `list_capture_styles`. Nothing written |
| a look id | **accepted, stored, echoed back to you, and then ignored at render** |

**A look id nothing recognises is dropped, not injected as text.** That is the
right behaviour at render — a prompt should never carry a word the product
cannot define — and it means a single wrong character is not a wrong look, it
is *no look on that axis at all*, with nothing anywhere saying so.

`set_lot_look` returns the look it stored, so the typo comes back in the
response looking exactly like a success. `get_project` will hand you the same
stored value for the life of the lot. **No tool here returns the composed
prompt**, so there is no call that shows you the axis went missing. The dogfood
workspace has carried a lot graded `{"palette": "chartreuse-dreams"}` for
precisely this reason.

So take look ids from `cinema_vocabulary` in the same session you use them, and
copy them rather than typing them.

---

## WHAT A VERTICAL IS FOR

A vertical is a category contract. It fills in what you did not name, appends a
clause to each planned shot, and **refuses inputs it cannot work with before a
single row is written** — so a violation costs one round trip and nothing else.

| vertical | reach for it when | it will not start without |
|--|--|--|
| physical_product | there is a thing you can hold and photography of it, and the label has to stay legible | at least one product image |
| apparel | the product is worn — fit, drape and movement are what sells it, so a static pose sells none of it | a product image, plus a creator in UGC |
| ugc_talking_head | one person, one room, one phone, and the words carry it | a creator, and the brief's problem and benefit |
| digital_saas | there is no SKU to photograph: the interface is the product | the page it is sold from |

Three of them run in both studios. **ugc_talking_head is UGC only** and passing
it to a marketing lot is refused by name — Marketing Studio's spokesperson is
free text on a brief, not a mounted face, so there is nothing for it to check.

**Read `requires` off the row rather than trusting the paragraph above.** It is
literally the predicate the refusal evaluates, and the refusal names *every*
unmet requirement at once — that is deliberate, so an agent can fix its call in
one go instead of four round trips with a person waiting through each.

Two details that decide whether a vertical is doing anything:

- **It fills blanks; it never overrules.** Anything you pass — the `format`, the
  `structure`, the `aspect_ratio` — wins over the vertical's default. apparel
  deliberately supplies *no* script default: which garment story to tell, a
  before-and-after or a day in the life, is an editorial call and guessing it
  would be the convenience overriding the decision it exists to save.
- **The clause is keyed to the shot's role or beat.** A shot whose role the
  vertical does not decorate comes back untouched, which is why a vertical can
  look like it did nothing on a short lot. digital_saas is the clearest case of
  a clause earning its place: every one of its clauses exists to stop the same
  failure, which is a model inventing UI copy that was never in the brief.

The listing tells you which roles are decorated and **not what the clause
says**, on purpose. If you could read the sentence you would be tempted to
write it into the prompt yourself, and then it is in there twice.

---

## WHAT A CAPTURE STYLE SAYS ABOUT WHO WAS HOLDING THE CAMERA

A vertical says what the lot is for; a capture style says how it was SHOT. They
compose — apparel on a phone is an ordinary lot. Without one, UGC renders as
pristine footage, which is the one thing real UGC never is.

Read the ten as an answer to one question: *who was holding it?*

| who is behind the lens | ids | what stays coherent |
|--|--|--|
| an operator, on a lit set | cinematic | anything the look already describes — it is the only style that overrules nothing |
| a broadcast crew | news | a stand-up, an interview, a piece to camera |
| the person in the story, arm's length | mobile | a room they are actually in, a hand bringing something into frame |
| someone who sat down in front of it | webcam | chest-up, close, addressed, lit by the screen |
| worn by the person in the story | body_cam, helmet_cam | their own arms entering from below, whatever they turn to look at — never their face |
| nobody | security_cam, home_cam, dash_cam, drone | only what the mounting can see |

`mount` on each row is the fact to reason from, and it constrains the shot more
than the grade does:

- **security_cam is the one style that makes a demand rather than a treatment.**
  Its clause requires the whole room in frame — walls, doorways, furniture and
  appliances each in its actual place — so a prompt written as a medium
  close-up is contradicted by the style attached to it. Write the room.
- **dash_cam** puts the bonnet along the bottom edge and the road in every
  frame. A shot with no vehicle in the story is not a shot this camera can take.
- **drone** is at altitude looking down. A hand holding a product is not
  available to it at any focal length.
- **helmet_cam** aims wherever the wearer looks and shakes on every footfall, so
  a still, level, composed frame is the one thing it cannot deliver.
- **home_cam** switches to flat grey infrared once the room goes dark. If your
  scene is at night, that is the picture you have asked for.
- **mobile** has a selfie sub-mode — a different lens and a shorter arm — and
  you do not pass it. It is read off whichever beats put the creator on camera,
  because the script already knows and the form must never ask a question that
  has been answered.

---

## THE SIX THAT REFUSE A TALKING-HEAD SCRIPT

Six of the ten cannot carry a script where somebody addresses the lens:
**body_cam**, **helmet_cam**, **home_cam**, **security_cam**, **dash_cam** and
**drone**. Three of those — **security_cam**, **dash_cam** and **drone** — go
further and refuse dialogue outright.

The two refusals are two different facts, and the wording of a rejection tells
you which one you hit:

- **direct_address** — nobody's face is in front of this lens. It is worn by the
  person in the story, or it is bolted to a wall nobody is looking at.
- **dialogue** — nobody is speaking near it at all. A written line rendered
  through an unattended camera is a voice with no source.

**This is a narrative conflict, not a policy.** A talking-head vertical writes
"the creator on camera, eyeline straight to the lens" into the prompt. A body
cam writes "the wearer's own arms entering frame from below" into the same
prompt. One prompt cannot assert both, and a model handed both averages them
into neither — so the pair is refused before anything is created rather than
rendered into a clip somebody pays for.

**You cannot dodge it by changing the script.** No UGC structure is voiceover
throughout; all eleven put the creator on camera on at least one beat. A worn or
unattended camera therefore belongs in Marketing Studio, on a format that needs
nobody on camera.

**And the same rule bites in Marketing, where no line is spoken at all.** Four
of the nine ad formats put a person in front of the lens — ugc, product_review,
try_on_ugc and try_on_pro — and the six styles above are refused against all
four. That check was missing once, on the reasoning that a marketing plan writes
roles and no voice: true, and the wrong conclusion, because it confuses a VOICE
with a FACE. Six styles times four formats composed "a person seated,
mid-sentence" into the same prompt as a camera worn by that person.

**webcam is the near miss and is allowed on purpose.** It is fixed, like four of
the six, and it is the one fixed camera that exists to be talked to. Four styles
in the whole list can carry a face: mobile, webcam, cinematic and news.

---

## THE EIGHT CINEMA AXES

Five belong to the lot, three to the shot.

| scope | axes | set with |
|--|--|--|
| the whole lot | genre, style, lighting, palette, moveset | `set_lot_look` |
| one shot | lens, aperture, move | `update_scene`, in `camera` |

The five lot-wide axes exist so a six-shot lot reads as one film without typing
"same grade as the previous shot" into every prompt. The three per-shot ones are
the exception to that, and they are **composed before** the lot's look, so the
specific qualifies the general: a shot asking for a macro lens has said
something the lot-wide move-set should not argue with.

- `genre` is about content and pacing. `style` is the capture medium — the film
  stock — not the mood. Reaching for a genre when you meant a stock is the most
  common mix-up in this list.
- `note` is free text appended verbatim, for anything the vocabularies do not
  cover, and it is also where a brand kit's palette rides as a sentence.
- Every axis has an off id whose phrase is empty. Setting it does the same thing
  as leaving the axis out.
- **A still drops the move-set and the move.** Asking an image model for "smooth
  dolly moves" asks for movement in a single frame, which it can only answer as
  blur. A spoken shot gets no look at all: its prompt is the script, and a
  native-audio model would read "teal and orange grade" aloud.
- **The look is never written into the shot's prompt.** It is appended at render
  time, so `get_brief` shows you the prompt without it and nothing here returns
  the composed result. `get_project` carries the stored look and the lot's
  capture style, which is how you read back what a lot is actually running
  under — a vertical's clause and a capture style's clauses, by contrast, were
  written INTO the prompt at create time, so `get_brief` shows those and
  `update_scene` can edit them.

---

## WHO OVERRULES WHOM

```
your own field       beats any default a vertical or a format would have supplied
the vertical         refuses the call before a row is written
the capture style    refuses the call before a row is written
the capture style    wins over the look, on the axes it names
the shot camera      composes before the lot look
the lot look         composes last, at render, and never edits your words
```

**A capture style takes four axes and leaves two.** It wins on film stock,
lighting, palette and camera moves — the four things a camera actually decides —
and leaves genre and the note alone. Genre is about content and no camera has an
opinion about it, and the note may be carrying somebody's brand colours;
deleting those because a security camera was chosen would be a bigger surprise
than a flat frame, and a silent one.

**The look it takes is usually one nobody chose.** Four ad formats carry a
default look and it is nearly always a move-set, so pairing tv_spot with a body
cam removes the format's dolly moves before you ever saw them. The plan and
create responses name the axes taken, as `capture_overrides` — read that field
and repeat it to the user, because an override discovered in a render is one
nobody was told about.

Two consequences worth holding on to:

- **cinematic is the control.** It overrules nothing, so it is the style to pick
  when the look is the point.
- **The look is editable for the life of the lot; the camera is not.** Call
  `set_lot_look` whenever you like. Nothing here changes a lot's capture style
  after creation — it is chosen on `run_marketing_recipe`, `run_ugc_recipe` or
  their create pair, and changing your mind means building the lot again.

---

## TWO THINGS CALLED A PRESET, AND ONE LIST THAT IS NOT CINEMA

- `list_short_presets` names the Shorts Studio looks — claymation, comic, glitch
  and the rest. They are ids for `restyle_take`, a **paid** pass over a take that
  already exists. They are not lot looks and they do not belong on a look.
- `create_from_preset` takes a `preset` too, and it is a different word
  entirely: a ready-made shot list, for starting a lot with rows already in it.
  Nothing from `list_short_presets` is valid there.
- `list_explainer_structures` is its own vocabulary — rhetorical shapes and 2D
  visual styles — and it is deliberately **not** the cinema look. An explainer
  that looks like footage invites the viewer to believe it is footage, and for a
  diagram that is a lie. Its structures are shapes rather than counts: a longer
  target runtime grows the beat the argument expands by, not the pace.
- `list_ad_formats` and `list_ugc_structures` are the fourth vocabulary, the one
  that decides how many shots there are and what each does. A vertical will
  supply one where you named none.

---

## CHECKLIST

1. Did every id come from a listing you called this session, or from memory? A
   wrong vertical or capture style says so; a wrong look id says nothing.
2. Have you read `requires` off the vertical's own row, and fixed every gap the
   refusal named in one call rather than one at a time?
3. Does anyone address the lens in this lot? If so, is the capture style one of
   the four that can carry a face — mobile, webcam, cinematic or news?
4. Is the prompt coherent with the mounting — the whole room for a corner
   camera, a vehicle for a dash cam, altitude for a drone?
5. Which look axes is the style about to take, and have you said so out loud
   instead of leaving `capture_overrides` sitting in the response?
6. Is the camera right for the life of the lot? The look can be changed later.
   The capture style cannot.
7. Are you reaching for a Shorts preset when you meant a look, or a look when
   you meant an explainer style?
8. And said out loud: choosing ids renders nothing and spends nothing.
