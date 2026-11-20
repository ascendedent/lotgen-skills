---
name: lotgen-workspace
description: >
  Living with LOTGEN lots after they exist — reading one before changing it, adding and
  reordering shots, writing without overwriting the person editing in the browser,
  renaming takes so an export is legible, delivering another aspect for free, finding
  old work, and archiving rather than deleting. Use when the user says "add a shot",
  "reorder these", "what is in this lot", "find that clip from last Tuesday", "make a
  9:16 version", "delete this lot", or when a write comes back refused.
---

# Working in a lot

Everything here is free. The one thing that can be lost is **the record of
what was billed**, and this says how not to lose it.

---

## READ IT FIRST — THERE IS A CALL FOR EXACTLY THIS

`get_brief` is **the one call to make before planning changes to a lot.** It
returns the lot as markdown: every shot with the settings that decide what it
costs, which shots already have a finished take, which are **blocked waiting on
the shot they continue from**, and what finishing the rest would cost.

It also does something no other read does: **a shot whose provider will hand
back a different length, shape or resolution than it asks for says so in a line
of its own.** The price quoted is always the price of what will *actually* be
made — a shot reading seven seconds on a model that renders four, six or eight
is quoted for eight. Reading the brief is how you find that before a user does.

And each shot carries the version stamp you need for the next section.

`get_project` is the same lot in structured form; `list_projects` finds it.

---

## WRITING WITHOUT OVERWRITING SOMEBODY

An agent that reads a shot, thinks, and writes has a window in it — and **a
person editing the same lot in the browser is standing in that window.**

The product does not resolve this silently, and neither should you:

| call | pass | why that field |
|--|--|--|
| `update_scene` | the version stamp you read | a write that would overwrite somebody else's edit is refused instead |
| `reorder_scenes` | the order you read | **a reorder's version is the order itself, not a timestamp** |

A write that would clobber somebody comes back refused, carrying the version
the row actually holds. **Re-read, decide, and write again** — do not retry the
same write, and do not strip the version field to make the refusal go away.
That field is the only thing standing between an agent and quietly undoing a
person's work.

**`update_scene` touches only the fields you pass.** You do not need to send
back a whole shot to change one line.

**`label_take` needs no version stamp at all**, and that is deliberate: a
rename cannot conflict with a render.

---

## ADDING, ORDERING, COPYING

- `create_project` makes an **empty** lot. It is rarely the right start — a
  studio recipe or `create_from_preset` arrives with a shot list already in it,
  and an empty lot means writing every shot by hand.
- `add_scene` appends a shot and **inherits the lot's default provider** unless
  told otherwise.
- `reorder_scenes` takes **every** shot id in the order you want, not just the
  ones that moved.
- `duplicate_project` copies a lot and its shots — **takes are not copied.** So
  a duplicate is a fresh shot list at the same settings, not a second copy of
  the work. That is usually what is wanted for a variant; say so, because
  "duplicate" sounds like it includes the renders.
- `delete_scene` removes a shot **and its takes**.

---

## NAMING TAKES, BECAUSE THE EXPORT USES IT

When a shot has four takes and the client is being sent the second one, the
shot's title cannot tell them apart. `label_take` names one.

**The export names its files after it**: the take's own label first, then the
shot's title, then the bare number. So labelling is not decoration — it is how
a delivered folder becomes readable. It is editorial only: it reaches no
provider, changes no price, and an empty string clears it.

---

## ANOTHER ASPECT, FREE

`reframe_project` delivers a lot in another aspect **without re-rendering it**.
It is ffmpeg, nothing goes to a provider, and **the crops are stored, so a
second delivery of the same aspect costs nothing at all.**

It is honest about what it is: **a centre crop with a chosen focus, not subject
tracking.** The focus decides which part of the frame survives. When that is
not good enough — when the crop throws away someone who matters — the paid
generative reframe exists, but reach for this first.

**Spoken shots are skipped**, because they have no picture, and they are named
rather than silently dropped. Then `export_project` in the same aspect ships
them.

---

## FINDING OLD WORK

`search_takes` searches prompts, notes, models and lot names, and **understands
"last Tuesday" and "yesterday"** — so pass the user's own phrasing rather than
converting it to a date first.

---

## ARCHIVE, DO NOT DELETE

This is the only irreversible thing in this file.

**`archive_project` moves a lot out of the default listing without deleting
anything.** Prefer it, essentially always.

**`delete_project` destroys the scene graph — and the scene graph is the record
of what was billed.** The rendered clips stay in the bucket; the account of
what was made and what it cost does not. A workspace that deletes its lots
keeps the files and loses the ledger.

So when a user says "delete this lot", the useful reply is usually: archive it,
and say why. If they genuinely want it gone, they can say so again — but do not
reach for the destructive call because the sentence contained the word.

---

## CHECKLIST

1. Did you `get_brief` before planning changes?
2. Did the brief name any shot whose provider will change its length or shape?
3. Are you passing the version you read on every write that takes one?
4. On a refusal: re-read and decide — never retry the same write.
5. Is a duplicate understood to carry no takes?
6. Would the free crop deliver the aspect the user asked for?
7. Is "delete" really delete, or is archive the right answer?
