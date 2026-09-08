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

**A workspace key has no role.** It is the workspace's own credential, not a
seat, so the role comes back empty for one. Nothing in this area changes a
role, removes a member or mints a key — membership is what decides what every
other tool may do, and a tool that could widen its own permissions would not be
a permission.

---

## WHAT HAS BEEN SPENT

`get_usage` gives spend by lot, by provider and by person, scoped to a month or
across all time.

Three things about the numbers that will otherwise be reported wrongly:

1. **It bills on provider-reported actuals where the provider reports them, and
   on our estimate where it does not.** So a total can be part measured and
   part estimated. Say which when it matters.
2. **An agent is not a member.** A workspace key's spend belongs to whoever
   minted the key, and there is a separate figure for the part of that person's
   spend that arrived through a door other than the web app.
3. **The unattributed row is a row, not a remainder.** Takes made before the
   column existed, and anything the bootstrap token enqueued, sit in their own
   entry with no person on it — which is why the people sum to the total. Do
   not compute it by subtraction and do not describe it as missing.

`get_usage` carries the cap too, beside the spend it governs, so one call
answers "how much, and how much is left".

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
render_scene / render_project   ← money
       ↓
get_job · cancel_job if it is wrong
       ↓
get_usage             what it actually came to
```

---

## CHECKLIST

1. Did you quote with the **real** shot — references and continuity included?
2. Did you read the incompatibilities, not just the number?
3. Is anything being degraded, and does the user know?
4. Does the workspace hold a working key for that provider?
5. Is there room under the cap?
6. When reporting spend: measured or estimated, and is the unattributed row
   being described as a row rather than a gap?
