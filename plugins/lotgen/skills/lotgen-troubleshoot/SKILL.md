---
name: lotgen-troubleshoot
description: >
  Work out why a LOTGEN render disappointed, and fix it without paying twice. Use when
  the user says "it came back wrong", "that is not what I asked for", "the render
  failed", "it timed out", "the look did nothing", "her face changed between shots",
  "the clip is cut off", "why is it eight seconds when I asked for seven", "nothing is
  happening", "it is still queued", "the export is missing a shot", "the client saw the
  wrong take", or "my edit was refused". Also use before re-rendering anything, because
  re-rendering is the expensive answer and is usually not the right one. Every check in
  here is free.
---

# It came back wrong

**The reflex is to render it again, and that is the only move in this file
that spends money.** Creation never renders and never spends; money moves at
`render_scene`, `render_project`, and the paid post passes. Everything below,
up to the last section, costs nothing — so run the checks in order and buy the
second take only if they all come back clean.

The order matters because the cheap checks answer the expensive question. A
take that timed out may already exist. A shot that "ignored" a look never had
one. A render that came back at the wrong length was quoted at that length and
nobody read the line.

---

## FIRST: THE TAKE MIGHT ALREADY BE THERE

**A failed take has not necessarily failed.** The worker stops watching at the
provider's own ceiling, measured from the moment the provider accepted the job,
and fails it with a message that says so — *it may still have charged for this
render*. We stopped watching; the provider did not stop working.

`recheck_take` asks the provider once more, for free, and a recovered take
lands through exactly the same path as one that never timed out: same file,
same measurements, same provenance. Not a second-class take.

It refuses in four different ways and **each refusal is a different diagnosis**,
so read the sentence rather than moving on:

| what it says | what it means |
|--|--|
| a re-check is already queued | wait for it; asking twice changes nothing |
| only a failed take can be re-checked | the take is queued, running or already done — you are looking at the wrong job |
| this take never reached the provider | it failed before submit, so nobody rendered anything and nobody charged |
| failed in a way the provider will not reverse | a rejected key, a refused prompt, a malformed request. Re-rendering is the only option, and it will fail the same way until the cause is changed |

That last row is the one worth slowing down on: a definitive failure repeats.
Re-rendering an authentication failure buys the same failure again.

---

## THE SYMPTOM TABLE

Start at the top. Every check in the third column is free.

| symptom | usually | cheapest check | fix |
|--|--|--|--|
| "the render failed" / "it timed out" | we stopped watching at the ceiling | `recheck_take` | recover it, or re-render once the recheck misses |
| "it is still queued" | it is queued, or it is chained and waiting | `get_job`, then `get_brief` | wait, or release the shot it waits on |
| "nothing rendered at all" | no key for that provider, or a stored key that last failed | `list_providers` | fix the key, or pick a provider the workspace has one for |
| "I asked for 7s and got 8" | the model makes fixed durations | `get_brief` | nothing is broken — or change the model |
| "the look did nothing" | an id the vocabulary does not know | `cinema_vocabulary` | set it again with a real id |
| "her face is a different person" | no anchors on the persona, or the references never reached the model | `estimate_render` with `refs` | anchors, or a model that takes references |
| "the edit came back cut" | the tier's window is shorter than the clip | `list_video_edit_tiers` | a row with a longer window |
| "the export is missing a shot" | that shot has no delivered take | `get_brief` | render it before exporting |
| "the client approved the wrong clip" | the shot had no mounted take | `get_project` | `select_variant`, then a fresh link |
| "my edit was refused" | somebody wrote first | re-read `get_brief` | re-read, decide again, write once |
| "which take are we even talking about" | four takes, one title | `search_takes`, `list_jobs` | `label_take` so it survives the conversation |

---

## THE QUOTE ALREADY TOLD YOU

Most "that is not what I asked for" is a degradation that was named before the
money moved and nobody repeated it to the user.

**The contract is that no degradation is ever applied that was not named in a
quote.** A quote carries two severities. `degrades` still makes the shot,
differently — five references become three, five seconds become six, an
unsupported resolution is rendered and billed at the closest one the model
offers. `blocks` defeats the shot and is refused at the render door, by name,
naming the model, because the fix is nearly always to change it.

`get_brief` prints the degradations per shot as a line of its own — what was
asked, and what will be billed. **The price beside a shot is always the price
of what will actually be made**, so a shot reading 7s on a model that renders
4, 6 or 8 seconds is quoted at eight and delivered at eight. The number was
right; the length beside it was the request.

**`accept_degradations: true` is a blanket yes.** On `render_project` it
applies to every shot in the batch, not to the one you were thinking about. It
is how a lot ends up rendered at a resolution nobody chose. Send it only after
reading the specific degradations back to the person.

---

## THE ONE THE QUOTE CANNOT TELL YOU UNLESS YOU ASK

`estimate_render` prices a shot you describe, not a shot that exists — so it
knows only what you pass it. **Without `refs`, it prices a shot with no
references and reports nothing about them.** That is the difference between a
quote that says "this model takes no references, the plate would be dropped and
the render would not contain the person" and a quote that says nothing at all.

The render door counts the shot's real references and refuses a blocking
pairing, so a creator plate is no longer dropped in silence. What it cannot
undo is the **choice**: you compared providers on a quote that did not describe
your shot, wrote a whole lot against the cheapest one, and find out at the
render door that it cannot make it. Pass `refs` and `continuity_mode` — and
`mode` and `input_video_fps` where a reference clip is involved — every time.

Two more things `estimate_render` is not: `list_providers` says what a
provider can do rather than what it charges, and `list_provider_models` says
which models a key can run. An uncatalogued model slug still submits and simply
cannot be quoted, which is a different problem from being unavailable. Money
questions belong to **lotgen-cost**.

---

## THE LOOK THAT DID NOTHING

`set_lot_look` stores what you send it and **echoes it back in its own answer**.
That echo confirms it was stored. It does not confirm it was recognised.

An id the vocabulary does not know is **dropped at render time** rather than
pasted into the prompt as literal text — right, because a prompt should never
carry a word the product cannot define, and invisible, because no tool on this
surface reports it. The whole symptom is that the render looks untouched.

So the check is manual and it is free: read every id in the look back against
`cinema_vocabulary`. One typo is silently no look at all.

Two other reasons a look looks inert, both correct behaviour and both covered
at length in **lotgen-refs**: the look never edits the prompts, so a shot reads
identically before and after; and a capture style overrules the look on the
axes it names, reported as `capture_overrides`. The composed prompt — the
string the provider actually receives — is not returned by any tool here, so
the composition cannot be inspected directly. Check the inputs instead.

---

## THE FACE THAT DRIFTED

Two causes, and they need opposite fixes. Tell them apart before changing
anything.

**The references never reached the model.** Quote the shot with `refs` set to
what it actually mounts. A model that takes none, or takes fewer than the pack
holds, says so — and the ones beyond its cap simply stay on the pack while the
first few are sent. This is a model problem and no amount of prompt writing
fixes it.

**Or the persona has nothing to hold on to.** A likeness held by reference
survives on anchors: freckles, a scar, a gap tooth. `plan_influencer`'s
`distinguishing` field is where they go, and **a face without any drifts
between shots** — the reference is doing all the work and the model is
inventing the rest each time.

The durable fix is one person, not one description: render the plate, pick the
best face, `cast_creator` on that take, and pass the `creator_asset_id`
everywhere afterwards. A prompt saying "a woman in her 30s" renders a different
woman in every shot; **lotgen-lot** says why.

---

## THE CLIP THAT CAME BACK CUT

A post pass runs on a tier, and **the tier's window decides how much of the
clip survives**. The default edit tier returns roughly five seconds whatever it
is handed, so a twelve-second take edited on it comes back cut while another
row would have returned it whole.

`list_video_edit_tiers` for `restyle_take` and `edit_take_video`;
`list_video_region_tiers` for `reframe_take` and `clean_plate_take`. Read the
window off the row rather than off any sentence, including this one. A clip
outside the row's stated window is refused by name instead of being queued and
then rejected — which is the good outcome, and the reason to read first.

**lotgen-post** carries the rest: which pass keeps what, the several different
things a price can be *per*, and the pass whose resolution and length trade the
wrong way round.

---

## THE LOT THAT DELIVERED SHORT

A shot with **no delivered take at all** becomes a gap on the exported
timeline and no file in the pack. That is deliberate — a gap is honest and a
path to a file that is not there relinks nothing — but it means the lot is
short by exactly that shot, and the export will not stop you.

`get_brief` answers this before anybody downloads anything: how many shots have
a mounted take, which are blocked behind the shot they continue from, and which
**cannot render at all**. That last state is a real one and it does not resolve
itself: a first shot set to continue from a previous shot has nothing to
continue from, and will sit unrendered forever while looking like ordinary
progress. `get_brief` names it rather than reporting it as merely not done.

A related and quieter one: `export_project` ships the shot's mounted take, and
where nothing is mounted it falls back to the **newest** take. A review link
does the same. So a shot with three takes and no winner ships whichever
finished last — which is why `select_variant` before sharing is not tidiness.
**lotgen-review** has the full delivery sequence.

---

## THE WRITE THAT WAS REFUSED

A write carrying `if_unmodified_since` that would overwrite somebody comes back
**409, carrying the version the row actually holds**. Somebody edited in the
browser, or a take mounted itself and bumped the shot.

**Never retry the same write.** Re-read, decide again with what is there now,
and write once. Retrying is how the overwrite happens anyway, one round trip
later.

The value wanted is the **shot's** `updated_at`, not the job's — a job
timestamp a millisecond off the scene is refused as stale. `get_brief` prints
the right one per shot. **lotgen-workspace** covers the rest of the family.

---

## NOTHING IS HAPPENING

Renders are asynchronous and "slow" and "dead" look identical from outside.

- `get_job` reports queue position **and how long that provider usually
  takes**, which is what separates the two.
- `list_jobs` shows every take on the shot, newest first — including the three
  earlier attempts somebody forgot about.
- `get_provider_health` gives success rate and latency **from this workspace's
  own jobs only**. A provider you have never used returns nothing, and nothing
  is not a health problem. `list_providers` is the tool for what exists.
- A chained shot is held back until its source lands and is reported as
  waiting. Run `render_project` again once the source has a take; it was never
  stuck.
- `cancel_job` stops a queued or running take. `delete_job` discards a finished
  one — the bytes stay in the bucket and the take stops counting as the shot's
  output.

---

## WHEN RE-RENDERING IS ACTUALLY THE ANSWER

After the free checks, and only then. Three cases where it genuinely is:

1. **`recheck_take` missed** and the provider says the render is gone.
2. **The model is wrong for the shot** — it takes no references, cannot make
   the length, cannot continue from another shot's last frame. No re-roll fixes
   a capability; change the model and quote it again with `refs`.
3. **The take is fine and the shot is wrong.** Fix the prompt with
   `update_scene` first, or the second take is the first take with a new seed.

Otherwise, compare: `estimate_render` prices a fresh take, and that is the
number a post pass is competing against. Sometimes rendering again really is
cheaper, and saying so is better than defaulting to a fix — but say the number
out loud either way, and get a yes before spending.

---

## CHECKLIST

1. Did the take actually fail, or did we stop watching? `recheck_take`, and
   read which of its four refusals came back.
2. Is the complaint a degradation the quote already named? `get_brief`, and
   read the asked-against-billed line before calling anything broken.
3. If a reference is involved, was the shot quoted **with `refs`** — or was the
   provider chosen on a quote that did not describe it?
4. If a look, a face or a length is the complaint, has the input been checked
   against the listing tool — `cinema_vocabulary`, `list_video_edit_tiers`,
   `list_providers` — rather than against memory?
5. Does every shot in the lot have a delivered take, and is the take on show
   the one that was meant? `get_brief`, then `select_variant`.
6. If a write was refused: re-read and decide again, rather than retrying the
   same write.
7. Has the cost of re-rendering been quoted and said out loud, and has somebody
   said yes in words?
