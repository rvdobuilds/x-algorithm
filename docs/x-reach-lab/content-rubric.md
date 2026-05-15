# Content Rubric

A 1-5 scoring rubric for evaluating an X post before you publish it.

The dimensions and weights below are derived from the public code, especially the predicted-action list in `home-mixer/scorers/weighted_scorer.rs` and the retrieval / ranking design in `phoenix/`. They are a practical scoring tool, not a reconstruction of X's production weights. Treat the numbers as a discipline for your own drafts, not a claim about what X is doing under the hood.

---

## How to use this rubric

1. Paste your draft.
2. Score each dimension 1-5 using the anchors below.
3. Compute the overall reach score with the formula in the last section.
4. Walk the "should I post this?" checklist.
5. If anything is a 1 or 2, decide whether to revise or drop the post.

Be strict. The rubric is useful only if you do not round up.

---

## 1. Niche clarity

Is it obvious, from this single post, what topic and angle your account stands for?

- **1** — Could come from any account. Generic take. No topical anchor.
- **3** — On-topic if a reader already follows you, but a stranger seeing it cold would not guess your niche.
- **5** — A new viewer can guess your niche from this one post alone. The post advertises your beat.

Why it matters: retrieval embeddings are easier to place when posts are consistently on one topic (`phoenix/README.md`, sports-corpus example).

---

## 2. Audience fit

Is this written for a specific, real cluster of people who already engage with similar content?

- **1** — "For everyone." No imagined reader.
- **3** — Aimed at a broad bucket (e.g. "developers") with no sharper sub-cluster.
- **5** — Clearly aimed at a sharp cluster you can name in one sentence (e.g. "indie devs who ship AI-built prototypes and care about prompt quality").

Why it matters: the ranker is conditioned on each viewer's history. Posts aimed at a definable cluster are likelier to have high predicted-action probabilities for that cluster.

---

## 3. Reply likelihood

Will a reader feel pulled to type a reply rather than scroll past?

- **1** — Closed statement. Nothing to add to. No question, no disagreement surface.
- **3** — Mild prompt at the end, or a take that invites agreement.
- **5** — A real question, a contestable claim, or a "here is my way - what is yours?" structure that creates a low-cost reply for the target reader.

Why it matters: `P(reply)` is a separate positively-weighted prediction in `weighted_scorer.rs`. Reply-driven posts feed multiple positive signals (reply, dwell, sometimes profile click).

---

## 4. Repost likelihood

Would a target reader repost or quote this without feeling like it makes them look bad?

- **1** — Self-promotional, brag-shaped, or low-status to share.
- **3** — Useful but not quotable. A reader might bookmark it instead of reposting.
- **5** — Frames an insight, contrarian take, or compact resource the target reader gains status by sharing.

Why it matters: `P(repost)` and `P(quote)` are both positive weighted predictions. Reposts also surface the post to that user's in-network audience via Thunder.

---

## 5. Profile-click likelihood

After reading this post, does a stranger want to check who wrote it?

- **1** — Nothing here suggests there is a person worth investigating.
- **3** — Interesting take, but the post is self-contained; no curiosity hook about the author.
- **5** — The post hints at a body of work, a story, or a track record that makes the reader want to see what else this account has.

Why it matters: `P(profile_click)` is a positive weighted prediction. Profile clicks are also the upstream step before `P(follow_author)`.

---

## 6. Dwell likelihood

Will the post pull the reader's eye and slow their scroll?

- **1** — One short line that resolves in 2 seconds.
- **3** — A normal-length post with a complete thought.
- **5** — Structured content (numbered points, a short story, a before/after, a screenshot) that needs more than a glance to absorb. Or a thread that opens a loop and resolves later.

Why it matters: `P(dwell)`, `dwell_time`, `P(video_quality_view)`, and `P(photo_expand)` are all positive weighted predictions. Dwell is one of the few signals that does not require an explicit click.

---

## 7. Proof / specificity

Does the post contain concrete detail rather than abstract claims?

- **1** — Generic principle, no example, no numbers, no artifact.
- **3** — One specific detail or example.
- **5** — A screenshot, a metric, a real prompt, a snippet of UI, a named tool, a date, a real result. A reader could test or copy something from the post.

Why it matters: this is not a direct algorithm signal in the repo. It is an inference: specific posts produce more replies, more dwell, and lower negative-signal risk (Not Interested, Mute) than vague claims.

---

## 8. Negative-signal risk

How likely is a viewer to mute, block, report, or hit Not Interested?

- **1 (worst)** — Ragebait, doom take, ambiguous medical/financial advice, mass-tagging, "follow me" hooks, screenshot pile-ons, naked self-promo. High risk.
- **3** — Probably fine but slightly off-topic for your stated niche, or slightly self-promotional.
- **5 (best)** — Clean. On-topic. No engagement bait. Nothing a viewer would regret seeing.

Score the **inverse** of risk: 5 means lowest risk.

Why it matters: `P(not_interested)`, `P(block_author)`, `P(mute_author)`, and `P(report)` are all **negatively** weighted in `weighted_scorer.rs`. The post-selection `vf_filter.rs` can also drop content outright.

---

## 9. Out-of-network potential

Could this post resonate with people who do not follow you yet?

- **1** — Insider reference that requires already knowing your account, your friends, or yesterday's drama.
- **3** — Comprehensible to a stranger, but uninteresting without context.
- **5** — Stands on its own. A stranger in your niche could land on it cold and still find it useful or interesting.

Why it matters: out-of-network reach is gated by retrieval similarity (`phoenix/run_pipeline.py`, two-tower). A post that requires prior context is unlikely to engage a viewer the ranker pulls in for the first time.

---

## 10. Follow conversion

If a stranger sees this post and clicks your profile, will the combination of post + profile make them follow?

- **1** — Post is fine, but it does not advertise a reason to follow. Or it contradicts the stated positioning of the account.
- **3** — Post hints at a reason to follow but you have to squint.
- **5** — Post is a clean sample of the exact thing the account promises. A reader who likes this post knows exactly what they would be subscribing to.

Why it matters: `P(follow_author)` is a positive weighted prediction, and profile-click → follow is the main organic acquisition funnel.

---

## Overall reach score

A simple, transparent formula. None of these weights are claimed to be X's production weights; they are practical weights for *your own draft review*.

```
reach_score =
    0.15 * niche_clarity
  + 0.10 * audience_fit
  + 0.10 * reply_likelihood
  + 0.10 * repost_likelihood
  + 0.10 * profile_click_likelihood
  + 0.10 * dwell_likelihood
  + 0.10 * proof_specificity
  + 0.10 * negative_signal_risk_inverse
  + 0.10 * out_of_network_potential
  + 0.05 * follow_conversion
```

Score range: 1.0 to 5.0.

Suggested verdict bands (calibrated by use, not claimed as algorithmic thresholds):

- **< 3.0** — Do not post. Revise.
- **3.0 - 3.7** — Post only if it is part of a series or you owe yourself a publish today.
- **3.7 - 4.3** — Good. Post.
- **> 4.3** — Strong. Post, and consider planning a follow-up that extends the same angle.

---

## Should I post this? Checklist

Before you publish, every item must be a clear "yes":

- [ ] A stranger could tell from this post alone what my niche is.
- [ ] I can name the specific cluster of people this post is for.
- [ ] There is at least one concrete detail (number, screenshot, real prompt, artifact, named tool).
- [ ] There is no ragebait, doom take, mass-tag, or "follow me" hook.
- [ ] If a stranger clicks my profile after this post, they will see the same kind of content waiting for them.
- [ ] I am not posting it just because I have not posted today.
- [ ] If it gets only one reply, I have a follow-up reply ready to extend the conversation.

If any box is "no", revise or drop the post.

---

## Examples of weak vs strong posts

These examples are illustrative for the rubric. They are not actual posts.

### Weak post

> "AI is going to change everything. Wild times. What do you think?"

- Niche clarity: 1 (could be anyone)
- Audience fit: 1 (no specific cluster)
- Reply likelihood: 2 (low-effort question)
- Repost likelihood: 1 (nothing to gain by sharing)
- Profile-click likelihood: 1 (no hook to the author)
- Dwell likelihood: 1 (resolves in two seconds)
- Proof / specificity: 1 (no detail)
- Negative-signal risk inverse: 4 (low risk, just bland)
- Out-of-network potential: 1 (no one outside the network has a reason to engage)
- Follow conversion: 1 (no reason to follow)

Reach score ≈ 1.4. Do not post.

### Strong post

> "Spent an afternoon turning a one-paragraph idea into a working tarot-card prototype with Claude.
>
> Three prompt mistakes that cost me two hours:
> 1. I described the UI in words instead of pasting a Figma frame.
> 2. I asked for "polished" without naming a reference style.
> 3. I let it pick the state-management pattern instead of pinning Zustand up front.
>
> Screenshot of the v1 vs v3 below."

- Niche clarity: 5 (clearly about building polished prototypes with AI agents, prompts, UI decisions)
- Audience fit: 5 (indie builders, prompt-curious devs)
- Reply likelihood: 4 (people will share their own mistakes)
- Repost likelihood: 4 (compact, quotable list)
- Profile-click likelihood: 4 (reader wants to see the v3)
- Dwell likelihood: 5 (numbered list + screenshot)
- Proof / specificity: 5 (named tools, real prompts, real screenshots)
- Negative-signal risk inverse: 5 (clean, on-topic)
- Out-of-network potential: 5 (works without context)
- Follow conversion: 5 (post is an exact sample of the account promise)

Reach score ≈ 4.7. Post.
