# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

CardIQ is a personal credit card strategy app for Romil, a specific Indian cardholder. The entire app is a single `index.html` file containing all CSS, HTML, and JavaScript inline. There is no build system, no package manager, no framework, and no external dependencies beyond Google Fonts (loaded via CDN). To "run" the app, open `index.html` directly in a browser or serve it with any static file server:

```bash
# Simplest option
python3 -m http.server 8080
# Then visit http://localhost:8080
```

There are no lint, test, or build commands.

## Architecture

**Everything lives in `index.html`** in three consecutive sections:

1. **`<style>`** — All CSS using custom properties defined on `:root`. No class name conventions like BEM; classes are short abbreviations (`.gcard`, `.stitle`, `.ntab`). Sections are commented with `/* ── TAB NAME ── */`.

2. **`<body>`** — Static shell only: fixed header, 6 empty `<div class="screen">` containers, the floating chat panel DOM, and the bottom nav. Tabs are rendered empty on load; content is injected by JavaScript.

3. **`<script>`** — Vanilla JS, no modules. Structured as:
   - **Data constants** at the top: `MS_DEFS`, `CATS`, `CARDS`, `RETURNS`, `NEWS`, `PROGRAMS`
   - **Tab builder functions** (`buildSpend`, `buildMiles`, `buildCards`, `buildOpt`, `buildNews`, `buildLoyalty`) that write innerHTML into each screen div
   - **Event handlers** for tab switching (`go`), chat (`toggleChat`, `send`, `reply`), milestone tracking (`updateMs`, `txnPip`), loyalty (`saveLoy`, `updateLoyProg`)
   - **`INIT` block** at the bottom calls all six `buildXxx()` functions once on page load

## Data Model

All data is hardcoded constants — no API calls, no server. The chat `reply()` function uses regex matching against the hardcoded data, not an LLM.

| Constant | Purpose |
|---|---|
| `MS_DEFS` | Milestone definitions: card name, spend threshold, reward value, deadline, effective rate math |
| `MS` | Runtime milestone state — user-editable spend amounts, in-memory only (resets on refresh) |
| `CATS` | Spend category routing: best card, alternatives, return %, dynamic card ranking |
| `CARDS` | Card details: earn rates, tags, info text, grad colors |
| `RETURNS` | Flat list for the Strategy tab returns table |
| `NEWS` | News articles: title, summary, tags, source, URL, impact notes |
| `PROGRAMS` | Loyalty program definitions: status, progress targets, perks, partners |

## Persistence

Only loyalty account data persists across page loads, via `localStorage` through the `lsGet`/`lsSet` helpers. Milestone spend inputs (`MS` object) reset on every page load — they are in-memory only.

## Key Conventions

**Personalisation is hardcoded.** The header subtitle ("Romil · Accor ALL @ ₹2.1/pt"), all card recommendations, reward valuations (₹2.1/pt Accor ALL, ₹1.5 CPP Amex MR, etc.), milestone defaults, and loyalty program statuses reflect Romil's specific wallet. When updating financial data, change the constants directly.

**Effective rate math** flows through `msEffRate(def)` → `msBoost(def)` → `msRemaining(def)`. The "milestone boost" is the extra % return earned on remaining spend before hitting a milestone threshold. This drives both the Miles tab and the dynamic Spend tab recommendations.

**Tab rendering** is imperative innerHTML injection. Each `buildXxx()` generates a complete HTML string and sets `el.innerHTML`. Partial updates (e.g., after user input) re-render only the affected sub-element by targeting its `id` directly.

**CSS design tokens** control all colors and spacing. Before adding new colors, check `:root` — `--accent` (sky blue), `--gold`, `--green`, `--red`, `--purple`, `--amber`, `--text`, `--text2`, `--muted` cover all current cases.

**Mobile-first layout.** The app is designed for iOS/Android home screen (PWA-style). `env(safe-area-inset-*)` is used for notch/home-bar padding. The viewport is locked (`user-scalable=no`). Test UI changes at ~390px wide.
