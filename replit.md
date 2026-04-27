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
