# Posts

Concrete posting strategy for `@RoyInProgress`: frequency, timing (Amsterdam local), and content mix. Grounded in the public algorithm code in this repo and in the observed posting pattern from the profile.

Read alongside:
- `algorithm-insights.md` — why frequency, freshness, and topic clarity matter.
- `my-positioning.md` — the three pillars and topics to avoid.
- `content-rubric.md` — the per-post scoring used below.

---

## Observed posting pattern

Pulled from the visible profile and the four-week analytics view:

- ~1,700 posts since February 2024 → roughly **2 posts/day** averaged over the lifetime of the account.
- Recent cadence (May 2026): posts visible on May 7, 8, 11, 13, 14 — daily-ish, with occasional skips ("Sick today, so no building").
- Recurring formats observed:
  - "Day N of building in public" (Day 19, Day 22…) — daily build log.
  - "Gm ☕" check-ins — short morning posts.
  - "Build or bin? #N" — numbered decision-prompt series.
  - Milestone posts ("300 ✅ Thanks for following my journey...").
  - Reflective posts ("Today I stopped building. Not because I don't like building anymore…").
  - Meta-AI commentary ("Just try AI is becoming useless advice").
  - "Time to cook…" shipping-mode posts.
- Engagement range (last 4 weeks): impressions 200–1,900, likes 9–47, replies 5–24.

The pattern is **high-frequency build-in-public**, not the polished-prototype positioning written in `my-positioning.md`. Either the positioning doc needs to widen (deliberately) to match the practice, or the practice needs to tighten to match the positioning. This file assumes the practice stays close to what is working — daily build-in-public — but trims the off-niche fat.

---

## What the algorithm rewards (compressed)

From `algorithm-insights.md`, the levers that matter for a post like Roy's:

1. **Topic consistency** → easier for retrieval embeddings to place near the right viewers (§8).
2. **Multiple positive actions per post**, not just likes (§3). Reply + dwell + profile click compounds.
3. **Freshness** — old posts are filtered out before ranking, not just down-weighted (§6).
4. **Avoiding negative signals** (`not_interested`, `mute`, `block`) — generic / off-topic posts trigger these (§4).
5. **In-network reach goes through Thunder**, but in-network candidates still get ranked against everything else (§9). Followers are entry tickets, not impressions.

The implication for cadence: **post often enough to stay in retention windows, niche enough that each post strengthens the topical embedding, and skip days rather than post filler that costs you mute/not-interested signals from existing followers.**

---

## Recommended cadence: 2 + 1

A simple, sustainable shape:

- **2 anchor posts per weekday.** One in the EU window, one in the US window (see timing below).
- **1 optional bonus post or thread on Tue / Wed / Thu** if a real build artifact is ready that day. Never as filler.
- **Weekends:** 1 post Saturday (recap or screenshot), 0–1 post Sunday. Use Sunday to log and plan, not to post.

That is ~10–12 posts/week, down from the observed ~14. The savings come from killing "Gm ☕ nothing special today" posts and consolidating two thin posts into one substantive one.

Hard rule: **no post if there is nothing to say.** Skipping a day costs less than posting filler. `vf_filter.rs` does not filter for "low effort", but viewers do — and viewer mutes feed `weighted_scorer.rs` directly.

---

## Timing (Amsterdam, CEST in summer / CET in winter)

Amsterdam = UTC+2 in summer (April–October), UTC+1 in winter. The two audiences worth optimising for:

- **EU builders** (Amsterdam, Berlin, London, Paris): online ~08:00–10:00 and ~19:00–22:00 local.
- **US tech audience** (NY → SF): online 14:00–18:00 CEST (8am–noon ET) and 21:00–23:00 CEST (3–5pm ET).

The two windows that maximise overlap of both clusters:

| Window | Amsterdam (CEST) | US East | US West | Audience reached                |
|--------|------------------|---------|---------|---------------------------------|
| **AM** | 08:30 – 09:30    | (early) | (asleep)| EU builders, early-rising US ET |
| **PM** | 15:00 – 16:00    | 09:00 – 10:00 | 06:00 – 07:00 | EU evening commute + US morning |
| **Late PM** | 20:30 – 21:30 | 14:30 – 15:30 | 11:30 – 12:30 | EU evening + US lunch / midday |

Recommended default slots:

- **Anchor 1: 08:30 CEST.** EU morning. Best for the build-log Pillar 1 post — what was done yesterday, what is next.
- **Anchor 2: 15:00 CEST.** Catches EU late-afternoon and US morning. Best for the Pillar 2 (prompt) or Pillar 3 (UI decision) post — the day's substantive artifact.
- **Optional 3: 20:30 CEST.** Reply window primarily (see `replies.md`); occasional second post if a thread is gaining traction and needs a re-up.

Day-of-week:

- **Tue / Wed / Thu:** Strongest reach days. Save the best artifact post for the 15:00 slot on one of these.
- **Mon:** Lighter, the audience is catching up; build-log content is fine.
- **Fri:** Recap / "what shipped this week" posts work well in the 15:00 slot.
- **Sat:** Single low-effort artifact post or screenshot in the AM slot.
- **Sun:** 0–1 posts. Use Sunday for the 30-minute weekly review from `README.md`.

These are calibrated for the current ~400-follower stage. Re-test when crossing 1K and 5K — the audience composition shifts and the timing tilts more US.

---

## Content mix: the 70 / 20 / 10 rule

For every 10 posts, target this split:

| Share | Type                                  | Pillar | Example                                          |
|-------|---------------------------------------|--------|--------------------------------------------------|
| 70%   | **On-niche artifact posts**           | 1 / 2 / 3 | "Prompt change that fixed the empty state."   |
| 20%   | **Connective tissue / build logs**    | 1      | "Day 23: shipped the streak counter."            |
| 10%   | **Personal context / reflection**     | —      | "Today I stopped building."                      |

The 10% personal-context bucket exists because it is what the audience actually engages with, and it is what makes the account feel like a person not a content engine. But it is capped at 1-in-10 because posts in that bucket score 1–2 on niche clarity (`content-rubric.md` §1) and dilute the retrieval embedding if they dominate.

Posts that fail the mix:

- **"Gm ☕" alone** → 0% niche clarity, 0% proof. Cut entirely, or only ship "Gm ☕ + one specific thing I am tackling today" — that turns it from filler into a build log.
- **"Nothing special today"** → cut. This is a hard-no per `my-positioning.md`'s "do not post just because you haven't posted today" rule.
- **"Just try AI is becoming useless advice"** → meta-AI commentary, off-niche per the positioning doc. Cut, or rewrite it as a Pillar-2 prompt post grounded in a specific build experience.
- **Milestone posts ("300 ✅")** → 1 per 100-follower threshold, max. They earn replies from existing followers but do not travel out-of-network.

---

## Post formats that fit the practiced voice

Adapted from `my-positioning.md` formats, tuned for Roy's actual style:

### Format A: Day N build log (Pillar 1)

Used as the **08:30 anchor**, 3–4×/week.

> Day 23 of building in public 🔨
>
> Yesterday: shipped the streak counter in the habits app. One screenshot below.
> Today: refactoring the prompt that generates workout variations — the agent keeps adding rest days I did not ask for.
>
> [Screenshot]

Rules:
- Always end with one concrete thing for today. "Today: family time" is fine once a month, not weekly.
- Always include a screenshot if the work was code/UI. A daily build log without an artifact scores low on dwell and proof.

### Format B: Prompt diff (Pillar 2)

Used as the **15:00 anchor**, 1–2×/week.

> One line in this prompt changed everything:
>
> Before: "make it look polished"
> After: "match the visual rhythm of Linear's empty states"
>
> The agent stopped generating gradient hero sections and started producing the actual spacing I wanted.

Rules:
- Real before / after text. No paraphrases.
- One change at a time. If two things changed, write two posts.
- Add the result in one sentence at the end.

### Format C: v1 → vN screen (Pillar 3)

Used as the **15:00 anchor**, 1×/week.

> v1 vs v3 of the home workouts day screen.
>
> Three changes in between:
> 1. Moved the rest timer from below the exercise to a sticky bottom card.
> 2. Killed the gradient. Solid background.
> 3. Replaced the icon row with a single primary action.
>
> The win was #1. The screen finally felt usable mid-workout.
>
> [Image 1: v1] [Image 2: v3]

Rules:
- Always include both images.
- The win sentence is mandatory; without it the post scores low on proof.

### Format D: "Build or bin?" (Pillar 1, recurring)

Used as a recurring series, 1×/week max. The numbered framing is doing real work for reach — the audience starts tracking the series.

> Build or bin? #5 — Streak freeze tokens
>
> The case for: keeps users who miss one day from losing a 40-day streak.
> The case against: blunts the loss aversion that made the streak feature work in the first place.
>
> What would you ship?

Rules:
- Always two-sided. If only one side is real, it is not "build or bin", it is just an announcement.
- End with the question. The series only works if replies arrive — see `replies.md`.

### Format E: Expensive lesson (Pillars 2 + 3)

Used opportunistically, 1×/week max.

> Two hours I lost yesterday, in one sentence:
>
> I described the empty state in words. The agent invented copy. Then I pasted one screenshot of the Linear empty state I actually wanted, and it shipped the right thing in one go.
>
> Rule now: paste references, do not describe them.

Rules:
- Lead with the lesson, not the story. The story is the second sentence.
- End with the new rule as a future-tense action, not a past-tense regret.

---

## Threads vs single posts

The repo does not specifically reward threads, but the `weighted_scorer.rs` action list includes `dwell` and `continuous dwell time` (`algorithm-insights.md` §3). Threads earn dwell when each new post in the thread pulls the reader down. Single posts are better when:

- The idea fits in one screen.
- The reader can act on the post alone (a prompt to copy, a screenshot to study).

Threads are better when:

- There is a real before / after sequence to walk through.
- The artifact is a series of screenshots that earn their own scroll.

Heuristic: if the second post in the thread does not earn its own engagement when read alone, the thread is padding. Compress to a single post.

Thread cadence: **at most 1 thread per week.** Threads cost more to write and they cap the day's posting budget — a thread + 2 anchor posts is the max for one day before you start cannibalising your own reach.

---

## Connective-tissue rules

The 20% bucket of "Day N" / build-log posts exists because the audience signs up for the journey. But they have to earn their place:

- ✅ "Day 23: shipped the streak counter. Screenshot below."
- ✅ "Day 24: stuck on the prompt for variation generation. Open to suggestions."
- ❌ "Day 25: today was mostly family."
- ❌ "Day 26: Gm. Nothing special today."

The first two earn dwell, replies, sometimes profile clicks. The last two are filler that costs niche clarity. If a day genuinely has no progress, skip the build log and write the personal-context post instead — but only 1×/week.

---

## Things to stop posting

From the four-week analytics view, the following patterns are dragging the average:

1. **"Gm ☕" with no anchor noun.** Three observed in two weeks. Cut.
2. **"Nothing special today"** as the body of a post. Cut.
3. **"Sick today, so no building"** posted as a build log. If sick, skip. The post itself is a niche-clarity 1.
4. **Meta-AI commentary** ("Just try AI is becoming useless advice"). Off-niche per `my-positioning.md`. Either rewrite grounded in a specific build, or cut.

These four cuts free up ~3 slots/week. Reallocate to:

- One extra prompt-diff post.
- One extra v1 → vN post.
- One thread per fortnight.

---

## Per-post checklist (before hitting publish)

Lightweight version of `post-analysis-template.md` for in-the-moment use:

- [ ] Does the post fit one of the three pillars in `my-positioning.md`?
- [ ] Is there one concrete artifact (screenshot, prompt, named tool, date, number)?
- [ ] Could a stranger guess my niche from this post alone?
- [ ] Is it in the AM or PM slot? If not, why am I posting now?
- [ ] If it gets one reply, do I have a follow-up reply ready?

If any answer is no, run the full `post-analysis-template.md`. If two or more are no, do not post.

---

## Logging

After each post, set a 24-hour calendar nudge to log it into `post-log-template.csv` (per `README.md`). The log only earns its keep at 6+ weeks of rows; start now.

The numbers that matter most at this stage:

- **`profile_clicks` per 1K impressions** — the upstream of follows. Targets: 5+ is healthy, 10+ is strong.
- **`follows` per 1K impressions** — the bottom line. Targets: 1+ is healthy at <1K followers, will trend down as the easy follows close out.
- **Replies / impressions ratio** — a leading indicator that the post hit the right cluster. Targets: 1%+ is strong.

Likes are the least informative number. Use them for sanity-checking but do not optimise for them; `weighted_scorer.rs` puts likes alongside several other positive predictions, not above them.
