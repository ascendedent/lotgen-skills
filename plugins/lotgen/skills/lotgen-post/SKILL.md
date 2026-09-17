---
name: lotgen-post
description: >
  Fix a rendered take without rendering it again — restyle it, edit an object out or in,
  remove the person and keep the room, upscale it, or reframe it to another aspect. Use
  when the user says "change the mug", "remove the logo", "make it look like film",
  "make it bigger", "I need this in 9:16", "take her out of the shot", "the render
  failed", or asks whether a fix is cheaper than a re-render. Every pass here costs
  money except two, and this says which two. Read the tier list before choosing anything.
---

# Post

A take came back and it is nearly right. **Re-rendering throws away the one
you have and rolls the dice again**; a post pass keeps it and changes the one
thing. This is when to do which, and what each one costs.

Every pass here **lands as another take of the same shot**, so the original
survives and the two can be compared. `reframe_take` is the exception and is
called out below.

---

## FIRST: THE TWO THAT ARE FREE

Before spending anything.

**A failed take may not have failed.** `recheck_take` asks the provider once
more whether a take that timed out was actually delivered. Re-rendering pays
twice for the same clip. Ask first — it costs nothing and it is the single
most common avoidable charge in this product.

**A different aspect is usually a crop.** `reframe_project` is ffmpeg, free
and instant. `reframe_take` paints the new edges with a model and costs money.
Reach for the crop first. Only when the crop is wrong — and it is wrong when
it throws away something that matters, because 16:9 to 9:16 keeps under a
third of the frame and turns a two-person shot into a one-person shot — is the
paid one the right call.

---

## THE TIER DECIDES EVERYTHING

Do not pick a pass and then find a tier. **Read the tier list first**, because
the row you land on decides three separate things:

| what the row decides | why it bites |
|--|--|
| **whose key pays** | rows name a `provider`. A workspace can hold a key for one tier and not another, and picking one it has no key for is refused by name before anything is queued |
| **how much of the clip survives** | the default returns about five seconds whatever you give it. A twelve-second take comes back CUT |
| **what the price is per** | source second, output megapixel, or started second — they are not the same thing and they do not scale the same way |

`list_video_edit_tiers` for `restyle_take` and `edit_take_video`.
`list_video_region_tiers` for `reframe_take` and `clean_plate_take`.

**The windows are on the rows.** Read them there rather than from any
sentence — including this one — because a sentence goes stale and a row does
not.

---

## WHICH PASS

| you want | call | what it keeps |
|--|--|--|
| a different look, same performance | `restyle_take` | motion, pacing and performance exactly as they are |
| one object changed or swapped | `edit_take_video` | the people, their clothes, the setting, the motion and the timing |
| one named thing GONE | `erase_from_take` | everything else, with the background rebuilt behind it |
| the subject gone, the room kept | `clean_plate_take` | the scene, rebuilt behind whoever was in it |
| more resolution | `upscale_take` | everything, larger |
| another aspect, nothing thrown away | `reframe_take` | the whole picture, with new edges painted |
| a still changed | `edit_still` | the source's dimensions |

**`restyle_take` does not cut anything down.** It is a look change, not an
edit, so nothing here needs a timeline and the output is the same length as
the input — which is also why it is priced per second of the source.

**`edit_take_video` is a sentence, not a mask.** "Replace the kettle with the
orange ceramic mug", "remove the logo on the wall". Everything unnamed is
kept, and the keep-clause is composed for you. It holds best between objects
of **similar size and place**; when shapes differ a lot the new object can
grow out of the old one. That is a re-roll, not a loss — the original take is
still there.

### Erasing by name, and the one flat price here

`erase_from_take` takes something out of a clip by **naming it** — "the man in
the red coat", "the logo on the wall", "the car parked on the left" — and
rebuilds what was behind it. No mask to paint, no frames to touch.

It is the **only pass in the product driven by free text about the footage
itself**, so the sentence is the whole instruction. **Name one thing**, the way
you would point at it.

**Describe the background too.** It is optional, and leaving it out is worse
than it looks: the fallback is the shot's own prompt, and **that prompt
describes the shot with the thing still in it.** So the fallback is actively
the wrong description. Say what should be there once it is gone.

**Its price is flat — the same for a two-second clip and a twenty-second
one**, which nothing else paid here does. That makes it the cheapest pass on a
long take by a wide margin, and worth reaching for before a per-second one when
either would do.

Three passes remove things and they are not interchangeable: this one names
its target in words, `clean_plate_take` removes the moving subject with nothing
said and bills by the megapixel, and `edit_take_video` changes the whole clip.

**`edit_still` is one tool for relighting, recolouring, removing, and swapping
a face or a product.** What separates those is the sentence, not the model, so
write the instruction the way you would tell a retoucher. The shot's own
reference images are carried in after the source, so a face or product already
attached to that shot can be swapped in.

---

## THE PRICE IS NOT ONE SHAPE

This is the part that surprises people, and every line of it changes what you
should choose.

- **`restyle_take` bills per second of the SOURCE.** Output length equals
  input length, so the quote is predictable.
- **`upscale_take` bills on the OUTPUT.** For stills that is per output
  megapixel, so **doubling the factor QUADRUPLES the price**. Its tiers differ
  by roughly twelve times between the cheapest and the dearest, and they are
  not the same product: one is faithful, one invents detail, one reinterprets.
- **`clean_plate_take` bills per megapixel of GENERATED video** — width times
  height times frames. Length and resolution both move it.
- **`reframe_take` bills per source second on one tier and per STARTED source
  second on another**, so a 6.2-second take is billed as seven on the second
  one.
- **`edit_still` rounds the output megapixel UP.** A 1.1 MP still bills as 2.

**Anything that was never measured cannot be quoted.** A take whose length,
or whose width and height, were never recorded comes back unknown rather than
priced — and one tier refuses such a clip outright rather than submitting it
and letting the provider decide.

**Use `dry_run` on every one of these.** It returns the quote and what will be
billed without spending. `estimate_render` prices a fresh render, which is the
number a post pass is competing against.

---

## THE ONE THAT INVERTS

`clean_plate_take`'s limit is **the resolution and the length together, and
they trade the wrong way round**: the higher resolution rebuilds the SHORTER
clip. A long take is refused at the higher one and accepted at the lower one.

That is the opposite of the usual trade, so an agent that reasons "it was
refused, try a smaller resolution and a longer clip" is reasoning correctly
here and would be wrong almost anywhere else. The refusal names which to pick.
Read it rather than guessing.

---

## WHERE THE RESULT LANDS

Everything above lands as **another take of the same shot**. Use
`select_variant` to mount the new one if it wins; the original stays where it
is either way, and `search_takes` will find both.

**`reframe_take` is different.** Its result lands as a **delivery format**,
where `reframe_project`'s crops land — not as a take. So `export_project` in
that aspect picks it up, and the shot's own takes are untouched. Do not go
looking for it with `select_variant`; it is not a variant.

---

## CHECKLIST

Before you spend anything on a post pass:

1. Did the take actually fail? `recheck_take` first.
2. Would the free crop do? `reframe_project` before `reframe_take`.
3. Read the tier row: whose key, what window, what the price is per.
4. Is the take long enough that the default tier will cut it?
5. Is the source measured? An unmeasured one cannot be quoted.
6. Run it with `dry_run` and read the number.
7. Compare that number to `estimate_render` for a fresh take. Sometimes
   re-rendering genuinely is cheaper — say so rather than defaulting to a fix.
8. Then, and only then, spend.
