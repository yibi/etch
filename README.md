# KODA

**No tie-in. No lockdown. Your files, your cloud, your rules. Pay once, own forever.**

KODA is a portfolio of mobile apps that respect your data sovereignty. No subscriptions. No cloud lock-in. No data harvesting. Your files stay in your iCloud, Google Drive, or local storage — KODA is just the window.

## Apps

| App | Status | Description |
|-----|--------|-------------|
| KODA: Notes | 🚧 In Development | Markdown notes editor. Files save as plain `.md` to your storage. |
| KODA: Ledger | 📋 Planned | Health & habits tracker. Logs to human-readable CSV in your storage. |
| KODA: Tally | 📋 Planned | Personal finance & inventory. Manual tracking, no bank sync, your data. |

## Philosophy

- **No tie-in** — Use whatever storage you already have (iCloud, Google Drive, local)
- **No lockdown** — Files are plain text (`.md`, `.csv`). If KODA disappears, your data survives
- **No subscription** — Pay once. Own forever. That's it.
- **No backend** — No servers to go down. No accounts to hack. No data to sell.
- **No tracking** — We can't see your data because it never touches our servers

## Target Market

- **Primary:** Male millennials (28-43), USA + Japan + Germany, iOS
- **Secondary:** Gen Z (15-25) drives virality, Millennials drive revenue
- **Language:** English → Japanese → German

## Pricing

| Tier | Price | What you get |
|------|-------|-------------|
| Free | $0 | Core features, 1 storage location |
| Pro (single app) | $9.99–$14.99 one-time | All features unlocked |
| Lifetime Bundle | $49 one-time | All current + future KODA apps, forever |

## Tech Stack

- **Platform:** iOS first (iPad/iPhone), Android later
- **Language:** Swift + SwiftUI
- **Storage:** User's existing iCloud Drive / local storage via `UIDocumentPicker`
- **Sync:** Handled by iOS (iCloud Drive). We never touch it.
- **Backend:** None. There is no backend.
- **Monetization:** StoreKit 2 (one-time IAP)
- **Analytics:** None. We don't track users.

## Project Structure

```
koda/
├── README.md                 ← You are here
├── PROJECT_PLAN.md           ← Full commercial + technical plan
├── SPECS/
│   ├── NOTES_APP_SPEC.md     ← Detailed app spec for KODA: Notes
│   ├── LEDGER_APP_SPEC.md    ← Future: KODA: Ledger spec
│   └── TALLY_APP_SPEC.md     ← Future: KODA: Tally spec
├── DESIGN/
│   ├── BRAND_GUIDE.md        ← Visual identity, tone, naming
│   └── SCREEN_SPECS/         ← Screen-by-screen UI specifications
├── MARKETING/
│   ├── LAUNCH_PLAN.md        ← Launch playbook
│   ├── GUERRILLA_TACTICS.md  ← Unconventional marketing ideas
│   └── ASSETS/               ← Landing page, migration script, calculator
└── SOURCE/                   ← Swift source (added by AI coding agents)
    └── (empty — first commit)
```

## How To Pick Up This Project (Any Machine, Any AI Agent)

This repo is the single source of truth. Any Hermes Agent, Claude Code, or Codex instance can pick up development by reading:

1. **`PROJECT_PLAN.md`** — The full plan (timeline, budget, risks, revenue model)
2. **`SPECS/NOTES_APP_SPEC.md`** — What to build (features, architecture, MVP scope)
3. **`DESIGN/BRAND_GUIDE.md`** — How it should look and feel
4. **`SOURCE/`** — Current code state

### To start coding on a new machine:
```bash
git clone https://github.com/yibi/koda.git
cd koda
# Read PROJECT_PLAN.md, SPECS/NOTES_APP_SPEC.md, DESIGN/BRAND_GUIDE.md
# Start writing Swift in SOURCE/
```

## License

Proprietary. Source code is private until launch. The "End of Life" guarantee (see PROJECT_PLAN.md) commits to open-sourcing if the project is ever abandoned.

---

*KODA — Heavy tools for your mind. But lighter.*