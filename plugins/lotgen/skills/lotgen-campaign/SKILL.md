---
name: lotgen-campaign
description: >
  Build a whole campaign in LOTGEN — many SKUs, one format, one brand — from a product
  library or a set of URLs, and stop at a priced quote before anything renders. Use when
  the user wants ads for a range rather than one lot: "make ads for all our products",
  "a campaign for these eight SKUs", "one video per product", "quote me a launch",
  "build the content plan". Five stages, and the last one hands a number to a person.
  It never renders and never spends.
---

# Campaign

Five stages: **library → mix → dry run → build → quote**. The line between
stage 4 and stage 5 is the whole point of this skill: everything up to and
including creating the lots is free, and the moment after it is not.

**This skill does not render.** It ends with a total and a person's decision.
If the user says "and render them", that is `lotgen-review`, and it starts by
showing them the number again.

---

## STAGE 0 — What this workspace can actually do *(run silently, never narrate)*

`list_providers` and `get_workspace`, in one pass.

- A provider without `key_configured` cannot render, however good the plan is.
- `key_error` on a stored key means it last failed — an empty prepaid balance,
  a rejected credential. Do not build a campaign onto it.
- The workspace's spend cap and its unpriced policy come back from
  `get_workspace`. A cap that refuses unpriced models will refuse a
  marketplace slug outside the catalogue, and it is much cheaper to find that
  out now than at stage 5.

Say nothing about any of this unless it changes what you propose.

---

## STAGE 1 — The library, because a batch is library-backed by definition

`run_recipe_batch` takes **`product_ids`, up to 12**, not twelve briefs. So
the campaign starts by making sure the products exist:

- `list_products` first. Most of the time they are already there.
- `extract_product` reads a product page into a saved product with its
  photography. This is the fastest route from a list of URLs to a library.
- `save_product` for anything you were given as text.
- `add_product_images` where a SKU has no usable still — apparel and anything
  else whose vertical **requires** an image will be refused without one, and
  it is better to find the gap here than in the batch's refusal list.

**More than 12 SKUs is more than one batch.** Split them and say so; do not
quietly drop four.

---

## STAGE 2 — The mix

Ask the person the questions you genuinely cannot infer, and ask them
together. Everything here applies to **every** SKU, which is what makes it the
campaign rather than a lot:

- **Marketing or UGC.** `run_recipe_batch` takes `recipe`. Photodump and
  Explainer take no product, so there is nothing to iterate — those are single
  lots, not a batch.
- **The format** (`list_ad_formats`) or **the structure**
  (`list_ugc_structures`). Marketing takes a format; UGC takes a structure, a
  hook and a platform, and a platform decides the runtime and the aspect.
- **The vertical** (`list_verticals`) — read its `requires` before proposing
  it, because it refuses SKUs that cannot satisfy it, by name.
- **The capture style** (`list_capture_styles`), if the campaign has one look
  across it. It composes with the vertical rather than replacing it, and it
  overrules the lot look on the axes it names.
- **The brand.** `list_brand_kits`, or `create_brand_kit_from_url` to read the
  mark and the stated colour out of the brand's own site. Usually you should
  pass **no** `brand_kit_id` at all: each product brings whatever kit it is
  filed under, which is what a multi-brand library wants.
- **UGC needs a person for every SKU.** One `creator_asset_id`, cast once with
  `cast_creator`, is what holds one face across the whole campaign.

Do not ask about provider or resolution unless the person raised cost or a
platform spec. The defaults are the answer.

---

## STAGE 3 — Dry run, always, before anything exists

```
run_recipe_batch  recipe … product_ids … dry_run: true
```

This plans every SKU and creates nothing. What you are reading it for:

- **the refusals** — which SKUs the vertical turned down and why. Fix them
  (usually a missing product image) or drop them from the batch, deliberately;
- **the shot lists** — whether the beats are the ad the person described;
- **`total_estimate_usd`** — the campaign's price if every shot renders once.

Show the refusals before the total. A quote for eleven SKUs when the person
asked for twelve is a wrong number quietly.

---

## STAGE 4 — Build

Same call, `dry_run` off.

- `continue_on_error` defaults true: a refused SKU comes back as a named
  refusal **beside** the lots that were made.
- **There is no rollback.** With `continue_on_error` false it stops at the
  first refusal and reports what already exists — those lots are real, and
  deleting them is the person's decision, not yours.
- `total_estimate_usd` is null when a lot that exists could not be priced.
  Null is not zero. Say which lot and why rather than reporting a total with a
  hole in it.

Then read one back. `get_brief` on the first lot tells you whether the shape
you quoted is the shape you built.

---

## STAGE 5 — Hand over the number and stop

Report, in this order:

1. **what was built** — lots, shots, which SKUs;
2. **what was refused** — by name, with the reason and the fix;
3. **what it costs** — `total_estimate_usd`, and say plainly that it is one
   take of every shot, that a second take of a shot costs again, and that
   **nothing has been spent yet**;
4. **what happens next** — `render_project` per lot, and that it is theirs to
   say.

Then stop. Do not render because the answer looked like a yes. If they want a
different provider's price first, `estimate_render` is read-only and prices
anything without creating a job.

---

## RULES

- **Never render inside this skill.** Not one shot "to check". A test render
  spends the workspace's own key, and a campaign is where a habit like that
  becomes real money.
- **A refusal costs nothing**, so prefer being refused at `dry_run` to
  discovering a gap at stage 4.
- **Do not paper over a partial batch.** Eleven of twelve is an eleven-SKU
  campaign and the person needs to know which one is missing.
- **Fix the lots, do not rebuild them.** A wrong beat is `update_scene`; there
  is no reason to re-run a batch over one shot.
- One face for the campaign, one brand per product, one number at the end.
