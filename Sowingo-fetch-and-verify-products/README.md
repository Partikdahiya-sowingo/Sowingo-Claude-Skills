# Fetch & Verify Products (Live)

A Claude skill that fetches a product **live** from Sowingo, Flipkart, Amazon, and reports whether it is actually in stock — returning a short, consistent, action-ready summary.

Unlike a simulated check, this skill opens the real product page and reads the real stock signals, so you get a true go/no-go before using a product in a campaign, email, or banner.

---

## What it does

- Takes a **product URL** or a **SKU + store name** as input.
- Fetches the live page (via `WebFetch`, with a Claude-in-Chrome browser fallback for JavaScript-rendered pages).
- Reads real stock signals and reports **In stock / Out of stock / Unknown**.
- Quotes the exact stock text from the page so you can audit the result.
- Adds a short value summary (uses, advantages, disadvantages) and a fix suggestion when verification fails.

---

## When to use it

Trigger this skill when you:

- Paste a product URL and want it checked
- Give a SKU and ask *"is this in stock?"*
- Say *"verify this product"*, *"check availability"*, or *"is this live?"*
- Want a quick go/no-go before adding a product to a campaign, email, or banner

---

## Inputs

| Input | Required | Notes |
|-------|----------|-------|
| Product URL | One of these | Must be from a supported store (see below) |
| SKU / product ID + store name | One of these | Used to build a search URL when no link is given |
| Product name | Optional | Improves reporting if the page doesn't surface it cleanly |

---

## Supported stores

| Store | Accepted hosts | SKU search fallback |
|-------|----------------|---------------------|
| Sowingo | `store.sowingo.com`, `app.sowingo.com`, `*.sowingo.com` | `store.sowingo.com/search?q=<SKU>` |
| Flipkart | `www.flipkart.com` | `flipkart.com/search?q=<SKU>` |
| Amazon | `www.amazon.in`, `www.amazon.com`, etc. | `amazon.in/s?k=<SKU>` |

For unsupported hosts with no store named, the skill returns an "invalid input" message.

---

## Output format

The skill always replies in this exact layout:

```
Product: <Product Name>
Store:   <Sowingo | Flipkart | Amazon >
URL:     <canonical URL>
SKU:     <SKU / ASIN / PID, or "Not provided">
Price:   <price as shown, or "Not provided">
Stock:   ✅ In stock | ❌ Out of stock | ⚠️ Unknown
Live signal: "<exact stock text quoted from the page>"

Uses & Importance:
- ...
Advantages:
- ...
Disadvantages:
- ...

Verdict: ✅ Ready to use | ❌ Not available | ⚠️ Couldn't fully verify
Fix:     <one-line suggestion, shown only on ❌ or ⚠️>
```

---

## How verification works

1. Capture and validate the input (URL host or SKU + store).
2. Normalize the URL — strip tracking params, keep the canonical product path (`?pid=` kept for Flipkart).
3. Live-fetch the page with `WebFetch`.
4. If the page is JavaScript-only (e.g. `app.sowingo.com` SPAs), fall back to the browser tool to read the rendered DOM.
5. Determine stock from real signals (e.g. "Add to Cart" / "Buy Now" = in stock; "Sold Out" / "Notify me" = out of stock).
6. Quote the exact signal, build the value summary, and render the report.
7. On ❌ or ⚠️, append a store-aware fix suggestion.

---

## Guarantees & rules

- Never invents stock numbers, prices, or SKUs — only reports what was read from the page.
- Always includes a `Live signal:` line; if the fetch failed, it says so explicitly.
- Unverifiable fields are marked `Not provided` or `⚠️ Unknown` — never guessed.
- Returns the report only, with no extra commentary.

---

## Limitations

- Pages behind a login wall or that block fetching are reported as ⚠️ Unknown with a fix suggestion.
- Only supports Sowingo, Flipkart, Amazon,  — other stores need to be added.

---
