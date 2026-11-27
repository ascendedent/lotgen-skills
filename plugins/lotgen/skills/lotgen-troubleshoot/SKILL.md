---
name: lotgen-troubleshoot
description: >
  Work out why a LOTGEN render disappointed, and fix it without paying twice. Use when
  the user says "it came back wrong", "that is not what I asked for", "the render
  failed", "it timed out", "the look did nothing", "her face changed between shots",
  "the clip is cut off", "why is it eight seconds when I asked for seven", "nothing is
  happening", "it is still queued", "the export is missing a shot", "the client saw the
  wrong take", "my edit was refused", or a Seedance job says the image "may contain
  real person". Also use before re-rendering anything, because
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
| a mouth that should stay closed opens on every take | a sound the face makes (breath, sigh, hum) in the prompt, with sound on | the shot's sound line | take the face sound out and put it in the edit (lotgen-prompt, the seven traps) |
| the location map closes a space that is open (a tub-shower walled in on four sides) or moves a fixture | the map had no layout; the model placed walls and fixtures | the plan's `advice` and the sheet's prompt | re-plan with `layout`: which walls, which side is open, each fixture's mount and height (lotgen-map) |
| the person stands in front of a flat photo of the room, or a fixture appears twice | the model copied a map panel as the backdrop: no panel named, no view in words, a fixture placed by frame position | the shot's `location_angle` and its prompt | set `location_angle` to the closest panel, write where the camera is, what it faces and what is behind the subject, place fixtures by mount (lotgen-prompt, the seven traps) |
| a fixture floats or moves (a shower head on the ceiling) | the shot named the fixture but not its mount | the shot's prompt | say where it is mounted, as the map's layout does; from the fixture's own position, leave it out of frame (lotgen-prompt, the seven traps) |
| a phone or camera appears in the frame | the prompt named the device as how it was filmed ("filmed on a phone") | the first line of the prompt | describe the look instead; for a selfie or propped style, say where the phone is held and that it is behind the camera |
| a UGC shot comes back as an over-the-shoulder ad, the filmer's hand and phone in front | the prompt or the opening still named who was filming | the shot prompt and its opening frame | never say who is filming; write phone footage and how it moves, `capture_mount` handheld_rear on a mobile lot (lotgen-prompt, trap 5) |
| the same person has a different face, or a different heritage, from row to row | a still or re-roll made without the face reference, or a person first made in another tool | the shot's refs and the still's own job | one character map, its reference on every still and clip; check each frame against it before showing (lotgen-casting) |
| the accent comes and goes, or vanishes after a voice change | a voice changer copies the source read's accent | which clips went through a changer | lock one voice with the accent before the second clip; ask for the accent in the read (lotgen-voice) |
| the person wears the reference's clothes in a scene they should not (jeans in a shower) | the character sheet's outfit carried over | the reference and the shot's wardrobe | state the scene's wardrobe in the shot, or use an outfit edit of the sheet (lotgen-casting) |
| "blocked by content moderation" on a shower, bath or bed scene | the provider's own moderation | `get_job` | on EvoLink Seedance 2.0/2.5, ask, then `update_lot` with `content_filter_off` (lotgen-cost); on any other model, dress the scene (a towel, steam, a frame that stops at the shoulders) |
| a multi-setup clip wrong in a different setup each take | every setup must land in one take | the shot's setups | split the proof and the person-plus-product setups into their own shots (lotgen-prompt) |
| "takes a reference video of 2 to 15 seconds" (or 30) | a motion clip outside Seedance's window | the refusal names the clip's length | trim it before attaching, or use 2.5 for up to 30 s |
| a Seedance shot with a clip came back not following the movement | the prompt never said what to take from the clip, or the clip carried identity the pictures should | the shot's prompt | say what to follow as a destination, cite `@Video1` on fal, keep who and where on the pictures (lotgen-motion) |
| a motion clip on ARK refused for a real person | ARK takes only faces Seedance made in the last 30 days | `get_job` | the same shot on EvoLink or fal |
| "does not take reference audio" / "only beside a reference picture or clip" | a track (`set_scene_refs` mp3/wav) on a shot whose model is not Seedance 2.0/2.5, or a 2.0 shot with nothing visual beside it | the quote's refusal names which | move the shot to Seedance 2.0/2.5, add a picture or clip (2.0), use 2.5, or detach the track (`lotgen-refs`) |
| "the look did nothing" | an id the vocabulary does not know | `cinema_vocabulary` | set it again with a real id |
| "may contain real person" on a Seedance job | ARK refuses a face it did not make on this key, AND a Seedream still of a person made with any reference picture (a product pack included), even on the same key, unedited; nothing was charged and a retry fails the same | `get_job` (provider `seedance`), then the opening frame's own take: did it have a `ref_pack_id`? | render the shot on EvoLink's `seedance-2.5`, or Kling 4.0 with the person and product as references, or keep the person ARK-made from words alone end to end (`lotgen-models`) |
| a Seedance render on EvoLink refused for content that is legal and the user stands behind | the provider's default content filter | `get_job`: provider `evolink`, a content or moderation refusal | ask the user, then `update_lot` with `content_filter_off: true` and render again: Seedance 2.0/2.5 on EvoLink only, at 1.1x (`lotgen-cost`). ARK has no such switch |
| "her face is a different person" | no anchors on the persona, or the references never reached the model | `estimate_render` with `refs` | anchors, or a model that takes references |
| "the edit came back cut" | the tier's window is shorter than the clip | `list_video_edit_tiers` | a row with a longer window |
| "the export is missing a shot" | that shot has no delivered take | `get_brief` | render it before exporting |
| "the client approved the wrong clip" | the shot had no mounted take | `get_project` | `select_variant`, then a fresh link |
| "my edit was refused" | somebody wrote first | re-read `get_brief` | re-read, decide again, write once |
| "the upload link failed" / `CONNECT tunnel failed, response 403` | the sandbox's network policy blocks the API host; the request never left the sandbox | nothing on LOTGEN's side to check | in a claude.ai org, an admin adds `api.lotgen.ai` to the code-execution allowlist (Organization settings > Capabilities); until then the person drops the file on Assets > References, then `list_references` (`lotgen-assets`) |
| "I cannot open the link to see it" | the signed URL is on the storage host, outside your sandbox | `view_take` | look at the picture in the result; give the URL to the person |
| "which take are we even talking about" | four takes, one title | `search_takes`, `list_jobs` | `label_take` so it survives the conversation |
| "the glow is a hot dot behind a lens-like part" | the words left the light source unnamed, so the lens-like part became it | `get_brief`: does the prompt name the body as what glows? | name the body as the light, the lens-like part as a capped lens (lotgen-product, failures table) |
| "a beam, a lamp or a second light appeared" | a directional word described a beam | read the prompt for "toward", "down", "casts" | "a soft, even glow in every direction", no direction words |
| "it grew a cord" | "plugged in" describes a cable | read the prompt for "plugged" | "self-contained, flush against the wall, nothing hanging from it" |
| a part doubled | an unstated count is invented | is the count in the locked block? | state the count of every part: "exactly one ..." |
| "every room mounted it differently" | no mounting rule | is one mounting sentence in every prompt? | one rule for every room: wall, spot, which way up |
| "she holds it like a TV remote" | no grip | is the grip in the prompt? | the grip in full, its size against the palm, the product map's in-hand panel |
| "I asked for four and got one" / "there are several lights" | a model cannot count | read the number in the prompt | describe the layout ("two rows of four"); "exactly one, the only light in the room" |
| "it is the size of a cereal box" / "it floats" | no units and nothing it rests on | is there a size in cm and a hand? | "12 × 18 × 5 cm, her hand nearly covers one", and what it rests on |
| "it came back thick and bulky" (a slim handheld) | "slim" and "small" carry no scale | is there a length and a thickness in cm? | cm, an everyday object and the hand (lotgen-product); never a weapon as the comparison |
| "she looks pasted in" / "she floats above the floor" | nothing ties her to the room | read the prompt for the feet | a concrete paragraph: where the feet meet the floor, the contact shadow under them and its direction, the floor pattern running unbroken under and past them. "Make her grounded" does nothing |
| "the product's back is wrong" / "the room changed" on a shot that opens on a still | on most models an opening frame is sent alone, so no map went; on Seedance before 2026-11-26 the same | the quote: "opening frame replaces references" | render it on Seedance, where the frame is tagged the first frame and the maps ride along (lotgen-refs); or put the maps into the still and add an end still for what it does not show |
| "it looks like a different product" after a re-roll | the product block was shortened or paraphrased | diff this prompt's block against the last good one | paste the full block back, word for word |
| "I took it out of the prompt and it is still there" | a reference outweighs the text | `get_brief`: which references are on the shot | drop the reference, or keep the object off screen and only its effect in frame |
| "there is music" / "she talks" on Seedance | it scores and voices by default | read the sound line | the closed sound list and "her lips stay closed throughout" (lotgen-prompt); `isolate_voice` if it scores anyway |

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

## THE PROMPT THAT WAS BLOCKED

A provider's filter refused the prompt. Treat it as a debugging problem, one
change at a time. The triggers seen (2026-11), most
common first:

1. **A real brand name spelled out again and again,** worst beside "must
   exactly match" or "reproduced precisely". Let the reference carry the
   logo; say the name once or not at all.
2. **Covert-sounding words stacked together:** "hidden", "unseen",
   "no signal at all", in a bedroom, from an unseen point of view. Each is
   mild; together they read as covert filming, even for an innocent
   product. Rewrite what the product does in everyday words.
3. **"Match this exact photo" repeated,** above all about a real person's
   face or hands or a branded product. Describe it plainly and keep
   exact-match for the one detail that needs it.
4. **A weapon as a size comparison** (lotgen-product).
5. **The chat's history.** A long run of prompts in that vein can block a
   mild one; the same prompt in a fresh chat is a fair test.

Order of work: rewrite in plain language; cut the repeated brand names and
the exact-match emphasis; remove any weapon comparison; then test the
paragraphs one at a time to learn whether the text, a reference or the
history is the trigger. After several honest rewrites that still block, say
so and point the person to the provider's reason code or its policy on real
brands and likenesses. Never state why something was blocked as a fact:
nobody outside the provider can see that.

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
