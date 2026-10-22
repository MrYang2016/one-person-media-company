---
name: one-person-media-company
description: >-
  Use when the user gives a content niche or topic and wants a Hermes-style
  one-person media loop on Grok Bot: shared vault, scout → research → angle →
  draft (+ image prompt) → human approval, no auto-publish; or to resume an
  existing topic vault under /home/box/agent-data/media-company-*/.
---
# one-person-media-company

Based on J.B. (@VibeMarketer_), [How to Build a One-Person Media Company With Hermes Bots](https://x.com/VibeMarketer_/status/2093330541177352217). Keep that URL in every topic vault’s `SOURCE.md`.

**Core idea:** writing is not the bottleneck. The loop is:

`idea → research → angle → flagship/draft → distribution → review → performance → updated playbooks`

Six isolated chatbots without shared context and inspectable handoffs are **not** a media company. On Grok Bot: shared markdown vault + role teammates (or one agent playing roles in sequence) + **human gates**. First useful version returns a researched opportunity, one angle brief, one approval-ready X post, **and one image-generation prompt** for the accompanying visual — then grow.

Prefer Chinese for user-facing guidance unless the user writes in another language.

## Folder root (required)

All topics live under:

`/home/box/agent-data/media-company-<topic-slug>/`

- `<topic-slug>`: kebab-case ASCII (e.g. `mx-humor`, `ai-agents-ops`).
- One topic → one vault. Never mix two topics in one vault.
- Before creating: `ls /home/box/agent-data/media-company-*/` (or list dirs). If a matching vault exists, **resume** unless the user wants a new slug.

### Vault layout (create on first run)

```
/home/box/agent-data/media-company-<topic-slug>/
  SOURCE.md
  index.md
  brand/
    voice.md
    audience.md
  discovery/
    round-N.md          # scout outputs
  engine/
    (optional later: angles, hooks, performance)
  platforms/
    x.md                # first platform only until proven
  campaigns/
    <signal-id>.md      # evidence packs, angles, drafts
  PROGRESS.md
```

`SOURCE.md` must include the VibeMarketer article URL and one-line topic.

`PROGRESS.md` minimum:

```markdown
# Progress
- topic-slug:
- phase: bootstrap | scout | research | angle_gate | draft | review | idle
- status: in_progress | blocked | done
- last_updated: YYYY-MM-DD (Asia/Shanghai)
- next_action: one concrete sentence
- teammates: (ids/names if created)
- human_gates_pending: []
```

`index.md` is the entry every role reads first: what we publish, who for, what every campaign must contain, routing, what humans must approve.

## On every invoke

1. **Parse intent**
   - New topic: user names a niche/topic (e.g. 「墨西哥幽默」).
   - Resume: names slug, vault path, or 「继续 xxx」.
   - If ambiguous and multiple vaults exist, list slug + `index.md` first line and ask which to open.
2. **New topic path**
   - Derive `<topic-slug>`; create vault layout; fill minimal `brand/`, `platforms/x.md`, `index.md`, `SOURCE.md`, `PROGRESS.md` at `bootstrap`.
   - Tell the user the vault path.
3. **Resume path**
   - Read `PROGRESS.md` + current phase artifacts; continue; do not restart unless asked.
4. **Pace**
   - Advance **one phase per user-visible beat** when a human gate is required.
   - After the user accepts a gate’s output, update files + `PROGRESS.md`, then offer the next phase.
5. **Never auto-publish** any external post. Drafts only until the user explicitly asks to send/post, and then still follow send-on-behalf / platform rules.

## Phases (run in order; MVP can stop after draft)

### 0. Bootstrap · shared brain

Fill minimum:

- `brand/voice.md` — curious, concrete; ban stereotypes / punching down; mark unknowns as 待核.
- `brand/audience.md` — who reads and what “done” looks like for them.
- `platforms/x.md` — one post = one entryway (mechanism / example / why it expires); no same-link spam; note that drafts ship with an image prompt.
- `index.md` — publish scope, audience, campaign must-haves, routing, human approval list.

**Campaign must-haves** (copy into index):

- one reader outcome
- one central claim / thesis
- source-backed consequential claims
- explicit 待核 / does_not_prove
- platform-native distribution (v1: X only)
- **one image-generation prompt** with every approval-ready post draft

**Human must approve:** angle; public drafts (copy + image prompt); brand/taboo changes; promoting a performance lesson into a permanent playbook rule.

Optional: create dedicated teammates with `CreateAgent` (e.g. Scout + Researcher) pointed at this vault. Contracts below. If the user wants a single-agent run, play roles **in sequence** with the same file handoffs — do not skip fields.

### 1. Signal scout → `discovery/round-N.md`

**Owns:** whether a signal deserves the team’s time now.  
**Must not:** pick final thesis; draft posts.

For each candidate return:

- what happened
- why this audience may care
- original source URL
- strongest proof clip / authority object (if any)
- question the finished piece could answer
- how fast the opportunity decays
- short reject reason when weak

**Discard far more than you pass.** Present pass/reject list to the user; only user-picked signals go to research.

### 2. Researcher → `campaigns/<id>.md` evidence pack

**Owns:** bounded facts, not the headline.  
**Must not:** choose a sensational angle then hunt for proof.

Required fields (missing → return to scout / refuse to invent):

- `urgency_source`
- `claims` (3–7) each with URL when consequential
- `quotes_or_clips`
- `contradictions`
- `does_not_prove`
- `mechanisms` (2–3 worth explaining)

Summarize the pack for the user; mark 待核 explicitly.

### 3. Angle gate (strategist) · human required

Turn the pack into **one** recommended angle (+ optional alternates). Output is an **angle brief**, not a draft:

- reader / reader outcome
- central tension
- thesis
- format (v1 usually: one X long post)
- reusable object (mechanism the reader can retell)
- proof required / risks
- optional: visual metaphor hint (1 line) to seed the later image prompt

Write candidates under `campaigns/<id>-angles.md`. **Stop and ask the user to pick** (widget preferred). Do not draft until they pick (or say skip).

### 4. Draft (writer / distribution light) → approval-ready post + image prompt

v1 default deliverable (both required in the same draft file):

1. **X draft** that teaches the mechanism with one concrete example and one “why this expires / 待核” line.
2. **Image-generation prompt** so the user can generate a companion image before posting (attractiveness without changing the claim).

Store in `campaigns/<id>-draft-*.md` with sections at least:

```markdown
## 草稿正文（可复制发帖）
...

## 配图提示词（可直接丢给文生图）
- aspect: 建议 16:9 或 1:1（按平台习惯写明）
- prompt: <英文为主、可带少量必要专有名词；一条完整提示，可复制>
- negative / avoid: <可选；刻板印象、血腥、真实名人脸、可读假 logo 等>
- alt_text_zh: <发帖用中文替代文字，一句说清画面>
```

**Image prompt rules:**

- Illustrate the **mechanism / metaphor** of the approved angle (not a screenshot of the tweet, not a wall of text).
- Prefer one clear scene or diagram-like illustration; high contrast; readable at small size on a phone feed.
- No real celebrity likeness; no sacred-symbol mockery; no punching-down stereotypes (esp. culture/humor topics).
- Do **not** auto-call image generation unless the user asks to generate now; always deliver the prompt text so they can run it themselves or ask you later.
- When presenting the draft to the user, show the image prompt in the same message (or immediately after the copy), not buried only in the file.

Later expansions (only when user asks):

- flagship long-form
- multi-platform package (each asset a **different entryway**, not a shortened copy; each public asset gets its own image prompt if it will be posted with media)

### 5. Editor / human review

Show final copy, **image prompt**, key sources, platform, and decision needed. User choices: approve for manual post / revise copy / revise image prompt / park / generate image now. Update draft status. **Do not publish** unless they explicitly order a send and send rules allow it. Generating an image on request is fine; attaching it to a live post still needs their publish decision.

### 6. Performance (optional, weekly)

After real posts exist, record observations (including whether image-with-post helped) and propose keep / test / stop lists. Human approves before writing permanent rules into `brand/` or `platforms/`.

## Role contracts (for teammates or sequential play)

Use this shape in each teammate description / SOUL-equivalent:

- **owns** — decision
- **reads** — vault paths allowed
- **returns** — exact artifact + path
- **must not** — other roles’ decisions; publishing; inventing missing sources
- **done when** — observable handoff complete

Suggested MVP roster (match the article’s build order):

1. Signal Scout
2. Researcher
3. Editor-as-gate (can be the parent agent)

Add Strategist / Writer / Distribution only after evidence packs and angle briefs are consistently useful. Writer (or parent in draft phase) **owns** the image prompt as part of the draft handoff.

## Handoff record

Every active campaign keeps one traveling record (fields may live in one md or linked files):

`status` · `signal` · `research` · `angle` · `flagship` · `distribution` · `image_prompt` · `review` · `performance`

If a required field is missing, **return to the previous stage** — never quietly fill gaps with plausible guesses. A draft without `image_prompt` is incomplete for v1 review.

## Build order (tell the user when starting cold)

1. Vault + voice/audience/x playbook
2. Scout + Researcher (+ human as editor)
3. Three real signals through scout → research → review
4. Then strategist / writer / distribution as needed
5. Weekly performance updates only after something actually shipped

## Do not

- Auto-publish or “just send” without an explicit user ask that satisfies send rules
- Skip the image prompt on an approval-ready post draft
- Auto-generate images unless the user asks; always leave a usable prompt
- Treat formatting as distribution (shortened long-form ≠ platform-native)
- Promote one viral result into a permanent rule without human approval
- Stereotype cultures / punch down; for humor topics, prefer mechanism + self-mockery framing over outsider exoticism
- Pollute the shared brain with every draft — campaigns stay in `campaigns/`; vault root holds accepted rules

## Example invoke

User: `用 one-person-media-company 跑「墨西哥幽默」`

Agent: create or resume `media-company-mx-humor` → bootstrap if needed → scout round → user picks signals → research packs → angle widget → draft X **+ 配图提示词** → user approve for manual post (optional: generate image on request).
