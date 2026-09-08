---
name: lotgen-runway
description: >
  Runway's seven recipes in LOTGEN — a whole finished piece from named inputs, with no
  model to pick and no prompt vocabulary to learn. Use when the user says "make an ad
  from these product photos", "a creator holding our product", "swap the product in this
  clip", "translate this ad", "campaign images", "stock images of this", or wants a
  finished result rather than a shot to direct. Runway bills on credits that are debited
  when the call is accepted and are NOT refunded if you cancel, so the dry run matters
  more here than anywhere else in the product.
---

# Runway recipes

A recipe is a **whole finished piece from named inputs**. No model to pick, no
prompt vocabulary to learn — you supply the pictures and the recipe does the
rest. Seven of them, split across two tools by one question:

> **Does this make a piece from nothing, or change one you already have?**

| | tool |
|--|--|
| makes one from nothing, and lands as a take of a shot | `originate_runway_recipe` |
| changes a take you already have | `run_runway_recipe` |

**Each tool refuses the other's recipes by name.** That is a helpful error, not
an obstacle — if you get it, you reached for the wrong one of the two.

---

## THE MONEY RULE, WHICH IS DIFFERENT HERE

**Credits are debited the moment the call is accepted, and Runway does not
refund a cancelled task.**

Everywhere else in this product, cancelling a queued job is a way out. Here it
is not: the credits are gone whether or not you keep the result. So the dry run
is not a nicety, it is the only chance to change your mind. **Always dry-run a
Runway recipe first.**

All seven run on the workspace's **Runway key**. A workspace without one is
told which key it needs, before anything is spent — but check with
`list_providers` first rather than finding out from a refusal, because that
call also says whether the stored key last **failed**. A key with an empty
balance is stored, looks configured, and cannot run a recipe.
`list_provider_models` says what that key can reach.

---

## THE FOUR THAT ORIGINATE

These run **on a shot** and land as a take of it — so the result is comparable
against anything the studios rendered, pickable as the winner, and exportable
in the same pack. They are not a side channel.

**The shot's KIND has to match**: the three video recipes need a video shot,
the image one needs a stills shot. Aspect and prompt default to the shot's own.

| recipe | takes | the thing to know |
|--|--|--|
| a finished product ad | product pictures | four seconds or more; the only recipe with two contract versions, and the newer one is pinned |
| a creator holding your product | who is on camera, plus **one** product | **VERTICAL ONLY** — a landscape ask is refused rather than quietly turned on its side |
| several shots from one prompt | a prompt | the duration is **exactly** 5, 10 or 15 — not a range, not a maximum |
| marketing stock images | a prompt | **FOUR pictures**, each a different camera angle on the same subject |

**The stock-image recipe returns four pictures and the count is not askable.**
No field this product can send moves it, and the price is flat for all four. It
lands as four takes of the shot, to choose between. Plan for four — a workflow
written expecting one will mis-report what happened, and a user told to expect
one will think something went wrong.

---

## THE THREE THAT CHANGE SOMETHING

These run on a take that already exists.

| recipe | needs | lands as |
|--|--|--|
| replace the product inside a clip | the new product's pictures **and** a picture of the one being replaced | another take of the same shot |
| remake a still in another language | the target language, one of twenty-two | a picture of the shot |
| campaign pictures from one still | a still plus a prompt | pictures of the shot |

Two of these have a **flat price** — one figure, no field that could make it
cost anything else. That makes them unusually easy to quote exactly, which is
worth saying to a user who is used to per-second estimates.

The product swap needs **both** pictures: the new product and the one being
replaced. Supplying only the new one is the common mistake, and the recipe
cannot infer what it is looking for.

---

## HOW THIS FITS THE REST OF THE PRODUCT

A recipe is an alternative to directing a shot yourself, not a replacement for
the craft. Reach for one when the user wants a **finished thing from assets
they already have**, and for the studios when they want control over what each
shot is.

Because an originated recipe lands as an ordinary take, everything else in the
product applies to it unchanged:

- `get_job` follows it. A recipe is a job like any other and does not block.
- `select_variant` mounts it if it wins.
- `label_take` names it so the export is legible — worth doing when four stock
  images land at once and the shot title cannot tell them apart.
- `estimate_render` prices a conventional render on another provider, which is
  the number a recipe is competing against. Compare before committing, because
  the credits are not coming back.
- `upscale_take` and the rest of the post family work on the result.
- `export_project` ships it in the same pack as everything else.

The one thing a recipe does NOT do is replace `render_project` for a whole lot:
recipes fill one shot at a time.

---

## CHECKLIST

1. Originate or change? Pick the right one of the two tools.
2. Does the shot's kind match the recipe — video for video, stills for images?
3. **Dry run.** Credits are debited on acceptance and never refunded.
4. Does the workspace hold a Runway key?
5. Vertical, if the recipe is the creator-with-product one.
6. Duration exactly 5, 10 or 15 where the recipe says so.
7. Are you expecting **four** pictures where four is what arrives?
8. For a swap: did you supply the old product as well as the new?
