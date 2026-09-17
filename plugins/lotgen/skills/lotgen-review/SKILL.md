---
name: lotgen-review
description: >
  Take a finished LOTGEN lot the last mile — render it, mount the right takes, send a
  client a review link, read the verdicts back and act on a change request. Use when the
  user says "render it", "send it to the client", "share this for approval", "what did
  they say", "are we clear to deliver", "they asked for a change", or wants a lot
  exported or sent to Drive. This is the only skill that spends, and it asks first.
---

# Delivery

Render → mount → share → read the verdicts → act. Five steps, and the first
one is the only place in this product where money moves.

---

## STEP 1 — RENDER, WHICH MEANS ASK FIRST

**Rendering spends the workspace's own provider key.** Every provider except
`fake` charges real money, and a lot that was created cost nothing at all —
so the person may well not realise the next call is the expensive one.

```
render_project  project_id … dry_run: true      → the price, nothing enqueued
```

Show `total_usd` and the count. Then get a yes, in words, from the person —
not from the fact that they asked you to finish the lot. Then run it without
`dry_run`.

- `render_project` renders every shot without a finished take. `render_scene`
  is one shot, which is what you want for a fix.
- A chained shot is held back until the shot it continues from lands, and
  comes back under `waiting`. Run `render_project` again once that take
  exists; it is not stuck.
- **Renders are asynchronous.** You get `job_ids`. Poll `get_job` — it reports
  queue position and how long that provider usually takes, so a long wait can
  be told from a dead one. `list_jobs` shows every take on a shot.
- A take that failed can be retried; `cancel_job` stops one that is queued or
  running, and `delete_job` discards one.

---

## STEP 2 — MOUNT WHAT YOU ACTUALLY MEAN TO SEND

The first successful take of a shot mounts itself. That is deliberate and it
is what makes a one-take flow work — but it means **the take on show is the
first one that finished, not the one you would choose.**

- `select_variant` overrules it. Pass the **shot's** `if_unmodified_since`
  from `get_project` or `get_brief`, never the job's: a job timestamp a
  millisecond off the scene is refused as 409.
- `label_take` names a take, so "the second one" survives the conversation.
- `render_scene` and `render_project` say `auto_mounts` when a shot in the
  batch has no winner yet and the next take will therefore mount itself. That
  is your warning that the shot's answer is about to be decided by whichever
  render finishes, not by you.
- `get_brief` before you share: it says which shots are done, which are
  blocked, and which cannot render at all. A lot with an unrendered shot is
  not a lot to send.

The mounted take is what export ships and what the review link shows. Those
are the same take on purpose — a client must never approve one clip while the
delivery contains another.

---

## STEP 3 — SHARE

```
create_review_link  project_id … label … expires_in_days
```

`url` on the answer is the page. Three things to say when you hand it over,
every time:

1. **It is public.** No login. Anyone the link is forwarded to can watch it
   and cast a verdict. That is the design — the room is whoever holds the
   token — and the person sending it should know that before they send it.
2. **It shows the take each shot has mounted** — and, on a shot with no
   winner yet, that shot's newest take, which is not the same thing and is
   exactly how a client approves the variant you had rejected. Beside them:
   the shot names, the prompts and the narration. Never cost, provider, model
   or the editor's own notes.
3. **Give it an expiry.** A link with no `expires_in_days` outlives the
   campaign it was made for.

**Hand the URL to the person. Do not post it anywhere yourself.**

`list_review_links` is how you tell "that link stopped working" from "that
link never existed" — a revoked, an expired and an invented token all answer
404 to whoever holds them, deliberately, so the only place that distinction
lives is here. `revoke_review_link` takes the link's `id`, not its token, and
is not reversible; the verdicts cast through it survive it.

---

## STEP 4 — READ THE VERDICTS, NOT THE LOG

```
list_review_decisions  project_id
```

`decisions` is the raw log — append-only, newest first. **It is a history, not
an answer.** A client who changes their mind adds a row rather than editing
one, so three entries can appear where two are stale.

`standing` is the answer, and it is the field to act on:

| | |
|--|--|
| `held` | shots with an outstanding change request — the work to do |
| `cleared` | at least one approval and nothing outstanding |
| `unreviewed` | nobody has cast a verdict at all |
| `clear` | every shot cleared, none held, none unreviewed |

Two rules make it, and both are easy to get wrong by reading the log
yourself: **only a reviewer's latest verdict counts**, and **one outstanding
change request holds a shot however many approvals sit beside it** — a client
who asked for a change has not been overruled by a colleague who has not.

`unreviewed` does not block; it also is not done. "Four of six approved" is
not a lot ready to deliver, and reporting it as one is the failure this field
exists to prevent. A comment with no verdict is neither held nor cleared:
somebody looked and nobody said yes.

---

## STEP 5 — ACT ON A CHANGE REQUEST

For each shot in `held`:

1. Read what they said. The comment is the brief.
2. Check **which take** it was cast on. A verdict names its `job_id`. If that
   shot has been re-rendered since, the verdict is about a take that is no
   longer on show — that is usually work already done, waiting to be re-sent,
   not an outstanding objection.
3. Fix the shot, not the lot: `update_scene`, then `render_scene`. The whole
   reason a lot is a shot list is that one note costs one shot.
4. Mount the new take deliberately with `select_variant` if the shot already
   had a winner.
5. Send them back. A new verdict on the new take is what clears the shot —
   the old change request stays in the log, superseded, which is the trail.

Then re-read `standing` rather than assuming your fix cleared anything.

---

## DELIVERING

Once `standing.clear` is true:

- `export_project` — the manifest, signed clip URLs, each shot's provenance,
  the EU AI Act Article 50 synthetic declaration, and `timeline_xml`, the lot
  as a sequence Premiere imports natively. Hand that XML to an editor **beside
  the downloaded clips**: its media paths are relative and on its own it
  relinks nothing.
- `export_to_drive` — the same delivery into the workspace's Google Drive.
  202, then poll `get_drive_export`. It never re-renders and never spends: it
  uploads takes that already exist.
  **`drive_not_connected` or `drive_needs_reauth` means say so and stop.** The
  connection is a Google grant belonging to a person, made in a browser; you
  cannot open a consent screen. Point at Settings → Workspace in the web app.
  A second call while one is running returns the running export rather than
  sending the lot twice.

---

## RULES

- **Ask before the first render and never after a maybe.** `dry_run` first,
  the number out loud, a yes in words.
- **Never share a link for a lot with unrendered or unmounted shots.**
  `get_brief` says which.
- **`standing`, never the log.**
- **Do not re-render a whole lot for one note.**
- **Do not chase a Drive refusal.** Two of them are a person's job and
  retrying is not.
