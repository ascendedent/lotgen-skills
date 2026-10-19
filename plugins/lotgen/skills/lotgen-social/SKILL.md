---
name: lotgen-social
description: >
  The two feed-native formats in LOTGEN — a photodump camera roll of one person, and an
  invented AI influencer who can front dumps, ads and lipsync clips. Use when the user
  says "make a photo dump", "a camera roll", "candid photos of", "create an influencer",
  "invent a creator", "I need a face for this brand", "a persona", or wants stills that
  read as one day photographed rather than an ad. Identity here is a held likeness, not
  a trained model, and that decides what to expect.
---

# Social

Two formats that share one problem: **the same person has to look like the
same person across a lot of pictures.**

---

## HOW IDENTITY ACTUALLY WORKS HERE

There is no trained model of anybody. Identity is **a reference photo carried
on every shot** — a likeness held, not a character learned.

Three consequences, and they are the whole reason this skill exists:

1. **Expect the face to hold across most of a roll, not all of it.** Misses
   happen. The answer is to re-roll the misses inside the lot with
   `render_scene`, not to abandon the person or apologise for the format.
2. **Anchors are what survive.** Freckles, a scar, a gap tooth, a distinctive
   hairline. When composing a persona, the distinguishing details field does
   the most work of any input — **a face with no anchors drifts between
   shots**, because there is nothing for the likeness to hold onto.
3. **A cast person is reusable forever.** `cast_creator` promotes a rendered
   still to a workspace-level reference and hands back an id. It is **free**,
   and the same person never needs uploading twice.

---

## PATH A — A PHOTODUMP OF SOMEBODY WHO EXISTS

One person, one aesthetic, a camera roll of candid stills, portraits,
fit-checks, details and walk-aways that read as **one day photographed**.

`run_photodump_recipe` is the way: one call plans the roll, creates the lot
with the person mounted as a reference on every shot, and prices it. Prefer it
over `plan_photodump` plus `create_photodump_lot`, which are the same job split
for when you need to intervene between the halves.

**It requires the person.** Either a photo — face clear, chest up — or the id
of somebody already cast. The id that comes back is reusable, so the next dump
costs no upload.

`list_photodump_catalog` carries the aesthetics, the archetypes and the counts
on offer. **The archetypes are ordered so that any prefix is a balanced set**,
which is a genuinely useful property: asking for six gives you a coherent six
rather than the first six of a list that front-loads portraits. Do not
hand-pick a subset to "balance" it yourself — that is what the ordering
already did.

**These are stills, so there is no runtime.** The planned duration comes back
empty, and that is the honest answer rather than a zero. Do not report it as a
zero-second video.

`run_photodump_recipe` does **not** render. `render_project` renders the roll,
and that is where the money goes.

---

## PATH B — INVENTING SOMEBODY FIRST

When there is no person yet, build one, then use them everywhere.

`list_influencer_options` carries the character sheet's axes: presentations,
age bands, builds, and six energies each of which sets an expression. Heritage,
skin, hair, eyes, distinguishing marks and wardrobe are free text — so those
are where a persona becomes specific rather than generic.

`plan_influencer` composes the persona and returns **the canonical plate**:
chest-up, frontal, even light, plain ground. **That plate is a reference, not
content.** It is not a shot anybody will see; it exists so every later picture
has something to hold onto. Do not try to make it interesting.

Then:

1. `create_influencer_lot` — a one-scene lot holding the plate. Nothing renders.
2. `render_project`, or re-render the scene, until you have takes.
3. **Pick the best face.**
4. `cast_creator` on that take. Free.

The id it returns is what the photodump and UGC builders take, so **the same
invented person fronts dumps, ads and lipsync clips**. That is the payoff: one
casting session, reused indefinitely.

---

## ORDER OF OPERATIONS

Getting this backwards wastes a render.

```
no person yet?  → plan_influencer → create_influencer_lot → render_project
                  → pick the face → cast_creator → id
person exists?  → the id, or a photo
                  ↓
run_photodump_recipe (plans, creates, prices — free)
                  ↓
estimate_render if you want the number again
                  ↓
render_project  ← the money moves HERE and nowhere earlier
                  ↓
re-roll the misses with render_scene · select_variant to mount the keeper
```

---

## CHECKLIST

1. Does the persona have distinguishing anchors, or will the face drift?
2. Is the plate being judged as a reference rather than as a picture?
3. Was the best face actually **cast**, so the next dump costs no upload?
4. Are you using the catalogue's ordering rather than hand-picking a subset?
5. Have you told the user a photodump has no runtime, rather than reporting a
   zero?
6. Is everything before `render_project` understood to be free?
