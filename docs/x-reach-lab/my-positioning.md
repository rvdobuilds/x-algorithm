# My Positioning

A narrow positioning for the account. The goal of this document is to keep posts on-niche so the retrieval and ranking stages described in this repo (`phoenix/`) have a consistent cluster of viewers to match against.

This is a constraint document, not a wish list. If a draft does not fit this positioning, the default action is "do not post" or "post from a different account".

---

## Account promise

> I build polished product prototypes with AI agents and share the prompts, UI decisions, and lessons behind them.

That sentence is the entire promise. Every pinned post, bio, and content pillar below has to ladder back to it. If a post does not, it does not belong on this account.

The promise has three load-bearing words:

- **Polished** - the work is not "look, it compiles". It is presentable to a real user.
- **Prototypes** - finished enough to demo, not finished enough to sell. The scope is set.
- **AI agents** - the building method is the story, not just the artifact.

And it commits to sharing three specific things:

- **Prompts** - the actual text used.
- **UI decisions** - the visual and interaction choices and why.
- **Lessons** - what went wrong and what changed.

---

## Who it is for

Three concentric circles, sharpest first:

1. **Core**: indie builders and small-team founders who are already using AI agents (Claude, Cursor, Codex-style tools) to build product, and who care about polish and prompt quality more than novelty.
2. **Adjacent**: designers and PMs who do not code daily but want to prototype with AI agents and want a credible practitioner to learn from.
3. **Outer**: AI-curious developers considering switching to an agent-first workflow.

If a post does not work for circle 1, it does not belong on the account, even if it would work for circle 3.

---

## Who it is NOT for

State this explicitly so drafts can be killed early.

- Generic AI commentary readers ("look at this new model").
- General SaaS / startup audiences.
- Indie hackers who only want revenue numbers.
- Game dev audiences (even when a prototype happens to be a game).
- X growth / Twitter-growth audiences.
- General productivity / "how I work" audiences.

A post that would appeal to one of those audiences and not to the core audience above is off-niche by definition.

---

## The 3 content pillars (and only 3)

Every post must fit cleanly into one of these. If it fits two, that is fine. If it fits none, it does not get posted.

### Pillar 1: Build logs from prototypes

Concrete posts about a specific prototype currently being built or recently shipped. The artifact is the anchor.

Sub-formats:
- "Day N of building <prototype>: <one specific thing>"
- Before/after screenshots of the same screen.
- One-screen demo clips with one sentence of context.
- "v1 looked like this, v3 looks like this, here is what changed in the prompts."

### Pillar 2: Prompts and prompt patterns

The actual text and structure of prompts used to build with AI agents. The closer to copy-pasteable, the better.

Sub-formats:
- One full prompt that worked, with two lines of context.
- A prompt diff: "I changed this one line and the agent stopped doing X."
- A reusable pattern (e.g. "How I describe a UI to an agent in one paragraph").

### Pillar 3: UI and product-decision lessons

Decisions about how the prototype looks and feels, and what was learned from them. Not generic design tips.

Sub-formats:
- "I almost shipped this. Here is why I changed it."
- A small detail (a hover state, an empty state, an error) and why it earned its space.
- Reference choices: "I copied the rhythm of <named reference> for this screen because <reason>."

That is the full pillar list. There are no "thoughts on AI" or "founder mode" pillars. Resist adding them.

---

## Topics to avoid

These are off-niche even though they are tempting:

- Model release commentary.
- Generic AI ethics / AGI takes.
- Productivity hacks.
- Twitter growth or "how to write a viral tweet".
- "Day in the life" without a build artifact attached.
- Replies to unrelated viral threads.
- Politics, current events.
- Quote-tweet dunks.
- Crypto / web3.

If a draft is on one of these topics, the answer is not "post it from a smaller account". The answer is do not post it.

---

## How game/app experiments should be framed

When a prototype happens to be a game (or a niche app outside SaaS), do not pivot the account to a game-dev audience. Frame the experiment so it still ladders to the core promise.

Rules:

1. **The story is the build method, not the game.** Lead with the AI-agent angle, not "I made a game".
2. **Show prompts and UI decisions, not gameplay.** The screenshot is of a screen or a prompt, not of a level.
3. **Name the lesson, not the genre.** "Here is how I got an agent to design a balanced card layout" beats "I shipped a card game".
4. **Use the same three pillars.** A game prototype is still a build log (Pillar 1), still has prompts (Pillar 2), and still has UI decisions (Pillar 3). If a game post does not fit one of those, it does not belong on the account.
5. **No genre tags in posts.** Avoid "#gamedev". The account is not a game-dev account.

If the prototype starts to demand its own audience (active community, regular game-only updates), spin it off to a dedicated account. Do not stretch this account's positioning to cover it.

---

## Bio variants

Three options to A/B over time. All three lead with the same promise.

**Bio A (direct)**
> I build polished prototypes with AI agents. I share the prompts, UI decisions, and lessons behind them.

**Bio B (specific)**
> Building polished prototypes with AI agents. Real prompts. Real UI decisions. Real lessons - including the ones that cost me hours.

**Bio C (proof-first)**
> Prototypes built with AI agents, polished enough to demo. I post the prompts, the UI decisions, and what went wrong.

Pin the latest prototype as a link or pinned post; do not put a generic call-to-action in the bio.

---

## Pinned post variants

Three options. Rotate every one to two months, or whenever you ship a new prototype that is a better sample of the account promise.

**Pinned A (sample of work)**

> Last weekend I built <prototype> with Claude. Three things I learned that changed how I prompt for UI:
>
> 1. <lesson 1>
> 2. <lesson 2>
> 3. <lesson 3>
>
> Screenshots and the actual prompts below.

**Pinned B (account promise + index)**

> I build polished prototypes with AI agents and share the prompts, UI decisions, and lessons.
>
> Recent builds:
> - <prototype 1>: <one line>
> - <prototype 2>: <one line>
> - <prototype 3>: <one line>
>
> Posts most worth reading: <link to one or two threads>.

**Pinned C (before/after thread)**

> A v1 vs v3 of the same screen. Same prototype, same agent, three prompt changes in between.
>
> [Image 1: v1] [Image 2: v3]
>
> Thread on what changed and why.

Each pinned variant must be a clean sample of the kind of content circle 1 is signing up for.

---

## Example post formats

Concrete templates the account can reuse. Each fits one or more of the three pillars.

### Format 1: The prompt diff

> Old prompt:
> "<old prompt>"
>
> New prompt:
> "<new prompt>"
>
> The agent stopped doing <X> and started doing <Y>. The change was <one sentence>.

Fits Pillar 2. Strong on proof/specificity.

### Format 2: The v1 → vN screen

> v1 of <screen name>: [screenshot]
> v3 of <screen name>: [screenshot]
>
> Three things I changed:
> 1. <change 1>
> 2. <change 2>
> 3. <change 3>
>
> The biggest win came from <one of them>, because <one sentence>.

Fits Pillars 1 and 3. Strong on dwell.

### Format 3: The "almost shipped" detail

> I almost shipped this <feature> as <approach A>.
>
> Then I tried <approach B> and the screen finally felt right.
>
> The difference: <one sentence and a screenshot>.

Fits Pillar 3. Strong on specificity and profile-click likelihood.

### Format 4: The expensive lesson

> Two hours I lost yesterday, in one sentence: <the lesson>.
>
> Context: I was prompting <agent> to <task>. The thing I missed: <specific gotcha>.
>
> What I do now: <the new rule>.

Fits Pillars 2 and 3. Strong on reply likelihood (other builders share their own version).

### Format 5: The short build log

> Day <N> of <prototype>: <one specific thing that happened today>.
>
> [Optional screenshot or one-screen clip]
>
> Tomorrow: <one specific next step>.

Fits Pillar 1. Used as connective tissue between bigger posts; do not let it become the only thing the account posts.

---

## Final rule

If a draft does not fit one of the three pillars, do not stretch the pillar to fit the draft. Save the draft for another time, or use it as raw material for a post that does fit. The positioning is the asset; protect it.
