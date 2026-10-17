---
name: lotgen-cost
description: >
  Money in LOTGEN — what a render will cost before it costs it, what a provider will
  silently change about a shot, what a workspace has already spent and on whose behalf,
  the monthly cap, and stopping a take that is already running. Use when the user asks
  "how much will this cost", "what have we spent", "which provider is cheapest", "set a
  budget", "stop that render", "why was this more than you quoted", or before committing
  to any provider. Quoting is free; read this before spending anything.
---

# Cost

Two calls spend money in this product — rendering a shot and rendering a lot —
plus the post passes. **Everything else is free**, including every way of
finding out what something will cost.

---

## THE QUOTE IS ALSO THE COMPATIBILITY CONTRACT

`estimate_render` says what one take would cost on a given provider, priced by
the adapter that would actually run it. Read-only: nothing is created, no key
is spent.

**But the price is only half of what it returns.** The other half is what that
provider will **change** about your shot, and that half is a contract:

> No degradation is ever applied that was not named in a quote.

Two kinds, and they are different decisions:

| kind | what happens | what you should do |
|--|--|--|
| **degrades** | the shot is still made, differently — five references become three, five seconds become six | decide whether that is acceptable, and tell the user |
| **blocks** | the shot is defeated and the render is refused | pick another provider before spending |

**Describe the shot you actually mean.** Pass the references and the continuity
mode. This matters more than it sounds: **without the references, the one
incompatibility that costs a real take — a creator plate handed to a
text-to-video-only model, and dropped silently — cannot be reported.** You will
get a clean quote for a render that will not carry the face you asked for.

That is the difference between a quote and a useful quote.

**Dry-run one shot of each kind, and read the route, not just the price.**
A lot has a few kinds of shot: references only, an opening frame only, a
frame and references, chained. Before rendering the lot, run
`render_scene` with `dry_run` on one of each and read three things off the
answer: the price, the incompatibilities, and the resolved model, which
names the endpoint that will run (`...-image-to-video`,
`...-reference-to-video`, `...-text-to-video`). The route is what decides
whether the face, the product and the opening frame reach the model at all,
and a price can be right to the cent on a route that drops them. On
2026-10-13 a board run spent $13 on five shots whose quotes were exact and
whose renders ignored the creator, the product and the opening frame; one
$0 dry run per kind would have shown the route first.

---

## CHOOSING A PROVIDER: THREE TOOLS, THREE QUESTIONS

They are easy to confuse and they answer different things.

| question | call |
|--|--|
| **can** this provider do it, and is a key stored? | `list_providers` |
| **what does it charge** for this shot? | `estimate_render` |
| which models can this key run? | `list_provider_models` |
| how has it behaved **for us**? | `get_provider_health` |

`list_providers` reports capability, **not price** — that is the confusion
worth avoiding. It also carries whether a key is stored and whether that key
last failed. **A stored key that last failed cannot render**: an empty balance
or a rejected credential. Pick another rather than queueing a job that will
fail after enqueueing.

`get_provider_health` is success rate and latency **from this workspace's own
jobs**. It says nothing at all about providers the workspace has never used —
so an absent provider there is not a bad provider, it is an unknown one.

An uncatalogued model still submits but **cannot be quoted**, and the
workspace's unpriced policy decides whether that is allowed.

---

## THE CAP

`get_workspace` carries the monthly spend cap, the unpriced policy, the plan
and seats — and it is **read-only**.

That is worth saying because it was not always true: reading the cap once meant
calling the update, which is a write, so the only way to see the limit was to
risk changing it. Never reach for a write to answer a question.

`update_workspace` sets the cap and how it treats a model that cannot be
priced. Clearing the cap is passing nothing.

A lot has its own cap, which the server enforces on every render in that lot.
`update_lot` changes it (and the lot's name), so a run that splits its budget
across two lots can move money between them instead of starting a third.

**An API key has no role; it has scopes.** It is a credential rather than a
seat, so `role` comes back empty for one and `key.scopes` says what it may do
instead: `read`, `write`, `render`, or `admin`, with a `preset` name for the
three combinations a person picks from. A key bound to one brand carries
`brand_kit_name`, makes every lot under that brand, and sees only that brand's
lots and usage. Say these back when asked who you are connected as. A tool the
key cannot call is not in your list; one reached anyway answers
`insufficient_scope` naming the scope, and that is not an error to retry.
Nothing in this area changes a role, removes a member or mints a key — keys are
minted from a signed-in session, and a tool that could widen its own
permissions would not be a permission.

---

## WHAT HAS BEEN SPENT

`get_usage` gives spend by lot, by provider, by person, by brand and by API
key, scoped to a month or across all time. `by_brand` is the roll-up a partner
fronting several client brands asks for; `by_key` is what each integration
spent. A brand-bound key sees only its brand's rows and totals.

One client's statement is the same call narrowed: `brand` (a kit id from
`list_brand_kits`) gives one brand's lots, `key` (an id from `by_key`) one
key's takes, and every figure in the answer narrows with it, the cap
excepted. Both need the Partner add-on; a credential bound to one brand may
name only its own, and asking for another is refused by name (`brand_scope`).
The answer echoes `brand` and `key`, so say which statement you are reading.

Three things about the numbers that will otherwise be reported wrongly:

1. **It bills on provider-reported actuals where the provider reports them, and
   on our estimate where it does not.** So a total can be part measured and
   part estimated. Say which when it matters.
2. **An agent is not a member.** An API key's spend belongs to whoever minted
   the key, and there is a separate figure for the part of that person's spend
   that arrived through a door other than the web app. Which KEY spent it is
   `by_key`, and it is a different question from whose money it was.
3. **The unattributed row is a row, not a remainder.** Takes made before the
   column existed, and anything the bootstrap token enqueued, sit in their own
   entry with no person on it — which is why the people sum to the total. Do
   not compute it by subtraction and do not describe it as missing.

`get_usage` carries the cap too, beside the spend it governs, so one call
answers "how much, and how much is left".

A month of takes does not fit in one answer: it came back at 124,085
characters and was cut off. Ask with `summary: true` first (totals, no
per-take rows), and `project_id` when the question is one lot.

---

## A CEILING ON ONE CALL

`render_scene`, `render_project` and `edit_still` take `max_usd`, the most
that one call may spend. The call quotes itself first and is refused, with
nothing queued, when the quote is higher or cannot be priced. The workspace
cap is often unset, so when the user names a budget, pass it here as well.

---

## DRAFT LOW, DELIVER HIGH: `upscale_to`

A clip can render at a low resolution and have only the take that is KEPT
upscaled to the size it is delivered at. Set it on the shot (`add_scene` or
`update_scene`): `resolution: "480p"`, `upscale_to: "720p"`.

- **What it costs.** Measured 2026-10-14 on ARK Seedance 2.0 Fast, 4 s with
  sound: native 720p $0.49; 480p $0.22 plus the upscale (Topaz precision on
  the fal key) about $0.04 to $0.08. The draft also renders faster.
- **Only the winner is upscaled, once.** Three variants at 480p cost three
  drafts and ONE upscale. The first take to land mounts itself and is
  upscaled; `select_variant` on another draft upscales that one instead. The
  upscale mounts itself and is then the shot's file everywhere: export, Drive,
  assembly, Premiere.
- **Quote it.** `estimate_render` with `resolution: "480p"` and
  `upscale_to: "720p"`, beside the same call at `resolution: "720p"`. The
  answer's `upscale` is the pass; `total_usd` includes it once. `render_scene`
  and `max_usd` count it too.
- **When not to.** An upscale is faithful: it does not invent the detail a
  native render has. For a face in close-up, small lettering on a product, or
  fine texture the client will inspect, compare one upscaled take against
  native before setting it on a whole lot.
- It needs a **fal key** (refused before anything is spent without one), and
  only a clip drafted BELOW its target takes it.

### Seedance 2.5's own draft mode: `draft`, then `finalize_take`

Seedance 2.5 (on ARK and on EvoLink) renders a true draft: `render_scene`
with `draft: true` on a shot at `resolution: "480p"` makes a 480p take billed
as an ordinary 480p render, and `finalize_take` on the take you keep renders
that same shot NATIVELY at 1080p from the draft (same prompt, pictures, seed,
framing and length; small detail such as lettering or a crowd can still
differ). Not an upscale: the model renders it again at 1080p.

- **Quote both halves.** The draft's dry run says `finalize_usd_per_take`
  beside its own price; `finalize_take` with `dry_run` quotes the one take.
  5 s, quoted 2026-10-16: ARK $0.52 draft, $2.87 final; EvoLink $0.69 draft,
  $3.69 final. Draft four variants and finalize one, and you pay four drafts
  and one final. Measured on ARK, 2026-10-16, 4 s with sound: $0.4155 draft
  (76 s) and $2.2982 final (286 s), each its quote to the token; the final
  kept the draft's seed, length and framing. Measured on EvoLink, 2026-10-17,
  the same 4 s shot: $0.55 draft (85 s, 854x480) and $2.955 final (46 s,
  1920x1080), each exactly its quote; the final is the same shot.
- **Within 24 hours.** EvoLink keeps a draft finalizable 24 h after it
  finishes and the take says when (`draft_expires_at`); ARK states no limit
  (the 48 h every ARK task carries is its queue's limit, not the draft's).
- The final lands as a new take and replaces the draft as the mounted take
  when the draft was mounted; finalize a variant that was not, and the final
  waits beside it to be picked.
- Refused before anything is spent on any other model, and on a shot not at
  480p.

---

## STOPPING AND DISCARDING

- `list_jobs` — every take on a shot, newest first.
- `get_job` — one take's progress.
- `cancel_job` — stop a take that is queued or running.
- `delete_job` — discard a take. **The bytes stay in the bucket**; what changes
  is that the take stops counting as the shot's output. It is not a way to
  reclaim storage.

---

## THE ORDER, BEFORE ANY MONEY MOVES

```
list_providers        can it, and is there a working key
       ↓
estimate_render       what it costs AND what it will change
       ↓              ← pass the refs, or the silent drop goes unreported
read the incompatibilities, decide, and TELL THE USER
       ↓
get_workspace         is there room under the cap
       ↓
render_scene / render_project   ← money; pass max_usd, the user's ceiling
       ↓
get_job · cancel_job if it is wrong
       ↓
get_usage             what it actually came to
```

---

## CHECKLIST

1. Did you quote with the **real** shot — references and continuity included?
2. Did you read the incompatibilities, not just the number?
3. Did you dry-run one shot of each kind and read the resolved model, the
   route it will take?
4. Is anything being degraded, and does the user know?
5. Does the workspace hold a working key for that provider?
6. Is there room under the cap?
7. Would drafting at 480p with `upscale_to`, or on Seedance 2.5 a `draft`
   finalized with `finalize_take`, deliver the same shot for less? Say the
   option when a lot is large or the user is iterating.
8. When reporting spend: measured or estimated, and is the unattributed row
   being described as a row rather than a gap?
