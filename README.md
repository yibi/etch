# Etch

**Your life story shouldn't have a monthly rent.**

Etch is a journaling app that saves every entry as a plain markdown file in your own iCloud. No subscription. No lock-in. No company that can lock you out of your own memories when your credit card expires.

Pay once. Own forever. Your grandkids can read your journal in any text editor in 50 years.

## The Problem

Day One charges $35/year to access your own memories. Miss a payment, they lock the doors. Your 5 years of journal entries — your child's first words, your grief, your joy — held hostage behind a paywall.

Your memories should not die with your credit card expiration date.

## The Solution

- **Plain files** — Every entry is a `.md` file in your iCloud. Open it in any text editor, on any device, forever.
- **No subscription** — $24.99 one-time. That's it. No monthly rent on your own life story.
- **No backend** — No servers. No cloud. No company that can go bankrupt and take your memories with it.
- **No lock-in** — If Etch disappears tomorrow, your journal survives in your Files app.
- **End of Life guarantee** — If we ever abandon this app, we open-source the entire codebase.

## The Angle

> *"When I die, my kids can find my physical journal on a shelf. If I use a subscription app, my memories die with my credit card expiration date. I built Etch so your story outlives the company that made the app."*

## Target Market

- **Primary:** Millennials (28-43) experiencing Day One subscription fatigue
- **Secondary:** Gen Z (15-25) who value privacy and data ownership
- **Tertiary:** New parents documenting their child's milestones
- **Language:** English → Japanese → German
- **Platform:** iOS first (iPhone + iPad)

## Pricing

| Tier | Price | What you get |
|------|-------|-------------|
| Free | $0 | Read all entries, write up to 50 entries, one workspace |
| Pro | $24.99 one-time | Unlimited entries, photos, PDF book export, themes, all future updates |
| Lifetime Bundle | $49 one-time | All current + future Etch apps, forever |

## Competitive Positioning

| App | Price | The Problem |
|-----|-------|-------------|
| Day One | $35/year | Subscription, proprietary format, can lock you out, corporate-owned |
| Journey | $40/year or $100+ "lifetime" | "Lifetime" feels risky, clunky UI |
| Diarium | ~$15 one-time | Best non-sub competitor but UI looks like 2012 |
| Apple Journal | Free | Too basic, locked to Apple, no export |
| Stoic | $40/year | Too "app-y", prompts not long-form writing |
| **Etch** | **$24.99 once** | **Beautiful, plain .md files, no subscription, no lock-in** |

## Tech Stack

- **Platform:** iOS 17+ (iPhone and iPad)
- **Language:** Swift + SwiftUI
- **Storage:** User's iCloud Drive via `UIDocumentPicker` — entries saved as `.md` files
- **Sync:** Handled by iOS (iCloud Drive). We never touch it.
- **Backend:** None. There is no backend.
- **Monetization:** StoreKit 2 (one-time IAP)
- **Analytics:** None. We don't track users.
- **Dependencies:** `swift-markdown-ui` for rendering. That's it.

## Project Structure

```
etch/
├── README.md                  ← You are here
├── PROJECT_PLAN.md            ← Full commercial + technical plan
├── SPECS/
│   ├── APP_SPEC.md            ← Detailed feature spec
│   └── TECHNICAL_DESIGN.md    ← Architecture decisions (TBD — needs rewrite for journaling)
├── DESIGN/
│   ├── BRAND_GUIDE.md         ← Visual identity (TBD — needs rewrite for Etch)
│   └── SCREEN_FLOW.md         ← Screen wireframes (TBD)
├── MARKETING/
│   ├── LAUNCH_PLAN.md         ← Launch playbook (TBD — needs rewrite)
│   └── GUERRILLA_TACTICS.md   ← Unconventional marketing (TBD)
└── SOURCE/                    ← Swift source (empty — ready for AI coding agents)
```

## How To Pick Up This Project

```bash
git clone https://github.com/yibi/etch.git
cd etch
# Read PROJECT_PLAN.md, SPECS/, DESIGN/
# Point AI coding agent (Claude Code / Codex) at the specs
# It writes Swift code into SOURCE/
```

## License

Proprietary. Source code is private until launch. The End of Life guarantee commits to open-sourcing if the project is ever abandoned.

---

*Etch — Carve your story. It won't wash away.*