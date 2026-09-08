---
name: lotgen-script
description: >
  The craft of writing words somebody says out loud in LOTGEN — a spoken line, a UGC
  hook, a voiceover, an explainer's narration. Use when the user says "write the
  script", "write the voiceover", "write the hook", "what should she say", "the line
  is too long", "it does not fit the clip", "cut this down to fifteen seconds", "the
  voice sounds rushed", "why did it read the stage direction out loud", "write the
  CTA", or when a line has been written and nobody has worked out how many seconds it
  is. A script is a duration before it is a sentence; read this before typing a word
  anybody is going to hear.
---

# Writing words somebody says out loud

**A script is a duration.** Prose is measured in words and video is measured in
seconds, and the whole craft here is converting between the two before anybody
spends money on a clip the words do not fit.

The rate is roughly **2.5 to 3 words per second** of finished video. Slow,
considered narration sits near 2.5. An unpolished creator talking fast sits
near 3. Nothing in this product will tell you that a line is too long; the
render succeeds, and the fault arrives as a clip that ends mid-sentence or a
last frame held for four seconds while somebody finishes talking.

Nothing in this document spends. Writing, rewriting and counting are free. The
money moves at `render_scene`, `render_project`, `voice_explainer` and
`lipsync_take`, and nowhere else.

---

## THE RATE IS THE ONLY UNIT

| the shot runs | at 3 words/sec | at 2.5 words/sec | in plain terms |
|--|--|--|--|
| 4s | 12 words | 10 words | one short sentence |
| 5s | 15 | 13 | one sentence, or two very short ones |
| 6s | 18 | 15 | one sentence and a tag |
| 8s | 24 | 20 | two sentences |
| 10s | 30 | 25 | two sentences, or three clipped ones |
| 15s | 45 | 37 | three sentences and a breath between them |

**Write the number down before you write the line.** "This beat is five
seconds, so it is thirteen to fifteen words" is a brief. "Write a punchy hook"
is not, and it produces twenty-seven words every time.

### The character arithmetic, which is the money

An English word plus its following space averages about six characters, so
**characters ≈ words × 6**, and the spoken passes here are billed per
character rather than per second.

- A spoken shot is refused above **3000 characters** — about 500 words, about
  three minutes — with a message naming the count it received.
- `narration` on a shot accepts **4000 characters**. **The field takes a
  thousand characters more than the voice model will read.** A narration
  written to the field's limit stores fine, prices fine, and is refused at
  render. Split it across shots before that happens.
- The explainer path caps each beat's narration at 1200 characters — around
  200 words, around 70 to 80 seconds — which is the honest ceiling for one
  breath-group of illustration anyway.
- Punctuation, spaces and anything you left in by accident are characters, and
  the bill comes back from the provider's own count of the string that was
  sent. A stage direction you forgot to delete is paid for twice: once in
  money, once in the voice reading it.

---

## THE TWO PLACES WORDS LIVE, AND WHY IT CHANGES THE WRITING

**These are two different crafts and confusing them is the expensive mistake.**

**One — inside the picture's prompt, in quotation marks.** This is the native
audio path, and it is what UGC Factory builds: the shot's prompt ends with
something like *The person on camera says, in a natural unpolished delivery:
"…"*. One render produces the picture and the voice together. The clip's
`duration_sec` is a hard wall — the model has that many seconds to render a
person saying your words, and a line that needs nine seconds inside a
six-second clip comes back gabbled, truncated, or finished by a mouth still
moving as the clip cuts.

**Two — on `narration`, mixed on afterwards.** The picture renders silent, the
line renders as its own spoken shot, and `narrate_take` or `mix_explainer`
lays one over the other with ffmpeg, free. **Here the picture and the line are
rendered separately, so the line decides the finished length and the picture
decides the quote.** A 26-word line over a 6-second clip delivers about ten
seconds of video: the last frame holds while the voice finishes, and nothing
is trimmed. A 6-word line over the same clip delivers six seconds with a
silent tail.

Four consequences for the writing:

**On the native path, cut the line to the clip.** The clip is the constraint
and it will not move.

**On the mixed path, write the line first and buy a picture long enough to be
interesting under it.** You are not buying seconds of speech; you are buying
seconds of something to look at while the speech happens. Holding a last frame
for two seconds reads as a beat. Holding it for eight reads as a broken file.

**Words over a mouth are expensive and rigid. Words over b-roll are cheap and
elastic.** A silent beat — every structure has at least one — is where a long
sentence belongs, because no lips have to agree with it, no creator reference
has to hold, and the mix is free.

**A line that has to come out of a specific face needs that face.** A spoken
shot on its own is a voice; `lipsync_take` is what puts it on a mouth, priced
by the length of the audio rather than the clip. So on that path, every word
you cut is money.

---

## FITTING A LINE TO A SHOT THAT ALREADY HAS A LENGTH

A planned lot has already decided how long each beat is, before anybody wrote
a word. The runtime is shared out by the structure's own weights and then
clamped between 4 and 15 seconds a shot. For the testimonial structure —
weights 2, 3, 2, 2 — that lands as:

| beat | TikTok, 24s | Reels, 30s | Shorts, 45s |
|--|--|--|--|
| hook, on camera | 5s → 13-15 words | 7s → 18-21 | 10s → 25-30 |
| proof, on camera | 8s → 20-24 words | 10s → 25-30 | 15s → 37-45 |
| demo, silent | 5s, no words | 7s, no words | 10s, no words |
| cta, on camera | 5s → 13-15 words | 7s → 18-21 | 10s → 25-30 |

Read the real numbers off `plan_ugc` or `get_brief` rather than off this
table; the structure you picked may weight it differently. **The point is that
the seconds exist before the sentence does.**

**Then the model rounds them again.** A requested length is snapped to the
lengths the chosen model actually makes — several take 4, 6 and 8 seconds and
nothing between — so a 5-second beat renders and bills as 6. Write to the
length that will render, not the one the plan asked for: on the native path
that is one more second of a mouth needing something to do.

### The five cuts, in the order to make them

**1. Cut the run-up.** The first clause of a spoken line is almost always
throat-clearing. Delete up to the first concrete noun.

> **Before — 27 words, 9.0 to 10.8s.** "Hi everyone, so I wanted to talk to
> you today about this water bottle I have been using, because I honestly
> think it is really quite good."
>
> **After — 15 words, 5.0 to 6.0s.** "I stopped buying water bottles a year
> ago. This is the one that did it."
>
> **Why.** The before spends seven words arriving and eight more hedging, and
> at 3 words a second the viewer has decided by word nine. The after opens on
> a completed action, and it fits a 5-second hook beat exactly.

**2. Cut the qualifier.** *Kind of, sort of, really, quite, actually, I think,
honestly, just, basically.* Each costs a third of a second and adds no
information; three of them in a line make the speaker sound uncertain about a
product they are recommending.

> **Before — 20 words, 6.7 to 8.0s.** "I think this is actually kind of a
> really good way to sort of keep on top of your washing."
>
> **After — 11 words, 3.7 to 4.4s.** "This is how I keep on top of the washing
> now."
>
> **Why.** Nine words removed, no fact removed. The line now fits a 4-second
> shot with room for a breath, and the delivery reads as certainty rather than
> as hedging.

**3. Contract everything.** "I have been" is three words and four syllables;
"I've been" is two and three. **Contractions are also the single strongest
signal that a person is talking rather than a brand.** Uncontracted speech
reads as a script being performed, which is precisely the thing UGC exists to
avoid.

**4. Make the verb do the work.** A noun phrase built out of a verb costs
words and slows the ear down. "Provides protection against" is a verb wearing
a coat: *protects*.

> **Before — 18 words, 6.0 to 7.2s.** "We built the seal so that the bottle
> can be carried in a bag lid down without leaking."
>
> **After — 11 words, 3.7 to 4.4s.** "Carry it lid down in a bag. It does not
> leak."
>
> **Why.** The passive construction and the subordinate clause both go, and
> the claim becomes an instruction the listener can picture themselves
> following. Seven words is 2.5 seconds back.

**5. Cut the second example.** At 3 words a second there is time for one. Two
examples means the listener is still processing the first when the second
arrives, and remembers neither.

**Never cut the concrete noun to save room.** Cut the adjective in front of
it. "The lid" survives; "the beautifully engineered lid" does not.

---

## WRITING FOR THE EAR

A reader can go back. A listener cannot. Everything below follows from that
one fact.

**One idea per breath.** A breath is eight to twelve words, which is three to
four seconds. If a clause needs more than that, it is two clauses.

**Do not stack subordinate clauses.** The ear cannot hold a sentence open
while a second sentence happens inside it. Front the main clause and let the
qualification follow as its own sentence.

> **Before — 29 words, 9.7 to 11.6s.** "Because the lid seals properly, which
> most bottles genuinely do not, you can put it in a bag without worrying that
> everything you own is about to get wet."
>
> **After — 16 words, 5.3 to 6.4s.** "The lid seals. It goes in the bag on its
> side and the bag stays dry."
>
> **Why.** The before opens with *because* and makes the listener wait
> nineteen words for the main clause, then buries the payoff in a negative
> ("without worrying that … about to get wet"). The after states the mechanism
> and then the result, in that order, and the result is a picture rather than
> an absence of a picture.

**Put the new information at the end of the sentence.** The ear keeps the last
thing it heard. "It stays cold for twelve hours" lands; "Twelve hours is how
long it stays cold" spends its emphasis on the setup.

**Repeat the noun rather than reaching for a pronoun.** By the third sentence
"it" could be the bottle, the lid or the bag, and a listener who has to work
that out has stopped listening. Repetition that looks clumsy on the page is
invisible out loud.

**One sentence, one full stop, under about 84 characters.** That is not a
style preference here: the caption track splits on sentences, and a sentence
over 84 characters is broken again at whatever word boundary the count lands
on. Cue boundaries are then apportioned by character count across the take's
measured length. So sentences of roughly even length caption where you meant
them, and one long sentence among short ones takes a slice of the clock far
bigger than the time it is actually spoken over. **84 characters is about
fourteen words is about five seconds — the caption line and the breath are the
same unit.**

**Line breaks do nothing.** All whitespace is collapsed before chunking. If
you want a pause, write a full stop.

---

## THE FIRST LINE IS THE WHOLE JOB

The platform note on TikTok in this codebase says it plainly: the hook has to
land in the first second, not the third. At 3 words a second that is **four
words**. Not four seconds — four words.

- **Front the claim.** The product name is not a hook; it is the thing the
  hook earns the right to mention. Name it in the second sentence.
- **Open on a completed action or a number.** "I stopped buying…", "Three
  weeks in…", "Nobody told me…". A state of affairs is not an opening; a
  change is.
- **One clause, then a full stop.** The hook beat is 5 seconds on a 24-second
  TikTok, which is thirteen to fifteen words, which is two short sentences.
  Spending it on one long sentence gives the listener nothing to land on.
- **Test claims, not synonyms.** `hook_variants` builds up to five alternative
  opening shots sharing one body, each on a different formula, so only the
  words differ. That comparison is only worth paying for if the variants say
  **different things** — a result claim against a curiosity claim against a
  contrarian claim. Four rewordings of one claim is four renders that teach
  you nothing. Pass the `hook` formula id you want as the baseline and let the
  variants come from formulas it is not.

> **Before — 22 words, 7.3 to 8.8s, and the value arrives last.** "Link in the
> bio, go and grab one now, and honestly you will not regret it, it has
> genuinely changed my mornings."
>
> **After — 15 words, 5.0 to 6.0s.** "I filled it at breakfast. It was still
> cold at midnight. Link in the bio."
>
> **Why.** See below — this is the CTA rule and the hook rule colliding in one
> line.

---

## THE CALL TO ACTION, AND WHY IT FAILS EARLY

A CTA is an instruction, and an instruction only works once the listener has a
reason to follow it. **A CTA that arrives before the value is a request from a
stranger.** Every structure here puts it last for that reason, and gives it
about a fifth of the runtime — 5 seconds on a 24-second cut, thirteen to
fifteen words.

Three rules:

**Value first, in the same breath.** The rewrite above works because the proof
("still cold at midnight") is the clause immediately before the instruction,
so the instruction inherits it.

**One verb, one destination.** "Go and grab one now and let me know what you
think and check the link" is three actions and produces none.

**Assume the sound is off.** The Meta placement note in this codebase says to
assume it is watched on mute. A spoken CTA on a muted feed is a silent shot of
somebody's face, so the last beat needs the instruction **in the picture** —
which is a job for the shot's prompt and the captions, not for the line. Write
the line so the caption of it is a legible instruction on its own.

---

## NUMBERS AND NAMES

These are the two things a listener cannot re-read, and they are the two
things a voice model is least predictable about.

**Write the number the way it should be said.** The string you write is the
string that is spoken and the string that is captioned. "£34.99" may be read
as "thirty-four ninety-nine", or as something else entirely, and you will
discover which after you have paid for it.

> **Before — 16 words, 75 characters.** "It holds 1.5L, it is 24hr cold, RRP
> £34.99, and there is 20% off this week."
>
> **After — 16 words, 90 characters.** "A litre and a half. Still cold a day
> later. Thirty-five pounds, or twenty-eight this week."
>
> **Why.** Same word count, same seconds, and every quantity now has one
> unambiguous reading. The after is fifteen characters more expensive and
> worth it. Note also that four figures in one 5-second line is three too
> many: the rewrite drops "20%" into a plain number and pushes nothing onto
> the listener's memory.

**One number per sentence.** Two competing figures in one breath and the
listener keeps neither.

**A comparative needs its baseline.** "Forty per cent fewer" is not a claim
until the sentence says fewer than what — and it is a claim somebody has to be
able to stand behind, so it comes from the brief rather than from you.

**Say a name early, then once more.** An unfamiliar product name needs a
short clause around it rather than being buried mid-sentence, and it needs
hearing twice in a thirty-second cut to be remembered. Three times is an
advertisement reading its own label.

**Spell nothing.** URLs, model numbers and hyphenated codes belong on screen,
not in a voice. A listener cannot transcribe from audio at 3 words a second.

---

## WHAT A VOICE OBEYS, AND WHAT IT SIMPLY READS OUT

| what you write | what actually happens |
|--|--|
| full stop, comma | a real pause; this is the reliable timing control |
| a short sentence | a breath the listener can use; the strongest tool here |
| "(laughing)", "[pause]", "*best*" | **read aloud, and billed as characters** |
| ALL CAPS for emphasis | unpredictable across voices — sometimes spelled out |
| exclamation marks | very little; delivery comes from the words and the voice |
| a line break | nothing at all |
| "say this excitedly" inside the quotes | the voice says "say this excitedly" |
| "£19.99", "1.5L", "24hr" | some reading of it, chosen by the model, not by you |

> **Before — 12 words, 78 characters.** "(laughing) Honestly? *Best* thing I
> have bought this year. [pause] Link below."
>
> **After — 11 words, 61 characters.** "Honestly, the best thing I have bought
> this year. Link below."
>
> **Why.** The before pays for seventeen characters of markup and then has
> them read out. The delivery instruction belongs **before** the quotation
> mark on a native-audio shot, where the scaffold already puts "in a natural
> unpolished delivery" — and on a spoken shot there is nowhere to put one at
> all, because **the prompt is the script, word for word**. Performance there
> comes from the voice's own controls: `speed` between 0.5 and 2, and
> `stability`.

**Fitting by `speed` rather than by cutting is available and mostly a
mistake.** 1.1 buys about nine per cent and nobody hears it. 1.25 is audible
as hurry, which is the one quality a considered claim cannot afford. Cut the
words.

**Every one of the rows above is a claim about somebody else's model.** The
cheap way to settle any of them is one spoken shot: it is billed per
character, so a twelve-word test line costs about seventy characters of spend
and answers the question for the voice you actually chose.

---

## WHAT NOT TO WRITE, BECAUSE THE CAMERA REFUSES IT

Ten capture styles exist. **Six of them refuse a talking-head script by name,
before anything is created**, and they are right to: nobody can speak to a
lens strapped to their own chest.

| style | carries a script? |
|--|--|
| **mobile** (phone), **webcam**, **cinematic**, **news** | yes — webcam and news exist to be talked to |
| **body_cam**, **helmet_cam**, **home_cam** | the operator's face is behind the lens: no one on camera |
| **security_cam**, **dash_cam**, **drone** | unattended: **no spoken beat at all, voiceover included** |

That last row is the distinction worth holding. The three worn cameras refuse
a face; the three unattended ones refuse **dialogue**, which means a beat
marked voiceover is refused too — a written line rendered through an
unattended camera is a voice with no source.

**So what do you write instead?** Move the words off the shot and onto
`narration`, mixed on afterwards. That is not a workaround; it is a different
register, and writing it in the first person will sound wrong:

> **Before — 16 words, first person, over a dash cam.** "I leave mine in the
> van all day and it is still cold when I finish."
>
> **After — 16 words, observed.** "The bottle goes into the van at six. Nine
> hours later the ice has not moved."
>
> **Why.** Same length, same claim, and the after belongs to a camera nobody
> is holding: present tense, third person, stated as observation rather than
> testimony. "I" over a security camera asks the viewer to work out who is
> talking and why they are not on screen.

Read the refusal when you get one — it names both halves, the structure and
the style, so you can change either. `list_capture_styles` carries the same
facts before you plan.

---

## THE EXPLAINER'S OWN PROBLEM: A LINE LONGER THAN ITS PICTURE

An explainer is narration-first — the script is the spine and every beat's
picture illustrates one sentence — and its beats are clamped to 15 seconds
like everything else. **A 46-word paragraph over a 10-second picture is not an
error anywhere; it just holds the last frame for eight seconds.**

> **Before — 46 words, 15.3 to 18.4s, 257 characters.** "A heat pump does not
> make heat, it moves it, which is why it can deliver more energy into your
> house than the electricity it consumes, and that ratio, which engineers call
> the coefficient of performance, is the whole reason anybody bothers with
> them at all."
>
> **After — 25 words, 8.3 to 10.0s, 120 characters.** "A heat pump does not
> make heat. It moves it. That is why it puts more warmth into a house than
> the electricity it draws."
>
> **Why.** One sentence became three, each one a breath. The parenthetical
> definition ("which engineers call…") went to its own beat, where it gets its
> own picture instead of competing with this one. The before also breaks the
> caption rule twice over — 257 characters is three forced mid-sentence
> splits — and it is 137 characters more expensive to speak.

Two further habits for long form:

**One beat, one idea, and the picture illustrates that idea.** If the sentence
needs two pictures, it is two beats. Beats are cheap; a confused illustration
is not.

**Write the lines yourself before spending.** The scaffolded narration fills
deterministically from what you already told it and researches nothing, so a
thin brief produces a thin line — and a slot the brief could not fill falls
back to a stand-in that is then read aloud. **"The usual problem", "products
like this" and "someone like me" are real fallback strings in this
codebase, and they will be spoken in a customer's ad.** Run
`run_explainer_recipe` or `plan_ugc` with `dry_run` first, read every line,
and pass your own.

---

## CAPTIONS ARE THE SAME STRING

The review link and the export pack both build a caption track out of the
words the shot already carries: `narration` first, and on a spoken shot the
prompt, because there the prompt is the script. **A line living inside a video
prompt's quotation marks is captioned nowhere** — captioning a video prompt
would put stage directions on screen as though somebody had said them.

So a native-audio UGC lot ships to a client with no captions unless you also
put the spoken words on `narration`. Setting that text adds no audio and
changes no render; it is what the caption track is built from. Timings are
estimated from line length against the take's measured duration, and the file
says so in a note at the top.

**This is why the script is written for two readings at once.** What you write
is what is heard and what is read. A number written for the voice is a number
printed on screen, and a sentence written for the ear is a caption cue.

---

## READING IT ALOUD IS THE ONLY REAL TEST

Everything above is arithmetic. This is the test.

1. **Read it out loud, with a stopwatch, at the pace you asked for.** Not in
   your head — in your head you read at 5 words a second and everything fits.
2. **Read it cold.** A line you have already read four times is a line you
   have learnt to perform. Hand it to somebody who has never seen it.
3. **Notice where you stumbled and put a full stop there.** A stumble is a
   clause the mouth cannot get through in one go, and a voice model will make
   the same mistake in a way you cannot fix afterwards.
4. **Notice where you took a breath.** That is where the sentence break
   belongs, and it is where the caption cue will land.
5. **Read it flat, with no performance.** If the line only works when you sell
   it, the voice will not save it. Rewrite until it works said plainly.
6. **Then compare your stopwatch to the shot's `duration_sec`.** If the line
   is long, cut words — do not buy seconds on the native path, and do not
   reach for `speed`.

---

## CHECKLIST

1. Do you know how many seconds this line has, from `get_brief` or the plan —
   and have you written the word budget down before writing the line?
2. Counted: does the word count divide by 2.5 to 3 into that many seconds,
   and is the shot's length one the chosen model actually renders?
3. Which path — native audio inside the prompt's quotes, where the clip is the
   wall, or `narration` mixed on afterwards, where the line sets the length
   and the picture only sets the quote?
4. One idea per sentence, every sentence under about 84 characters,
   contractions throughout, no stacked subordinate clauses?
5. Do the first four words carry a claim, and does the product name arrive
   after them rather than in them?
6. Does the CTA arrive after the value it is asking to be paid for, name one
   action, and read as an instruction with the sound off?
7. Is every number written the way it should be said, one per sentence, with
   its baseline where it is a comparative?
8. Nothing inside the quotation marks except the words to be spoken — no
   parentheticals, no markup, no delivery notes?
9. If the capture style is one of the six that refuse a script, have the words
   moved to `narration` and been rewritten as observation rather than
   testimony?
10. Read aloud, cold, flat, against a stopwatch — and any scaffold fallback
    ("the usual problem", "products like this") found and replaced before the
    spend?
11. Are the spoken words also on `narration` where a client will open a review
    link, so the piece is captioned?
12. Said out loud to whoever asked: nothing has rendered and nothing has been
    spent.
