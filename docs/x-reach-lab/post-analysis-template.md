# Post Analysis Template

Paste a draft X post into this template, fill in each field, and decide whether to post, revise, or kill the draft.

The fields are tied to predicted-action types referenced in `home-mixer/scorers/weighted_scorer.rs` and the retrieval/ranking design in `phoenix/`. Predictions in this template are your own judgment - the public code does not let you query the production model. Treat the form as a discipline, not a forecast.

---

## Original post

```
<paste the exact draft here, line breaks and all>
```

Character count: <fill in>

Media attached (image / video / none): <fill in>

Link attached (yes / no, and to where): <fill in>

---

## Intended audience

One sentence. Who exactly is this written for?

> e.g. "Indie devs who use AI agents to build prototypes and care about prompt quality."

If you cannot write this sentence cleanly, the post is probably too broad.

---

## Likely audience cluster

Based on the intended audience, what cluster of viewers on X is the retrieval stage most likely to match this post to? Be honest - this is the cluster whose history embeddings will overlap with your post, not your dream audience.

> e.g. "People who follow indie-hacker / prompt-engineering / AI-tooling accounts and have recently engaged with build-in-public content."

---

## Predicted strongest action

Of the predicted actions in `weighted_scorer.rs` (favorite, reply, repost, quote, click, profile_click, photo_expand, video_quality_view, share, dwell, follow_author), which one is this draft most likely to earn?

> Strongest action: <e.g. profile_click>
>
> Why: <one or two sentences>

This is a self-prediction, not an algorithm output.

---

## Predicted weakest action

Which positive action will this draft *fail* to earn? Be specific.

> Weakest action: <e.g. reply>
>
> Why: <one or two sentences>

If the weakest action is something the post needs in order to travel (e.g. a list post that gets no replies and so generates no thread activity), consider revising.

---

## Rubric scores (1-5)

Use `content-rubric.md` for the anchors.

| Dimension                     | Score (1-5) | Note |
|-------------------------------|-------------|------|
| Niche clarity                 |             |      |
| Audience fit                  |             |      |
| Reply likelihood              |             |      |
| Repost likelihood             |             |      |
| Profile-click likelihood      |             |      |
| Dwell likelihood              |             |      |
| Proof / specificity           |             |      |
| Negative-signal risk inverse  |             |      |
| Out-of-network potential      |             |      |
| Follow conversion             |             |      |

Compute the reach score using the formula in `content-rubric.md`.

> Reach score: <0.0 - 5.0>

---

## Main risk

The single biggest reason this post could underperform or trigger a negative signal (Not Interested, Mute, Block, Report).

> e.g. "Reads as self-promotion because the screenshot dominates and the lesson is buried at the end."

---

## Improved version

Rewrite the original to address the main risk and raise the two lowest rubric scores. Keep it the same length or shorter.

```
<rewrite here>
```

What changed and why: <two or three sentences>

---

## Shorter version

Same idea, but under 280 characters and no thread. Test whether the post survives compression.

```
<short version here>
```

If the shorter version loses its proof or specificity, that is a signal the original was carrying weight that does not fit in a single post. Consider a thread instead.

---

## More reply-driven version

Rewrite so the post explicitly invites a reply from the target audience without sounding desperate. A real question, a contestable claim, or a "here is my way - what is yours?" structure.

```
<reply-driven version here>
```

What you are trading away: <one sentence - e.g. "Loses some of the resource feel, but earns more thread activity.">

---

## More profile-click-driven version

Rewrite so the post earns profile clicks. The post should hint at a body of work, a track record, or "wait, what else has this person made?".

```
<profile-click version here>
```

What you are trading away: <one sentence>

---

## Final recommendation

Pick one:

- [ ] **Post** - the original or the improved version is strong enough.
- [ ] **Revise** - one or more rubric scores below 3; use the improved/shorter/reply-driven variants as the basis for another pass.
- [ ] **Do not post** - reach score below 3.0, or the main risk is large enough that publishing damages the account positioning. Save the draft for parts; do not just hit send.

Which version are you actually publishing? (original / improved / shorter / reply-driven / profile-click-driven)

> <fill in>

Follow-up plan: if this post gets traction, what is the next post that extends the same angle? Write one line.

> <fill in>
