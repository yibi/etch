# KODA: Notes — Screen Flow & UX Map

## App Flow Overview

```
                    ┌─────────────┐
                    │  App Launch  │
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │ Workspace    │── No workspace set ──► ┌──────────────┐
                    │ bookmark     │                       │ Folder Picker │
                    │ exists?      │◄──────────────────────│ (Empty State) │
                    └──────┬──────┘                       └──────────────┘
                           │ Yes
                    ┌──────▼──────┐
                    │  MAIN VIEW   │
                    │  (Route A or  │
                    │   Route B)    │
                    └──────┬──────┘
                           │
              ┌────────────┴────────────┐
              │                         │
       ┌──────▼──────┐          ┌──────▼──────┐
       │ ROUTE A:      │          │ ROUTE B:    │
       │ Terminal     │◄─────────│ Note List   │
       │ (default on   │  List btn│ (default if │
       │  launch)      │─────────►│ notes exist)│
       └──────┬──────┘          └──────┬──────┘
              │                         │
              │ Save note                │ Tap note
              ▼                         ▼
       ┌──────────────┐         ┌──────────────┐
       │ Note saved   │         │ Note Editor  │
       │ → back to     │         │ (Edit/Preview│
       │ Terminal      │         │  toggle)     │
       └──────────────┘         └──────┬──────┘
                                        │
                                 ┌──────┴──────┐
                                 │ Shake phone │
                                 ▼             ▼
                          ┌────────────┐  ┌───────────┐
                          │ Settings    │  │Spark Modal │
                          │ (gear icon) │  │(3 random   │
                          └────────────┘  │ sentences) │
                                          └───────────┘
```

---

## Screen-by-Screen Specs

### SCREEN 1: Empty State — First Launch
```
┌─────────────────────────────┐
│                             │
│          [ KODA ]            │
│                             │
│   Your notes. Your files.   │
│       Your rules.            │
│                             │
│  Choose a folder to start.  │
│  (iCloud Drive or local)    │
│                             │
│    ┌──────────────────┐     │
│    │  Choose Folder   │     │
│    └──────────────────┘     │
│                             │
│                             │
│  No subscription.           │
│  No account.                │
│  No lock-in.                │
│                             │
└─────────────────────────────┘

Background: #0D0D0D (dark) / #FAFAFA (light)
Text: #E8E8E8 / #1A1A1A
Button: accent green #00FF88 (dark) / #00C853 (light)
Font: SF Pro Display for title, SF Pro Text for body
KODA logo: large, bold, centered, accent green
```

### SCREEN 2: Folder Picker (System UI)
- iOS `UIDocumentPickerViewController` — system native, not custom UI
- User selects ANY folder (iCloud Drive, On My iPhone, etc.)
- App stores security-scoped bookmark
- On success → go to Screen 3 (Terminal) or Screen 4 (Note List)

### SCREEN 3: Terminal Editor — Default Main View
```
┌─────────────────────────────┐
│ koda:~$              [List] │  ← Header: monospace, secondary color
├─────────────────────────────┤
│                             │
│ █                           │  ← Blinking cursor (accent green)
│                             │
│                             │
│                             │
│                             │
│                             │
│                             │
│                             │
├─────────────────────────────┤
│ @folder = save to subfolder  │  ← Hint text (secondary, small)
│ Double newline = save       │
└─────────────────────────────┘

Background: #000000 (pure black for terminal feel)
Text: #E8E8E8
Cursor: #00FF88 blinking
Header: SF Mono 14pt, #888888
Input: SF Mono 15pt, #E8E8E8
Hints: SF Mono 11pt, #555555
List button: SF Pro Text 14pt, accent green

Behavior:
- Auto-focus keyboard on appear
- Cursor blinks until user types
- @prefix parsed on save → creates subfolder
- Double newline saves note → clears input → cursor returns
- "List" button → dismiss to Note List
- If no notes exist and user dismisses → goes to Note List (empty)
```

### SCREEN 4: Note List — With Decay
```
┌─────────────────────────────┐
│ KODA              [⚙]  [+]  │  ← Nav bar: title left, settings, new
├─────────────────────────────┤
│ ● Project Alpha              │  ← 100% opacity (modified today)
│   Last sentence preview...   │
│   247 words · 2h ago         │
├─────────────────────────────┤
│ ● Meeting Notes              │  ← 95% opacity (modified 2 days)
│   Last sentence preview...   │
│   340 words · 2d ago         │
├─────────────────────────────┤
│ ● Ideas Dump                 │  ← 70% opacity (modified 15 days)
│   Last sentence preview...   │
│   1.2k words · 15d ago       │
├─────────────────────────────┤
│ ○ Old Note                   │  ← 40% opacity (modified 60 days)
│   Last sentence preview...   │
│   89 words · 60d ago         │
├─────────────────────────────┤
│ ○ Ancient Note               │  ← 20% opacity (modified 120 days)
│   Last sentence preview...   │
│   45 words · 120d ago        │
├─────────────────────────────┤
│                              │
│  Swipe left → delete          │
│  Tap → open note              │
│                              │
└─────────────────────────────┘

Nav bar: #1A1A1A bg, #E8E8E8 text
Note rows: no card background, full-bleed divider lines (#333333)
● = bright dot (recent), ○ = faded dot (old)
Title: SF Pro Text 16pt Semibold
Preview: SF Pro Text 13pt Regular, #999999, single line truncated
Meta: SF Mono 11pt, #666666
Decay: opacity applied to entire row EXCEPT nav bar
Swipe-to-delete: red background, white trash icon

Behavior:
- Sorted by modificationDate (newest first)
- Opacity calculated by days since modification (see TDD section 3.1)
- Tap any note → Screen 5 (Note Editor)
- [+] button → Screen 3 (Terminal)
- [⚙] button → Screen 7 (Settings)
- Long-press note → context menu: Share, Move, Seal
```

### SCREEN 5: Note Editor — Edit Mode
```
┌─────────────────────────────┐
│ ‹ Notes   [Edit│Preview] 🔒 │  ← Nav bar: back, toggle, seal
├─────────────────────────────┤
│                              │
│ # Project Alpha              │  ← Raw markdown in editor
│                              │
│ This is a **bold** statement │
│ and a `code` reference.      │
│                              │
│ - Item one                   │
│ - Item two                   │
│                              │
│ ## Subsection                │
│                              │
│ More text here...            │
│                              │
├─────────────────────────────┤
│ ✓ Saved · 247 words         │  ← Status bar
└─────────────────────────────┘

Nav bar: #1A1A1A bg
Toggle: segmented control [Edit | Preview], accent green when active
Seal button: lock icon, #888888 (unsealed) / accent green (sealed)
Editor: SF Mono 15pt, #E8E8E8, black bg
Status bar: SF Mono 11pt, #666666, "✓ Saved" in accent green

Behavior:
- Opens in Edit mode by default (unless Read-Only toggle was last set)
- Auto-save: debounce 1.5s after last keystroke
- Save indicator: "✓ Saved" appears briefly after save
- Word count updates live
- [Preview] → Screen 6 (Preview mode)
- 🔒 tap → seal note (24hr lock)
- Long-press 🔒 → break seal (with confirmation)
- Swipe down / back → save and return to list
```

### SCREEN 6: Note Editor — Preview Mode
```
┌─────────────────────────────┐
│ ‹ Notes   [Edit│Preview] 🔒 │
├─────────────────────────────┤
│                              │
│ Project Alpha                │  ← Rendered H1, SF Pro Display 24pt Bold
│                              │
│ This is a **bold** statement │  ← "bold" in bold
│ and a `code` reference.      │  ← "code" in SF Mono, bg #1A1A1A, accent green
│                              │
│ • Item one                   │  ← Bulleted list
│ • Item two                   │
│                              │
│ Subsection                   │  ← Rendered H2, SF Pro Display 20pt Bold
│                              │
│ More text here...            │
│                              │
└─────────────────────────────┘

Preview: swift-markdown-ui Markdown() view
Theme: basic, with accent green for code/links
Background: #0D0D0D (dark) / #FAFAFA (light)
Text: #E8E8E8

Behavior:
- Read-only — no editing possible
- Same nav bar as Edit mode
- [Edit] → back to Screen 5
- If note is sealed, Preview is forced (can't switch to Edit)
```

### SCREEN 7: Settings — Config File Aesthetic
```
┌─────────────────────────────┐
│ ‹ Notes                     │
├─────────────────────────────┤
│ # KODA Configuration         │  ← Monospace header
│ # Version 1.0.0              │
│ # Generated: 2026-06-27     │
│                              │
│ [workspace]                  │
│ path = ~/iCloud/Notes        │
│ files = 247                  │
│                          [Change]
│                              │
│ [appearance]                 │
│ theme = dark            [▾]  │
│ decay_rate = default    [▾]  │
│   off / fast / default / slow │
│                              │
│ [pro]                        │
│ status = locked         [🔒] │
│                          [Unlock $14.99]
│                              │
│ [about]                      │
│ version = 1.0.0              │
│ philosophy = "Your files,    │
│   your cloud, your rules"    │
│ guarantee = "End of Life:    │
│   open source if abandoned"  │
│                              │
│ [danger]                     │
│                      [Reset] │
│                      [Change Folder]
└─────────────────────────────┘

Font: SF Mono 14pt throughout (config file aesthetic)
Headers (#): #888888
Keys: #E8E8E8
Values: #00FF88 (accent green)
Buttons: standard iOS buttons, accent green
Background: #0D0D0D

Behavior:
- Looks like a config file — monospace, key = value format
- [Change] → opens Folder Picker (Screen 2)
- [Unlock $14.99] → StoreKit 2 purchase flow
- [Reset] → confirmation dialog → clears metadata, keeps files
- [Change Folder] → confirmation → current workspace bookmark cleared
```

### SCREEN 8: Shake Spark — Modal
```
┌─────────────────────────────┐
│                              │
│   ✨ SPARK                    │
│                              │
│ ┌─────────────────────────┐ │
│ │ "The best time to plant  │ │
│ │  a tree was 20 years     │ │
│ │  ago. The second best    │ │
│ │  time is now."           │ │
│ │  — ideas-dump.md         │ │
│ └─────────────────────────┘ │
│                              │
│ ┌─────────────────────────┐ │
│ │ "Systems survive when    │ │
│ │  people don't."          │ │
│ │  — project-alpha.md      │ │
│ └─────────────────────────┘ │
│                              │
│ ┌─────────────────────────┐ │
│ │ "Simple is harder than   │ │
│ │  complex."              │ │
│ │  — quotes.md            │ │
│ └─────────────────────────┘ │
│                              │
│  [Save as Note]  [Shake ↻]  │
│                      [✕]     │
└─────────────────────────────┘

Modal: presented over current screen
Card bg: #1A1A1A with subtle border #333333
Quote text: SF Pro Text 15pt, #E8E8E8
Source: SF Mono 11pt, #888888
Buttons: accent green border, transparent bg
Title: SF Pro Display 18pt Bold, accent green

Behavior:
- Triggered by shake gesture
- 3 random sentences from 3 different random .md files
- [Save as Note] → creates new .md with all 3 quotes → dismiss
- [Shake ↻] → re-roll new 3 sentences
- [✕] → dismiss modal
- If < 3 notes exist → show message: "Need at least 3 notes to spark"
```

### SCREEN 9: Sealed Note — Edit Attempt Blocked
```
┌─────────────────────────────┐
│ ‹ Notes   [Edit│Preview] 🔒 │
├─────────────────────────────┤
│                              │
│                              │
│      🔒 SEALED               │
│                              │
│   This note is sealed.       │
│   Unlocks in 18h 32m.       │
│                              │
│   ┌────────────────────┐    │
│   │  Read in Preview    │    │  ← Button → forces Preview mode
│   └────────────────────┘    │
│                              │
│   Long-press 🔒 to break.   │  ← Hint text, #555555
│                              │
└─────────────────────────────┘

Lock icon: large, centered, #888888
Text: SF Pro Text 16pt, #888888
Button: accent green border

Behavior:
- Shows when user taps [Edit] on a sealed note
- [Read in Preview] → switches to Preview mode (Screen 6)
- Long-press 🔒 in nav bar → "Break seal?" confirmation → removes seal
```

### SCREEN 10: Folder Picker Success → Note List (Empty)
```
┌─────────────────────────────┐
│ KODA              [⚙]  [+]  │
├─────────────────────────────┤
│                              │
│                              │
│      No notes yet.           │
│                              │
│      Just start typing.      │
│                              │
│   ┌────────────────────┐    │
│   │   Start Writing     │    │  ← Button → Terminal (Screen 3)
│   └────────────────────┘    │
│                              │
│   Your .md files will        │
│   appear here.               │
│                              │
│                              │
└─────────────────────────────┘

Same styling as Screen 1 empty state
Button: accent green
Text: SF Pro Text 16pt, #888888
```

---

## Navigation Map Summary

```
                    ┌──────────────┐
                    │  Empty State  │
                    │  (Screen 1)   │
                    └──────┬───────┘
                           │ Choose Folder
                    ┌──────▼───────┐
                    │ Folder Picker │
                    │ (Screen 2)   │
                    └──────┬───────┘
                           │
              ┌────────────┴────────────┐
              │                         │
       ┌──────▼──────┐          ┌──────▼──────┐
       │  Terminal    │          │ Note List    │
       │ (Screen 3)  │◄─────────│ (Screen 4)   │
       │  default    │  List    │  if notes    │
       │  on launch  │─────────►│  exist       │
       └──────┬──────┘          └──────┬──────┘
              │                         │ tap note
              │ save                    │
              │                         ▼
              │                  ┌──────────────┐
              │                  │ Note Editor   │
              │                  │ (Screen 5/6) │
              │                  │ Edit|Preview │
              │                  └──────┬───────┘
              │                         │
              │              ┌──────────┼──────────┐
              │              │          │          │
              │              ▼          ▼          ▼
              │        ┌──────┐  ┌──────┐  ┌──────┐
              │        │Sealed│  │Shake │  │Setts │
              │        │ Blk  │  │Spark │  │      │
              │        │(Sc 9)│  │(Sc 8)│  │(Sc 7)│
              │        └──────┘  └──────┘  └──────┘
              │
              ▼
        Back to Terminal (Screen 3) or Note List (Screen 4)
```

---

## Design System Summary

### Colors

| Token | Dark Mode | Light Mode |
|-------|-----------|------------|
| `bg.primary` | #000000 | #FAFAFA |
| `bg.surface` | #0D0D0D | #FFFFFF |
| `bg.elevated` | #1A1A1A | #F5F5F5 |
| `text.primary` | #E8E8E8 | #1A1A1A |
| `text.secondary` | #999999 | #666666 |
| `text.tertiary` | #555555 | #AAAAAA |
| `accent` | #00FF88 | #00C853 |
| `divider` | #333333 | #E0E0E0 |
| `danger` | #FF6B6B | #D32F2F |
| `success` | #00C853 | #2E7D32 |

### Typography

| Token | Font | Size | Weight |
|-------|------|------|--------|
| `font.display` | SF Pro Display | 24pt | Bold |
| `font.heading` | SF Pro Display | 20pt | Bold |
| `font.body` | SF Pro Text | 16pt | Regular |
| `font.caption` | SF Pro Text | 13pt | Regular |
| `font.meta` | SF Mono | 11pt | Regular |
| `font.mono` | SF Mono | 15pt | Regular |
| `font.terminal` | SF Mono | 15pt | Regular |

### Spacing

| Token | Value |
|-------|-------|
| `space.xs` | 4pt |
| `space.sm` | 8pt |
| `space.md` | 16pt |
| `space.lg` | 24pt |
| `space.xl` | 32pt |

### Corner Radius

| Token | Value |
|-------|-------|
| `radius.sm` | 4pt (buttons) |
| `radius.md` | 8pt (cards) |
| `radius.none` | 0pt (full-bleed rows) |