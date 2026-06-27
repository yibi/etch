# KODA: Notes — Technical Design Document

*This document resolves every technical decision so any AI coding agent can build the app without ambiguity.*

---

## 1. Project Foundation

| Decision | Value | Rationale |
|----------|-------|-----------|
| Framework | **SwiftUI** | Modern, declarative, Apple's recommended path. AI agents write SwiftUI better than UIKit. |
| Minimum iOS | **iOS 17.0** | SwiftUI is mature at 17. StoreKit 2 works. NavigationSplitView available. Don't support older — our target demo has newer phones. |
| Bundle ID | **com.koda.notes** | Clean, brandable. If unavailable, fall back to com.yibi.koda.notes. |
| App type | **Library-based app** (not Document-based) | User picks a workspace folder once. App manages .md files inside it. Not a document browser — that's Apple's job. |
| Dependency manager | **Swift Package Manager (SPM)** | Apple-native, no CocoaPods, no Carthage. |
| Device target | **iPhone only (MVP)** | Universal app later. iPhone first because that's where the market is. iPad layout is V2. |
| Orientation | **Portrait only** | Notes apps don't need landscape. Simplifies layout. |
| iCloud | **Yes — user's own iCloud Drive** | App does NOT use iCloud Documents or CloudKit. App uses UIDocumentPicker to let user select any folder (including iCloud Drive). iOS handles sync. App never touches the network. |

---

## 2. Architecture

### 2.1 App Structure

```
KODANotes/
├── KODANotesApp.swift          ← @main entry point
├── App/
│   ├── AppState.swift           ← Global state: workspace URL, IAP status, settings
│   └── Constants.swift          ← Colors, fonts, IAP product IDs, config keys
├── Models/
│   ├── Note.swift               ← Note model: filename, title, content, modifiedDate, wordCount
│   ├── NoteMetadata.swift       ← Seal timestamp, decay level (stored in UserDefaults, NOT in file)
│   └── Workspace.swift           ← Selected folder: bookmark URL, display name, file count
├── Views/
│   ├── ContentView.swift         ← Root: NavigationStack with note list + detail
│   ├── NoteListView.swift       ← List of notes with decay opacity
│   ├── TerminalEditorView.swift ← Blinking cursor, zero-click entry
│   ├── MarkdownEditorView.swift  ← Split: TextEditor (raw) + toggle preview
│   ├── MarkdownPreviewView.swift ← Rendered markdown (using swift-markdown-ui)
│   ├── ShakeSparkView.swift      ← Modal: 3 random sentences from 3 notes
│   ├── SettingsView.swift        ← Config-file aesthetic settings
│   ├── FolderPickerView.swift    ← UIDocumentPicker wrapper
│   ├── SealBadgeView.swift       ← Lock icon + countdown for sealed notes
│   └── EmptyStateView.swift      ← "No notes yet. Start typing." + folder setup
├── Services/
│   ├── FileService.swift         ← All FileManager operations: list, read, write, delete
│   ├── BookmarkService.swift     ← Security-scoped bookmark persistence/resolution
│   ├── MarkdownService.swift      ← Parse markdown to attributed string or AST
│   ├── MetadataService.swift     ← Read/write per-note metadata in UserDefaults
│   ├── ShakeDetector.swift       ← UIEvent.motionShake handler
│   ├── IAPService.swift          ← StoreKit 2: purchase, restore, entitlement check
│   └── ManifestService.swift     ← Generate Manifest.txt in workspace folder
└── Resources/
    ├── Assets.xcassets           ← App icon, accent color
    └── StoreKit Config.storekit  ← IAP product configuration (for testing)
```

### 2.2 State Management

**No Core Data. No SwiftData. No SQLite. No databases.**

| State | Storage | Why |
|-------|---------|-----|
| Workspace folder bookmark | `UserDefaults` (Data — security-scoped URL bookmark) | Persists user's selected folder across app launches |
| IAP purchase state | `UserDefaults` (Bool — `koda.pro.unlocked`) + StoreKit 2 `Transaction.currentEntitlements` | StoreKit 2 handles the source of truth; UserDefaults is cache |
| App settings (theme, decay rate, min opacity) | `@AppStorage` / `UserDefaults` | Simple key-value, no DB needed |
| Per-note metadata (seal timestamp, has been opened) | `UserDefaults` with key pattern `note.{filenameHash}.sealedUntil` | Lightweight, no sidecar files, no xattrs (xattrs don't survive iCloud sync) |
| Note content | **The file itself** | The .md file IS the data. No cache. No copy. Read on open, write on save. |

### 2.3 Data Flow

```
App Launch
  → BookmarkService.resolve() → get security-scoped URL
  → FileService.listFiles(url) → [Note] array
  → NoteListView renders with decay opacity applied
  → User taps note → FileService.read(note.url) → String
  → MarkdownEditorView displays raw markdown in TextEditor
  → User edits
  → On save: FileService.write(note.url, content) → writes .md file
  → iOS iCloud sync happens automatically (we do NOTHING)
  → File coordinate via NSFileCoordinator on read/write (prevents iCloud conflicts)
```

### 2.4 File Access Details

**Workspace selection (one-time setup):**
1. User taps "Choose Folder" in empty state
2. `UIDocumentPickerViewController` with `allowMultipleSelection = false`, `shouldShowFileExtensions = true`
3. User selects any folder (iCloud Drive, On My iPhone, Downloads, etc.)
4. App receives URL with security scope
5. `BookmarkService.save(url.bookmarkData())` → stores in UserDefaults
6. App has permanent access to that folder

**Reading files:**
```swift
// On every app launch and workspace open:
let url = BookmarkService.resolve()  // security-scoped URL
url.startAccessingSecurityScopedResource()
defer { url.stopAccessingSecurityScopedResource() }

let contents = try FileManager.default.contentsOfDirectory(
    at: url,
    includingPropertiesForKeys: [.contentModificationDateKey, .fileSizeKey],
    options: [.skipsHiddenFiles]
)

let notes = contents
    .filter { $0.pathExtension == "md" }
    .map { Note(url: $0, modifiedDate: ..., wordCount: ...) }
```

**Writing files:**
```swift
// Use NSFileCoordinator to prevent iCloud sync conflicts:
let coordinator = NSFileCoordinator()
var error: NSError?
coordinator.coordinate(writingItemAt: noteURL, options: .forReplacing, error: &error) { newURL in
    try content.write(to: newURL, atomically: true, encoding: .utf8)
}
```

**iCloud conflict resolution:**
- The app does NOT handle conflicts programmatically
- `NSFileCoordinator` handles the file-level coordination (prevents two processes writing simultaneously)
- If iCloud detects a conflict (user edited same file on two devices), iCloud keeps BOTH versions: `note.md` and `note (1).md`
- App shows both files in the list. User merges manually by deleting the duplicate.
- This is by design — automatic merge of markdown is dangerous and error-prone
- Document this in the FAQ: "If you see a duplicate file, it means you edited on two devices. Delete the one you don't want."

---

## 3. Feature Technical Specifications

### 3.1 Half-Life Decay View

**Behavior:**
- Notes list is sorted by `contentModificationDate` (most recent first)
- Each note's opacity is calculated by days since last modification:
  - 0-7 days: 100% opacity
  - 8-30 days: linear fade from 100% → 60%
  - 31-90 days: linear fade from 60% → 30%
  - 91+ days: 20% opacity (minimum, still readable)
- Notes do NOT delete. Notes do NOT change content. Purely visual.
- User can configure decay rate in Settings: Fast (3/14/60), Default (7/30/90), Slow (14/60/180), Off
- Tapping a faded note restores it to 100% (because opening it updates the modificationDate... NO — opening should NOT update modificationDate. Only editing does.)
- Opening a note sets a "lastViewedDate" in metadata, but does NOT touch the file. Opacity is based on modificationDate only.

**Implementation:**
```swift
func decayOpacity(for note: Note, rate: DecayRate) -> Double {
    let daysSinceModified = note.modifiedDate.daysFromNow
    switch rate {
    case .off: return 1.0
    case .fast: // 3/14/60
        if daysSinceModified <= 3 { return 1.0 }
        if daysSinceModified <= 14 { return 1.0 - Double(daysSinceModified - 3) / 11.0 * 0.4 }
        if daysSinceModified <= 60 { return 0.6 - Double(daysSinceModified - 14) / 46.0 * 0.4 }
        return 0.2
    case .default: // 7/30/90
        if daysSinceModified <= 7 { return 1.0 }
        if daysSinceModified <= 30 { return 1.0 - Double(daysSinceModified - 7) / 23.0 * 0.4 }
        if daysSinceModified <= 90 { return 0.6 - Double(daysSinceModified - 30) / 60.0 * 0.4 }
        return 0.2
    case .slow: // 14/60/180
        if daysSinceModified <= 14 { return 1.0 }
        if daysSinceModified <= 60 { return 1.0 - Double(daysSinceModified - 14) / 46.0 * 0.4 }
        if daysSinceModified <= 180 { return 0.6 - Double(daysSinceModified - 60) / 120.0 * 0.4 }
        return 0.2
    }
}
```

### 3.2 Terminal Entry (Zero-Click)

**Behavior:**
- When app launches (and a workspace is set), the first thing the user sees is a full-screen text input with a blinking cursor
- There is no "New Note" button
- User starts typing immediately
- If user types `@work Project Alpha` and hits save:
  - App parses prefix: `@work` → folder name `work`
  - If `/work/` doesn't exist in workspace, create it
  - Save file as `work/Project-Alpha-{timestamp}.md` (or first 50 chars of content if no prefix)
  - Content of file: everything after the prefix
- If user types without prefix: save to root of workspace as `{timestamp}.md` or first 50 chars
- Hitting "Enter" twice (blank line) = save note
- Swiping down or tapping "List" button = dismiss terminal, show note list

**Implementation:**
```swift
struct TerminalEditorView: View {
    @State private var text = ""
    @FocusState private var isFocused: Bool
    
    var body: some View {
        VStack {
            // Terminal-style header
            HStack {
                Text("koda:~$ ")
                    .font(.system(.body, design: .monospaced))
                    .foregroundStyle(.secondary)
                Spacer()
                Button("List") { dismiss() }
            }
            
            TextEditor(text: $text)
                .font(.system(.body, design: .monospaced))
                .focused($isFocused)
                .background(Color.black)
            
            // Blinking cursor effect on empty state
            if text.isEmpty {
                Text("█")
                    .font(.system(.body, design: .monospaced))
                    .foregroundStyle(.green)
                    .opacity(isFocused ? 1.0 : 0.3)
                    .animation(.easeInOut(duration: 0.6).repeatForever, value: isFocused)
            }
        }
        .onAppear { isFocused = true }
        .onChange(of: text) { _, newValue in
            // Detect double-newline = save
            if newValue.hasSuffix("\n\n") {
                saveNote(newValue)
            }
        }
    }
    
    func saveNote(_ raw: String) {
        let trimmed = raw.trimmingCharacters(in: .whitespacesAndNewlines)
        let (folder, content) = parsePrefix(trimmed)
        let filename = generateFilename(from: content)
        FileService.write(folder: folder, filename: filename, content: content)
        text = ""
    }
    
    func parsePrefix(_ text: String) -> (folder: String?, content: String) {
        guard text.hasPrefix("@") else { return (nil, text) }
        let parts = text.split(separator: " ", maxSplits: 1)
        let folder = String(parts[0].dropFirst()) // remove @
        let content = parts.count > 1 ? String(parts[1]) : ""
        return (folder, content)
    }
}
```

### 3.3 Shake to Shuffle

**Behavior:**
- User shakes phone (detected via `UIEvent.EventSubtype.motionShake`)
- App picks 3 random .md files from workspace
- From each file, extracts 1 random sentence (non-empty line)
- Displays them in a modal "Spark" card with a dismiss button
- User can tap "Save as Note" → saves the 3 sentences as a new .md file
- User can tap "Shake Again" → re-roll

**Implementation:**
```swift
// In UIViewControllerRepresentable or via UIWindow override
// SwiftUI way: use a UIKit wrapper

struct ShakeDetector: UIViewRepresentable {
    var onShake: () -> Void
    
    func makeUIView(context: Context) -> UIView {
        let view = ShakeUIView()
        view.onShake = onShake
        return view
    }
    func updateUIView(_ uiView: UIView, context: Context) {}
}

class ShakeUIView: UIView {
    var onShake: (() -> Void)?
    override var canBecomeFirstResponder: Bool { true }
    override func motionEnded(_ motion: UIEvent.EventSubtype, with event: UIEvent?) {
        if motion == .motionShake { onShake?() }
    }
}

// Spark generation:
func generateSpark() -> [String] {
    let allNotes = FileService.listNotes()
    guard allNotes.count >= 3 else { return [] }
    let randomNotes = Array(allNotes.shuffled().prefix(3))
    return randomNotes.map { note in
        let content = FileService.read(note.url)
        let lines = content.split(separator: "\n").filter { !$0.trimmingCharacters(in: .whitespaces).isEmpty }
        let randomLine = lines.randomElement() ?? ""
        return String(randomLine)
    }
}
```

### 3.4 24-Hour Seal

**Behavior:**
- After saving a note, a "Seal" button appears
- Tapping it sets `note.{hash}.sealedUntil = Date() + 24h` in UserDefaults
- Sealed notes show a lock icon with countdown ("Unlocks in 18h 32m")
- Attempting to edit a sealed note shows: "This note is sealed. Unlocks in 18h."
- User CAN override: long-press seal icon → "Break seal?" → confirms → removes seal
- This is friction-by-design, not a hard lock

**Implementation:**
```swift
struct NoteMetadata {
    static func sealDate(for filename: String) -> Date? {
        let key = "seal.\(filename.hashValue)"
        return UserDefaults.standard.object(forKey: key) as? Date
    }
    
    static func seal(_ filename: String) {
        let key = "seal.\(filename.hashValue)"
        UserDefaults.standard.set(Date().addingTimeInterval(86400), forKey: key)
    }
    
    static func breakSeal(_ filename: String) {
        let key = "seal.\(filename.hashValue)"
        UserDefaults.standard.removeObject(forKey: key)
    }
    
    static func isSealed(_ filename: String) -> Bool {
        guard let until = sealDate(for: filename) else { return false }
        return Date() < until
    }
}
```

### 3.5 Read-Only Toggle

**Behavior:**
- A toggle switch in the navigation bar of the note editor
- OFF (default): Edit mode — `TextEditor` with raw markdown
- ON: Read mode — rendered markdown via `MarkdownPreviewView`, no editing possible
- Toggling is instant, no animation delay
- State is per-note (remembered per file via UserDefaults: `note.{hash}.readOnly`)

### 3.6 Sysadmin Manifest

**Behavior:**
- On every app launch, write a `Manifest.txt` to the root of the workspace folder
- Contents:

```
# KODA Workspace Manifest
# Generated: 2026-06-27 21:00:00 UTC
# KODA Version: 1.0.0
#
# This file is auto-generated by KODA: Notes.
# It describes the .md files in this folder.
# If you delete KODA, this file remains.
# Your notes are plain text. They don't need KODA.

Files: 247
Total words: 89,432
Last modified: 2026-06-27 18:32:01

# --- File Index ---
# filename.md | words | modified
# project-alpha.md | 1,204 | 2026-06-27
# meeting-notes.md | 340 | 2026-06-25
# ...
```

- This file is NOT a .md file (so the app ignores it in the note list)
- If user deletes the file, app regenerates on next launch
- This is the "I was just a guest" proof — when the app is gone, the manifest explains what the files are

**Implementation:**
```swift
func generateManifest(at workspaceURL: URL, notes: [Note]) {
    let manifestURL = workspaceURL.appendingPathComponent("Manifest.txt")
    let totalWords = notes.reduce(0) { $0 + $1.wordCount }
    let lines = ["# KODA Workspace Manifest", "# Generated: \(ISO8601DateFormatter().string(from: Date()))", ...]
    let fileIndex = notes.map { "# \($0.filename) | \($0.wordCount) | \($0.modifiedDate.shortDate)" }
    let content = (lines + fileIndex).joined(separator: "\n")
    try? content.write(to: manifestURL, atomically: true, encoding: .utf8)
}
```

### 3.7 Markdown Rendering

**Library choice:** `swift-markdown-ui` (https://github.com/gonzalezreal/swift-markdown-ui)
- Rationale: Pure SwiftUI renderer (no WebView, no UIKit). Renders markdown directly as SwiftUI views. Supports GitHub-flavored markdown. No HTML intermediate step.
- Not `Ink` — Ink produces HTML, would need WebView. We don't want WebView (performance, security, native feel).
- Not `MarkdownView` — that's a WebView wrapper. Same problem.

**Implementation:**
```swift
import MarkdownUI

struct MarkdownPreviewView: View {
    let content: String
    var body: some View {
        Markdown(content)
            .markdownTheme(.basic)
            .markdownTextStyle(\code) { font(.system(.body, design: .monospaced)) }
    }
}
```

### 3.8 IAP (StoreKit 2)

**Products:**

| Product ID | Type | Price | Description |
|------------|------|-------|-------------|
| `koda.notes.pro` | Non-consumable | $9.99 (launch) → $14.99 → $19.99 | Unlock all Pro features |
| `koda.bundle.lifetime` | Non-consumable | $49.00 | All current + future apps (when App #2 ships) |

**Free vs Pro gating:**

| Feature | Free | Pro |
|---------|------|-----|
| Create/edit/delete notes | ✅ | ✅ |
| Basic markdown rendering | ✅ | ✅ |
| One workspace folder | ✅ | ✅ |
| Light/dark mode | ✅ | ✅ |
| Half-life decay view | ❌ | ✅ |
| Terminal entry | ❌ | ✅ |
| Shake to shuffle | ❌ | ✅ |
| 24-hour seal | ❌ | ✅ |
| Read-only toggle | ❌ | ✅ |
| Multiple workspace folders | ❌ | ✅ |
| Custom themes | ❌ | ✅ |
| Sysadmin Manifest | ✅ | ✅ (this is a brand feature, always free) |

**Implementation:**
```swift
import StoreKit

@Observable
final class IAPService {
    var isPro: Bool = false
    
    func checkEntitlement() async {
        for await result in Transaction.currentEntitlements {
            if case .verified(let transaction) = result {
                if transaction.productID == "koda.notes.pro" {
                    isPro = true
                    return
                }
            }
        }
        isPro = false
    }
    
    func purchase() async throws {
        let product = try await Product.products(for: ["koda.notes.pro"]).first!
        let result = try await product.purchase()
        if case .success(let verification) = result {
            if case .verified(let transaction) = verification {
                isPro = true
                await transaction.finish()
            }
        }
    }
}
```

---

## 4. UI/UX Specifications

### 4.1 Navigation Structure

```
NavigationStack
├── NoteListView (root)
│   ├── Navigation bar: "KODA" title + Settings gear + New Note (Terminal) button
│   ├── List of notes with decay opacity
│   ├── Swipe to delete
│   ├── Tap to open → MarkdownEditorView
│   └── Empty state if no notes: "Start typing" prompt
├── TerminalEditorView (presented fullscreen)
│   ├── Blinking cursor, monospace input
│   ├── Prefix parsing (@folder)
│   ├── Save on double-newline or button
│   └── Dismiss to return to list
├── MarkdownEditorView (pushed)
│   ├── Navigation bar: note title + Read-Only toggle + Seal button
│   ├── Toggle: Edit mode (TextEditor) ↔ Preview mode (MarkdownUI)
│   ├── Auto-save on text change (debounced 1.5s) or on close
│   └── Swipe actions: delete
└── SettingsView (sheet)
    ├── Workspace: current folder + change button
    ├── Appearance: dark/light/auto
    ├── Decay rate: off / fast / default / slow
    ├── Pro status: locked/unlocked + purchase button
    ├── About: version, philosophy, end-of-life guarantee
    └── Reset: clear metadata, change workspace
```

### 4.2 Markdown Editor Detail

- **Edit mode:** `TextEditor` with `.monospaced` font, line numbers optional (V2)
- **Preview mode:** `MarkdownUI` rendering with basic theme, accent color applied
- **Toggle:** segmented control in nav bar: `[ Edit | Preview ]`
- **Auto-save:** debounce 1.5s after last keystroke. Show "Saved" indicator.
- **Read-only toggle:** when ON, forces Preview mode and disables Edit toggle
- **Seal button:** lock icon. When sealed: shows countdown, long-press to break.

### 4.3 Empty State

When no workspace is selected:
```
   [KODA icon]

   Your notes. Your files.
   Your rules.

   Choose a folder to start.
   (Any iCloud Drive or local folder)

   [ Choose Folder ]
```

When workspace is set but no notes:
```
   No notes yet.

   Just start typing.

   [ Start Writing ] ← opens Terminal
```

### 4.4 Color Application

| Element | Light Mode | Dark Mode |
|---------|-----------|-----------|
| Background | #FAFAFA | #0D0D0D |
| Surface (cards) | #FFFFFF | #1A1A1A |
| Text primary | #1A1A1A | #E8E8E8 |
| Text secondary | #888888 | #999999 |
| Accent | #00C853 (green) | #00FF88 (bright green) |
| Divider | #E0E0E0 | #333333 |
| Note decay | opacity reduction | opacity reduction |

### 4.5 Typography

| Use | Font | Size | Weight |
|-----|------|------|--------|
| App title (nav bar) | SF Pro Display | 17pt | Bold |
| Note title in list | SF Pro Text | 16pt | Semibold |
| Note preview in list | SF Pro Text | 13pt | Regular |
| Editor text | SF Mono | 15pt | Regular |
| Markdown body | SF Pro Text | 16pt | Regular |
| Markdown headings | SF Pro Display | 24pt | Bold |
| Markdown code | SF Mono | 14pt | Regular |
| Settings labels | SF Pro Text | 16pt | Regular |
| Terminal cursor | SF Mono | 15pt | Regular |

---

## 5. Dependencies

| Package | Version | Purpose | SPM URL |
|---------|---------|---------|---------|
| `swift-markdown-ui` | 2.4.0+ | Markdown rendering in SwiftUI | https://github.com/gonzalezreal/swift-markdown-ui |

**That's the only dependency.** Everything else is Apple-native:
- StoreKit 2 (IAP)
- FileManager (file I/O)
- UIDocumentPicker (folder selection)
- NSFileCoordinator (conflict prevention)
- UserDefaults (settings, metadata, bookmarks)
- CoreMotion (shake detection — actually UIEvent.motionShake, no import needed)

---

## 6. Testing

### Unit Tests (XCTest)

| Test | What it validates |
|------|-------------------|
| `FileServiceTests` | Create, read, write, delete .md files in temp directory |
| `BookmarkServiceTests` | Save and resolve security-scoped bookmarks |
| `MetadataServiceTests` | Seal/break seal, expiry check |
| `MarkdownServiceTests` | Prefix parsing (@folder), filename generation |
| `DecayServiceTests` | Opacity calculation for all decay rates and edge cases |
| `IAPServiceTests` | Mock StoreKit transactions, entitlement checking |

### UI Tests

| Test | What it validates |
|------|-------------------|
| `OnboardingTests` | First launch → choose folder → see empty state → write first note |
| `NoteCRUDTests` | Create note via Terminal → see in list → edit → delete |
| `DecayTests` | Create notes with old modification dates → verify opacity in list |
| `SealTests` | Seal a note → attempt edit → blocked → break seal → edit works |
| `ShakeTests` | Shake → spark modal appears → save as note |
| `IAPTests` | Mock purchase → Pro features unlock → restore purchases |

### Manual Testing Checklist

- [ ] Fresh install, select iCloud Drive folder, create note, verify file appears in Files app
- [ ] Create note on iPhone, verify it appears on iPad (iCloud sync)
- [ ] Edit same note on two devices → verify iCloud creates duplicate (not crash)
- [ ] Test with 1,000+ .md files — list scroll performance
- [ ] Test with "Optimize Storage" on — verify graceful handling of evicted files
- [ ] Test offline — create/edit notes with no internet
- [ ] Test shake on real device (simulator doesn't support shake)
- [ ] Test IAP purchase flow and restore
- [ ] Test dark/light mode switching
- [ ] Verify Manifest.txt is created and correct
- [ ] Delete the app → verify .md files and Manifest.txt still exist in Files app

---

## 7. App Store Configuration

| Field | Value |
|-------|-------|
| App Name | KODA: Notes |
| Subtitle | Markdown notes. No subscription. |
| Primary Category | Productivity |
| Secondary Category | Utilities |
| Keywords (100 char) | `markdown,notes,offline,privacy,local,editor,no subscription,plain text` |
| Age Rating | 4+ (no user content) |
| Privacy: Data Collected | None |
| Privacy: Data Used for Tracking | No |
| Privacy: Data Linked to You | No |
| Bundle ID | com.koda.notes |
| Team | User's Apple Developer account |
| iCloud Entitlement | NOT needed (we use UIDocumentPicker, not iCloud Documents) |
| StoreKit Configuration | koda.notes.pro (non-consumable, $9.99) |

### Screenshots (6 required)

1. **Terminal entry** — blinking cursor, monospace text, "koda:~$" prompt. Caption: "Just start typing."
2. **Decay view** — note list with varying opacity. Caption: "Notes you ignore fade away."
3. **Markdown preview** — rendered markdown with accent green code blocks. Caption: "Your notes, beautifully rendered."
4. **Files app** — .md files visible in iOS Files app. Caption: "Plain text files. In YOUR iCloud."
5. **Settings** — config-file aesthetic. Caption: "No account. No login. No tracking."
6. **Comparison** — text overlay: "Notion: $120/year. KODA: $15 once." Caption: "Pay once. Own forever."

---

## 8. Build Instructions for AI Agents

### To build KODA: Notes from this repo:

```bash
# 1. Clone
git clone https://github.com/yibi/koda.git
cd koda

# 2. Read the specs
# Read SPECS/NOTES_APP_SPEC.md (features)
# Read DESIGN/BRAND_GUIDE.md (visual identity)
# Read this file (TDD) for all technical decisions

# 3. Create Xcode project
# - New Project > App
# - Product Name: KODA Notes
# - Bundle ID: com.koda.notes
# - Interface: SwiftUI
# - Language: Swift
# - Minimum Deployments: iOS 17.0
# - Save to SOURCE/ directory

# 4. Add dependency
# Xcode > File > Add Package Dependencies
# URL: https://github.com/gonzalezreal/swift-markdown-ui
# Add to target

# 5. Build the app following the structure in section 2.1
# All file names and locations are specified
# All feature behaviors are specified in section 3
# All colors and fonts are specified in section 4

# 6. Test
# Run unit tests in section 6
# Run on iPhone 15 Pro simulator
# Test on real iPhone if possible

# 7. Commit
git add -A
git commit -m "feat: KODA Notes MVP implementation"
git push
```

### Agent Instructions

1. Follow this TDD exactly. Do not deviate from the architecture.
2. Use SwiftUI only. No UIKit except `UIDocumentPickerViewController` (wrapped in `UIViewControllerRepresentable`).
3. The only third-party dependency is `swift-markdown-ui`. Do not add others.
4. All file I/O goes through `FileService.swift`. Never use `FileManager` directly in views.
5. All metadata goes through `MetadataService.swift`. Never access `UserDefaults` directly in views.
6. All IAP goes through `IAPService.swift`. Never call StoreKit directly in views.
7. Write unit tests for every service. Write them BEFORE the implementation (TDD).
8. If something is ambiguous, check the spec. If still ambiguous, make the simplest choice and document it in a comment.
9. The app has NO backend. If you find yourself writing networking code, you are wrong.
10. The app has NO database. If you find yourself writing Core Data or SwiftData, you are wrong.