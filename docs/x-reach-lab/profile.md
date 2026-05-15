# Profile

Concrete profile recommendations for `@RoyInProgress`, grounded in what is observable on the profile today and in what the public algorithm code (`phoenix/`, `home-mixer/`) implies about reach.

This file is a sibling to `my-positioning.md` (the rules) and `algorithm-insights.md` (the why). Read those first if anything below feels arbitrary.

---

## Current state (observed)

| Field            | Value                                                                                              |
|------------------|----------------------------------------------------------------------------------------------------|
| Handle           | `@RoyInProgress`                                                                                   |
| Display name     | `Roy` (verified)                                                                                   |
| Bio              | "IT architect learning to build instead of only design. Building with AI, taste, and small daily reps 🔨🔧" |
| Website          | `rvdobuilds.com`                                                                                   |
| Joined           | February 2024                                                                                      |
| Following        | 205                                                                                                |
| Followers        | 393 (approaching 400)                                                                              |
| Posts            | ~1.7K                                                                                              |
| Banner           | Solo figure on a moonlit mountain peak — generic / aspirational                                    |
| Pinned (24/04/2026) | A long manifesto: "AI-assisted coding turned me from someone who designs systems into someone who also builds them." 10-years-of-business-IT origin story, then a list of "Daily habits → app", "Home workouts → app", "Finance tracking → app", then "serious hobby got slightly out of hand" closer. |

---

## What works

1. **Handle is on-brand.** `@RoyInProgress` advertises the account promise (a person mid-transition) in three syllables. Do not change it.
2. **Bio names a clear "before / after"** ("learning to build instead of only design"). That is a specific cluster the retrieval embeddings can place — IT architects / PMs / designers who want to ship.
3. **Verified + a real website + 1.7K posts** signals a serious account, not a fresh growth-hack project. That reduces the negative-signal risk (`block_author`, `not_interested`) that brand-new accounts trigger.
4. **Pinned post is a clean account-promise statement.** A stranger who reads it knows exactly what they are subscribing to.

---

## What is leaking reach

### 1. Bio: "taste" is a soft word

> "Building with AI, taste, and small daily reps 🔨🔧"

"Taste" is the weakest of the three nouns. It is unfalsifiable, it does not name a topic the embeddings can place, and a stranger cannot picture the artifact. The bio currently scores well on niche clarity but poorly on **proof / specificity** — there is no hint of what gets shipped.

### 2. Banner is inspirational, not evidential

The mountain-peak banner is the kind of image any "building in public" account in 2026 could use. It does not show one screenshot of a prototype, one prompt, or one before/after. A new viewer who lands on the profile from an out-of-network impression sees: bio → banner → pinned. Two of those three should advertise the work; right now only one does.

This is the single highest-leverage change on the profile.

### 3. Pinned post leads with autobiography, not artifact

The current pinned opens with "I've spent 10 years working between business and IT…" and the artifacts (Daily habits app, Home workouts app, Finance tracking app) appear ~6 paragraphs in. Per the rubric in `content-rubric.md`, this is **profile-click-strong but follow-conversion-weak**: it explains *who* but not *what*.

`algorithm-insights.md` §3 makes the point that `P(follow_author)` and `P(profile_click)` are separate weighted predictions. The profile is the page that has to convert clicks into follows. An artifact-led pinned post does that better than a manifesto.

### 4. Website link goes to a domain, not a recent build

`rvdobuilds.com` is the right domain, but only one slot can be used. If the homepage shows a long landing page rather than the most recent prototype, you are spending the highest-CTR link in the bio on something that does not close the loop the profile opened.

---

## Recommended changes (in order of leverage)

### A. Replace the banner with proof (highest leverage)

Replace the mountain image with one of:

- **Option 1 (collage of three):** Tight crops of the three apps named in the pinned (Daily habits, Home workouts, Finance tracking). One sentence on each. No logos, no text overlays — let the UI speak.
- **Option 2 (v1 vs v3):** A side-by-side of one screen, before and after a meaningful prompt change. This is the Pillar 3 format from `my-positioning.md` Format 2, used as a banner.
- **Option 3 (single hero screenshot):** The single best-looking screen from the strongest of the three apps. Lowest friction, highest signal per pixel.

Pick **Option 3** if you are about to ship a prototype that is the new pinned. Pick **Option 1** otherwise.

### B. Swap the bio to a more specific variant

The bio in `my-positioning.md` is too startup-y for the practiced account. Adapt **Bio B** from that doc with the actual practiced voice:

> "IT architect learning to build, not just design. Shipping small apps with AI: daily habits, home workouts, finance tracking. I post the prompts, the UI calls, and what broke."

Why this version:
- Keeps the "before / after" identity hook ("learning to build, not just design").
- Replaces "taste" with three named artifacts (daily habits / home workouts / finance tracking) — three concrete topical anchors the retrieval embeddings can use.
- Closes with "I post the prompts, the UI calls, and what broke" — promises the three deliverables, which sets follower expectations and reduces post-follow disappointment (the upstream cause of mute / not-interested signals).

### C. Rewrite the pinned post as a sample of work

Use **Pinned A** from `my-positioning.md` (sample of work), populated with the actual builds:

> "Three apps I shipped on the side while keeping an architect job and two young kids:
>
> 1. Daily habits → app
> 2. Home workouts → app
> 3. Finance tracking → app
>
> Each one started as a problem I had. Each one taught me something specific about prompting AI agents to build polished UI.
>
> Below: the v1 → v3 of the home workouts screen, and the one-line prompt change that fixed the spacing."

If a screenshot of v1 → v3 is not ready, ship the artifact first and then pin. Do not pin a thread that ends on a promise of a screenshot you have not yet posted.

The current pinned can be re-posted as a normal post (not pinned) — it works as a Pillar-3 personal-context post, just not as the profile's front door.

### D. Use the link slot for the freshest prototype

If `rvdobuilds.com` is a multi-prototype hub, that is fine — keep it. If it is a generic landing page, point the link directly at whichever app has the strongest UI right now. The bio link is the second-highest-CTR profile element after the avatar; it should close the loop the pinned opens.

### E. Keep the avatar

The headshot is high-contrast, readable at 32×32, and consistent across the profile and the post-author thumbnails. Do not change it unless you also re-shoot the banner; mismatched avatars and banners make profiles look unmaintained.

---

## Follower count: the 400 / 1K / 2K thresholds

You are about to cross 400. Three notes on what to do at each early threshold:

- **At 400-500:** Switch the pinned to a clean sample of work. The accounts that follow you in this range are doing so on the strength of the pinned; the manifesto can move to a normal post once the artifact-led pinned is up.
- **At 1K:** Add one external proof point to the bio (a podcast appearance, a featured build, an Indie Hackers post — whatever the strongest third-party signal is by then). Replace one of the named app categories with "+ now <new build>" so the bio reads as alive.
- **At 2K:** Drop the "learning to build" framing. By 2K you are no longer learning, you are practicing. Phrase it accordingly.

---

## Follow / following ratio

| Metric        | Current | Note                                                                  |
|---------------|---------|-----------------------------------------------------------------------|
| Followers     | 393     |                                                                       |
| Following     | 205     |                                                                       |
| Ratio         | 1.92    | Above 1.0 is healthy for a personal account at this stage.            |

`algorithm-insights.md` §9 makes the point that followers feed Thunder, but a small list of well-matched followers outperforms a large unmatched one. The 205 you follow should be:

- Mostly accounts in the three concentric circles from `my-positioning.md`.
- Audited quarterly. Unfollow accounts that have stopped posting in your niche; they pollute the in-network retrieval signal for you when you reply to them.

Do not chase the ratio. It is a downstream number, not a lever.

---

## Profile-level negative-signal checklist

Before publishing any new bio, banner, or pinned, run the profile through `vf_filter.rs`-style hygiene:

- [ ] No promises the account does not keep (no "I help X grow Y" if the account does not actually do that).
- [ ] No emoji-only sentences in the bio. Emojis fine as accents, not as load-bearing nouns.
- [ ] No CTAs that sound like the bio is selling something the account is not selling (e.g. "DMs open" when DMs are not how you actually operate).
- [ ] No flag emojis or political signals unless they are core to the account.
- [ ] Banner is your work or your environment, not a stock image.
- [ ] Pinned ends on something a stranger can act on (read the thread, click the link, follow for the next post).

---

## Re-review cadence

- **Weekly:** Glance at the profile from a logged-out browser. Anything stale?
- **Monthly:** Rotate the pinned if a stronger sample of work has been published. Update the bio if a new build has reached a state worth naming.
- **Quarterly:** Re-audit the 205 follows. Drop accounts that have drifted off-niche.

The profile is not a one-time setup. It is the slowest-moving post on the account.
