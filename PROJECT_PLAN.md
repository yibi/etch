# KODA: NOTES — Complete Project Plan

## Executive Summary

**Product:** Markdown notes app for iOS. No backend. Files save as plain `.md` to user's iCloud Drive / local storage. No database. No subscription.

**Brand:** KODA — "Your files, your cloud, your rules. Pay once, own forever."

**Target:** $30,000 revenue Year 1 (2,354 sales at $14.99 one-time IAP)

**Developer:** Solo product manager. 30 years sysadmin/telco/data center experience. Currently drives Grab 50-60 hrs/week. AI coding agents (Claude Code / Codex / Hermes) write all code. User reviews and ships.

**Machine:** Has MacBook (no need to purchase). AI agents handle all Swift/SwiftUI development.

**Timeline:** 2-3 months (AI agents code, not the user). User is product manager, not coder.

---

## 1. PRE-PRODUCTION (Week 1)

### 1.1 Hardware & Accounts

| Item | Cost (USD) | Status |
|------|-----------|--------|
| MacBook | $0 | ✅ Already have |
| Apple Developer Program | $99/year | Need to sign up |
| GitHub | $0 | ✅ Created (github.com/yibi/koda) |
| App icon design (Fiverr) | $20-50 | Later |
| Domain (koda.app or similar) | $10-20 | Later |
| **Total** | **~$150** | |

### 1.2 Tools Setup (On MacBook)

```bash
# Install Xcode (from App Store, free)
# Install Homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install AI coding tools
brew install node
npm install -g @anthropic-claude/claude-code  # or codex

# Install Hermes Agent (optional, for autonomous coding)
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash

# Clone the project
git clone https://github.com/yibi/koda.git
cd koda
```

### 1.3 No Learning Curve Needed

The user does NOT need to learn Swift. AI coding agents write all code. The user:
- Reviews code via GitHub PRs
- Tests builds on their MacBook
- Makes product decisions (features, pricing, marketing)
- Approves or rejects work

**Sysadmin advantage:** Understands file systems, networking, version control, deployment. Reviews AI output for logical correctness even if not Swift-fluent.

---

## 2. DEVELOPMENT TIMELINE (8-12 Weeks)

**Reality check:** AI agents code 10x faster than a human learning Swift. But debugging, Apple platform quirks, and review cycles add time. Plan for 8-12 weeks, not 8-12 months.

### Phase 1: MVP CORE (Weeks 1-3)

| Week | AI Agent Task | Deliverable |
|------|--------------|------------|
| 1 | Create Xcode project. SwiftUI app. Note list + create/edit/delete. Basic markdown rendering (using Ink/MarkdownView library). | App opens, shows notes, can create/edit |
| 2 | `UIDocumentPicker` integration — user selects any iCloud/local folder. App reads/writes `.md` files in that folder. | App reads/writes files from user's storage |
| 3 | Settings page ("config file" aesthetic). Light/dark mode. App polish. | Alpha build |

**MVP Scope:**
- ✅ Create, edit, delete markdown notes
- ✅ Open/save to any iCloud Drive or local folder
- ✅ Basic markdown rendering (library-based)
- ✅ Settings page (config-file styled)
- ✅ Light/dark mode

**MVP Cuts (V2):**
- ❌ Half-life decay, Terminal entry, Shake, Vibe search, 24hr seal, Read-only toggle
- ❌ iPad layout, Apple Watch, localization
- ❌ Google Drive / Dropbox API integration

### Phase 2: THE HOOKS (Weeks 4-6)

| Week | AI Agent Task | Difficulty |
|------|--------------|------------|
| 4 | Half-life decay (sort/fade notes by last-modified age) | Easy |
| 5 | Terminal entry (app opens to blinking cursor, type to create, prefix tagging) | Medium |
| 6 | Shake to shuffle (accelerometer → random note "spark") + 24hr seal + read-only toggle | Easy |

### Phase 3: SHIP (Weeks 7-8)

| Week | Task | Deliverable |
|------|------|-------------|
| 7 | StoreKit 2 IAP ($9.99 launch price). Pro/Free gating. App Store assets. | Monetization works |
| 8 | TestFlight beta (recruit from Reddit/HN). Bug fixes. App Store submission. | Ready for review |

### Phase 4: LAUNCH (Week 9-10)

| Week | Task |
|------|------|
| 9 | App Store review. Build marketing assets (landing page, migration script, ransom calculator). Community seeding. |
| 10 | LAUNCH. HN Show HN. Product Hunt. Reddit. Mastodon. |

---

## 3. TECHNICAL ARCHITECTURE

```
┌─────────────────────────────────────────┐
│              KODA: Notes                  │
├─────────────────────────────────────────┤
│  SwiftUI Views                           │
│  ├── NoteListView (with decay opacity)   │
│  ├── TerminalEditor (blinking cursor)    │
│  ├── MarkdownRenderer (library-based)    │
│  ├── SettingsView (config-file style)    │
│  ├── ShakeSparkView (random sentences)   │
│  └── ReadOnlyToggle                      │
├─────────────────────────────────────────┤
│  Data Layer                              │
│  ├── FileManager (read/write .md files)  │
│  ├── UIDocumentPicker (user picks folder)│
│  ├── FileDocument (iCloud sync delegate) │
│  └── UserDefaults (app settings, IAP)    │
├─────────────────────────────────────────┤
│  Monetization                            │
│  └── StoreKit 2 ($9.99-14.99 one-time)   │
├─────────────────────────────────────────┤
│  User's Storage (NOT ours)               │
│  ├── iCloud Drive (/KODA/Notes/)        │
│  ├── Local storage                      │
│  └── Manifest.txt (auto-generated)       │
└─────────────────────────────────────────┘
```

**Key libraries:**
- Markdown rendering: `Ink` or `MarkdownView` (Swift package)
- IAP: `StoreKit 2` (Apple native)
- File access: `FileManager` + `UIDocumentPickerViewController` (Apple native)
- **No backend. No database. No cloud SDK. No analytics SDK.**

**Testing:**
- AI agent writes unit tests for file I/O
- TestFlight beta with 5-10 testers from Reddit
- Test on real iPhone (iCloud sync behaves differently than simulator)
- Test with 1,000+ files for performance

---

## 4. PRE-LAUNCH

### 4.1 App Store Assets

| Asset | Notes |
|-------|-------|
| App icon | Hire Fiverr ($20-50). 1024x1024 PNG |
| Screenshots | Use "Shot.at" or "AppScreens". Focus on Terminal + Decay views |
| Description | Lead with philosophy: "Your notes are hostages. KODA is the exit." |
| Keywords | "markdown notes offline privacy no subscription local" |
| Preview video | 15-30s: Terminal entry → typing → Shake feature |
| Privacy policy | "We don't collect anything. Period." |

### 4.2 Marketing Assets

| Asset | Time | Purpose |
|-------|------|---------|
| Landing page (HTML) | 1 day | Brand + App Store link |
| SaaS Ransom Calculator | 1 day | Viral: "You'll spend $12K on notes over your lifetime" |
| Notion → Markdown migration script | 1 day | GitHub repo, removes switching friction |
| "Export or Die" manifesto | 1 day | The narrative: "Your notes are hostages" |
| End of Life guarantee | 1 hour | ToS: "If I abandon this, I open-source it" |

### 4.3 Community Seeding

| Community | Action | When |
|-----------|--------|------|
| r/selfhosted | Post migration script | 2 weeks before launch |
| r/markdown | Share app preview | 1 week before |
| Hacker News | Prepare Show HN draft | 1 week before |
| Mastodon (fosstodon.org) | Build presence, dev journey | Ongoing |
| TestFlight beta | 50 testers from above | 3 weeks before |

### 4.4 Pricing

| Phase | Price | Duration |
|-------|-------|----------|
| Launch | $9.99 | First 100 buyers |
| Post-launch | $14.99 | Buyers 101-500 |
| Mature | $19.99 | After 500 sales |
| Lifetime Bundle | $49 | When App #2 ships |

---

## 5. LAUNCH (48-Hour Playbook)

**Launch on Saturday EST morning (= Saturday evening SGT)**

| Time (EST) | Action |
|------------|--------|
| 08:00 | Post "Show HN" — title: *"Show HN: I spent 30 years in data centers and built a notes app that doesn't own your data"* |
| 08:05 | Product Hunt — title: *"KODA: Notes — The app that doesn't want to keep you"* |
| 08:30 | r/Apple, r/markdown, r/selfhosted |
| 09:00 | Mastodon (fosstodon.org) |
| 09:30 | Lobste.rs |
| 10:00+ | Respond to EVERY comment |

---

## 6. POST-LAUNCH

### Failure Signals

| Signal | Threshold | Action |
|-------|-----------|--------|
| Sales velocity | < 10 in Week 1 | Cut to $4.99, reassess |
| Crash rate | > 5% | Stop marketing, fix stability |
| Rating | < 3.5 after 20 reviews | Fix top complaint, repush |
| Refund rate | > 10% | Survey refunders |
| 3 months, < 50 sales | PMF failure | Open-source it, move to App #2 |

### When to Start App #2

**Trigger:** KODA: Notes hits 1,000 paid sales.

At that point: Start KODA: Ledger (health/habits) on the same architecture.

---

## 7. RISK MATRIX

| Rank | Risk | Probability | Impact | Mitigation |
|------|------|-------------|--------|-----------|
| 1 | AI-generated code has subtle bugs | MEDIUM | Data loss | Extensive testing, TestFlight beta, unit tests for file I/O |
| 2 | iCloud sync data loss | MEDIUM | App dies | Use `FileDocument`/`UIDocument`, never custom sync |
| 3 | Apple App Store rejection | MEDIUM | 2-4 week delay | Read HIG, follow IAP rules exactly |
| 4 | Scope creep (adding features) | HIGH | Never ships | If not in MVP, it goes in GitHub Issues |
| 5 | No market demand at $14.99 | MEDIUM | $0 revenue | Launch at $9.99, drop to $4.99 if needed |
| 6 | App Store discoverability | HIGH | Low organic | Target long-tail keywords: "markdown editor offline" |
| 7 | iCloud "Optimize Storage" evicts files | HIGH | Bad UX | Graceful "downloading from iCloud" states |
| 8 | Markdown rendering lag on large notes | MEDIUM | Bad reviews | Use native SwiftUI Text, not WebView |
| 9 | Lone entitled user 1-star threat | HIGH | Stress | Be polite, "Added to roadmap," move on |
| 10 | User loses interest / Grab burnout | MEDIUM | Project stalls | AI agents do heavy lifting, user is PM not coder |

---

## 8. FINANCIAL MODEL

### Investment

| Item | Cost |
|------|------|
| Apple Developer Program | $99 |
| App icon | $30 |
| Domain | $15 |
| Marketing assets | $0 (DIY) |
| **Total** | **~$150** |

### Revenue Projection (Conservative)

| Month | Event | Sales | Revenue (net) |
|-------|-------|-------|---------------|
| M1-2 | Development (AI agents) | 0 | $0 |
| M3 (Launch) | HN + PH + Reddit spike | 200 | $1,699 |
| M4 | Post-launch tail | 80 | $680 |
| M5 | Organic + word of mouth | 100 | $850 |
| M6 | Blog coverage | 120 | $1,020 |
| M7-8 | Steady state | 130/mo | $1,105/mo |
| M9-10 | Localization (JP/DE) | 150/mo | $1,275/mo |
| M11 | Holiday boost | 200 | $1,700 |
| M12 | Steady | 160 | $1,360 |
| **Total** | | **1,570** | **~$13,348** (first 12 months post-launch) |

*Note: Conservative. Viral HN launch could 3x this.*

### Sensitivity

| Scenario | Sales | Revenue | Outcome |
|----------|-------|---------|---------|
| 🟢 Viral | 5,000 | $42,475 | Replace Grab income |
| 🟢 Target | 2,354 | $20,000 | Goal hit |
| 🟡 OK | 1,000 | $8,495 | Keep iterating |
| 🔴 Poor | 300 | $2,549 | Pivot or kill |
| ⚫ Worst | 50 | $425 | Out $150, gained app |

### Break-Even

- Investment: ~$150
- Break-even: **11 sales** at $14.99 (after Apple's 15%)
- Likely: Launch day

---

## THE FINAL NOTE

AI agents code. You review. The file system IS the system. Your app is a window into `.md` files. Ship in 8-12 weeks, not 8-12 months.

Drive Grab. Direct AI. Ship KODA. Collect $30K. Start the next one.