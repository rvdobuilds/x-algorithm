# X Growth Analysis — Read Against the Public Algorithm Repo

This document compares your 30-day X growth sprint against what is actually
visible in the `xai-org/x-algorithm` repository (the "For You" feed code
released May 2026).

**Important caveat applied throughout:** this public repo is a partial,
frozen snapshot. The model checkpoint is a *mini* model. The actual ranking
weights are loaded from an external `params` service that is **not in this
repo**. So this repo tells us *what the system measures and how it is
structured*, but not *how strongly each thing is weighted*. Every conclusion
below is labelled with a confidence level. Anything below 75% is flagged.

---

## 1. Executive Summary

**Verdict: your plan is directionally supported, with three specific
corrections needed.** (Confidence: ~80%)

The repo confirms the core logic your plan rests on:

- The feed has two doors: **in-network** (your followers) and
  **out-of-network** (ML retrieval). Both are real and both are in the code.
- The ranking model explicitly predicts **follow, profile-click, reply,
  like, repost, share, dwell, photo-expand and video-view** probabilities.
  These are the exact actions your plan optimises for. That is a strong
  match.
- Negative actions (**not interested, block, mute, report**) carry negative
  weight. Engagement-bait that annoys people is not neutral — it actively
  hurts.

The three corrections:

1. **"3 posts a day is risky" is mostly a myth here** — but there *is* a
   real per-feed author-diversity penalty. The fix is about *spacing*, not
   *count*.
2. **Don't lean on near-identical reposts/format clones** — the repo has
   explicit duplicate and repost-dedup filters.
3. **The repo is For-You-feed mechanics only.** It says nothing about
   Communities. Don't build strategy on community-distribution assumptions
   from this repo.

You should run the sprint. The plan is sound. Tighten it, don't rebuild it.

---

## 2. What The Repo Clearly Supports

### 2.1 Two distribution channels: in-network and out-of-network
- **Finding:** The feed sources candidates from `ThunderSource` (posts from
  accounts you follow) and `PhoenixSource` / `PhoenixMOESource` /
  `PhoenixTopicsSource` (ML retrieval from a global corpus).
- **Why it matters:** Followers are a *guaranteed candidate pool*. Every
  follower you gain permanently adds you to an in-network retrieval set.
  Growth compounds.
- **Files:** `README.md`; `home-mixer/sources/thunder_source.rs`;
  `home-mixer/sources/phoenix_source.rs`;
  `home-mixer/candidate_pipeline/phoenix_candidate_pipeline.rs`.
- **Confidence: 95%.**

### 2.2 In-network content is prioritised over out-of-network
- **Finding:** `OONScorer` multiplies out-of-network candidate scores by
  `OON_WEIGHT_FACTOR`; `RankingScorer::effective_oon_weight` applies an
  out-of-network weight factor. The comment literally reads "Prioritize
  in-network candidates over out-of-network candidates."
- **Why it matters:** Reaching a *new* person cold (out-of-network) is
  mathematically harder than reaching an existing follower. This is the
  single strongest argument for your follower-count goal: followers are not
  vanity, they are a structural ranking advantage.
- **Files:** `home-mixer/scorers/oon_scorer.rs`;
  `home-mixer/scorers/ranking_scorer.rs` (lines ~220-239).
- **Confidence: 90%.** (Exact factor value is not in the repo.)

### 2.3 The model predicts the exact engagement actions your plan targets
- **Finding:** The ranking model outputs probabilities for: favorite,
  reply, repost, quote, click, **profile_click**, video view (vqv),
  **photo_expand**, share (+ via DM, + via copy link), dwell, dwell_time,
  **follow_author**, not_interested, block_author, mute_author, report.
- **Why it matters:** Replies, profile visits and follows are not folk
  wisdom — they are named model outputs. `follow_author` and `profile_click`
  having their own predicted heads means the system is *built* to find
  content that makes people follow.
- **Files:** `README.md` ("Scoring and Ranking");
  `home-mixer/scorers/weighted_scorer.rs`;
  `home-mixer/scorers/ranking_scorer.rs` (`ScoringWeights`).
- **Confidence: 95%** for the action list. **The relative weights are NOT
  visible** (loaded from `params`).

### 2.4 Final score = weighted sum of predicted actions; negatives subtract
- **Finding:** `Final Score = Σ (weight_i × P(action_i))`. Positive actions
  add; `not_interested`, `block_author`, `mute_author`, `report`, and
  `not_dwelled` subtract (`negative_sum` in `ranking_scorer.rs`).
- **Why it matters:** A post that gets likes from your niche *and*
  "not interested" from mismatched viewers is being scored *both* ways.
  Bait that reaches the wrong audience is taxed.
- **Files:** `home-mixer/scorers/weighted_scorer.rs`;
  `home-mixer/scorers/ranking_scorer.rs`.
- **Confidence: 90%.**

### 2.5 Author diversity penalty within a single feed response
- **Finding:** `AuthorDiversityScorer` / `RankingScorer::apply_author_diversity`
  multiply a candidate's score by `decay_factor^position`, where `position`
  is how many times that author *already appears higher in the same ranked
  response*. The first post from an author is untouched; the 2nd, 3rd, etc.
  are progressively attenuated down to a floor.
- **Why it matters:** If two or three of your posts are *all eligible in one
  user's single feed refresh*, only your strongest gets full score; the
  others are demoted. This is per-response, not per-day.
- **Files:** `home-mixer/scorers/author_diversity_scorer.rs`;
  `home-mixer/scorers/ranking_scorer.rs` (lines ~186-217).
- **Confidence: 90%.**

### 2.6 Duplicate and repost de-duplication filters exist
- **Finding:** `DropDuplicatesFilter` (same post id), `RetweetDeduplicationFilter`
  (reposts of the same content), `DedupConversationFilter` (conversation
  branches).
- **Why it matters:** Recycling *identical* content / reposting the same
  thing does not multiply reach — it is collapsed. This targets duplicate
  *content*, not similar *topics* (see §4).
- **Files:** `README.md` ("Filtering"); `home-mixer/filters/`.
- **Confidence: 90%.**

### 2.7 "Already seen / already served" filters
- **Finding:** `PreviouslySeenPostsFilter` (+ backup) and
  `PreviouslyServedPostsFilter` remove posts a user has already seen or that
  were already served this session, using bloom filters + seen IDs.
- **Why it matters:** You get essentially one shot per post per user. A post
  that flops on first impression is not silently re-served later. New posts
  are how you get new at-bats.
- **Files:** `home-mixer/filters/previously_seen_posts_filter.rs`;
  `previously_served_posts_filter.rs`.
- **Confidence: 90%.**

### 2.8 Out-of-network retrieval is driven by engagement history similarity
- **Finding:** Phoenix retrieval is a two-tower model. The **user tower**
  encodes a viewer's engagement history into an embedding; the **candidate
  tower** encodes posts; retrieval is top-K by dot-product similarity.
- **Why it matters:** You get discovered out-of-network when your post's
  embedding is close to what a user's engagement history "points at." Your
  *content* and *who engages with it* shape who you reach next.
- **Files:** `phoenix/README.md`; `phoenix/recsys_retrieval_model.py`;
  `home-mixer/sources/phoenix_source.rs`.
- **Confidence: 88%.**

### 2.9 The system uses no hand-engineered relevance features
- **Finding:** README "Key Design Decisions #1": every hand-engineered
  feature and most heuristics were removed; the Grok-based transformer
  learns relevance from raw engagement sequences.
- **Why it matters:** There is no "post at 7:30am" rule, no hashtag rule, no
  keyword trick in the code. Relevance = "does this match real engagement
  patterns of real users." Tricks aimed at a rule that does not exist are
  wasted effort.
- **Files:** `README.md`.
- **Confidence: 85%.**

### 2.10 A separate "Who To Follow" module exists
- **Finding:** `WhoToFollowSource` injects up to 3 account recommendations
  into the feed, excluding already-served accounts.
- **Why it matters:** Follower growth has a path *independent of your
  posts* — being recommended as an account. The repo does not expose how
  candidates are chosen, but the channel exists.
- **Files:** `home-mixer/sources/who_to_follow_source.rs`.
- **Confidence: 80%** that the module exists; **<60%** on how it picks
  accounts — *I don't know how Who-To-Follow ranks candidates. My best
  hypothesis is graph + engagement similarity, but that code is not here.*

### 2.11 Engagement counts and media presence are hydrated as context
- **Finding:** `EngagementCountsHydrator` attaches fav/reply/repost/quote
  counts; `HasMediaHydrator` attaches a has-media flag; video duration is
  hydrated and `vqv` weight only applies to videos over a minimum duration.
- **Why it matters:** Absolute engagement counts and media presence are
  available to the model as signal. Media also *unlocks extra positive
  actions* (photo_expand, video view) that simply do not exist for text-only
  posts.
- **Files:** `home-mixer/candidate_hydrators/engagement_counts_hydrator.rs`;
  `has_media_hydrator.rs`; `video_duration_candidate_hydrator.rs`.
- **Confidence: 80%** that these are features; **the model's actual use of
  them is not visible.**

### 2.12 Social proof: followed users who replied
- **Finding:** `FollowingRepliedUsersHydrator` builds a facepile of accounts
  *the viewer follows* who replied to a post — but only for viewers who
  themselves have ≥1000 followers.
- **Why it matters:** Replies from connected accounts can be surfaced as
  visible social proof to others. Warm-network replies are not invisible.
- **Files:** `home-mixer/candidate_hydrators/following_replied_users_hydrator.rs`.
- **Confidence: 78%.**

---

## 3. Reasonable Inferences (not directly proven)

### 3.1 Early engagement from *relevant* users probably helps
- **Inference:** The model is trained continuously on real-time engagement,
  retrieval is similarity to engagers' histories, and engagement counts are
  context features. So early engagement — especially from people whose
  histories resemble your target audience — plausibly improves both ranking
  and who you get retrieved to next.
- **Why it matters for you:** Your "reply to people who engage with my
  posts" and warm-network reply blocks are well-aimed.
- **Confidence: 65%.** *I am not sure about this.* There is **no explicit
  "first 30 minutes" or velocity-boost code in this repo.** The benefit is
  indirect (training + retrieval), not a visible ranking multiplier.

### 3.2 Niche consistency helps retrieval *aim* — not per-post retrieval
- **Inference:** Each post is embedded individually, so one off-niche post
  is not "penalised." But a consistent niche means the people who engage
  with you have *coherent* histories, so the two-tower model keeps
  retrieving you to a similar, relevant audience — which converts to follows
  better than scattered reach.
- **Why it matters for you:** Your 70/20/10 mix is fine *if* the 70% stays
  recognisably in the AI-assisted-building lane. Random broad takes dilute
  the audience signal.
- **Confidence: 70%.** *I am not sure about this* — it is an architectural
  inference, not a stated rule.

### 3.3 Media can help, mainly by unlocking extra positive signals
- **Inference:** Photo-expand and video-view are separate scored actions.
  A screenshot or short video creates *additional* ways to earn positive
  score that a text post cannot. That is a structural advantage, not a
  "boost."
- **Why it matters for you:** Your 20% proof/product posts (screenshots,
  before/after) are well-suited — they naturally carry media.
- **Confidence: 68%.** *I am not sure about this* — vqv has a known
  duration gate, but there is no visible "media gets +X" rule.

### 3.4 Warm-network engagement helps in two ways
- **Inference:** (a) Directly — your followers' feeds pull your posts
  in-network with the in-network advantage. (b) Indirectly — when warm
  accounts engage, that engagement seeds retrieval/training so similar
  out-of-network users can find you.
- **Why it matters for you:** Warm-network replies are not "only useful if
  they go viral." They are the seed layer.
- **Confidence: 72%.** *I am not sure about the exact magnitude.*

### 3.5 Profile-visit → follow is the realistic growth chain
- **Inference:** `profile_click` and `follow_author` are both predicted.
  Practically, a stranger sees a post → visits profile → follows. The repo
  does not model the profile page, but it confirms both endpoints matter.
- **Why it matters for you:** Your bio + pinned post + recent-post
  consistency are the actual follow-conversion surface. Posts get the click;
  the profile earns the follow.
- **Confidence: 70%.**

---

## 4. Speculation / Claims To Avoid

Do **not** state these as facts — the repo does not support them:

- ❌ "Posting N times a day is optimal / penalised." The repo has **no
  per-author per-day frequency rule.** Author diversity is *per single feed
  response*, not per day.
- ❌ Any exact ranking weight ("likes are worth X", "replies are 13×
  likes"). **Weights are loaded from an external `params` service not in
  this repo.** All weight constants (`FavoriteWeight`, `ReplyWeight`, etc.)
  are fetched at runtime — their values are invisible.
- ❌ "The first 30 minutes decide everything." No first-N-minutes ranking
  rule exists in this code.
- ❌ Exact media weighting / "video gets 2× reach." Only the vqv
  duration-gate is visible.
- ❌ "Communities boost distribution" or any community-routing claim. **The
  repo contains no Communities feature** (one unrelated "community note"
  safety label aside).
- ❌ "The algorithm rewards posting consistency / streaks." Nothing about
  posting cadence streaks exists in the code.
- ❌ "Hashtags / keywords / links are penalised or boosted." No such
  heuristic — hand-engineered features were explicitly removed. (Muted
  *keywords* are a per-viewer filter, not a global penalty.)
- ❌ "Topic-similar posts from one author get downranked against each
  other." The dedup filters target *identical/repost* content, not similar
  topics.

---

## 5. Assessment Of Your 30-Day Plan

| Plan element | Verdict | Reason | Confidence |
|---|---|---|---|
| 70/20/10 content mix | **Keep (adjust)** | Mix is fine. Adjust *only* so the 70% stays recognisably in your AI-building niche — retrieval rewards a coherent audience signal (§3.2). | 70% |
| 3 posts/day baseline | **Keep** | No per-day frequency penalty exists. 3/day is fine for getting more at-bats. | 80% |
| 4th post on momentum | **Keep** | Same reasoning. Extra posts = extra independent retrieval attempts. | 78% |
| Posts clustered in tight windows | **Adjust** | Author-diversity penalty is per-feed-response. Posts within a couple of hours can land in the *same* user's refresh and demote each other. Space them out. | 75% |
| Short questions | **Keep** | Directly farms `reply` — a named predicted action. | 80% |
| Builder-connect posts | **Keep** | Farms replies + profile clicks + follows from a relevant audience. Strong fit. | 78% |
| Broad AI/building takes ("proven formats") | **Adjust** | Fine if on-niche. Generic bait risks `not_interested` from mismatched viewers (negative weight) and dilutes retrieval aim. | 65% — *I am not sure about this.* |
| Proof/product posts (screenshots, before/after) | **Keep** | Naturally carry media (unlocks photo_expand/video) and earn profile clicks + follows. Likely your best follow-converters. | 75% |
| Day X / personal posts | **Keep** | Builds the profile narrative that converts profile-click → follow. Keep at ~10%. | 65% — *I am not sure about this* — repo doesn't model the profile page. |
| 10 warm-network replies/day | **Keep** | In-network advantage + retrieval seeding + reply social-proof facepile. | 72% |
| 10–15 discovery replies/day | **Keep** | Replies are how you appear in front of out-of-network audiences cheaply. | 70% |
| Reply to people who engage | **Keep** | Reinforces the engager relationship that seeds retrieval. | 68% |
| Analytics only at set moments | **Keep** | Repo-neutral, but healthy. No reason to change. | n/a |
| Weekly format review | **Keep** | Sensible. Each post is scored independently, so testing formats and repeating winners is valid. | 70% |

**Nothing in the plan should be killed.** A few items need spacing/aim
adjustments.

---

## 6. Recommended Revised 30-Day Plan

Changes are small. The plan is mostly right.

**Post frequency:** 3/day baseline, 4 on momentum days — **unchanged**.

**Posting windows (revised for spacing):**
- 07:30–07:50 — light network/community-style connect post.
- 12:15–12:35 — growth-native AI/building post.
- 20:45–21:10 — product proof / Day X / lesson / reflection.
- These three are already well spaced (~5h, ~8h apart) — **good, keep**.
- If you add a 4th post, slot it ~16:00, **not** back-to-back with another
  post. The only real spacing risk is two posts within ~1–2 hours.

**Reply blocks:** keep 10 warm + 10–15 discovery. Tighten *targeting* of
discovery replies to accounts in or adjacent to the AI-building niche — the
retrieval model benefits when your engagers form a coherent cluster.

**Content categories (revised emphasis):**
- 70% growth-native — keep, but **all of it recognisably AI-building lane.**
  Short questions and builder-connect posts over generic broad takes.
- 20% proof/product — keep; prefer posts that carry a screenshot or short
  clip (unlocks extra scored actions).
- 10% personal journey — keep.

**Community vs public posting:** The repo only covers the public For You
feed. **Default to public posting.** Treat Communities as an unmeasured
experiment, not a core channel — do not over-invest based on this repo.

**What to avoid:**
- Near-identical reposts / re-sharing the same post (dedup filters).
- Two posts inside the same ~1–2h window (author-diversity overlap).
- Engagement bait likely to draw "not interested" from off-niche viewers.
- Expecting a flopped post to get a second life — it won't be re-served.

**What to repeat:**
- Formats that produce replies, profile visits and follows per impression.
- Posts that carry media when natural.
- Reply patterns that lead to real conversations with relevant accounts.

---

## 7. Daily Metrics To Track

X analytics does not expose everything the model uses, but track what you
*can* see. Suggested manual table (one row per post):

| Field | Notes |
|---|---|
| Date / time | posting window |
| Post type | growth-native / proof-product / personal |
| Public vs Community | keep mostly public |
| Topic / format | e.g. "short question", "before/after", "Day X" |
| Impressions | denominator for all rates |
| Likes | |
| Replies | named predicted action — watch closely |
| Reposts | |
| Quotes | |
| Bookmarks | proxy for save/share intent |
| Shares | incl. DM shares if shown |
| Profile visits | named predicted action — key for growth |
| Follows (day total) | account-level; X doesn't attribute per-post |
| Unfollows (day total) | watch for negative trend |
| Detail expands | dwell/interest proxy |
| Media views / video views | only for media posts |
| **Replies per 1k impressions** | reply rate |
| **Profile visits per 1k impressions** | curiosity rate |
| **Follows per 1k impressions** | conversion rate (day-level estimate) |

The three per-1k rates matter more than raw totals — they tell you which
*format* converts, independent of how much reach a post happened to get.

---

## 8. Weekly Review Method

Once per week, not daily:

1. **Rank posts by profile-visits-per-1k and replies-per-1k**, not by likes.
   Likes are the cheapest, weakest signal; profile visits and replies are
   closer to follows.
2. **Repeat:** any format in the top third on *either* rate — make 2–3 more
   of it next week.
3. **Stop:** any format in the bottom third on *both* rates after at least
   3 posts. One bad post is noise; three is a pattern. (Your "no strategy
   change after one bad post" rule is correct — formalise it as "n≥3".)
4. **Test next:** pick one new format per week. Each post is scored
   independently, so testing is cheap and low-risk.
5. **Check audience coherence:** glance at *who* is replying/following. If
   they are off-niche, your 70% is too generic — pull it back toward the
   AI-building lane.
6. **Watch the negative trend:** rising unfollows or "not interested"
   (if visible) means a format is reaching the wrong people — kill it
   regardless of its like count.

---

## 9. X Reach Lab Implications

Based on repo concepts, X Reach Lab should score a draft post on dimensions
that map to *actual model outputs*, not vibes:

- **Reply potential** — does the post invite a reply (question, mild
  disagreement, ask)? Maps to `P(reply)`.
- **Profile-click curiosity** — does it create a reason to check who wrote
  it? Maps to `P(profile_click)`.
- **Follow-conversion potential** — does it signal "more like this exists
  here"? Maps to `P(follow_author)`.
- **Niche/relevance clarity** — is it unambiguously in one lane? Drives
  retrieval coherence (§3.2). Flag posts that are off-lane.
- **Proof / specificity** — concrete numbers, screenshots, real artifacts
  vs generic claims. Specific posts convert profile visits better.
- **Negative-signal risk** — would an off-niche viewer hit "not interested"?
  Maps to negative-weighted actions. Flag bait.
- **Repetition / duplicate risk** — is this too close to a recent post?
  Maps to dedup/author-diversity behaviour. Warn on near-duplicates and on
  two posts queued too close together.
- **Media / dwell potential** — does it carry or could it carry an image or
  clip? Unlocks `photo_expand` / `video_view`.
- **Community vs public intent** — classify and default to public; mark
  community posts as "unmeasured by this repo."
- **Post-type classification** — auto-tag each draft as growth-native /
  product-proof / personal-journey so the lab can enforce the 70/20/10 mix
  and report the realised mix vs target.

A useful single output: a per-post estimate of **reply, profile-visit and
follow potential**, plus a **negative-risk flag** and a **duplicate-risk
flag** — because those are the four things the repo most clearly shows the
algorithm actually reacts to.

---

## 10. Final Recommendation

**Should you run the 30-day sprint? Yes.** (Confidence: ~80%) The plan's
foundation matches the repo: in-network followers are a structural
advantage, and replies/profile-visits/follows are literally named model
outputs. The plan is aimed at the right targets.

**What to change (small):**
1. Space posts so two never land within ~1–2 hours (author-diversity).
2. Keep the 70% growth-native content recognisably in your AI-building lane
   — coherence helps retrieval aim and follow conversion.
3. Never recycle near-identical posts/reposts — they get deduped.
4. Track *profile-visits-per-1k* and *replies-per-1k*, not likes.
5. Treat Communities as unmeasured; default to public posting.

**What NOT to worry about:**
- Posting "too often." No per-day frequency penalty exists in the repo.
- Hashtags, keywords, links, post length tricks. Hand-engineered features
  were removed; there is no rule to game.
- A single flop. Posts are scored independently; one bad post does nothing
  lasting. Your "no strategy change after one bad post" rule is correct.
- Exact algorithm weights. They are not in the repo and not knowable here —
  optimise behaviour, not numbers.

**Biggest risk:** Not the algorithm — it is **you switching tactics and
burning out.** The repo shows growth is a slow compounding loop
(follower → in-network advantage → more reach → more followers) with **no
shortcut**. The mechanic that actually threatens you is consistency, and the
challenges you listed (FOMO, low energy from posting, tactic-switching) all
attack consistency. Your strongest lever is the boring one: keep the system
stable for the full 30 days and let the compounding run.

A secondary, smaller risk: chasing generic "proven formats" so hard that
your audience becomes incoherent, which weakens retrieval aim and slows
follow conversion even while impressions look fine. Watch *who* follows, not
just how many.

---

*Scope note: analysis is based solely on the public `xai-org/x-algorithm`
repository (May 2026 release), which is a partial, frozen, mini-model
snapshot. It does not represent the full current production system. Ranking
weights, Who-To-Follow internals, and any Communities mechanics are not in
this repo and were not invented here.*
