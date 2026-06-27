# Etch — Complete Project Plan

## Executive Summary

**Product:** A premium journaling app for iOS. Entries save as plain `.md` files to user's iCloud Drive. No backend. No subscription. No lock-in.

**Brand:** Etch — "Carve your story. It won't wash away."

**Core angle:** Your memories should not die with your credit card expiration date.

**Target:** $30,000 revenue Year 1 (1,200 sales at $24.99 one-time IAP)

**Developer:** Solo product manager. AI coding agents write all Swift code. User reviews and ships.

**Machine:** MacBook (already have). $0 hardware cost.

**Timeline:** 8-12 weeks (AI agents code, user reviews)

**Investment:** ~$150 (Apple Developer account + app icon)

---

## 1. The Opportunity

### Validated Demand

| Metric | Data |
|--------|------|
| Day One monthly revenue | ~$600k ($7.2M/year) |
| Day One 1-star reviews mentioning subscription | 45% |
| Journaling search trend | Growing 22% YoY |
| Est. paying journaling market | 1.5-2M users globally |
| Top non-sub competitor (Diarium) revenue | ~$20k/month |
| Gap | No beautiful, premium, local-first, one-time-purchase journaling app exists |

### Why People Will Switch

1. **Day One betrayal** — Original users paid one-time, were forced into subscription. They feel personally cheated.
2. **Emotional hostage** — "I stopped paying and can't access 5 years of my own life story"
3. **Privacy fear** — Automattic (WordPress) acquired Day One. Users don't trust where their private thoughts go.
4. **Feature bloat** — Users just want to write. Day One added prompts, audio, social. Too busy.
5. **Export friction** — Can't get data out in readable format. It's a JSON nightmare.

### The USP

> *"Your memories should not die with your credit card expiration date."*

Not "no subscription" (that's pricing). Not "markdown files" (that's tech). The USP is **permanence** — your journal outlives the company, the subscription, and you.

---

## 2. Product Spec

### MVP (Phase 1 — Weeks 1-4)

| Feature | Description |
|---------|-------------|
| Write entry | Beautiful, minimal text editor. Markdown support. |
| Read entries | Timeline view of past entries, grouped by date |
| Photo attach | Attach 1-4 photos per entry (stored alongside .md file) |
| iCloud sync | Entries save as .md files to user's iCloud Drive folder |
| Date stamp | Automatic date/time for each entry |
| Search | Full-text search across all entries |
| Dark/light mode | Beautiful in both |
| Onboarding | "Choose a folder in your iCloud" → start writing |

### Phase 2 — Hooks (Weeks 5-8)

| Feature | Description |
|---------|-------------|
| "On This Day" | Show entries from 1, 2, 5 years ago |
| PDF book export | One-click export a year of entries as a printable PDF |
| Mood tracking | Optional mood tag per entry (emoji-free, color-based) |
| Passcode lock | Face ID / passcode to open the app |
| End of Life page | In-app page: "If we abandon this, we open-source it" |
| Reminders | Optional daily writing reminder (off by default) |

### Never

| Feature | Why |
|---------|-----|
| Audio recording | Bloat. Day One complaint #4. |
| Daily prompts | Bloat. Users hate them. |
| Social sharing | Wrong philosophy. Journal = private. |
| AI coaching | Privacy risk. |
| Streaks/badges | Gamification ruins journaling. |
| Cloud account | There is no cloud. No account. No login. |

---

## 3. The Angle — Marketing Positioning

### Core Narrative

*"When I die, my kids can find my physical journal on a shelf. If I use a subscription app, my memories die with my credit card expiration date. I built Etch so your story outlives the company that made the app."*

### Four Pillars

1. **Permanence** — Plain .md files. Readable in any text editor in 50 years.
2. **Ownership** — Your files, your iCloud, your rules. No landlord.
3. **Simplicity** — No prompts, no streaks, no badges. Just you and your thoughts.
4. **Privacy** — No servers. No cloud. No analytics. We literally can't see your data.

### The One-Sentence Pitch

*"Your life story shouldn't have a monthly rent."*

### Target Personas

| Persona | Day One Pain | Why They Switch |
|---------|-------------|-----------------|
| The Burned Legacy User | Paid one-time in 2013, forced into sub | Feels betrayed. Switches on principle. |
| The New Parent | Baby photos/milestones in Day One | Terrified app dies, photos vanish |
| The Privacy Nerd | Uses ProtonMail, Signal | Wants journal as files on own drive |
| The Minimalist Writer | Hates Day One bloat | Just wants a blank page |

---

## 4. Financial Model

### Investment

| Item | Cost |
|------|------|
| Apple Developer Program | $99/year |
| App icon (Fiverr) | $30 |
| Domain (etch.app or etchjournal.com) | $15 |
| **Total** | **~$150** |

### Revenue Projection (Conservative)

| Month | Event | Sales | Revenue (net after Apple 15%) |
|-------|-------|-------|-------------------------------|
| M1-2 | Development | 0 | $0 |
| M3 (Launch) | HN + Reddit + TikTok spike | 150 | $3,186 |
| M4 | Post-launch tail | 60 | $1,274 |
| M5 | Blog coverage, word of mouth | 80 | $1,699 |
| M6 | Steady organic | 90 | $1,911 |
| M7 | Steady + minor update | 100 | $2,124 |
| M8 | Steady | 100 | $2,124 |
| M9 | Localization (JP/DE) boost | 130 | $2,761 |
| M10 | Steady | 100 | $2,124 |
| M11 | Holiday boost | 140 | $2,974 |
| M12 | Steady | 100 | $2,124 |
| **Total** | | **1,150** | **~$22,424** |

### Sensitivity

| Scenario | Sales | Revenue | Outcome |
|----------|-------|---------|---------|
| 🟢 Viral (TikTok + HN front page) | 5,000 | $106,200 | Replace Grab income |
| 🟢 Target | 1,200 | $25,488 | Goal hit |
| 🟡 OK | 600 | $12,744 | Keep iterating |
| 🔴 Poor | 200 | $4,248 | Pivot or kill |
| ⚫ Worst | 50 | $1,062 | Out $150, gained iOS skill |

### Break-Even

- Investment: ~$150
- Break-even: **7 sales** at $24.99 (after Apple's 15%)
- Likely: Launch day

---

## 5. Launch Strategy

### Pre-Launch (2 weeks before)

| When | What |
|------|------|
| W-2 | Post "Day One to Markdown migration script" on GitHub. Share r/journaling, r/selfhosted |
| W-2 | Post "Your memories shouldn't have a landlord" manifesto on GitHub README |
| W-1 | TestFlight beta to 50 testers from Reddit, HN, Mastodon |
| W-1 | Prepare App Store listing, screenshots, description |
| W-1 | Build landing page with "SaaS Ransom Calculator" (Day One $350/10yr vs Etch $25 once) |

### Launch Day (Saturday EST = Saturday evening SGT)

| Time EST | Action |
|----------|--------|
| 08:00 | Show HN: *"Show HN: I spent 30 years in data centers and built a journal that outlives the company that makes it"* |
| 08:05 | Product Hunt: *"Etch — The last journal you'll ever need"* |
| 08:30 | r/journaling, r/selfhosted, r/privacy |
| 09:00 | Mastodon (fosstodon.org) |
| 09:30 | TikTok: "Digital Ransom" video (Day One locked screen → Etch) |

### Viral Hooks

| Hook | Format |
|------|--------|
| **"Digital Ransom"** | Video: "I journaled in Day One for 5 years. Missed one payment. Look what happened to my memories." |
| **"Inheritance"** | Show Moleskine vs app: "When I die, my kids find this notebook. With a sub app, memories die with my credit card." |
| **"Comparison"** | Side-by-side: "Day One: $350 over 10 years + no data ownership. Etch: $25 once + you own the files." |

---

## 6. Risk Matrix

| Rank | Risk | Mitigation |
|------|------|-----------|
| 1 | iCloud sync data loss | Use `FileDocument`/`UIDocument`, never custom sync |
| 2 | Apple rejection | Read HIG, follow IAP rules exactly |
| 3 | No market demand at $24.99 | Launch at $19.99, if still nothing drop to $14.99 |
| 4 | Day One ships a "local files" feature | Our moat is one-time pricing + the End of Life guarantee |
| 5 | Apple Journal app improves | Apple won't add iCloud file export or markdown — too niche |
| 6 | Scope creep | If not in MVP, it goes in GitHub Issues |

---

## 7. Post-Launch

### Failure Signals

| Signal | Threshold | Action |
|-------|-----------|--------|
| Sales | < 10 in Week 1 | Drop price, reassess |
| Crash rate | > 5% | Stop marketing, fix stability |
| Rating | < 3.5 after 20 reviews | Fix top complaint |
| 3 months, < 50 sales | PMF failure | Open-source it, pivot |

### When to Start App #2

**Trigger:** Etch hits 1,000 paid sales.

**Next app:** Expense tracker (manual, privacy-first, no bank sync) — same philosophy, same architecture.

---

*Etch — Carve your story. It won't wash away.*