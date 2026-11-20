---
name: lotgen-song
description: >
  Making a SONG VIDEO in LOTGEN, where the song is the content: a sung or instrumental
  track written by Suno from a style and your lyrics, then the clips made to it. Use when
  the user says "make a song about our product", "a jingle", "a music video", "a song
  video", "write and sing a track", "put these lyrics to music", or names Suno. NOT for
  background music: a bed under a voiceover, an ad or an explainer stays on ElevenLabs
  (lotgen-voice), and this skill never offers Suno for one. Suno here is an unofficial
  route with no statement on commercial rights to the song; say so before spending.
---

# Song videos

A song video is one where the song IS the content: a jingle, a product song,
a music video, a lyric clip. LOTGEN makes the song with **Suno** and the
pictures with any video model, and the two meet in the edit.

**Background music is not this.** A bed under a voiceover, an ad or an
explainer is ElevenLabs music (`lotgen-voice`, MUSIC, EFFECTS, AND A CLEAN
VOICE). If the user wants "some music under it", that is a bed: do not reach
for Suno.

---

## SAY THIS FIRST: THE ROUTE IS UNOFFICIAL

Suno publishes no developer API, and this workspace has no access to its MCP
server. LOTGEN reaches Suno through **EvoLink's resale**, on the workspace's
EvoLink key. EvoLink labels it beta, "suitable for development and testing",
and says nothing about commercial rights to the songs. Every Suno quote
carries that warning (an incompatibility at severity `warns`).

So before the first render, tell the user in one sentence: the song is made
through an unofficial route, LOTGEN cannot say the brand owns it, and a paid
ad should not rely on it. Then do what they decide.

---

## MAKING THE SONG

The song is an **audio shot** (`add_scene` with kind audio) with a sound of
kind music, on provider **suno**:

1. **The prompt is the song's style**, up to 1000 characters: genre, mood,
   tempo, instruments, the voice. *"Upbeat 2000s pop-punk, 160 BPM, palm-muted
   guitars, gang vocals on the chorus, bright female lead"*. Not the lyrics,
   and not a description of the video.
2. **The sound** (`update_scene` with sound):
   - kind music and **seconds, 10 to 300**: the length of the cut. Suno
     treats it as a target, not a promise; measure the take.
   - **lyrics**: the words sung, word for word, up to 5000 characters. Mark
     the parts the way Suno reads them: `[Verse]`, `[Chorus]`, `[Bridge]`,
     `[Outro]`. A 30 s jingle is one verse and a chorus at most; a song's
     length and its lyrics have to agree, or Suno rushes or repeats.
   - **instrumental** true for no singing (then no lyrics and no singer).
     With no lyrics and instrumental left out, the track is an instrumental.
   - **title** (up to 80), **vocal_gender** "m" or "f" (a likelihood, not a
     guarantee), **negative_tags** for styles to keep out ("autotune, EDM
     drops").
3. **Model**: suno-v6 (the default), suno-v6-mini, suno-v6-wild or
   suno-v5.5. Only these take a length, so only these are offered.
4. **Quote** with a `dry_run`: $0.118 a request whatever the length (8
   EvoLink credits), with the rights warning. **Each request returns two
   takes**; both land on the job (the second is its second file). Listen to
   both and keep one.
5. **Render** with `render_scene`. A song takes one to four minutes; the job
   polls like a clip. `get_job` to follow it.

ElevenLabs refuses `lyrics`, `title`, `vocal_gender` and `negative_tags` by
name: a song's fields only go to Suno.

---

## MAKING THE PICTURES TO IT

Two ways, and they combine:

- **Cut the clips to the song.** Plan the shots against the song's measured
  sections (`get_job` gives each take's seconds; count the bars from the
  tempo you asked for). Render the clips silent, then lay them over the song
  in the editor: `export_project` or `export_to_drive` hands over the takes
  and the song together. **An episode assembly (`assemble_episode`) does not
  take a generated track as its music**: an episode's disclosure says its
  music is not generated, and that is the owner's rule, so a song video is
  cut in the editor.
- **Let Seedance follow the song** (`lotgen-refs`, reference audio). A song
  of 30 s or less can be a Seedance shot's reference audio: `get_asset_url`
  on the song take, `set_scene_refs` with that URL on a Seedance 2.5 shot
  (2.0 takes 15 s at most, and only beside a picture), with_audio on, and a
  prompt that says what to do with it (*"she sings the chorus of the audio
  reference to camera"*). How closely Seedance follows a track has not been
  measured; render one short shot first and look at it.

---

## WHAT IT COSTS, AND WHAT IS NOT KNOWN

- **$0.118 a request**, two takes, at any length from 10 to 300 s, on the
  EvoLink key. The clips cost what clips cost (`lotgen-cost`).
- **Not measured yet** (2026-11-18): no paid Suno task has run through
  LOTGEN, so the request fields are EvoLink's page and the charge is its
  published rule. The first song is the measurement; check its `get_job`
  cost against the quote.
- EvoLink's links to the songs live 72 hours; LOTGEN copies both takes into
  the workspace when the task finishes, so they do not expire here.

---

## CHECKLIST

1. Is the song the content? If it is music under something else, stop: that
   is ElevenLabs (`lotgen-voice`).
2. Has the user heard that the Suno route is unofficial and carries no rights
   statement, before anything is spent?
3. Is the prompt a style, and are the lyrics the words, with section tags,
   sized to the length?
4. Quoted with `dry_run`, and said out loud that one request is two takes?
5. Are the pictures planned against the song's measured length, cut in the
   editor, or following a 30 s song as Seedance reference audio?
