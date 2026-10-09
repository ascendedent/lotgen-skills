---
name: lotgen-refs
description: >
  Making a LOTGEN lot look like a particular thing — reference images, the picture a clip
  opens on, reusable reference packs, the look that ties a multi-shot lot together, and
  the preset shot lists. Use when the user says "use this image as a reference", "start
  the shot on this frame", "make it all look the same", "match this style", "the shots
  do not look like one film", "use our photo of the product", or asks why a look they set
  had no effect. Everything here is free and none of it edits a prompt.
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

`clear_start_frame` takes it off, and the shot goes back to rendering from its
prompt — or from its continuity chain, if it has one.

---

## PACKS, FOR WHEN THE SAME REFERENCES REPEAT

`create_ref_pack` makes a named set from stills already in this workspace —
**including takes from other lots**, which is the part people miss. A face you
liked in one lot can become a reference in the next without leaving the
product.

`apply_ref_pack` mounts a set on a shot, and passing nothing clears it.
`list_ref_packs` shows what exists. Look there before assembling the same five
images by hand for the third time.

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
6. Is everything set **before** the render, while changes are free?
