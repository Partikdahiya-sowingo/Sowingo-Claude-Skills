---
name: Sowingo-fetch-and-verify-products

description: "Fetch a product LIVE from Sowingo using a product URL or SKU and verify whether it is in stock and available. 
Use this skill whenever the user says \"check this product\", \"verify this product\", \"is this product available\",
\"is this in stock\", or pastes a Sowingo  product URL or SKU on its own. Also trigger when the user asks for a quick stock check,
availability confirmation, or wants to know whether a listing is live before using it in a campaign, email, or banner. 
This skill performs LIVE web fetching — it actually opens the product page (via WebFetch, with Claude-in-Chrome as a fallback for
dynamic pages) and reads real stock status, then returns a short, consistent status report with stock status, uses, importance, 
advantages, and disadvantages, plus a fix suggestion when verification fails."
---
 
# Product Verifier (Live)
 
Fetches a product LIVE from Sowingo, Flipkart, Amazon, and reports whether it is actually in stock, with a short, consistent summary the user can act on.
 
This skill replaces the previous simulation-based version. It now reads real product pages and reports the real stock state.
 
---
 
## When to Use This Skill
 
- User pastes a product URL from Sowingo, Flipkart, Amazon, and wants it checked
- User gives a SKU and asks "is this in stock?"
- User says "verify this product", "check availability", or "is this live"
- User wants a quick go/no-go before adding a product to a campaign, email, or banner
## What You Need
 
- **Input (required, one of):**
  - A product URL from a supported store
  - A SKU or product ID, plus the store name (so the right search URL can be built)
- **Optional:** A product name, used for clearer reporting if the live page doesn't surface it cleanly
## Supported Stores and URL Patterns
 
| Store    | Accepted URL hosts                                      | SKU search fallback                          |
|----------|---------------------------------------------------------|----------------------------------------------|
| Sowingo  | `store.sowingo.com`, `app.sowingo.com`, `*.sowingo.com` | `https://store.sowingo.com/search?q=<SKU>`   |
| Flipkart | `www.flipkart.com`                                      | `https://www.flipkart.com/search?q=<SKU>`    |
| Amazon   | `www.amazon.in`, `www.amazon.com`, etc.                 | `https://www.amazon.in/s?k=<SKU>`            |   |
 
If the host doesn't match any of the above and no store is named, return the "invalid input" error.
 
## Workflow Steps (Live)
 
The flow is now real — actually fetch the page, read the live stock signals, and report back.
 
1. **Capture input.** Read the URL or SKU. If both are missing, return the "missing input" error.
2. **Validate and normalize.**
   - URL must match a supported host. Strip tracking params (`?_gl=`, `?algoliaQueryID=`, `?lid=`, `?marketplace=`, etc.) when reasonable, but keep the canonical product path. Keep `?pid=` for Flipkart.
   - For a bare SKU, ask the user which store (if not obvious) and build the SKU search URL from the table.
3. **Live fetch (primary path).** Use the `WebFetch` tool on the product URL with a prompt like:
   > "Extract the product name, SKU/ASIN/PID, price, stock status (in stock / out of stock / sold out / add to cart vs. notify me), pack size, and brand from this product page. Quote the exact stock-related text shown on the page."
4. **Fallback for dynamic/SPA pages.** Some pages (notably `app.sowingo.com` and certain Amazon/Flipkart variants) render with client-side JavaScript that `WebFetch` can't see. If WebFetch returns thin or empty content, fall back to the Claude-in-Chrome browser tool: `navigate` to the URL, then `read_page` or `get_page_text` to read the rendered DOM. Look for store-specific stock signals:
   - **Sowingo:** "Add to cart" enabled vs. "Out of stock" / "Notify me".
   - **Flipkart:** "ADD TO CART" / "BUY NOW" vs. "Sold Out" / "Currently unavailable" / "Coming Soon".
   - **Amazon:** "In Stock" / "Only X left in stock" vs. "Currently unavailable" / "Temporarily out of stock".
5. **Determine stock status** based on what was actually read:
   - ✅ `In stock` — clear positive signal (Add to Cart / Buy Now / "In stock").
   - ❌ `Out of stock` — clear negative signal (Sold Out / Currently unavailable / Notify me / Out of stock).
   - ⚠️ `Unknown` — only if both WebFetch and the browser fallback failed, or the page returned nothing usable. Always say *why* it's Unknown in the Fix line (e.g. "page requires login", "fetch blocked", "JS-only render and browser tool unavailable").
6. **Build the value summary.** Write a short "Uses & Importance", "Advantages", and "Disadvantages" block based on the product category surfaced from the page. Keep each to 1–2 short bullets.
7. **Render the report** in the exact output format below. Quote the exact stock-related text from the page in a `Live signal:` line so the user can audit the result.
8. **On failure, suggest a fix.** If stock is `❌ Out of stock` or `⚠️ Unknown`, append a one-line, store-aware suggestion (see Error Handling).
## Output Format
 
Always use this exact layout. Keep each line short.
 
```
Product: <Product Name>
Store:   <Sowingo | Flipkart | Amazon | Meesho>
URL:     <canonical URL>
SKU:     <SKU / ASIN / PID, or "Not provided">
Price:   <price as shown, or "Not provided">
Stock:   ✅ In stock | ❌ Out of stock | ⚠️ Unknown
Live signal: "<exact text quoted from the page that justifies the stock call>"
 
Uses & Importance:
- <1 line on what the product is used for>
- <1 line on why it matters for the buyer / clinic / shopper>
 
Advantages:
- <short bullet>
- <short bullet>
 
Disadvantages:
- <short bullet>
- <short bullet>
 
Verdict: ✅ Ready to use | ❌ Not available — see fix below | ⚠️ Couldn't fully verify
Fix:     <only shown when Verdict is ❌ or ⚠️ — one-line, store-aware suggestion>
```
 
## Error Handling
 
- **Missing input** → `No product URL or SKU provided. Please share a product link from Sowingo / Flipkart / Amazon / Meesho, or a SKU plus the store name.`
- **Invalid input** → `Input doesn't look like a supported product URL or SKU. Supported hosts: store.sowingo.com, www.flipkart.com, www.amazon.in/.com, www.meesho.com.`
- **Fetch blocked / 403 / login wall** → Mark Stock as ⚠️ Unknown. Fix: `The page requires login or blocked the fetch. Open it in your browser (signed in if needed) and share the visible stock text, or send the public store URL.`
- **JS-only page and browser fallback unavailable** → Mark Stock as ⚠️ Unknown. Fix: `Page is JavaScript-rendered and the browser tool isn't available right now — share the public store URL (e.g. store.sowingo.com/...) so a static fetch can succeed.`
- **Out of stock** → Suggest: `Try a similar SKU in the same category, or contact the vendor / seller to confirm a restock date before using this in a campaign.`
- **Page resolves but stock is ambiguous** → Mark Stock as ⚠️ Unknown and quote whatever signal you saw. Fix: `Stock signals on the page were ambiguous — open the listing directly to confirm before relying on it.`
## Per-Store Notes
 
### Sowingo
- `store.sowingo.com` paths are usually static and WebFetch-friendly.
- `app.sowingo.com/#/app/product-details/<slug>/` and `d2wyul3yh1kwcl.sowingo.com/#/...` are SPAs — WebFetch will return a near-empty shell. Use the Chrome browser fallback, or ask the user for the public `store.sowingo.com` equivalent.
- Sowingo SKUs are usually numeric (e.g. `17236`) or vendor-coded (e.g. `038-7944`).
### Flipkart
- Listing URLs include a long querystring (`?pid=`, `?lid=`, `?marketplace=`) — keep `?pid=` since Flipkart sometimes needs it; trim the rest.
- Stock signals: "ADD TO CART" / "BUY NOW" = in stock; "Sold Out" / "Currently unavailable" / "Coming Soon" = out of stock.
- SKU on Flipkart usually maps to the `pid` parameter (e.g. `SHTH8PSH7RDP5GTY`).
### Amazon
- Product URLs use `/dp/<ASIN>` — the ASIN is the SKU equivalent.
- Stock signals are in the buybox: "In Stock", "Only X left in stock - order soon", "Currently unavailable", "Temporarily out of stock".

## Rules
 
- Always return the report in the exact output format — no extra commentary, no opinions.
- Always include the `Live signal:` line. If you couldn't read the page, say `Live signal: <fetch failed: reason>` so the user knows the result wasn't grounded in real data.
- Never invent stock numbers, prices, or SKUs that weren't read from the page.
- If a field can't be determined, mark it `Not provided` or `⚠️ Unknown` — never guess.
- Keep "Uses & Importance", "Advantages", and "Disadvantages" to 1–2 short bullets each.
- Only include a `Fix:` line when the Verdict is ❌ or ⚠️ — omit it on ✅.
- One skill = one job: this skill only verifies products on Sowingo, Flipkart, Amazon. For other stores, ask the user to add them or use a different skill.
