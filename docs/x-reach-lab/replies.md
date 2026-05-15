# Replies

Reply strategy for `@RoyInProgress`. Replies are not a side activity — they are a separately weighted positive prediction (`weighted_scorer.rs`) **and** the cheapest way to get in front of viewers the retrieval stage would otherwise never match to you.

Read alongside:
- `algorithm-insights.md` §3 (positive predicted actions, including `P(reply)`) and §9 (followers feed Thunder, but ranking decides who actually sees you).
- `my-positioning.md` for who counts as on-niche.
- `posts.md` for the time slots reserved for replying.

---

## Why replies matter more than they look

Three mechanisms, all visible in the public code:

1. **`P(reply)` is positively weighted** in `weighted_scorer.rs`. When *you* reply, you contribute that signal to the parent post. When *someone replies to you*, that same signal is contributed to your post. Posts that earn replies feed the ranker something it actively rewards.
2. **Replying surfaces you in the parent author's notifications and in the thread** — a viewer reading the thread sees you as an in-context voice, not a cold profile. The next time the retrieval stage scores their history, your account has a small new signal in their behavior.
3. **Reply quality decides whether the parent author's audience clicks your profile.** `P(profile_click)` and `P(follow_author)` are separately weighted. A substantive reply on a high-impression post can produce more profile clicks than a mediocre original post.

The implication: at a follower count under ~1K, replies are likely the highest-leverage activity on the account. Posts compound slowly; replies compound on every thread you join.

---

## Daily target

| Activity                    | Daily target | Where it fits                                  |
|-----------------------------|--------------|------------------------------------------------|
| **Substantive replies**     | 8–12         | Two reply blocks: 09:00–09:30 and 20:30–21:30 CEST. |
| **Replies to your own posts** | 2–3        | Reply within 2 hours to the first commenters on your anchor posts. |
| **Quote-replies / quote-tweets** | 0–1     | Only when you can add a real artifact (screenshot, prompt, diff). |
| **Dunks, drive-by takes, "🔥🔥🔥"** | 0    | Hard zero. They cost negative-signal risk and earn nothing.       |

8–12 substantive replies > 30 generic ones. The unit of value is "did a stranger in my niche read this and want to see what else I do?", not "did I leave a mark on the thread".

---

## Reply blocks (Amsterdam, CEST)

Two 30-minute blocks, mirrored to the post anchors in `posts.md`:

### Morning block: 09:00 – 09:30 CEST

- 5–7 replies on EU builder accounts (designers, PMs, indie devs in your three circles).
- Catch threads from US accounts that posted overnight — those threads are still active but past peak; a substantive reply at this hour often lands at the top once the OP wakes up.

### Evening block: 20:30 – 21:30 CEST

- 5–7 replies on US accounts that are mid-day in their local time.
- Highest-leverage block for out-of-network reach. US tech audience is the larger cluster; your replies here travel further.

Outside these blocks, do **not** open replies. Treat the inbox like email — batched, not interrupt-driven. Continuously checking replies costs you focus and pushes the reply count up without raising quality.

---

## Who to reply to (priority order)

From sharpest to broadest, mirroring the three circles in `my-positioning.md`:

1. **Circle 1 (highest priority): indie builders shipping AI-built products.** Accounts where you can credibly trade notes on prompts, agents, UI calls. Reply within 20 minutes of their post when possible — early replies are seen by more of their incoming audience and earn more downstream profile clicks.
2. **Circle 2: designers and PMs prototyping with AI.** Slightly broader. Reply with screenshots or short prompt examples that show, not tell.
3. **Circle 3: AI-curious developers considering an agent-first workflow.** Reply with one concrete recommendation grounded in your builds. Avoid generic "you should try X" — name the build, the agent, and the specific outcome.

Outside the three circles → do not reply. A reply to a viral thread outside your niche dilutes the retrieval embedding for your account just as much as an off-niche post does (`algorithm-insights.md` §8).

**Build a list, do not freestyle.** Maintain a private list (X Lists or a simple text file) of 30–50 accounts in circles 1 and 2. Open it twice a day during the reply blocks. Without a list you will default to whatever is loud on the timeline, which is usually outside the niche.

---

## What a strong reply looks like

A strong reply does at least one of:

1. **Adds a concrete artifact** the parent post is missing (a screenshot, a prompt, a named tool, a number).
2. **Tests the claim with one specific counter-example** from your own builds.
3. **Asks a sharper question** than the thread is currently asking. Not "thoughts?" — a question that narrows the discussion.
4. **Extends the OP's idea** with one tangential observation that shows you have built something adjacent.

Template:

> [One sentence reacting to the specific claim, not the post as a whole.]
>
> [One specific artifact, number, or example from your work — or one sharper question.]

Two to four sentences. No more. Replies longer than four sentences read as derailing.

### Example: weak vs strong

**Weak reply:**

> "This is so true! AI agents are wild right now 🔥"

- Niche clarity: 1. Adds nothing.
- Profile-click likelihood: 1. The reader has no reason to investigate you.
- Negative-signal risk: medium (low-effort replies feed `not_interested` if the OP has many).

**Strong reply (to a post about empty states in agent-generated UIs):**

> "Same trap — I had Claude generate a workout app's empty state and it produced motivational copy by default. The fix was pasting one screenshot of Linear's empty state and writing one line: 'match the rhythm.' Cut the copy in half on the next pass."

- Niche clarity: 5. Names tool, build, technique.
- Profile-click likelihood: 4. A reader curious about the result will click through.
- Negative-signal risk: 5 (lowest). Clean, on-topic, no self-promo.

The pattern: react to one specific thing, contribute one specific artifact, stop.

---

## Replies to your own posts

The first 2 hours after publishing are the highest-leverage window for replying to commenters. `algorithm-insights.md` §7 makes the point that the model does not have an explicit "first hour rule", but it does reward dwell, reply count, and continued thread activity. The way you earn those on your own post is by extending the conversation when it starts.

Rules:

1. **Reply to the first 3–5 commenters** within 2 hours. After that, batch the rest into one or two replies at the end of the day.
2. **Never reply with a one-word thanks.** Treat your own thread the same way you treat someone else's — add an artifact or sharpen the question.
3. **Use replies to extend, not defend.** If someone disagrees, ask one question; do not relitigate the post.
4. **Save the best follow-up for a new post tomorrow.** If a reply thread produces a new artifact or a sharper version of the original idea, that is tomorrow's anchor post, not a buried sub-reply.

---

## Quote-replies / quote-tweets

Quotes are powerful and dangerous. They count as a separate positive action (`P(quote)`), but they also lock you into the parent post — if the parent ages badly, your quote ages with it.

Use a quote-reply when:

- You have a real visual to add (a screenshot, a v1 → v3, a prompt diff) that does not fit in a reply.
- The parent post is in-niche and the addition is collaborative, not corrective.

Do not quote-tweet:

- To dunk. `algorithm-insights.md` §4 lists `block_author`, `mute_author`, `report` as negatively weighted. Dunks generate those signals from the dunked account's audience.
- To "react." If the only addition is your opinion, write a reply instead.
- Off-niche viral content. Even a great quote-tweet on a viral thread costs you niche clarity if the parent is unrelated to your three pillars.

Cadence: **0–1 quote-replies per week.** They should feel rare.

---

## Threads under bigger accounts: the leverage move

Replying early under a much-larger account in your niche is the single highest-reach activity available at <1K followers. Mechanics:

1. **Watch your reply list** (the 30–50 accounts above) for posts from accounts >50K followers.
2. **Reply within 10–20 minutes** of their post, when the thread is just starting and your reply has a chance to rank high.
3. **Add a specific artifact** that complements the OP's post. Not a counter-argument, not a "great post."
4. **Do not pitch.** No "btw I made X." If your reply is good, the profile click finds the pitch.

This works because the OP's post is already being served to a large audience by the ranker. Your in-context reply is shown to viewers whose history strongly matches the OP, which by definition is more aligned with your niche than your average out-of-network impression.

Expected outcome: one good reply under a 100K-follower account in your niche outperforms an average original post by 5–20x on profile clicks. Track it.

---

## What never to reply with

A short kill-list, derived from the negative-signal actions in `weighted_scorer.rs` (§4 of `algorithm-insights.md`):

- **Emoji-only replies.** No 🔥, ❤️, 💯 as the full body.
- **"Following!" / "Subscribed!"** Free admission ticket; nothing earned.
- **"Need help? DM me."** Reads as solicitation. Triggers `mute_author` from the parent's audience.
- **Mass-tagging "you should reply too @X @Y @Z."** Triggers reports.
- **Drive-by political / current-events replies on threads in your niche.** Off-topic, high negative-signal risk.
- **Auto-promo: "Check out my [app/account]!"** Even if the build is relevant, this format is the single biggest mute trigger in the kill-list. Earn the profile click instead.
- **Correcting typos / grammar.** No upside, real downside.
- **"This. ⬆️"** Empty agreement.

---

## Reply hygiene checklist

Before sending any reply, glance at this:

- [ ] Does this reply name a specific build, prompt, tool, or screenshot?
- [ ] Would a stranger reading just this reply (without scrolling up) want to click my profile?
- [ ] Is it ≤4 sentences?
- [ ] Is the OP in one of the three circles, or replying to one of them?
- [ ] Am I adding, not arguing?

Three or more checkboxes failing → do not send. Save the energy for a better thread.

---

## Logging replies

You do not need a CSV for replies. But once a week, during the Sunday review (`README.md`), spend 5 minutes on:

1. Open your X profile → Replies tab.
2. Scroll the past week of replies.
3. Mark the **three best replies** (most profile clicks, or the ones that produced the most useful thread). Pin them mentally — they are the templates for next week.
4. Mark the **three worst** (one-word, off-niche, lazy). Note the trigger ("I was tired", "I was scrolling outside my reply blocks"). Cut the trigger, not the reply.

The point is not to track every reply. The point is to keep getting better at the format.

---

## A final note on cadence

Replies are not optional connective tissue. They are the cheapest way to earn the signals (`P(reply)`, `P(profile_click)`, `P(follow_author)`) that move the account at this size. Treat them with the same care as posts:

- Two reply blocks a day, every day.
- 8–12 substantive replies per day.
- Zero dunks, zero filler.
- One quote-reply per week, max.
- The best replies become next week's post topics.

If posts are the storefront, replies are the foot traffic. The storefront only works because the foot traffic walks past.
