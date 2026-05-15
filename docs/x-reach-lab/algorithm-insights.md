# Algorithm Insights

Plain-English notes on what this repository implies about getting more reach on X.

The notes below are grounded in the public files in this repo (especially `README.md`, `phoenix/README.md`, `phoenix/run_pipeline.py`, `home-mixer/scorers/weighted_scorer.rs`, `home-mixer/candidate_pipeline/for_you_candidate_pipeline.rs`, and `candidate-pipeline/candidate_pipeline.rs`). Where something is not stated in the code, it is labeled as an inference.

This is not a description of X's full production system. The repo itself notes that the released model is a mini, frozen checkpoint and that production runs a larger, continuously trained version. Treat everything here as a practical reading of the public code, not a leak of the live ranking.

---

## 1. Retrieval vs ranking

The For You feed is built in two stages:

1. **Retrieval**: cut a huge corpus down to a small candidate set.
2. **Ranking**: score that small set and order it.

From `README.md` and `phoenix/README.md`:
- Retrieval uses a **two-tower model**. A "user tower" turns the viewer's history into an embedding. A "candidate tower" turns each post into an embedding. The top matches are picked by dot-product similarity (`phoenix/run_pipeline.py` lines around `scores = corpus_repr @ np.asarray(user_repr[0])`).
- Ranking uses a Grok-based transformer that takes the user history plus the candidates and predicts probabilities for many engagement actions.

Practical reading:
- If retrieval never picks your post, ranking never sees it. Out-of-network reach starts with being **embeddable** as something similar to what target viewers already engage with.
- Being in-network (followed by the viewer) is a different path: Thunder pulls in recent posts from followed accounts directly, without needing similarity search.

---

## 2. In-network vs out-of-network distribution

Two distinct candidate sources feed the same ranker:

- **Thunder** (in-network): recent posts from accounts the viewer follows. `README.md` describes it as an in-memory store of recent posts trimmed by a retention period.
- **Phoenix Retrieval** (out-of-network): ML-based similarity search across a global corpus.

Practical reading:
- Followers give you a guaranteed seat in Thunder's candidate pool for their feeds. That is necessary but not sufficient: your post still has to win the ranking against everything else.
- Out-of-network reach is gated by retrieval similarity to viewers you do not follow. That is why a post that resonates with a clear cluster of users tends to travel further than a post that is "for everyone".
- There is a `home-mixer/scorers/oon_scorer.rs` mentioned in the README under "Scoring" ("OON Scorer: Adjust scores for out-of-network content"). The repo does not publish the exact adjustment, so any specific multiplier claim is an inference. What is clear is that out-of-network candidates are scored on a separate path before being blended in.

---

## 3. Positive predicted actions

The ranker outputs probabilities for a long list of actions. From `README.md` and `home-mixer/scorers/weighted_scorer.rs`, the positively-weighted ones include:

- favorite (like)
- reply
- repost
- quote
- click
- profile click
- video quality view (only when video is long enough; see `vqv_weight_eligibility` in `weighted_scorer.rs`)
- photo expand
- share, share via DM, share via copy link
- dwell, continuous dwell time
- follow author
- quoted click

`phoenix/run_pipeline.py` uses an illustrative weighted score:

```
weighted = 1.0 * P(favorite)
         + 0.5 * P(reply)
         + 0.3 * P(repost)
         + 0.2 * P(dwell)
```

The production weights are not published, and `weighted_scorer.rs` references constants in a `params` module that is not exposed in the repo. So the only honest statement is: **multiple positive actions are summed with weights, and replies and follows are not small line items**.

Practical reading:
- The model is not optimizing for likes alone. A post that gets people to stop, read, reply, click your profile, or follow you contributes through several different scores at once.
- "Engagement-bait that earns only a fast like" probably scores worse than "a slower post that earns dwell + reply + profile click".

---

## 4. Negative predicted actions

`weighted_scorer.rs` also folds in negatively weighted actions:

- not interested
- block author
- mute author
- report

These are subtracted from the score (see `offset_score` and the negative-weight branch in `weighted_scorer.rs`). The README's "Scoring and Ranking" section explicitly states that "negative actions ... have negative weights, pushing down content the user would likely dislike."

Practical reading:
- Posts that look like spam, ragebait, off-topic noise, or "follow me!" hooks are the kind of content that triggers Not Interested, Mute, and Block. The ranker is explicitly trained to suppress predicted versions of those.
- Pre-scoring filters in `home-mixer/filters/` (e.g. `muted_keyword_filter.rs`, `author_socialgraph_filter.rs`) and the post-selection `vf_filter.rs` ("deleted/spam/violence/gore etc.") will also drop content before ranking even runs.

---

## 5. Relevance to user history

The ranker is conditioned on the viewer's **history**: their recent posts, the authors of those posts, and the actions they took (see `RecsysBatch` fields in `phoenix/run_pipeline.py` and the attention diagram in `phoenix/README.md`). User and history attend bidirectionally; candidates attend to user and history but **not to each other**.

Practical reading:
- The model is asking, in effect, "given what this viewer recently liked, replied to, and dwelled on, how likely are they to do each action on this candidate?"
- A post is judged in the context of its viewer's recent behavior, not in isolation.
- An inference: the cleaner and more consistent your posting topic, the easier it is for the model to learn which viewers have a history pattern that predicts engagement with your posts. Topic-scattered accounts are harder for any embedding-based system to place.

---

## 6. Freshness and candidate filtering

The pipeline drops candidates well before scoring. From `home-mixer/filters/`:

- `AgeFilter` removes posts older than a threshold.
- `DropDuplicatesFilter`, `RepostDeduplicationFilter`, `DedupConversationFilter` collapse duplicates.
- `PreviouslySeenPostsFilter`, `PreviouslyServedPostsFilter` remove posts the viewer has already been shown.
- `SelfpostFilter` removes the viewer's own posts.
- `MutedKeywordFilter`, `AuthorSocialgraphFilter` enforce viewer mutes and blocks.
- `IneligibleSubscriptionFilter` removes paywalled content the viewer cannot access.
- Thunder also "trims posts older than the retention period".

Practical reading:
- There is a hard recency cutoff before ranking even sees a candidate. Old posts are not just "down-ranked"; many are filtered out.
- Once a post has been served to a given viewer, it is filtered out of future requests for that viewer. The window for that specific viewer to act on it is short.
- An inference: bookmarks and follow-up replies that drive return visits matter, because once a post is "seen", that viewer is unlikely to be re-served it.

---

## 7. Why early engagement from the right people matters

The retrieval stage matches a candidate to viewers whose history "looks like" the candidate. The ranker conditions on viewer history. Neither component is fed by the post's recency alone; both are heavily shaped by the embeddings learned from real user actions.

Practical reading (inference, since the repo does not publish online update cadence for the embeddings):
- Early engagement from accounts with a clear, on-topic history sends a stronger signal than the same number of engagements from random or off-topic accounts.
- "The first ten replies decide the trajectory" is a folk claim, not in the repo. What the repo does support is: replies, reposts, dwell, and follows are all separate predicted actions the model uses, and a post that earns several of them quickly from on-topic viewers fits the kind of behavior the model is trained on.

---

## 8. Why niche clarity matters

The retrieval corpus example in `phoenix/README.md` is explicitly filtered to a single topic ("Sports", ~537K posts in a 6-hour window). The system also has topic-related candidate sources and hydrators ("followed topics", "Phoenix topics", "new_user_topic_ids_filter.rs", "topic_ids_filter.rs").

Practical reading:
- Topics are a real first-class object in this pipeline, not just a UI label.
- An account that consistently posts on one clear topic is easier for the retrieval embeddings to place near the right viewers. An account that mixes ten unrelated topics is harder to place near any one cluster.
- This is an inference about the practical effect, not a claim that the system explicitly penalizes mixed-topic accounts.

---

## 9. Why followers are useful but not enough

Followers feed Thunder. Thunder feeds the in-network candidate pool. Without followers, the only path to a viewer is out-of-network retrieval.

But Thunder candidates still pass through the same ranking step as out-of-network candidates. The README's pipeline diagram shows both sources merging into the same hydration, filtering, and scoring stages.

Practical reading:
- 10,000 followers does not equal 10,000 impressions. It means up to 10,000 chances to **enter the candidate pool**, after which the ranker decides whether your post outscores everything else for each viewer.
- A small follower list of viewers whose history strongly matches your content can outperform a larger, less-aligned list, because the ranker's predicted-action probabilities will be higher for the aligned viewers.

---

## 10. What this repo does NOT prove

To stay honest, here is what the repo does **not** establish:

- It does **not** publish the production weights for the weighted scorer. `weighted_scorer.rs` references a `params` module with constants like `FAVORITE_WEIGHT`, `REPLY_WEIGHT`, etc., but those values are not in the open code.
- It does **not** publish the live retrieval corpus or live embeddings. The shipped example uses a 6-hour sports snapshot and a mini frozen checkpoint.
- It does **not** describe time-decay curves, exact recency thresholds, or how often embeddings are refreshed in production.
- It does **not** prove any specific multiplier for verified, premium, follower count, link presence, media presence, or external-link penalties. Some of those signals appear as hydrators or features in the pipeline, but the published code does not quantify their effect.
- It does **not** describe any "shadowban" mechanism. The visible suppression paths in the public code are the named filters, the negative-action terms in the weighted scorer, and the post-selection visibility filter (`vf_filter.rs`).
- It does **not** describe ads ranking in detail beyond noting an `ads/` blending module and an `AdsSource`.

Anything beyond the above is inference, folklore, or marketing. This document tries to keep those three separated.
