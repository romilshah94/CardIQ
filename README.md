# CardIQ

Personal credit card strategy app for an Indian premium card stack — one self-contained `index.html`, installable as a PWA.

**Live:** https://romilshah94.github.io/CardIQ/

## Tabs
- **⚡ Spend** — best card per category with net return after forex/fees; live milestone-adjusted comparison for INR spend.
- **🔀 Routes** — interactive network graph of every transfer partner reachable from Axis EDGE Miles, HDFC RP, HSBC points and Amex MR, including 2–3 hop chains (Avios pool, Accor → airlines, Marriott → airlines). Tap a program to see every route ranked by ₹ value per bank point; tap a bank to see its best exits. Valuations are editable and saved on the device.
- **🎯 Miles** — DCB quarterly / fee waiver, Amex Plat Travel, MRCC monthly bonuses. Progress is saved and resets automatically each quarter/month.
- **💳 Cards · 📊 Strategy · 📰 News · 🏅 Loyalty**
- **💬 Chat** — live Q&A with Claude (`claude-haiku-5-5`), streaming, optional web search, with the full card + transfer data as context.

## AI chat setup
Tap 💬 → 🔑 and paste an Anthropic API key. The key is stored only in that browser's `localStorage` — it is never in this repo. Set a monthly spend limit on the key in the Anthropic Console.

## Install on iPhone
Open the live link in Safari → Share → **Add to Home Screen**. Runs full-screen and works offline (chat needs a connection).

## Updating data
Card terms live in plain JS arrays near the top of the script: `MS_DEFS`, `CATS`, `CARDS`, `TX_CUR` / `TX_PROG` / `TX_EDGES`, `NEWS`. Bump `DATA_ASOF` and the `VERSION` in `sw.js` when you change them.

Data last verified: 21 Nov 2026. Ratios change without notice — confirm on the bank portal before transferring.
