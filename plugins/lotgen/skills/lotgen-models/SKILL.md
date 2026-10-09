---
name: lotgen-models
description: >
  Choosing which provider and which model a shot renders on — the judgement this product
  deliberately does not make for you. Use when the user says "which model should I use",
  "what should this render on", "is fal cheaper than going direct", "compare the
  providers", "can this do twelve seconds", "which one makes audio", "why did it come
  back 16:9", "why is the face missing from my render", "why was that quote unknown", or
  names a model and you need to know what it will change about the shot. Choosing costs
  nothing. Read this before pointing a lot at a provider, and long before rendering one.
---

# Choosing a model

**Nothing in this document spends money.** Listing providers, listing models,
quoting a shot and reading the workspace are all free and all read-only. Money
moves at `render_scene`, at `render_project`, and at the paid post passes —
nowhere else. So there is never a reason to guess: quote the two candidates and
read the answer.

Every number below is an **illustration of an axis**, not a lookup. Rates,
limits and shelves change; the tools answer at runtime. What is worth carrying
in your head is which axis decides, and what a mismatch does to a shot you have
already paid for.

---

## THE FOUR QUESTIONS, AND THE THREE TOOLS PEOPLE MERGE

| question | call |
|--|--|
| what CAN this provider do, and is there a working key? | `list_providers` |
| which models can this key run, and which are switched on? | `list_provider_models` |
| what does it CHARGE for this shot, and what will it change? | `estimate_render` |
| how has it behaved **for us**? | `get_provider_health` |

**`list_providers` reports capability, not price.** That is the confusion this
section exists for. A provider being able to make a ten-second 1080p clip with
audio says nothing about whether it is the cheapest, the dearest or the only
one that can. Price is a separate question with a separate call, and the answer
depends on the shot.

`get_provider_health` is success rate and p50/p90 latency **from this
workspace's own jobs**. A provider the workspace has never used is absent from
it — which means unknown, not bad. Do not report an empty health row as a
warning.

---

## A MARKETPLACE ROW'S CAPABILITY IS A UNION

The single most expensive misreading here.

fal, EvoLink and Runway are **gateways**: one stored key runs many labs' models,
and each model is a row of data describing how to spell one request rather than
an adapter of its own. When `list_providers` reports what such a provider
supports, it reports the **union across the whole shelf** — the longest
duration any row reaches, every resolution tier anything publishes, the highest
reference count on the list. **Nothing you can actually run does all of it.**

So a marketplace's headline capability is a promise no single model keeps. Ask
per model instead: `list_provider_models` for the rows, and `estimate_render`
with `model` set, which narrows to that row's own limits before it prices
anything.

A direct provider does not have this problem in the same way, but two of them
have a smaller version of it: one vendor key can run more than one model family
— BytePlus ARK runs two Seedance generations off a single key, and they differ
by a factor in price and by a spread of seconds and resolution tiers in what
they accept. **One lab is not one model.** `list_provider_models` returns an
empty list only where the provider genuinely runs one thing.

---

## MARKETPLACE OR DIRECT

Three real consequences, none of them "marketplaces are dearer".

**1. The same model sits on several shelves at different prices, and the
ordering is not consistent.** One gateway carries a video model at roughly
three times the direct rate on the vendor's own key; the same gateway carries
another at about half what the vendor charges directly. Both are true at once.
This is why some catalogue rows ship **switched off** with the price comparison
that decided it attached to the row — never a bare flag, because "off" with no
reason is a decision nobody can audit. It stays a default and never a gate: an
override turns it back on, and naming the model explicitly always works.
**Compare with `estimate_render` on the same shot, never by reputation.**

**2. An uncatalogued model still submits, and cannot be quoted.** Paste a slug
the catalogue has no row for and a gateway will send it with a generic body —
prompt, duration, aspect, and the first reference as an opening frame — but the
estimate comes back **unknown** rather than invented. That is deliberate: a
wrong price corrupts the dry run, the kept-versus-discarded meter and the spend
cap all at once. What happens next is the workspace's `unpriced_policy`, on
`get_workspace`: `block` refuses it, `allow` runs it and it spends against a cap
it could not be counted against in advance. Decide that deliberately with
`update_workspace` before pointing anything at a slug the product does not know
— and note that one direct provider quotes unknown for **everything** until its
international billing has been opened once, so it needs `allow` to render at
all.

**3. Whether the bill is ever measured is a property of the provider.** Some
report what a job actually cost and the workspace's spend is reconciled against
it; one large gateway returns no per-request cost at all, so every row on it is
billed on our estimate forever. That is not a failure to reconcile, and it is
the reason a total from `get_usage` can be part measured and part estimated.

---

## ONE KEY, SEVERAL PROVIDERS — AND A KEY THAT LAST FAILED

Keys belong to a **platform**, not to a model. One Google key pays for three
providers here; one ARK key pays for a video provider and a stills one; one
Runway key pays for its video shelf and its stills shelf. So "we have no key for
that" is often wrong, and the provider row says where a borrowed credential
comes from rather than leaving it a surprise.

Two fields decide whether a render can happen at all:

- `key_configured` — a key is stored.
- `key_error` — that key **last failed to render**, in the provider's own
  words: an empty prepaid pack, a rejected credential.

**A stored key with `key_error` set cannot render.** A prepaid Kling pack at
zero is still a configured key, and treating it as ready is how a submit gets
spent on nothing. Pick another provider rather than queueing a job that fails
after it is enqueued.

Read the row's `note` too. It carries the vendor fact that decides the choice
and lives nowhere else — prepaid packs with a high floor, a cheap direct
provider that is **not available in the US** so the gateways are the only route,
a stills model that must be switched on in the vendor's own console first.

---

## THE AXES THAT ACTUALLY DECIDE

A model changes a shot it cannot make as asked. `estimate_render` names every
change before you spend, and the rule behind it is absolute: **no degradation is
ever applied that was not named in a quote.**

| axis | what a mismatch does to your shot |
|--|--|
| **references** | a model that takes none **drops them all** — the creator's face is not in the render, and it succeeds anyway. Over the cap: the first N are sent and the rest stay on the pack. Some rows use a reference as the **literal opening frame**, so a plate in the wrong shape ships letterboxed. A clip-only model refuses the stills and says so, which is different from taking no references at all. A few rows **require** a picture and cannot render without one |
| **duration** | a fixed set snaps to the nearest and is **billed at the nearest** — one provider accepts 4, 6 or 8 seconds and refuses 5, quoting a range that includes it. Over the ceiling comes back short; under the floor is delivered and billed at the floor |
| **resolution** | a tier the model does not offer is quietly **downgraded and billed at the tier that ran** |
| **aspect** | an unsupported shape becomes the nearest of the same orientation. One provider has no aspect parameter at all: text-to-video is 16:9 and image-to-video follows the frame it was given |
| **audio** | some models **always score and cannot be silenced** — the track arrives and is billed whether the shot asked for one or not. Others make none, so a line written for the shot goes unspoken |
| **first frame** | a shot chained to the one before it cannot render on a model that will not start from another shot's last frame |

### Degrade or block

| severity | meaning | what to do |
|--|--|--|
| `degrades` | still the shot, differently | decide, **tell the user**, and pass `accept_degradations` if they agree |
| `blocks` | the reason the shot exists is gone | pick another model. There is no override and there will not be one |

`accept_degradations` answers `degrades` only. A talking head whose creator
reference would be dropped is the wrong model, not a shot somebody consents to.
On `render_project` the refusal applies to the **whole batch** — one degrading
shot stops the other nine — which is why the flag exists on that door too.

**Pass `refs` and `continuity_mode` when you quote.** `estimate_render` prices a
hypothetical shot rather than reading one, so without them the single
incompatibility that costs a real take — a face silently dropped — cannot be
reported, and you get a clean quote for a render that will not contain what you
asked for. Where the shot is driven by a clip, pass `input_video_sec` and
`input_video_fps`: three rows bill in **frames**, so the same clip costs twice
as much at 60fps as at 30, and without the rate they quote unknown rather than
guessing.

### Seeds, and why variants can fail on a working key

Variants are the same shot rendered N times differing only by seed. One provider
**refuses a seed outright**, which fails every variant batch on it; another
drops it silently, so the takes differ but none can be reproduced. If the user
wants a reproducible re-roll, that alone decides the provider — check before
queueing four jobs.

---

## CHOOSING, BY WHAT THE JOB IS

| the job | the axis that decides | how to check |
|--|--|--|
| a talking head, or any shot carrying a creator plate | takes reference images at all | `estimate_render` with `refs` set to the real count |
| a shot that continues the previous one | last-frame chaining | quote it with `continuity_mode` |
| a product across several angles | the reference cap **on that model**, not on the marketplace | `list_provider_models` |
| dialogue or sound inside the clip | makes audio, and whether it can be turned off | `list_providers`, then quote with `with_audio` |
| a strict 9:16 or 4:5 delivery | the aspect list — several providers refuse 4:3, 3:4 and 4:5 outright | `estimate_render` with `aspect_ratio` |
| anything longer than about ten seconds | the ceiling on the chosen row | `list_provider_models` |
| fixing a clip you already have | this is not a render at all | `list_video_edit_tiers`, and `lotgen-post` |
| a still | the provider's modality — stills are sized by token budget, not pixel height | `list_providers` |
| a spoken line | the speech provider, billed per character | `list_providers` |
| "the cheapest one" | nothing, until you quote it | `estimate_render` on each candidate, same shot |

**A model that fixes a take is a different shelf from a model that makes one.**
`list_video_edit_tiers` rows name a `provider` of their own, and a workspace can
hold a key for one tier and not another. Do not assume the provider a lot
renders on can also edit it.

---

## THE ORDER

```
list_providers          can it, is there a key, did that key last fail
       ↓
list_provider_models    which row — the marketplace union is not a model
       ↓
estimate_render         per candidate, with the REAL shot: refs, continuity_mode
       ↓                read incompatibilities, not just the number
get_workspace           the cap, and the unpriced_policy
       ↓
render_scene            ONE shot first  ← money
       ↓
get_job                 cancel_job if it is wrong
       ↓
render_project          the rest, once the choice is proven
```

**Render one shot before rendering the lot.** The expensive mistake in this
product is not a bad model, it is a bad model applied to six shots at once. One
take on the candidate answers what no capability list can: whether it looks
right.

---

## CHECKLIST

Before you point a lot at a provider:

1. Did you quote the **model**, or only the provider? A marketplace's
   capabilities are a union nothing runs.
2. Did the quote carry the real shot — references, continuity, and the clip's
   length and frame rate where there is one?
3. Did you read the incompatibilities, and is anything `blocks`?
4. Have you told the user what will be degraded, in the provider's own words,
   before asking them to accept it?
5. Does the workspace hold a key that has not just failed, and do you know which
   platform it comes from?
6. Was the model quotable at all — and if not, is `unpriced_policy` set the way
   the user would want?
7. Did you compare at least two candidates on price, rather than assuming direct
   is cheaper than a gateway or the reverse?
8. Is one shot rendered and looked at before the other five are queued?
