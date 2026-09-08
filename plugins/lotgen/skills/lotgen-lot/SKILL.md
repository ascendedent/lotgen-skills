---
name: lotgen-lot
description: >
  How to build a lot in LOTGEN and how to write a shot that renders what you meant.
  Use whenever the user wants to make an ad, a UGC clip, a product video or a shot list
  with LOTGEN, wants a prompt improved, asks why a render came back wrong, or asks how
  the vertical, the capture style, the look and the brand kit fit together. Trigger on
  "make an ad", "build a lot", "write the shots", "fix this prompt", "why does it look
  like that", "what format should this be". Creation never renders; read this before
  writing any prompt for this product.
---

# Writing a lot

A **lot** is a shot list, not a clip. Every shot renders on its own, can be
re-rendered on its own, and holds its own takes — that is what makes a single
bad shot a five-second fix instead of a re-run of the whole ad.

**The one thing to get right before anything else: creating a lot never
renders and never spends.** `run_marketing_recipe` and its siblings plan,
create, attach references and price. Nothing has been spent when the call
returns. `estimate_render` is read-only and prices anything. The money moves
at `render_scene` and `render_project`, and nowhere else.

---

## THE FOUR WORDS

| | |
|--|--|
| **lot** | the project — a named shot list with a look, a brand and an aspect |
| **shot** | one scene in it: a prompt, a duration, a model, its references |
| **take** | one render of one shot. A shot can hold many; one is mounted |
| **mounted** | the take that is the shot's answer — what export and the review link show |

The first successful take of a shot mounts itself. `select_variant` is how you
**overrule** that, not how mounting starts.

---

## CORE PRINCIPLE: WRITE WHAT A CAMERA COULD RECORD

A video model answers to what can be seen and measured. It does not answer to
mood.

- "premium feel" → *the bottle on brushed steel, one hard key from the left,
  the label edge catching a thin white line*
- "energetic" → *she turns fast enough that her hair swings past her shoulder,
  then stops square to camera*

Three rules that are not style preferences:

1. **Positive phrasing only.** "No people in frame" is a reliable way to get
   people in frame. Say what IS there. Every capture-style clause in this
   product is tested for negations for exactly this reason.
2. **One shot, one intent.** A prompt asking for two beats gets a model's
   average of them.
3. **Say where the light comes from and where the subject looks.** Those are
   the two gaps a model fills on its own, and it will not fill them the way
   you meant.

---

## THE FOUR LAYERS, AND WHO OVERRULES WHOM

A lot is composed, not typed. Four things stack, and they compose — they do
not compete:

```
format / structure   what the ad IS          list_ad_formats · list_ugc_structures
      ↓
vertical             what it is FOR          list_verticals
      ↓
capture style        what the camera IS      list_capture_styles
      ↓
look + brand kit     how it is GRADED        cinema_vocabulary · list_brand_kits
```

**The format or structure** is the beat pattern: how many shots and what each
one does. Marketing formats are ad shapes; UGC structures are script shapes,
eleven of them, and each one decides where the spoken line falls.

**A vertical** is a category contract. It fills in the format and the aspect
you did not name, appends a clause to every planned shot, and **refuses**
inputs it cannot work with before a single row is written — apparel with no
product image is turned down by name, and every gap is named at once rather
than one per round trip. Read the vertical's own `requires` rather than
guessing what it checks.

**A capture style** says how the thing was SHOT. It splices optics, motion,
lighting, grade and an ambient audio clause into every shot, and it
**overrules the lot's look** on the axes it names — the response tells you
which as `capture_overrides`. An override you discover in a render is one
nobody told you about, so read that field and repeat it to the user.

Three of the ten reach a talking-head script, plus `news`; the other six are
refused against any UGC structure, by name, because nobody is speaking to an
unattended camera and nobody's face is in front of a helmet cam. Selfie is not
on the list on purpose — it is a sub-mode of `mobile`, read off whichever
beats put the creator on camera, so there is nothing to pass.

**The look** is the grade and the camera language, and its job is to make a
six-shot lot read as one film without typing "same grade as the previous shot"
six times. Take the ids from `cinema_vocabulary`; an id it does not know is
ignored rather than pasted in as literal text. Colour belongs to a material
and a light — "crimson silk catching the cold spill from the corridor", not
"red and blue".

---

## THE WORKFLOW

1. **Know what can render.** `list_providers` — a workspace with no key for
   the provider you pick fails after the job is enqueued, and `key_error`
   means the stored key last failed to render. `get_provider_health` only
   describes jobs this workspace has already run.
2. **Get the product in.** `save_product`, or `extract_product` from a
   product page. A saved product carries its own photography, and the recipe
   attaches it to every shot as a reference pack — which is what actually
   holds one bottle across four clips.
3. **Choose the four layers.** Ask the person the one question you cannot
   infer, not four.
4. **Build it in one call.** `run_marketing_recipe` or `run_ugc_recipe` —
   resolve, plan, create, attach, price. `estimate_usd` on the answer is what
   one take of every shot would cost, and it is a quote, not a charge. Use
   `plan_ad` / `create_ad_lot` (or `plan_ugc` / `create_ugc_lot`) only when you
   need to edit the plan in between; that pair exists for exactly that.
5. **Read the shots back and fix them.** `get_brief` is the one call to make
   before changing anything: every shot with the settings that decide its
   cost, which are done, which are blocked behind the shot they continue
   from, which cannot render at all, and what finishing the rest would cost.
   Then `update_scene`.
6. **Stop.** Rendering is a separate decision and it belongs to a person.
   `lotgen-review` picks up from there.

**A UGC lot requires a person.** Pass `creator_image_url` or
`creator_asset_id`; a prompt saying "a woman in her 30s" renders a different
woman in every shot. The creator leads the reference pack, ahead of the
product, because several models weight the first reference most heavily.

---

## EDITING A SHOT

- `update_scene` takes `if_unmodified_since` — the `updated_at` you read. A
  write that would overwrite somebody in the browser comes back **409**
  carrying the version the row actually holds. Re-read and decide again.
  **Never retry the same write**, which is how the overwrite happens anyway
  one round trip later.
- The value it wants is the **shot's** `updated_at`, not the job's. A take
  mounting itself bumps the shot, which is the usual reason a timestamp you
  were holding is refused.
- `reorder_scenes` takes `from_order` instead, because a reorder's version is
  the order itself.
- References: `set_scene_refs` by URL, or `apply_ref_pack` to mount a named
  set on a shot. `set_start_frame` decides the picture a clip opens on.
- A chained shot renders **after** its source has a finished take. Set
  `continuity_mode` to chain, and expect `render_project` to hold that shot
  back and list it under `waiting` until the source lands.

---

## BEFORE YOU HAND THE LOT OVER

- Every prompt states where the light is and where the subject looks?
- Everything positive — no "does not", no "without", no "avoid"?
- One beat per shot?
- The product's photography actually on the pack — `ref_count` checked, not
  assumed? An empty error list is not proof the reference attached.
- A person, not a description, on every UGC shot?
- Colour tied to a material and a light rather than listed?
- The vertical's refusals read, and the `capture_overrides` reported to the
  user rather than left in the response?
- Priced with `dry_run` before anybody is asked to say yes?
- And said out loud: **nothing has rendered and nothing has been spent.**
