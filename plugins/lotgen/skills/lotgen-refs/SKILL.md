---
name: lotgen-refs
description: >
  Making a LOTGEN lot look like a particular thing — reference images, the picture a clip
  opens on, reusable reference packs, the look that ties a multi-shot lot together, and
  the preset shot lists. Use when the user says "use this image as a reference", "start
  the shot on this frame", "make it all look the same", "match this style", "the shots
  do not look like one film", "use our photo of the product", "the room keeps changing",
  "make a location map", or asks why a look they set had no effect. Everything here is free and none of it edits a prompt.
---

# References and looks

Four different ways to point a render at something that already exists. They
are not interchangeable, and choosing the wrong one is the usual cause of "I
set it and nothing happened".

---

## THE RULE THAT BREAKS THE MOST CALLS

**Pass an asset id. Never a signed URL.**

`get_asset_url` hands back a signed, time-limited address, and it is tempting
to feed that into a reference field. Do not. Locally it is a loopback address
that the renderer cannot fetch, so the attachment appears to work on the
machine you built it on and reaches nothing anywhere else.

Every reference field here takes an **id**. `list_assets` is where ids live.
The one exception is `set_scene_refs`, which is explicitly for attaching a
reference by a public http(s) URL — an address on the open internet, not one of
ours.

---

## WHICH ONE YOU WANT

| you want | call |
|--|--|
| this shot should look like this picture | `set_scene_refs`, or a pack |
| this clip should **begin** on this picture | `set_start_frame` |
| the same set of references on many shots | `create_ref_pack` then `apply_ref_pack` |
| the whole lot to read as one film | `set_lot_look` |
| a shot list already written | `create_from_preset` |

---

## THE OPENING FRAME IS NOT A REFERENCE

`set_start_frame` is categorically different from attaching a reference. The
shot renders **image-to-video from that frame** instead of from the prompt
alone — a still take, a creator plate, a product photograph.

Four things to know before using it:

1. **It OVERRIDES the continuity chain.** A lot set to chain each shot from the
   previous shot's last frame will use your supplied frame instead, on that
   shot. That is usually what you want and occasionally a surprise.
2. **One per shot, replaced rather than added to.** Setting a second does not
   give the shot two.
3. **The picture is copied.** Deleting the source later leaves the shot's
   opening frame alone.
4. **Only a clip has one.** A still is one picture and a spoken line has none,
   so the call has no meaning on either.
5. **An opening frame and a pack on one shot: the frame wins.** The shot
   renders image-to-video from the frame, and the references are not sent;
   the quote names that as a `refs` incompatibility. So do not mount a pack
   on a shot that opens on a frame, and put what the pack was for (the face,
   the product, the room) into the frame instead. Before 2026-10-13 some
   routes went the other way: a Seedance 2.5 shot with both was sent to
   reference-to-video and the frame was lost.

`clear_start_frame` takes it off, and the shot goes back to rendering from its
prompt — or from its continuity chain, if it has one.

---

## STILL FIRST, THEN MOVE IT

For any shot with a person and a product in it, the most reliable route
measured so far is two steps, not one:

1. **Make the still.** A still shot (`add_scene` with kind image) with the
   creator plate and the product photography as references, prompted as the
   opening moment of the clip. Look at it (`view_take`) before going on: the
   face, the room and the product must all be right here, because nothing
   later will fix them. A still costs cents; a clip costs dollars.
2. **Animate it.** `set_start_frame` on the clip with that still, no pack on
   the clip, and a prompt that describes only what moves and what is said.
   The still carries the face, the set and the product; the video model only
   has to move them.

Measured 2026-10-13 on Seedance 2.5 via evolink: a still made this way,
animated image-to-video, held the same woman, kitchen and lit product for
the whole clip, where the same shots rendered from a pack alone came back
with a different person and a generic product. Image-to-video on that model
takes its shape from the frame, so make the still at the lot's shape.

**Every set still after the first is made FROM the first.** Two stills from
the same words are two different rooms: the window moves, the cabinets
change, the product swaps sides. So the second set still carries set still
one as a reference, beside the creator and the product. A still model has a
reference cap (Nano Banana takes three), and when it bites, keep the
creator, the product's clearest picture and set still one, and drop the
rest: the room is the thing two stills cannot agree on without help, and
the extra product angles are already in set still one.

---

## A LOCATION MAP HOLDS THE ROOM

(The lotgen-map skill has both maps in full: this one, and the product map
that does the same for the item, drawn from its own photos with
`create_product_map` and filed on it with `save_product_map`.)

When several shots happen in one room, make the room a picture of its own
before any shot: a **location map**. One still, a titled grid of numbered
angles of the same room ("BEDROOM LOCATION MAP", 1. Bed from the doorway, 2.
Window wall...), optionally in lighting bands with the same numbers in each
("lights on", "lights off"). Editors build these by hand because the numbers
let a shot say which view it is.

1. `plan_location_map` (free) shows the sheet's prompt, its numbered panels
   and the price. Describe the room in full in `room`: walls, floor, where
   each piece of furniture stands, windows and doors. Every panel has to be
   that room, so vague words give sixteen different rooms. List the
   `angles` your shots need; omit them for eight standard ones. At most 24
   panels (angles times bands).
2. `create_location_map`, then `render_project`, then `view_take`: is it
   one room in every panel, and are the numbers legible? Re-render until it
   is. A photo sheet of a real room, numbered the same way, works too:
   upload it and skip to the cast.
3. `cast_location` on the winning still. The `location_asset_id` it returns
   serves every later lot (`list_locations` finds it again).
4. Put it on the pack: `create_ref_pack` with `location_asset_id` (or the
   same field on `create_ugc_lot` / `create_ad_lot`). It is sent after the
   person and the product map, and the legend calls it "location map:
   numbered angles".
5. Name each shot's panel with `update_scene`: `location_angle: 4`,
   `lighting_state: "lights off"`. The prompt then ends "The room is angle 4
   of the location map, lights off." Read it in the dry run's
   `composed_prompt`.

The sentence is only written while the map is among the pictures sent. A
clip that opens on a frame drops its references on most models, so the map
does its work on the STILLS: make each set still with the creator, the
product and the map, then animate the still. That replaces carrying set
still one into every later still, and keeps the room when the shots are
made in any order. A model's reference cap counts the map: Nano Banana takes
three, which is exactly person, product map and room.

---

## PACKS, FOR WHEN THE SAME REFERENCES REPEAT

`create_ref_pack` makes a named set from stills already in this workspace —
**including takes from other lots**, which is the part people miss. A face you
liked in one lot can become a reference in the next without leaving the
product.

**The person goes first.** A cast creator stays the person in the pack; a face
that is not a cast creator (a plate still, a take) goes in as
`creator_asset_id`, or it is copied as an anonymous "reference" and sent after
the product photos. `list_ref_packs` shows each picture's `role` and
`creator` in send order: read it before paying, and read the dry run's
`composed_prompt` for the legend the model will get.

`apply_ref_pack` mounts a set on a shot, and passing nothing clears it.
`list_ref_packs` shows what exists. Look there before assembling the same five
images by hand for the third time. `delete_ref_pack` removes a set you made by
mistake; every shot it was mounted on loses it.

---

## THE LOOK: WHY IT LOOKS LIKE IT DID NOTHING

`set_lot_look` declares genre, film stock, lighting, palette and camera style
for a **whole lot**, appended to every shot at render time. It is what makes a
multi-shot lot read as one film, and it replaces typing "same grade as the
prior shots" into every prompt.

**It never edits the prompts themselves.** Read a shot back after setting a
look and the prompt is unchanged — that is correct, not a failure. The look is
appended when the shot renders.

**And an id the vocabulary does not know is IGNORED, not injected.** It is not
passed through as literal text and it does not error. So a typo'd look id is
silently no look at all, and the only symptom is that the render looks
untouched — which is exactly what a user reports as "the look did nothing".

So: **take ids from `cinema_vocabulary`.** It carries every genre, film stock,
lighting, palette, camera move-set, lens, aperture and move, each with the
phrase it appends. Do not guess an id from a name that sounds right.

Pass nothing to clear a look.

---

## PRESETS

`create_from_preset` creates a lot already filled with a shot list — the
fastest route from nothing to something renderable.

`list_short_presets` is a different thing despite the similar name: it is the
list of **restyle looks**, and its names deliberately follow the vocabulary an
agency arriving from elsewhere already uses. **Call it before a restyle rather
than guessing an id**, for the same reason as the look vocabulary — a guessed
id is a wasted round trip at best.

---

## THE ORDER THAT SAVES A RENDER

Everything in this file is **free**, so all of it belongs before the first
render rather than after it.

```
create_from_preset / an existing lot
        ↓
set_lot_look           ids from cinema_vocabulary
        ↓
create_ref_pack → apply_ref_pack    or set_scene_refs per shot
        ↓
set_start_frame        where a clip should open on a known picture
        ↓
estimate_render        the number, still free
        ↓
render_scene / render_project   ← the only calls that spend
```

---

## CHECKLIST

1. Asset **id**, not a signed URL?
2. Is this a reference, or an opening frame? They do different things.
3. Did the look id come from the vocabulary, or from a guess that will be
   silently ignored?
4. Does a reference pack already exist for these images?
5. Is the shot a clip? A start frame means nothing on a still or a spoken line.
6. A frame and a pack on one shot? The frame wins; move the pack's job into
   the frame.
7. A person and a product in one clip? Make and check the still first, then
   animate it. Later set stills carry the first one as a reference.
8. Is everything set **before** the render, while changes are free?
9. Several shots in one room? Make and cast a location map first, mount it
   on the pack, and give each shot its `location_angle`.
