# Create Campaign JSON File
 
A Claude skill that produces a clean, structured **JSON file** for a banner ad campaign — pairing both banner sizes (BigBox 300×250 and Leaderboard 728×90) with product data, targeting, offer details, and campaign metadata.
 
The JSON is the bridge between static banner images and a live campaign, ready for downstream ad servers like **AdButler**, **Google Ad Manager**, or in-house systems.
 
This is **Step 5** in the Sowingo marketing workflow: build → verify product → generate messages → generate banners → **wrap everything into JSON**.
 
---
 
## What it does
 
- If you share a **product URL**, it fetches the page and pre-fills product name, price, status, usage, and image first.
- Walks you through the campaign **one section at a time**, skipping any field already inferred — you just fill the gaps or confirm.
- Rewrites your rough **Product Usage** note into a polished, benefit-led marketing line.
- Auto-suggests punchy headlines and CTAs for both banner sizes — accept or replace them.
- Echoes back everything captured, then saves one clean JSON file.
---
 
## When to use it
 
Trigger phrases include *"create json file"*, *"create banner json"*, *"make json for banner ads"*, *"generate ad json"*, *"banner ad config"*, or *"attach product data to banner"*.
 
Also triggers when you share a product URL and want it wrapped into a banner config — even without saying "JSON".
 
---
 
## The seven sections
 
The skill asks one section per message, in this order:
 
| # | Section | Fields |
|---|---------|--------|
| 1 | **Campaign** | Campaign ID, Name, Platform |
| 2 | **Product** | Name, Price, Status, Usage (rewritten), Image URL |
| 3 | **Targeting** | Audience, Location |
| 4 | **Offer** | Discount, Valid From, Valid Till |
| 5 | **Click URL** | Click-through URL (used for both banners) |
| 6 | **BigBox (300×250)** | Banner image URL, Headline, CTA |
| 7 | **Leaderboard (728×90)** | Banner image URL, Headline, CTA |
 
Type `skip` next to any field to omit it. Fields already pulled from a URL are shown at the top of their section for confirmation.
 
---
 
## Output schema
 
The schema is fixed. Each creative contains only `headline`, `cta`, `banner_image`, and `click_url`. Missing or skipped fields are omitted entirely (no empty strings or `null`).
 
```json
{
  "campaign":  { "id": "", "name": "", "platform": "" },
  "product":   { "name": "", "price": "", "status": "", "usage": "", "image_url": "" },
  "targeting": { "audience": "", "location": "" },
  "offer":     { "discount": "", "valid_from": "", "valid_till": "" },
  "creatives": {
    "bigbox_300x250":     { "headline": "", "cta": "", "banner_image": "", "click_url": "" },
    "leaderboard_728x90": { "headline": "", "cta": "", "banner_image": "", "click_url": "" }
  }
}
```
 
Key rules:
 
- 2-space indentation; keys in the exact order shown.
- Both BigBox and Leaderboard are **always** included; BigBox comes first.
- `product.usage` always carries the rewritten marketing line, never the raw seed.
- Never emits `subheadline`, `dynamic_data`, `description`, or `defaults` — not part of the schema.
- An entirely-skipped block (e.g. `offer`) is omitted rather than left empty.
---
 
## Output location
 
Saved as `campaign-<campaign-id>.json` (e.g. `campaign-CMP12345.json`). If no Campaign ID, it uses the slugified product name; if neither, `campaign-banner-ads.json`. The file is then shared for download.
 
---
 
## Example output
 
```json
{
  "campaign": { "id": "CMP12345", "name": "Summer Dental Sale", "platform": "AdButler" },
  "product": {
    "name": "Electric Toothbrush Pro",
    "price": "₹2,499",
    "status": "In Stock",
    "usage": "Brighter smiles, cleaner teeth, fresher confidence — every single day 😁",
    "image_url": "https://example.com/product-image.jpg"
  },
  "targeting": { "audience": "Adults, Dental Care Users", "location": "India" },
  "creatives": {
    "bigbox_300x250": {
      "headline": "Get a Brighter Smile Today 😁",
      "cta": "Shop Now",
      "banner_image": "https://example.com/banner-300x250.jpg",
      "click_url": "https://example.com/product"
    },
    "leaderboard_728x90": {
      "headline": "Limited Time Dental Deal 🦷",
      "cta": "Buy Now",
      "banner_image": "https://example.com/banner-728x90.jpg",
      "click_url": "https://example.com/product"
    }
  }
}
```
 
*(Here the `offer` block was skipped, so it's omitted entirely.)*
 
---
 
## Notes & guarantees
 
- Asks one section at a time and waits for each reply — never bundles sections.
- Adds concrete examples only if your reply signals confusion ("what?", "?", "like what?").
- Needs at least a **product name or product URL** — won't generate empty JSON.
- Produces JSON for **one product at a time**; multiple products get separate files.

---
