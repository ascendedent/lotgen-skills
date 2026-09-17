---
name: lotgen-continuity
description: >
  Making many shots read as one film rather than a pile of clips — the lot look appended
  to every shot, chaining each shot from the last frame of the one before, an explicit
  opening frame, and reference packs that hold a face or a product across cuts. Use when
  the user says "the shots do not match", "shot 3 does not look like shot 2", "make it
  feel like one film", "continue from the previous shot", "keep the same room", "hold
  this face across the ad", "why is this shot blocked", "why is it rendering one shot at
  a time", or asks how to fix one shot without re-rendering the whole lot. Setting any of
  it is free; only render_scene and render_project spend.
---

# Continuity

Four shots of the same product are not an ad until they look like they were
made on the same day, by the same crew, of the same thing. Three levers do
that, they hold different things, and picking the wrong one is why a lot that
was carefully configured still cuts like a stack of stock clips.

**Setting all of this is free. It is also, in one case, expensive in time.** A
chained lot renders one shot at a time, and that changes both how long it takes
and what a quote means. Money still moves only at `render_scene`,
`render_project` and the paid post passes.

---

## THE QUOTE FOR A CHAINED LOT IS NOT THE PRICE OF THE LOT

Lead with this one, because it is the sentence an agent gets wrong in front of
a user.

`get_brief` and `render_project` with `dry_run` price **the shots that would
render now**. A chained shot cannot render now — its source has no finished
take — so it is not in the estimate and not in the count. A six-shot chain with
shot one ready quotes **one shot**.

The brief says so on its own line, and that line is easy to skim past: "N
shot(s) are chained and wait for their source". Read the count beside the money
before repeating the money. To price the whole thing, use `estimate_render` per
shot — it is read-only, it takes `continuity_mode` as an argument, and it is
the only door here that will quote a chained shot at all. `render_scene` with
`dry_run` on a blocked shot answers **409**, not a price: the chain check runs
before the quote.

---

## THE THREE LEVERS

| lever | what it holds | what it costs | what it cannot do |
|--|--|--|--|
| **the look** — `set_lot_look` | the grade: genre, film stock, lighting, palette, camera language, appended to every shot at render time | nothing, and no time | hold a subject. It makes four different women look consistently lit |
| **the chain** — `continuity_mode` on `update_scene` | the joint: this shot opens on the previous shot's final frame | **the lot renders one shot at a time** | a hard cut, and any shot that must open on something the previous one does not end on |
| **the opening frame** — `set_start_frame` | one shot's first picture, exactly, from a still this workspace already holds | nothing, and no time | anything after frame one. The clip drifts from there like any other |

The fourth thing people reach for when they say "continuity" is a **reference
pack**, which holds the *subject* rather than the grade or the joint. It has
its own section below, and it is usually the right answer.

The three compose. A look with no chain is a lot that is graded alike and cuts
freely — which is what most ads want.

---

## THE CHAIN IS SEQUENTIAL, AND THAT IS THE WHOLE COST

A chained shot needs the previous shot's finished take to exist before it can
be submitted, because that take's last frame *is* its first frame. So:

- **`render_project` queues only the shots that are ready** and returns the
  rest under `waiting`. It does not queue the chain and hope. Queueing it all
  at once would not be faster; it would be a race, where a downstream shot
  either fails or quietly chains from a stale take.
- **Each call releases one link.** Run it, wait for the take, run it again. A
  six-shot chain is six calls and six renders end to end, so the wall clock is
  the **sum** of the shots rather than the longest of them. An unchained
  six-shot lot is one call and one render's worth of waiting.
- **Each call is its own approval and its own charge.** There is no single
  moment where the lot's whole price is committed.
- **Release a chain with `render_project`, not with `render_scene` per shot.**
  The batch door checks each shot against the shot immediately before it. The
  single-shot door's guard is not the same guard, and a chained shot that
  reaches the worker with nothing to chain from dies at submit with a failed
  take to explain.

`get_brief` is where the state lives. Every shot reads `done`, `not rendered`,
`blocked, needs shot N to finish first`, or `UNRENDERABLE` with the reason.

**A first shot set to chain can never render.** There is nothing before it. The
brief calls that out as unrenderable rather than as ordinary progress, in bold,
with the count — because it looks exactly like "not started yet" and will sit
there forever. Fix it with `update_scene`: continuity none, or `reorder_scenes`
so it follows the shot it continues.

`render_scene` on a shot whose source is not finished is refused **409, before
a job row exists**. Nothing is queued and nothing is spent; the message names
the shot to render first.

---

## WHAT THE CHAIN ACTUALLY OPENS ON

Not "the previous shot". The previous shot's **mounted take** — the winner —
and, if nothing is mounted yet, the newest take that still exists.

That has a consequence worth having ready: **`select_variant` on shot 2 changes
what shot 3 will open on.** Mount the take you meant, *then* render shot 3.
Mounting after the fact does not retouch a shot 3 that has already rendered; it
only changes what the next render inherits. Mounting also bumps the shot's
`updated_at`, which is the usual reason a held `if_unmodified_since` comes back
409 — re-read and decide again rather than retrying the same write.

Two smaller facts that decide whether a chain works at all:

- **The source must make a clip.** A still's finished take counts as "done" to
  the scheduler and has no last frame, so a shot chained to a still is released
  and then fails at submit. Changing a shot to a still also silently clears
  that shot's own chain, along with its duration and audio.
- **Setting the chain after the source has already rendered still works.** The
  frame is extracted from the finished clip rather than handed over by the
  provider. It is asked for in advance only when something downstream already
  wanted it. Either way it lands as an asset on the shot and `list_assets`
  shows it.

---

## THE CHAIN IS A POSITION, NOT A LINK

By default "continue from the previous shot" means **the shot before me in
sort order**, resolved at render time. Nobody wrote down which shot that is.

So `reorder_scenes` silently re-points every unpinned chain in the lot, and
inserting a shot in the middle re-points the one after it. The lot renders
without complaint and the joint is between two shots you did not pair.

`continuity_source_scene_id` on `update_scene` pins it to a named shot, which
survives a reorder and lets a chain skip a shot — shot 4 continuing shot 2 with
an insert between them. Two things about the pin:

- **Point it at an EARLIER shot.** `render_project` walks the lot in order and
  only ever sees earlier shots as finished, so a source that sits later in the
  lot leaves the shot in `waiting` forever.
- **There is no null to send it back to.** Re-point it; do not expect to clear
  it the way `ref_pack_id` clears.

---

## WHEN CHAINING IS WRONG

Chaining is a **match cut, forced**. The new shot begins on the exact frame the
old one ended on, so the two shots share a picture.

That is right for a continuous beat — a hand keeps moving, a camera keeps
pushing, the room stays the room. It is wrong wherever the film is supposed to
*cut*:

- **A hard cut between scenes.** New location, new time of day, a jump from the
  product to the person. The chain will faithfully begin the new scene on the
  last frame of the old one, and the edit reads as a dissolve nobody asked for.
- **Alternative openings.** Three versions of a hook are three candidate FIRST
  shots, not a fourth beat. They chain from nothing on purpose.
- **A shot that must open on a specific picture** — a packshot, a logo end
  card, a creator plate. Use `set_start_frame`. Its whole job is that the
  opening is not inherited.
- **A model that cannot start a clip from another shot's last frame.** That
  pairing is refused rather than rendered unchained, and in a batch **one
  refused shot refuses the whole run** — nothing is queued, so nine good shots
  wait on one bad setting. `estimate_render` with `continuity_mode` names it
  before you build the lot.

Reach for a look plus a reference pack first, and chain only the joints that
genuinely have to be continuous. A lot with one chained pair renders almost as
fast as a lot with none.

---

## HOLDING A FACE OR A PRODUCT, WITHOUT SERIALISING THE LOT

A prompt describing a person renders a different person every time. The thing
that actually holds one face or one bottle across four clips is a reference on
every shot — and unlike a chain, references cost no time at all, because the
shots still render in parallel.

`create_ref_pack` makes a named set from stills this workspace already holds,
including takes from other lots. `apply_ref_pack` mounts it on a shot;
`set_scene_refs` attaches a single reference by public URL.

**Send order is decided for you, and it is not the order you passed
`asset_ids`.** The pack sorts creator first, then product map, then packshot,
then oldest. That matters because a model with a small reference limit takes
the front of the list and drops the rest — the face survives and the prop is
dropped, rather than the other way round. Pack references also come **before**
anything attached with `set_scene_refs`, so on a one-reference model the URL
reference is the one that never arrives.

Check `ref_count` on the response. An empty error list is not proof anything
attached.

---

## THE LOOK APPLIES AT RENDER TIME, WHICH CUTS BOTH WAYS

`set_lot_look` never edits a prompt. It is appended when the shot renders,
which is why a shot reads unchanged afterwards and why ids must come from
`cinema_vocabulary` — an id the vocabulary does not know is dropped silently
rather than pasted in as text.

The half that bites a half-finished lot: **changing the look does not reach the
shots that have already rendered.** Their takes were composed with the old one.
Set the look before the first render; if you change it midway, every mounted
take made under the old look now disagrees with the new one, and the only fix
is to render those shots again.

Two shots in a lot are deliberately outside all of this: a spoken shot gets no
look at all, because its prompt is the script and the voice would read the
grade aloud, and a still gets everything except the camera move, because a
photograph has no motion to give.

---

## FIXING SHOT 3 WITHOUT RE-RENDERING THE LOT

`render_project` renders shots that have **no finished take**, so it will skip
shot 3 entirely and is not the tool for this. `render_scene` is.

1. `get_brief`, and read shot 3's settings beside shot 2's. Same provider, same
   model, same aspect, same resolution? A mismatch there explains more
   "continuity" complaints than any continuity setting does.
2. Decide which lever failed. Wrong **grade** is the look. Wrong **person or
   product** is the references. Wrong **join** — the room jumps, the hand
   teleports — is the chain.
3. If shot 2 has several takes and the wrong one is mounted: `select_variant`
   on shot 2 with its own `updated_at`, then `render_scene` shot 3 alone. Shot
   3 now inherits a different frame.
4. If shot 2 is right and shot 3 simply rolled badly: `render_scene` shot 3
   again. The new take lands beside the old one and the first successful take
   of a shot with no winner mounts itself — the response says `auto_mounts`
   when that will happen — so use `select_variant` to overrule it.
5. If shot 3 must open on an exact picture, stop rolling dice: `set_start_frame`
   from a still, **and set continuity to none in the same breath**. The frame
   overrides the chain at render time, but the scheduler still reads
   `continuity_mode`, so a shot left chained waits for a predecessor whose
   frame it will never use — and on a model that cannot chain it is refused
   outright while the same shot with continuity none renders happily from the
   supplied frame. `clear_start_frame` puts it back.
6. Nothing above touches shot 1, 2, 4 or 5. That is the point of a lot.

```
get_brief          done · blocked · unrenderable · what it would cost
   ↓
set_lot_look       the grade, ids from cinema_vocabulary
create_ref_pack    the subject
apply_ref_pack
   ↓
update_scene       continuity_mode, only on the joints that must be continuous
set_start_frame    where a shot must open on an exact picture
   ↓
estimate_render    prices a chained shot the render doors refuse to quote
   ↓
render_project     one link per call, and the only thing here that spends
```

---

## CHECKLIST

1. Does this lot need a chain at all, or does a look plus a reference pack give
   the coherence and keep the shots parallel?
2. Is every chained shot a shot that should NOT cut — and is the first shot
   unchained?
3. If the order changed, or a shot was inserted, is every chain still pointing
   where you think? Pinned with `continuity_source_scene_id` if it matters.
4. Does any shot carry both a supplied opening frame and a live chain? Set
   continuity to none, or it waits for a frame it will discard.
5. Was the price you are about to quote the whole lot, or only the shots that
   can render right now?
6. Has the look been set **before** the first render, with ids from
   `cinema_vocabulary`?
7. Is the reference the model will actually receive the one you meant —
   `ref_count` checked, and the face at the front of the pack?
8. And for a repair: is the smallest fix one `render_scene`, or did you reach
   for `render_project` and re-render things that were already right?
