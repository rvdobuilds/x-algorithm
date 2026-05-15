# X Reach Lab

A plain-English toolkit for improving reach on X, built on top of what the public files in this repository imply about how the For You feed retrieves, filters, and ranks posts.

This is a thinking and discipline toolkit. Nothing in here calls X's API, scrapes X, or runs the model. It is a set of markdown files and a CSV.

---

## What this toolkit is

- A practical reading of the algorithm code in this repo (`README.md`, `phoenix/`, `home-mixer/`, `candidate-pipeline/`) translated into rules for drafting and reviewing X posts.
- A rubric for scoring a draft before publishing.
- A template for analyzing a single draft in depth.
- A narrow positioning document so posts stay on-niche.
- A CSV template for manually tracking post performance.
- A weekly routine for spotting patterns in your own data.

## What this toolkit is NOT

- It is **not** a copy of X's production ranking. The repo itself notes that the released model is a mini, frozen checkpoint and that production runs a larger, continuously trained version.
- It is **not** a growth course, motivational guide, or list of "viral hooks".
- It is **not** an external service. There are no API calls, no scraping, no data collection. You enter numbers from your own analytics manually.
- It is **not** a guarantee. Any post can fail. The rubric is a discipline that improves your hit rate over time, not a forecast.
- It is **not** an excuse to stop reading the code. When a claim here surprises you, go to the file cited next to it and verify.

---

## Files in this folder

| File | Purpose |
|------|---------|
| `algorithm-insights.md` | Plain-English reading of what the repo implies about reach. |
| `content-rubric.md` | 1-5 scoring rubric for X posts, with a final reach formula. |
| `post-analysis-template.md` | Per-draft analysis template. Paste a post, score it, revise it. |
| `my-positioning.md` | Narrow positioning for the account. Pillars and topics-to-avoid. |
| `post-log-template.csv` | CSV template for manually tracking post performance. |
| `README.md` | This file. |

---

## How to score a draft post

1. Open `post-analysis-template.md`.
2. Copy the contents into a scratch file (e.g. `drafts/2026-05-15-prompt-diff.md`). Do **not** edit the template itself.
3. Paste your draft into the "Original post" block.
4. Fill in intended audience, likely audience cluster, predicted strongest and weakest actions.
5. Score each rubric dimension 1-5 using the anchors in `content-rubric.md`. Be strict.
6. Compute the overall reach score with the formula at the bottom of `content-rubric.md`.
7. Walk the "should I post this?" checklist.
8. Write the improved, shorter, reply-driven, and profile-click-driven versions.
9. Pick a verdict: post / revise / do not post.

Time budget: 5-10 minutes per draft. If you find yourself spending an hour, the post is probably off-niche - check it against `my-positioning.md`.

---

## How to log performance

After a post has been live for 24-48 hours, copy `post-log-template.csv` into a working file (e.g. `data/post-log-2026.csv`) and add a row.

Columns:

- `date`, `time` - when you posted.
- `post_type` - which pillar from `my-positioning.md` (build_log / prompt / ui_decision / other).
- `post_text_short` - a short label, not the full post.
- `intended_audience` - the one-sentence intended audience from your analysis.
- `asset_type` - image / video / text / link.
- `impressions`, `likes`, `replies`, `reposts`, `bookmarks`, `profile_clicks`, `follows` - from X's native analytics, entered by hand.
- `engagement_rate` - (likes + replies + reposts + bookmarks) / impressions.
- `follows_per_1000_impressions` - follows / impressions * 1000.
- `notes` - one line on context (time of day, replied to a big account, etc.).
- `reuse_angle` - if this post worked, what is the follow-up post that extends the same angle?
- `verdict` - post / revise / killed-after-the-fact.

Do not over-engineer the log. The point is to have enough rows that patterns appear after a few weeks.

---

## How to identify patterns weekly

Once a week, sit with `data/post-log-*.csv` for 15 minutes and answer:

1. **Which post_type produced the best follows-per-1000-impressions?** That is your highest-leverage pillar this week.
2. **Which posts had high impressions but low profile_clicks?** Those posts reached people who did not care to learn who you are - usually a sign of an off-niche topic that got lucky, not a repeatable win.
3. **Which posts had low impressions but high engagement_rate?** Those are the posts your existing followers love. Worth repeating; will not necessarily grow the account on their own.
4. **Which posts had high replies?** Look at the reply threads - are they the right audience (circle 1 in `my-positioning.md`) or the wrong audience?
5. **Anything to add to "topics to avoid"?** If a post drew off-niche attention, write the lesson into `my-positioning.md` so you do not repeat it.
6. **Anything to add to "example post formats"?** If a format worked twice, codify it.

Update `my-positioning.md` when you find a stable pattern. The positioning document is allowed to evolve; the pillars are allowed to sharpen. They are not allowed to broaden.

---

## How to use it with Claude or ChatGPT

The toolkit is designed to be pasted into a chat with a model so the model has the same context you do. A workflow that works:

1. Open a new chat.
2. Paste `algorithm-insights.md` first, then `my-positioning.md`, then `content-rubric.md`. Tell the model these three files are the ground rules.
3. Paste your draft post.
4. Ask the model to fill out `post-analysis-template.md` for the draft, scoring honestly.
5. Read the model's scores critically. The rubric is the source of truth; the model is a faster way to get a first pass. If you disagree with a score, override it.
6. Ask for the three rewrites (shorter, reply-driven, profile-click-driven) explicitly.
7. Make the final decision yourself. The model does not get to pick the verdict.

Cautions:

- Do not let the model invent claims about "the algorithm" beyond what `algorithm-insights.md` says. If it does, push back.
- Do not let the model broaden the positioning. If a suggested rewrite drifts off-niche, reject it.
- Do not use the model to write the post for you. Use it to score and stress-test what you already wrote.

---

## Recommended weekly routine

A minimal cadence designed to avoid both under-posting and over-posting.

**Sunday evening (30 minutes)**
- Read the past week's rows in the post log.
- Run the six "identify patterns" questions above.
- Update `my-positioning.md` if a stable pattern emerged.
- Sketch three to five post ideas for the coming week, each tagged to a pillar.

**Each posting day (15 minutes per post)**
- Pick one of the sketched ideas.
- Draft the post.
- Run it through `post-analysis-template.md`.
- Post the chosen version.
- Set a calendar nudge 24 hours later to log the numbers.

**24-48 hours after each post (5 minutes)**
- Pull the numbers from X analytics.
- Add a row to the post log.
- Decide the reuse_angle.

**Once a month (45 minutes)**
- Re-read `algorithm-insights.md` against the repo. If a file in the repo has changed in a way that contradicts the doc, update the doc and note the change.
- Rotate the pinned post if a stronger sample of the account promise has been published.
- Drop the bottom 10% of post types from your rotation. Sharpen.

The toolkit only works if it is used regularly. The whole point is to make scoring and logging cheap enough that you actually do them.
