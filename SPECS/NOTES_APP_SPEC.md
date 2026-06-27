# KODA: NOTES — App Specification

## Overview

A markdown notes app for iOS that saves plain `.md` files to the user's own storage (iCloud Drive, local). No backend. No database. No subscription. The app is a window into files the user already owns.

## Core Philosophy

- Files are plain markdown (`.md`). If the app disappears, the files survive.
- The app is a **guest** in the user's file system, not the owner.
- No cloud APIs. No sync engine. iOS handles iCloud sync natively.
- No analytics. No tracking. No accounts.

---

## MVP Scope (Phase 1 — Weeks 1-3)

### Must Have

| Feature | Description | Technical Approach |
|---------|-------------|-------------------|
| Note list | List of `.md` files in user-selected folder | `FileManager.contentsOfDirectory` |
| Create note | Create new `.md` file | `FileManager.createFile` with `.md` extension |
| Edit note | Edit markdown text with live preview toggle | `UITextView` or SwiftUI `TextEditor` + markdown library |
| Delete note | Delete `.md` file | `FileManager.removeItem` |
| Markdown rendering | Render markdown to formatted text | `Ink` or `MarkdownView` Swift package |
| Folder picker | User selects any iCloud/local folder as "workspace" | `UIDocumentPickerViewController` |
| File persistence | Read/write `.md` files in selected folder | `FileManager` + security-scoped resources |
| Light/dark mode | Follow system or manual toggle | SwiftUI `.preferredColorScheme` |
| Settings | Config-file aesthetic settings page | SwiftUI `Form` with monospace font |

### Cut from MVP (Phase 2 — Weeks 4-6)

| Feature | Phase | Difficulty |
|---------|-------|------------|
| Half-life decay view | Phase 2 | Easy |
| Terminal entry (zero-click) | Phase 2 | Medium |
| Shake to shuffle | Phase 2 | Easy |
| 24-hour seal | Phase 2 | Easy |
| Read-only toggle | Phase 2 | Easy |
| Vibe search (CoreML) | V3 | Hard — cut |

### Never (V1)

| Feature | Why |
|---------|-----|
| Cloud API integration (Google Drive, Dropbox) | Hostile APIs, high maintenance, use iCloud only |
| On-device AI / face recognition | Wrong app category |
| Collaboration / sharing | Adds complexity, wrong philosophy |
| Apple Watch | Distraction |
| iPad-specific layout | Later |
| Web version | Never — this is mobile only |

---

## Unique Features (The "Hooks")

These features differentiate KODA from every other notes app:

### 1. Half-Life Decay View
- Notes you haven't touched slowly **fade** (opacity decreases) and **sink** to bottom of list
- Notes you recently edited are **bright** and at top
- Configurable: decay rate (7 days, 30 days, 90 days), minimum opacity (20%)
- Purpose: Kills "note graveyard" — forces engagement with old notes or lets them visually die
- Technical: Sort by `modificationDate`, apply opacity based on days since last edit

### 2. Terminal Entry (Zero-Click)
- App opens directly to a **blinking cursor** and keyboard
- No "New Note" button. Just type.
- Prefix-based filing: `@work Project Alpha` → saves to `/work/` folder
- No prefix → saves to root of selected workspace
- Purpose: Capture speed. The moment of thought, not the moment of organization
- Technical: SwiftUI view with auto-focus `TextEditor`, parse prefix on save

### 3. Shake to Shuffle
- User shakes phone → app pulls **3 random sentences** from 3 different notes
- Displayed as a "Spark" card — cross-pollination of forgotten ideas
- User can dismiss or save the spark as a new note
- Technical: `UIEvent.subtype == .motionShake`, random file + random line extraction

### 4. 24-Hour Seal
- After writing, user can "Seal" a note → becomes **read-only for 24 hours**
- Prevents infinite tweaking habit
- Sealed notes show a lock icon and countdown timer
- Technical: Store seal timestamp in `UserDefaults`, check on edit attempt

### 5. Sysadmin Manifest
- Every folder the app manages gets an auto-generated `Manifest.txt`
- Lists: file count, total word count, last modified date, app version
- If user deletes the app, the Manifest stays. "I was just a guest."
- Technical: Generate on folder open, write to root of workspace

### 6. Read-Only Toggle
- Physical-feeling toggle switch in the UI
- Read Mode: renders markdown beautifully, **cannot edit**
- Write Mode: raw markdown editor
- Purpose: Psychological boundary between "thinking/writing" and "reviewing/consuming"
- Technical: Boolean state, swap between `TextEditor` and rendered markdown view

---

## Technical Architecture

### File Access

```
User selects folder via UIDocumentPicker
        ↓
App stores bookmark (security-scoped URL) in UserDefaults
        ↓
On launch: resolve bookmark → access folder
        ↓
FileManager.contentsOfDirectory → list .md files
        ↓
User taps note → FileManager.read → display
        ↓
User edits → FileManager.write → save .md file
        ↓
iOS handles iCloud sync (we do NOTHING)
```

### Key Decisions

| Decision | Choice | Why |
|----------|--------|-----|
| Markdown parser | `Ink` Swift package | Lightweight, native, no WebView |
| File access | `UIDocumentPickerViewController` + security-scoped bookmarks | Apple-recommended, works with iCloud |
| Sync | None — iOS handles it | Never build custom sync. Ever. |
| Data storage | User's filesystem only. App stores only: bookmark URL, settings, IAP state in `UserDefaults` | No database, no Core Data |
| State management | SwiftUI `@State` + `@AppStorage` | Simple, native |
| IAP | StoreKit 2 | Apple native, one-time purchase support |

### Dependencies

| Package | Purpose | Source |
|---------|---------|--------|
| `Ink` or `MarkdownView` | Markdown rendering | Swift Package Manager |
| `StoreKit 2` | In-app purchase | Apple (built-in) |
| No other dependencies | | |

---

## Monetization

### Free Tier
- Full note editing (create, read, update, delete)
- One storage folder
- Basic markdown rendering
- Light/dark mode

### Pro Tier ($9.99-14.99 one-time IAP)
- Half-life decay view
- Terminal entry (zero-click)
- Shake to shuffle
- 24-hour seal
- Read-only toggle
- Multiple storage folders
- Custom themes
- No ads (there are no ads anyway, but this is a "Pro" framing)

### Lifetime Bundle ($49 one-time, when App #2 ships)
- All current KODA apps
- All future KODA apps
- Forever

### IAP Implementation
- `StoreKit 2` with `Product.purchase()`
- One non-consumable product: `koda.pro.unlock`
- Restore purchases: `Transaction.currentEntitlements`
- No subscription. No consumables. No tricks.

---

## App Store Information

| Field | Value |
|-------|-------|
| App name | KODA: Notes |
| Subtitle | Markdown editor. No subscription. Your files. |
| Category | Productivity |
| Keywords | markdown, notes, offline, privacy, local, editor, no subscription |
| Description | See MARKETING/ section |
| Privacy | No data collected. No analytics. No tracking. |
| Age rating | 4+ |

---

## Testing Strategy

| Test | How | Who |
|------|-----|-----|
| Unit tests | AI agent writes XCTest for file I/O, markdown parsing, IAP | AI agent |
| UI tests | SwiftUI Preview + manual testing on MacBook simulator | User |
| Beta test | TestFlight to 50 testers (Reddit, HN, Mastodon) | Community |
| iCloud test | Test on real iPhone with iCloud Drive enabled, "Optimize Storage" on | User |
| Large file test | Create 1,000+ .md files, test performance | AI agent |
| Edge cases | Empty folder, permission denied, offline mode, iCloud eviction | AI agent |

---

## Accessibility

| Feature | Support |
|---------|---------|
| VoiceOver | Full support — all buttons labeled |
| Dynamic Type | Scales up to XXXL |
| Reduce Motion | Disable decay animation, shake animation |
| Voice Control | All actions accessible via voice |
| High Contrast | Compatible with iOS high-contrast mode |