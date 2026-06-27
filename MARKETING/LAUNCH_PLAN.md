# KODA — Launch Plan

## Launch Strategy: "The Anti-Launch"

Don't launch like a SaaS product. Launch like a manifesto.

### Core Narrative
*"You don't own your notes. Notion owns them. Apple owns them. Evernote definitely owns them. KODA is the exit ramp. Pay $15 once. Your notes live as plain text files in your iCloud. If KODA disappears tomorrow, your files are still there. No subscription. No lock-in. No hostage situation."*

---

## Pre-Launch (2 weeks before)

### Week -2
| Day | Task |
|-----|------|
| Mon | Post Notion → Markdown migration script on GitHub. Share on r/selfhosted, r/markdown. |
| Wed | Post "Export or Die" manifesto on GitHub README. Share on HN (not Show HN yet). |
| Fri | Share dev journey screenshots on Mastodon (fosstodon.org). |

### Week -1
| Day | Task |
|-----|------|
| Mon | Open TestFlight beta. Recruit 50 testers from Reddit, HN, Mastodon. |
| Wed | Prepare App Store listing. Write description, create screenshots. |
| Thu | Build landing page (simple HTML). "SaaS Ransom Calculator" web tool live. |
| Fri | Prepare Show HN draft. Prepare Product Hunt listing. Dry run. |

---

## Launch Day (Saturday EST)

**Why Saturday?** Less competition on HN. Hobbyist crowd is active. Not buried by corporate news.

### Schedule (EST → SGT)

| Time EST | Time SGT | Action | Where |
|----------|----------|--------|-------|
| 08:00 | 21:00 | Show HN post | Hacker News |
| 08:05 | 21:05 | Product Hunt launch | producthunt.com |
| 08:30 | 21:30 | Reddit posts | r/Apple, r/markdown, r/selfhosted |
| 09:00 | 22:00 | Mastodon post | fosstodon.org |
| 09:30 | 22:30 | Lobste.rs post | lobste.rs |
| 10:00+ | 23:00+ | Respond to EVERY comment | All platforms |

### Show HN Post

**Title:** `Show HN: I spent 30 years in data centers and built a notes app that doesn't own your data`

**Body:**
```
I'm a sysadmin who got tired of paying rent on my own thoughts.

Every notes app I tried wanted $8-15/month forever. Notion, Obsidian Sync, Evernote — all subscription traps. And if they go down, my notes go with them.

So I built KODA: Notes. It's a markdown editor that saves plain .md files to YOUR iCloud Drive. No backend. No database. No subscription.

- Pay $15 once. That's it.
- Files are plain markdown. If KODA dies, your notes survive.
- No analytics. No tracking. No account needed.
- Open source guarantee: if I abandon this, I open-source the code.

It has a few things other notes apps don't:
- Notes "decay" if you don't touch them (they fade away)
- Shake your phone to get random "sparks" from old notes
- Opens directly to a blinking cursor — no "New Note" button

Built with SwiftUI. Uses your existing iCloud storage. No server required.

I'd love feedback. Especially on the iCloud sync — that's the hard part.
```

### Product Hunt

**Title:** `KODA: Notes — The app that doesn't want to keep you`
**Tagline:** `Markdown notes. Your files. Your cloud. Pay once, own forever.`

### Reddit Posts

**r/Apple:** *"After 30 years in IT, I built the notes app I always wanted: plain .md files in your own iCloud, no subscription, pay $15 once."*

**r/selfhosted:** *"Not exactly self-hosted, but same philosophy: markdown notes that live as plain files in your iCloud. No backend. No vendor lock-in."*

**r/markdown:** *"A new markdown editor for iOS that saves .md files to your own storage. No proprietary format."*

---

## Post-Launch (First 48 Hours)

### The "Grab Hack"
You'll be driving. But launch only happens once.

1. Set up iOS Text Replacement shortcuts for common responses
2. Respond at traffic lights (safely)
3. Be the "grumpy but honest sysadmin" — people love authenticity
4. Don't be salesy. Be genuine. Answer questions honestly, including criticisms.

### Monitoring

| What | Where | How often |
|------|-------|-----------|
| Sales | App Store Connect | Every 2 hours |
| Crashes | Xcode Organizer | Every 4 hours |
| Comments | HN, PH, Reddit, Mastodon | Every 30 min (respond immediately) |
| App Store reviews | App Store Connect | Every 2 hours |
| Landing page traffic | Simple analytics (or server logs) | Daily |

### First 48 Hours Targets

| Metric | Target | Stretch |
|--------|--------|---------|
| Sales | 50 | 200 |
| HN points | 50 | 200+ (front page) |
| Product Hunt upvotes | 100 | 500+ (Top 5) |
| Reddit upvotes | 200 total | 1,000+ |
| TestFlight signups (post-launch) | 100 | 500 |
| App Store rating | 4.5+ | 5.0 |

---

## Post-Launch (Weeks 1-4)

| Week | Action |
|------|--------|
| 1 | Bug fixes only. Respond to every review. Post update notes. |
| 2 | Reach out to "Digital Minimalism" YouTubers (Matt D'Avella types). Offer free Pro for review. |
| 3 | Post "competitor death watch" — when Notion/Evernote has an outage, reply on Twitter/Reddit with KODA link. |
| 4 | First feature update (based on user feedback). Post update on HN/Reddit. |

---

## Marketing Assets Checklist

- [ ] Landing page (koda.app or similar)
- [ ] SaaS Ransom Calculator (web tool)
- [ ] Notion → Markdown migration script (Python, GitHub)
- [ ] "Export or Die" manifesto (GitHub README)
- [ ] App Store screenshots (6, using Shot.at)
- [ ] App Store preview video (15-30s)
- [ ] App icon (Fiverr designer)
- [ ] Privacy policy page (simple HTML)
- [ ] End of Life guarantee (in ToS)
- [ ] GitHub repo with README (this repo)