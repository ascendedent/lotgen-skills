---
name: lotgen-map
description: >
  Maps in LOTGEN: one still that holds a ROOM (a location map) or a PRODUCT (a product
  map) as a titled grid of numbered views, or a PERSON (a character map) as a full-length
  turnaround over a row of their expressions, so the room, the item or the person stays
  the same thing from shot to shot. Use when the user says "make a location map", "make a
  product map", "make a character map", "a character sheet", "an avatar map", "a
  turnaround of the product", "a turnaround of her", "a sheet of every angle", "the room
  keeps changing", "the product looks different in every shot", "the label moved", "her
  outfit changed", "the back of his head is wrong", "she never makes the face the script
  asks for", "use our product sheet", "use this character sheet", "number the angles",
  "show it open in this shot", or has several shots in one room, of one item or with one
  person. Planning is free; a map costs one still per try.
---

# Maps: the room and the product, numbered

A video model is handed a few pictures and a sentence. If the pictures are
three unrelated photos, it averages them, and the room or the product drifts
from shot to shot: the window moves, the cabinets change, the label slides to
the other side, the bag comes back a different green. Editors fix this by
hand with a **map**: one picture holding every view of one thing, each view
numbered, so a shot can say "this is view 4".

LOTGEN makes three kinds:

| | Location map | Product map | Character map |
|---|---|---|---|
| What it holds | One room | One saved product | One person |
| Drawn from | Your words (`room`) | The product's own photos, plus your words | Your words (`person`, `wardrobe`), plus a creator's face if you give one |
| Bands | Lighting (`lighting_states`: "lights on", "lights off") | State (`states`: "closed", "open", "in use") | Two fixed rows: full-length `views` on top, `expressions` below |
| Where it lives | The workspace, cast with `cast_location` | The product, filed with `save_product_map` | The workspace, as the creator, cast with `save_character_map` |
| How a lot gets it | `location_asset_id` on the pack | Automatically, from the product, first in the pack | `creator_asset_id`, exactly as any creator |
| How a shot names its view | `location_angle`, `lighting_state` | `product_angle`, `product_state` | It does not: the sheet has no numbers. The shot says the expression in words |
| The sentence the model reads | "The room is angle 4 of the location map, lights off." | "The product is angle 3 of the product map, open." | "1. the person: character sheet", then that the sheet is one person who appears once |

The location and product maps are a single still: title across the top
("BEDROOM LOCATION MAP", "TRAIL PACK PRODUCT MAP"), a grid of numbered
panels with a short label under each number, up to 16 views, up to 3 bands
with the same numbers in each band, and at most 24 panels in all. Ask for
more and the plan refuses with the arithmetic before anything is made. The
character map is different on purpose, and has its own section below.

---

## THE ONE RULE: A NUMBER ONLY MEANS SOMETHING BESIDE ITS MAP

The angle sentence is written into a shot's prompt ONLY when that map is
among the pictures actually sent. If it isn't, the prompt doesn't say "angle
4", because the model would go looking for a picture it never got. So:

- A shot that opens on a frame (a chained clip, `set_start_frame`) drops its
  references on most models, and the map with them. Do the map's work on the
  STILLS (the opening frames, the keyframes), then animate the stills.
- A model's reference cap counts the map. Nano Banana takes three: the person,
  the product map and the room, in that order. That is why both maps rank
  high: person, product map, location map, packshot, the rest, the logo last.
- Read the dry run's `composed_prompt` (or `get_project`'s) before rendering.
  If the angle sentence is not at the end, the map is not on the shot.

---

## A PRODUCT MAP

Use it when the same item appears in more than one shot, or when one shot
has to show a particular side, the label or the item open.

1. **The product, with photos.** `list_products` or `save_product`. The map
   is drawn FROM its photos, so it needs at least one: `add_product_images`
   for public URLs, `upload_product_assets` for files on your machine or
   attached to the chat (`lotgen-assets`, FILES ON THIS MACHINE).
   `get_product` lists what is there. A product with no photos is refused,
   because the model would draw a plausible item that isn't theirs.
2. **Plan it, free.** `plan_product_map` with the `product_id`. It returns the
   sheet's prompt, its numbered panels, which photos it would be drawn from
   (`drawn_from`) and the price.
   - `product`: the item in full, the way a person describing it over the
     phone would: kind and size, materials and colours, the label and where
     it sits, the parts that move. It defaults to the product's saved name
     and summary; write your own when the summary is sales copy ("the last
     bag you'll ever need") rather than a description.
   - `angles`: the views your shots need, in order. Omit them for an
     eight-view turnaround: front, back, left, right, top down,
     three-quarter, the label close up, held in a hand for scale. List only
     the views you'll use: fewer panels are bigger panels.
   - `states`: bands for an item that changes ("closed", "open", "in use").
     One state describes the whole sheet.
   - `product_asset_ids`: which photos to draw from. Nano takes three, so by
     default it gets the existing product map, then packshots, then the
     rest. Pick the clearest and most different views (front, back, a
     detail) over three near-identical packshots.
3. **Build and render it.** `create_product_map` with the same fields, then
   `render_project`. Then LOOK at it with `view_take`: is every panel the
   same item as the photos (shape, colour, label text, logo position), and
   are the numbers legible? A map that redesigns the product is worse than
   no map: it teaches every later shot the wrong item. Re-render until one
   holds, or narrow `angles`.
4. **File the winner on the product.** `save_product_map` with the take's
   `asset_id` and the `product_id`, and `replace: true` when it is better than
   the product's old map, so the new one leads alone on a model that takes a
   single reference. It answers with the `product_asset_id` of the copy now in the
   product's library; deleting the map lot does not take it away. Without
   `replace`, the old maps stay tagged and are listed in `other_product_maps`.
5. **Use it.** Every lot built from the product AFTER this carries the map
   first (after a person): `create_ugc_lot`, `create_ad_lot` and the recipes
   pick it by default. Lots already built keep their packs; to add the map
   to one, `create_ref_pack` with the map's id in `asset_ids` and
   `apply_ref_pack`.
6. **Name each shot's view.** `update_scene` with `product_angle: 3` and,
   for a banded sheet, `product_state: "open"`. Only on a NUMBERED sheet:
   an uploaded product map with no numbers gives the sentence nothing to
   point at, so leave the angle off and let the picture do the work.

An existing product sheet (a photographer's turnaround, a manufacturer's
spec sheet) is a product map too. Upload it with `upload_product_assets`
with its `role` set to product map (an attached sheet is already at
`/mnt/user-data/uploads/`), or tag one already there with
`set_product_asset_tags`, or file any still with `save_product_map`.

---

## A LOCATION MAP

Use it when several shots happen in one room.

1. **Plan it, free.** `plan_location_map`. Describe the room in full in
   `room`: walls, floor, where each piece of furniture stands, windows and
   doors, era. Every panel has to be that room, so vague words give sixteen
   different rooms. List the `angles` your shots need, or omit them for
   eight a recce would shoot. `lighting_states` adds bands.
2. **Build, render, look.** `create_location_map`, `render_project`,
   `view_take`: one room in every panel, numbers legible. Re-render until it
   holds. A numbered photo sheet of a real room works too: upload it and go
   straight to the cast.
3. **Cast it.** `cast_location` on the winning still returns a
   `location_asset_id` that serves every later lot. `list_locations` finds
   it again.
4. **Put it on the pack.** `create_ref_pack` with `location_asset_id`, or
   the same field on `create_ugc_lot` and `create_ad_lot`.
5. **Name each shot's panel.** `update_scene` with `location_angle: 4` and
   `lighting_state: "lights off"`.

---

## A CHARACTER MAP

Use it whenever one person appears in more than one shot, and above all
when the ad is a performance: reactions, comedy beats, a face that has to
change on cue. A plate holds the front of a face. A character map holds the
whole person: the body and how they stand, the wardrobe from the back as
well as the front, the back of the head, and the face already moving
through the expressions the script needs. It is the sheet behind the best
ad made with this stack so far (the BUL "Mom's Junk Drawer" cut, 2026-10-27,
Seedream 5.0 Pro sheets as references into Seedance 2.5 in Higgsfield).

What it looks like: one 16:9 still on a plain light grey seamless. Across
the top, the person full length, head to shoes, in five views (front,
three-quarter left, left profile, back, three-quarter right). Across the
bottom, four head-and-shoulders close-ups, each a different expression.
**No title, no numbers, no labels.** The sheets that worked were clean, and
lettering on a face sheet is lettering a model can carry into a frame.

1. **Cast the expressions from the script, not from a list of moods.**
   Before planning, read the script's beats and write down the four faces
   this person actually makes in it. The daughter's sheet in the BUL ad was
   a smirk, deadpan, an eye-roll and a laugh because those were her four
   beats; the mother's had an open-palmed shrug because her line needed
   one. Write each as what the face does (lotgen-casting has why): *"a
   smirk, one corner of the mouth up, eyes narrowed"*, not *"amused"*. A
   gesture may ride along with a face (*"palms up at shoulder height, a
   shrug"*). Up to six. Omitted, you get four generic faces, which is a
   plate with extra steps.
2. **Plan it, free.** `plan_character_map` with:
   - `person`: in lotgen-casting's order (the age as it reads, build,
     skin, hair, then two or three anchors a camera sees from any side:
     glasses, earrings, a hair part, a watch). Every panel must be this
     person, and the back view is drawn from these words alone.
   - `wardrobe`: head to foot, shoes included, one outfit. The turnaround
     is where a robe grows a second belt and trainers turn into slippers,
     so say every item and where it sits.
   - `expressions` from step 1; `views` only if a shot needs one the
     default lacks (a seated view, the left three-quarter only).
   - `creator_asset_id` to draw the sheet from a face already cast. Omit
     it to make the person from words.
   - `name`: what they are called. It names the lot and the creator, never
     the sheet.
3. **Build, render, look.** `create_character_map`, `render_project`, then
   `view_take` and crop each panel. Check: one person in every panel (face,
   skin, hair, build); every item of wardrobe in the same place, front and
   back; the back of the head consistent with the front; the shoes in
   frame; each close-up the expression asked for, in order; and nothing
   lettered anywhere (the take's `lettering_detected` flag is the OCR's
   view of that). Re-render until one holds. A sheet with a drifted panel
   teaches every later shot that drift.
4. **Cast the winner.** `save_character_map` with the take's `asset_id`
   and a `name`. It returns a `creator_asset_id`, and that id is the person
   from now on: `create_ugc_lot`, `run_ugc_recipe`, `create_photodump_lot`,
   `create_ref_pack`, anywhere a creator goes. It lists on the Creators page
   with `character_sheet: true`. A sheet made elsewhere (Craig's, a costume
   department's turnaround) is cast the same way from any still the
   workspace holds.
5. **Write the shot to the sheet.** The legend tells the model the first
   picture is a character sheet of one person who appears once in the
   shot; your prompt says which of the sheet's faces this beat wants, in
   the same words the expression row used, and what the body does. On a
   clip, the expression is one beat of a performance (lotgen-casting), not
   a state held for eight seconds.

**Which model makes the sheet.** The default is `seedream` (Seedream 5.0
Pro, on the ARK key), the owner's call on 2026-10-28 because the sheets that
worked were Seedream sheets. It matters most for Seedance 2.x on the ARK key,
which refuses a face it did not make; a Seedream picture made **from text
alone** is one of its stated exceptions (lotgen-models). So for a lot that
will render on ARK Seedance, make the sheet from words, with no
`creator_asset_id`. Whether ARK accepts a Seedream sheet drawn from a face
made elsewhere has not been measured; render that pairing on EvoLink's
`seedance-2.5` instead. Where the workspace has no ARK key, pass
`provider: "nano"`.

**What it costs a pack.** The sheet is one picture, sent first as the
person, so it takes the slot the plate took. On Nano Banana's three that
leaves the product map and the room, as before. A shot that opens on a
frame drops its references on most models, the sheet with them, so do the
sheet's work on the opening still and animate that, as with the other maps.

**What has been measured.** Nothing here yet: no paid character map has
been rendered through LOTGEN. The evidence is the BUL run above, made in
Higgsfield. The first sheet you make, check every panel before casting it.

---

## BOTH MAPS IN ONE SHOT

A shot can carry both: the pack holds the person, the product map, then the
room, and the prompt ends "The product is angle 6 of the product map. The
room is angle 4 of the location map, lights off." Set both pairs of fields.
On Nano Banana that is all three slots, so a packshot on the same pack is
dropped; that is the right trade, since the product map already holds the
packshot's views.

For a continuous lot, make the keyframe STILLS with both maps on their pack
and each still's angles named, check them with `view_take`, then render the
clips between them (`keyframe_lot` wires every clip at once; the
lotgen-continuity skill has the method).

**From Premiere.** The LOTGEN panel for Premiere names views too. Its
Generate tab shows a room view and a product view under the reference pack
when the chosen pack holds that map, and its agent bridge takes the pack and
the views on quote_shot and generate_shot and lists each pack's maps in
get_lot (the premiere-editing skill has the details). A shot rendered there
reads the same sentence beside the same map as one rendered here.

---

## WHAT IT COSTS, AND WHAT HAS BEEN MEASURED

- A plan is free. A map is one still per try; quote it with the plan's
  `estimate_usd`.
- The default model is Nano Banana. It was chosen on a measured location map
  test (2026-10-14, a 6-angle, 2-band kitchen): it drew all twelve panels,
  numbered and labelled right, one room throughout, for $0.0672.
  gpt-image-2 drew ten with a wrong label and another room in one panel;
  Seedream garbled the labels. The product map uses the same default and
  has NOT yet been measured on a product sheet; check every panel against
  the photos before filing one.
- A product with fine print (an ingredients panel, a serial plate) will not
  survive at panel size. Give the label its own close-up view, and expect
  the model to paraphrase small text; it is a reference for shape and
  placement, not a source of legible copy.
