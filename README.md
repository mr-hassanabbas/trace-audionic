# Audionic Price Check: "Is the sale real?"

A Chrome extension that records Audionic's prices over time and tells you, product by product, whether an advertised
discount is real, using evidence from the prices it has actually seen.

## Install (2 minutes)

1. Unzip the folder `audionic-price-check`.
2. Open Chrome (or Edge) and go to `chrome://extensions` (Edge: `edge://extensions`).
3. Turn on **Developer mode** (top right).
4. Click **Load unpacked** and choose the `audionic-price-check` folder (the one that contains `manifest.json`).
5. Pin the extension (puzzle icon, then the pin) so you can see its badge.

It downloads the Audionic catalogue straight away and checks again every 6 hours while Chrome is open.

## How to use it

- Open any product on audionic.co. A small card appears at the bottom-right with the verdict, the claimed vs real
  discount and a 90-day price line. Click the toolbar icon for the full details.
- **Product**: verdict, reasons, stats, price-history chart (7D / 30D / 90D / All), watchlist, notes, share card (PNG).
- **Watchlist**: set a target price and get notifications for price drops, target reached, back in stock, and
  "discount claim raised without a price drop".
- **Sale Audit**: every Audionic product in one table with claimed %, real %, and verdict. Filter and sort, "fakest first".
- **History**: every change the extension saw, store-wide sale events (shown on charts), CSV/JSON export and import.
- **Settings**: check interval, notifications, thresholds, theme, optional older-price sources, clear data.

## What the verdicts mean

| Verdict | Meaning |
|---|---|
| Real deal | The price really fell (at least 10%, and at least half of the claimed discount) from the highest price charged in the last 90 tracked days. |
| Misleading | A discount is shown, but the price fell far less than claimed, or the crossed-out price was raised without a price drop. |
| Fake-looking sale | A claim of 20% or more, the "original" price was never seen charged, and the price has not changed for 14+ days. |
| No discount claimed | No crossed-out price is shown. |
| Collecting data | Less than 3 days of data (or under 14 days for a flat price). Not enough to judge yet. |

Verdicts describe what tracked prices show over the tracked period. They are never accusations, and the confidence line
("Based on 60 d of tracked data") tells you how much evidence is behind them. All thresholds can be changed in Settings.

## Getting history immediately (recommended)

Audionic only shows today's prices, so a brand-new install knows nothing about last week or last month.
Two optional ways to fix that:

1. **Collector (best)**: follow `collector/README.md` to run a free GitHub job that records prices every 6 hours.
   Paste its `history.json` link into Settings. You then have history for as long as the collector has run.
2. **Internet Archive backfill (best effort)**: Settings, turn on "Archive backfill", then use the button in the Product tab.
   Results are approximate and often sparse; they are marked as such on the chart.

## Privacy

No account, no analytics. Data stays in your browser. The extension only contacts `audionic.co`, plus
`web.archive.org` and `raw.githubusercontent.com` if you enable those options (Chrome asks your permission first).

## Files

```
manifest.json          Manifest V3
background.js          service worker: scheduled sync, notifications, message router
content/content.js     overlay card, verdict dots, urgency spotter (runs on audionic.co)
popup/                 popup UI (tabs/ = one file per tab, share.js = PNG card)
lib/                   audit logic, sync engine, chart, storage, API, archive parser (pure, unit-tested)
collector/             optional GitHub price collector
tests/                 unit tests: node --test tests/audit.test.mjs tests/lib.test.mjs
```

## If something breaks

- **"Couldn't load the Audionic catalogue"**: check your internet. If it says the store refused the request (403/404), Audionic
  may have turned off its public product list (`audionic.co/products.json`), and `lib/api.js` would need a new data source.
- **No card on a product page**: the product must be in the catalogue first. Click the refresh button in the popup, then reload the page.
- **Verdict says "Collecting data"**: normal on day one. Verdicts need a few days of tracked prices (or use the collector).
- **Developer-mode warning on Chrome start**: normal for unpacked extensions; dismiss it.
