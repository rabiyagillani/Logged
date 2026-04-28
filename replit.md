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
