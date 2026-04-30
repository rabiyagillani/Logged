# Logged — Remember Everything

A privacy-focused personal journaling and record-keeping Progressive Web App (PWA).

## About

Logged helps users record incidents (workplace issues, landlord disputes, personal health, etc.) with verifiable proof through SHA-256 integrity hashing and optional Bitcoin blockchain timestamping via OpenTimestamps.

## Tech Stack

- **Frontend**: Vanilla JavaScript (ES6+), HTML5, CSS3 — single-file architecture (`index.html`)
- **Storage**: LocalStorage (metadata/settings) + IndexedDB (photos, voice memos)
- **Auth/Sync**: Supabase (OTP auth + optional cloud sync) — SDK inlined in `index.html`
- **Integrity**: Web Crypto API (SHA-256 hashing) + OpenTimestamps (Bitcoin anchoring via CDN)
- **PWA**: Service Worker (`sw.js`) + Web App Manifest (`manifest.webmanifest`)
- **No build step** — fully static, no bundler or package manager

## Project Structure

```
index.html          # Entire app (HTML + CSS + JS, ~800KB)
sw.js               # Service Worker for offline/PWA support
manifest.webmanifest # PWA configuration
favicon.svg         # App icon
README.md           # Brief project note
```

## Development

The app is served via Python's built-in HTTP server:

```
python3 -m http.server 5000 --bind 0.0.0.0
```

## Deployment

Configured as a **static** deployment — files served directly with no backend.

## Recent Updates (Apr 2026 — Home Page Refresh)

Implemented a full set of home-page improvements based on expert review:

- **Trust-focused stat tiles** (4 chips): Total · Verified · Anchored · Last log — replacing the gamified day-streak.
- **Pending-anchor inline status** when entries are awaiting Bitcoin confirmation.
- **Quick voice / capture button** placed beside the New Entry button for one-tap capture.
- **Date filter chips** (All / Today / Week / Month / Year) above the records list.
- **Month dividers** in the records list when sorting chronologically.
- **Export Report** CTA on the home page (uses existing `exportAllPDF`).
- **Draft autosave + resume**: form inputs autosave to `localStorage` (`logged_draft_v1`); a "Continue draft" card appears on home. Cleared automatically on save.
- **Richer empty state**: lock icon, starter category chips, and a privacy-promise card highlighting the SHA-256 + Bitcoin + local-first guarantees.
- **App Lock (PIN)**: optional 4–8 digit PIN with SHA-256-hashed storage (`logged_pin_hash`). Settings → Privacy section to enable/change. Lock screen shown at boot and on visibility-resume after 60s. Custom on-screen number pad.

## Recent Updates (Apr 2026 — Light Mode as Default)

- **Light mode is now the default theme** (was dark). Stored under `logged_theme` key.
- **Removed "Install app" entry** from settings (PWA install prompt still works natively in browsers).
- **Privacy/Terms modals**: replaced placeholders with comprehensive store-compliant policies (Apple/Google), GDPR/CCPA, account deletion, arbitration/class waiver, AS-IS disclaimers, $100/12-mo liability cap, indemnity. Governing law: UAE (DIAC arbitration). Contacts: privacy@/legal@/security@/copyright@loggedapp.io.
- **Light-mode polish**: fixed splash + onboard logos (bracket text was invisible on the dark wine tile that `--cream` resolves to in light mode); fixed home empty-state ("Ready when you are") which used inherited muted color and was nearly invisible — now has a proper hierarchy (dark-wine title, soft icon tile, rose sub copy, amber promise icons).
- **Deep-link hash router**: opening the app with `#<screenId>` (e.g. `#settingsScreen`) jumps straight to that screen and skips the splash.

## Recent Updates (Apr 2026 — Brutal-Critique Pass v1)

Surgical fixes for the six items the user approved after a top-to-bottom critique of integrity, privacy, UX honesty, and organisation. **Key principle: no feature ships unless it actually does what it claims.**

### Shipped

- **Honest evidence hash (#1)**: `buildEvidenceManifest()` now constructs a versioned `{v, cat, title, note, ts, imp, gpsLat, gpsLng, photoHashes[], voiceHash, voiceDuration}` manifest covering *all* evidence — including per-photo and voice SHA-256 hashes. `entry.fp` is the SHA-256 of the canonical-JSON of that manifest, and `entry.manifest` is persisted alongside. OpenTimestamps stamps `entry.fp` directly (single source of truth). Detail card and verify view both surface a "Hash covers" / "What this proof covers" pill row in plain English. Pre-v2 entries fall back gracefully and are labeled "(text only — pre-v2)" so the UI never overstates what's protected.
- **PIN brute-force protection (#2 attempts)**: 5 wrong PINs → 30s lock, then 1m / 5m / 15m / 1h ladder. Persisted in `logged_pin_attempts` + `logged_pin_locked_until` so reloading doesn't bypass the lockout. Live countdown on the lock screen with attempts-remaining message. Counter resets on success.
- **Anchor status surface (#3)**: Compact home bar — `✓ X confirmed · ⏳ Y pending · ⟲ Z queued` plus a "Retry now" button that calls `retryAllQueued()`. Per-entry detail card now includes a "Retry anchoring" call-to-action when the entry is queued (offline at save) and a "Re-check / re-stamp" warning when pending > 48h.
- **Verify-page upgrade (#5)**: Verify URL payload now includes a slim `mf` (manifest summary: photo count, voice flag, gps flag, has-note flag, importance) so the recipient of a share link sees a "What this proof covers" block in plain language, plus a collapsible "What does Bitcoin verification actually mean?" explainer. No more raw 64-char hex with no context.
- **Cases / threads (#7)**: Optional `entry.caseId` linking to records in `logged_cases_v1`. New "Cases" section on Insights, picker modal (None / existing / + Create new) on form *and* detail screens, case filter chip strip on home, and an in-app filter pill that's clearable.
- **Tags first-class (#9)**: Optional `entry.tags[]`. Chip input on the form (Enter / comma / Tab to add, Backspace to remove last, blur auto-commits), recent-tag suggestions row sourced from existing entries, top-tag chip strip on home, search now matches `#tag` as well as title/note/category.

### Staged (NOT shipped — too risky for a single session)

These need careful migration UX (passphrase setup, recovery warnings, schema migration) and will be tracked here:

- **#2 part 2 — Full data encryption at rest**. Plan: opt-in passphrase flow → derive key with PBKDF2 (200k+ iterations, per-device salt) → re-encrypt entries on enable → display recovery warning that losing the passphrase = losing data → background re-encrypt with progress bar. Fallback: keep current plaintext storage for users who decline.
- **#4 — End-to-end sync encryption**. Plan: add `encrypted_payload` column to Supabase `entries` table → encrypt with the same passphrase-derived key before sync → server only ever sees ciphertext + opaque metadata (id, ts, fp). Requires schema migration, salt persistence per account, and a passphrase-recovery story.

### QA / dev hooks

The boot block accepts a `?qa=cat:<id>` / `?qa=detail:<id>` / `?qa=insights` query param (no-op in normal use) used during development to deep-link into specific screens with seeded localStorage. Safe to leave in — has no effect without explicit query string.
