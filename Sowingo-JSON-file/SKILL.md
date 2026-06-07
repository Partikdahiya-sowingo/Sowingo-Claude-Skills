---
name: Sowingo-JSON-file

description: "Generates a structured JSON file for a banner ad campaign — both BigBox (300x250) and Leaderboard (728x90) — 
with campaign metadata, product details, targeting, offer info, and per-banner creative copy. Triggers on \"create json code\",
\"make json for banner ads\", or when the user shares a product URL. If a URL is given, infers product name, price, status,
and usage from the page first. Then asks the user one SECTION at a time (Campaign → Product → Targeting → Offer → Click URL → 
BigBox → Leaderboard), and within each section skips any field already inferred — user just fills the gaps or confirms. 
For Product Usage, takes the user's raw idea and rewrites it into an attractive, benefit-led marketing line before saving.
Type \"skip\" to omit any field. Output is one clean JSON file ready for AdButler, Google Ad Manager, or in-house ad servers."

---

# Banner Ad JSON Generator (Step 5)

This skill produces a clean, structured JSON file that pairs banner ad creatives with product data, targeting, offer details, and campaign metadata.
The JSON is the bridge between static banner images and a live campaign, so downstream ad servers 
(AdButler, Google Ad Manager, in-house systems) can render banners with current product info, target the right audience,
and route clicks to the right place.

## When to use this skill

Trigger on these phrases:
- "create json file"
- "create banner json"
- "make json for banner ads"
- "generate ad json"

Also trigger when:
- The user shares a product URL on its own and asks to wrap it into a banner config (even without saying "JSON")
- The user asks to attach product data to existing banners, pair banner images with click-through URLs, or connect banner ads to product data
- The user wants to build a config file that connects ads to product info, targeting, or offers

## Core behavior — six non-negotiable rules

These rules apply throughout the entire workflow.

### Rule 1 — If the user provides a product URL, infer details FIRST, then ask

When the user's message contains a product URL (or one is already in the conversation), fetch the page using `web_fetch` and try to extract:

- Product Name
- Product Price
- Product Status (In Stock / Out of Stock / Limited Stock)
- A short usage line (what the product is used for — will be the seed for the marketing-style usage line in Rule 2b)
- The product image URL (if findable)
- The URL itself (which becomes the `click_url` for both banners)

Use whatever you can extract to **pre-fill** the relevant sections in Step 2. Do not invent values for fields you can't find — leave those blank so the user can fill them in.

If no product URL is given, skip this step (Claude will still auto-generate creative suggestions later once the product is known).

If the URL fetch fails (timeout, blocked, 404), proceed to the section walkthrough with no pre-filled product values and tell the user briefly: *"Couldn't pull details from that URL — I'll ask you for the details section by section."*

### Rule 2 — Auto-generate marketing-style creative copy as suggestions

For per-banner headlines and CTAs, the skill **always** writes marketing-style suggestions and pre-fills them in the BigBox and Leaderboard sections. The user can accept them, edit them, or replace them.

Suggestions should be:
- **Punchy** — under ~10 words for headlines
- **Benefit-driven** — focus on what the user gets, not what the product is
- **Emoji-friendly** — 1 emoji per headline is fine, never more than 2 per line
- **Tonally varied between sizes** — BigBox can be more direct/visual ("Get a Brighter Smile Today 😁"), Leaderboard can lean into urgency or offer ("Limited Time Dental Deal 🦷")
- **CTAs short** — "Shop Now", "Buy Now", "Get Yours", "Order Today"

Generate suggestions based on the product name, usage, and offer (if known). If the user hasn't provided a product yet, do not pre-fill creatives — wait until the Product section is answered, then generate suggestions before reaching the BigBox section.

### Rule 2b — Rewrite the user's Product Usage into attractive marketing copy

The Product section asks the user for **Product Usage** — a plain-language idea of what the product is used for. Treat the user's input as a **raw seed**, not the final value. Before saving the JSON, rewrite that seed into one polished, benefit-led marketing line.

The rewrite should:
- **Keep the meaning** — never invent claims, dosages, certifications, or facts the user didn't imply
- **Be one short line** — roughly 8–18 words, never more than 25
- **Lead with the benefit** — what the customer gets, not what the product technically does
- **Sound on-brand for marketing** — confident, fresh, light, slightly aspirational
- **Use 0 or 1 emoji** — only when it genuinely fits the product mood
- **Fix typos and grammar quietly** — don't preserve the user's typos in the final JSON
- **Avoid filler** — no "this product is", "designed to", "perfect for use in", "helps to"

Examples of the rewrite in action:

- Seed: `"used for safety and hyginene purpose"`
  → Rewritten: `"Stay protected, stay spotless — everyday safety and hygiene made effortless ✨"`
- Seed: `"good for cleaning teeth"`
  → Rewritten: `"Brighter smiles, cleaner teeth, fresher confidence — every single day 😁"`
- Seed: `"sterilization in dental clinics"`
  → Rewritten: `"Clinic-grade sterilization that keeps your practice safe and patient-ready"`

If the user types `skip` for Product Usage, omit the field from the JSON entirely — do not invent a line.

If the user types something already polished and clearly final (a complete marketing line with emoji or punctuation), still apply light cleanup (typos, capitalization) but otherwise pass it through unchanged.

Show the **rewritten** version in the echo summary (Step 3) so the user can see exactly what will be saved before the JSON is written.

### Rule 3 — ALWAYS ask one SECTION at a time (smart: skip fields already inferred)

After the optional inference step, walk the user through the campaign **one section per message**. The user replies with all the fields for that section in a single message. Wait for the reply before moving to the next section.

**The seven sections, in order:**

1. **Campaign** — Campaign ID, Campaign Name, Platform
2. **Product** — Product Name, Product Price, Product Status, Product Usage (raw seed — will be rewritten per Rule 2b), Product Image URL
3. **Targeting** — Audience, Location
4. **Offer** — Discount, Valid From, Valid Till
5. **Click URL** — Click-through URL (used for both banners by default)
6. **BigBox (300x250)** — Banner image URL, Headline (with auto-suggestion), CTA (with auto-suggestion)
7. **Leaderboard (728x90)** — Banner image URL, Headline (with auto-suggestion), CTA (with auto-suggestion)

**Smart skipping within a section:** if some fields in a section were already inferred from the URL (Step 0) or are known from earlier conversation, show them at the top of the section block and ask the user only for the remaining (missing) fields. The user can also correct any inferred values in the same reply.

**If every field in a section is already inferred**, still show the section as a confirmation block and ask "anything to change, or all good?" — then move on once the user replies.

**Auto-generated creative suggestions** (BigBox/Leaderboard headline and CTA) appear inside their respective sections as defaults the user can accept or replace.

**No examples by default.** Ask each section cleanly. Only add examples **if the user's reply signals confusion** — short replies like "what?", "huh?", "like what?", "?", "i don't get it", "example?", or anything that clearly isn't an answer. When that happens, re-ask the same section with concrete examples for each field, then wait again.

**Skip is always an option.** Mention "type `skip` next to any field to omit it" once at the very start of the flow, not in every section.

**One section per message.** Do NOT bundle multiple sections in one message. Do NOT proceed to the next section until the current one is answered.

### Rule 4 — Echo back the values you received before building the JSON

After all seven sections are answered, post a short summary that lists each field grouped by section and the value the user provided (or "skipped" if they skipped it). The user should be able to see exactly what was captured before the JSON is written.

This echo step is required — never skip it.

### Rule 5 — Schema is fixed; no `subheadline`, no `dynamic_data`, no `defaults`

The JSON schema does **not** include a `subheadline` field on creatives, a `dynamic_data` block, or a `defaults` block. Do not add them under any circumstances, even if the user asks.

Each creative contains only: `headline`, `cta`, `banner_image`, `click_url`.

### Rule 6 — "skip" omits a field; both banners are always included

If the user types "skip" (or "skip it", "no", "n/a") for any field, omit that field from the final JSON entirely (no placeholders, no `null`, no `""`).

Both banner ads (BigBox 300x250 and Leaderboard 728x90) are **always** included in the output as a single JSON file under `creatives` — the user does not get to pick one.

If the user has provided **zero** product data and skips everything (no product name AND no product URL), respond with a single line saying you need at least a product name or product URL to build the JSON, and stop.

## Workflow

### Step 0 — Infer from product URL (if provided)

If the user's message includes a product URL, fetch the page with `web_fetch` and try to extract:
- Product Name
- Product Price
- Product Status (In Stock / Out of Stock / Limited Stock)
- Short usage line (what the product is used for)
- Product image URL

Pre-fill any extracted values into the relevant sections in Step 2. Don't invent values you can't find. If the fetch fails, proceed with empty sections and tell the user briefly that you couldn't pull details from the URL.

If no product URL is provided, skip this step.

### Step 1 — Auto-generate creative suggestions (when product is known)

Based on whatever product info you have (from Step 0 or earlier conversation), generate marketing-style suggestions for both banner sizes:
- BigBox headline + CTA
- Leaderboard headline + CTA

These will be shown inside the BigBox and Leaderboard sections in Step 2 as defaults the user can accept or replace.

If you have no product info at all yet, hold off on creative suggestions — generate them after the Product section is answered, before reaching the BigBox section.

### Step 2 — Walk through the campaign one SECTION at a time

Open the flow with a single short intro message:

```
Got it — I'll walk you through the campaign section by section. Type "skip" next to any field you want to leave out. Reply with all the fields for each section in one message.
```

**If anything was inferred in Step 0**, mention it briefly in the intro so the user knows you've already got some details — e.g. *"I've already pulled some details from the URL — I'll show them in each section so you can confirm or correct."*

Then go through the sections **one at a time**, in this order. **Wait for the user's reply after each section before moving on.**

---

#### Section 1 — Campaign

Ask:
```
**Campaign** — please send:
- Campaign ID
- Campaign Name
- Platform
```

If any of these are already known (rare for this section, but possible from earlier conversation), show them at the top and only ask for the missing ones.

Wait for the reply, capture the values, then move to Section 2.

---

#### Section 2 — Product

If product details were inferred from the URL, show them first and only ask for what's missing. Example when everything was inferred except `usage`:

```
**Product** — here's what I pulled from the URL:
- Name: Electric Toothbrush Pro
- Price: ₹2,499
- Status: In Stock
- Image URL: https://example.com/product-image.jpg

Still need:
- Product Usage (a rough idea — I'll rewrite it into a marketing line)

Send the missing field, and correct anything above if it's wrong.
```

If nothing was inferred, ask the full section:

```
**Product** — please send:
- Product Name
- Product Price
- Product Status (In Stock / Out of Stock / Limited Stock)
- Product Usage (a rough idea — I'll rewrite it into a marketing line)
- Product Image URL
```

Wait for the reply, apply corrections, capture the values.

**After this section is answered, run Step 1** (generate creative suggestions for BigBox and Leaderboard) before reaching Sections 6 and 7.

Then move to Section 3.

---

#### Section 3 — Targeting

Ask:
```
**Targeting** — please send:
- Audience
- Location
```

Wait for the reply, capture the values, then move to Section 4.

---

#### Section 4 — Offer

Ask:
```
**Offer** — please send:
- Discount
- Valid From (format: YYYY-MM-DD)
- Valid Till (format: YYYY-MM-DD)
```

Wait for the reply, capture the values, then move to Section 5.

---

#### Section 5 — Click URL

If a product URL was provided in Step 0, default the click URL to that URL and confirm:

```
**Click URL** — I'll use the product URL for both banners: `https://example.com/product`. Send a different one to override, or say "all good" to keep it.
```

Otherwise, ask:
```
**Click URL** — what URL should both banners link to?
```

Wait for the reply, capture the value, then move to Section 6.

---

#### Section 6 — BigBox (300x250)

Show the auto-generated creative suggestions and ask for the image URL:

```
**BigBox (300x250)** — here are my suggestions:
- Headline: Get a Brighter Smile Today 😁
- CTA: Shop Now

Please send:
- Banner image URL
- Headline (or "use it" to keep mine)
- CTA (or "use it" to keep mine)
```

Wait for the reply. If the user says "use it" or "use both" for headline/CTA, keep the suggestions as-is. If they send their own, use exactly what they wrote.

Then move to Section 7.

---

#### Section 7 — Leaderboard (728x90)

Same pattern as Section 6, with the Leaderboard suggestions:

```
**Leaderboard (728x90)** — here are my suggestions:
- Headline: Limited Time Dental Deal 🦷
- CTA: Buy Now

Please send:
- Banner image URL
- Headline (or "use it" to keep mine)
- CTA (or "use it" to keep mine)
```

Wait for the reply, capture the values, then proceed to Step 3.

---

**Confusion handling — applies to every section:**

If the user's reply to any section signals confusion — short replies like "what?", "huh?", "?", "like what?", "example?", "i don't get it", "what do you mean" — re-ask the **same** section with concrete examples next to each field. Then wait again.

Example re-ask for Section 1 (Campaign):
```
**Campaign** — please send:
- Campaign ID — something like `CMP12345` (the unique ID your ad platform uses)
- Campaign Name — e.g. `Summer Dental Sale`
- Platform — e.g. `AdButler`, `Google Ad Manager`
```

Example re-ask for Section 4 (Offer):
```
**Offer** — please send:
- Discount — e.g. `20%`
- Valid From — when the offer starts, format `YYYY-MM-DD`, e.g. `2026-05-01`
- Valid Till — when the offer expires, format `YYYY-MM-DD`, e.g. `2026-05-10`
```

If the user types `skip` next to any field (or skips the whole section by saying "skip all"), accept it silently and move on.

### Step 3 — Echo back the values

Once all seven sections are answered, post a short summary that lists each field grouped by section, showing the value the user provided. Mark skipped fields as `skipped`. Use this format:

```
Here's what I got:

**Campaign**
- ID: CMP12345
- Name: Summer Dental Sale
- Platform: AdButler

**Product**
- Name: Electric Toothbrush Pro
- Price: ₹2,499
- Status: In Stock
- Usage (your idea): good for cleaning teeth daily
- Usage (rewritten): Brighter smiles, cleaner teeth, fresher confidence — every single day 😁
- Image URL: https://example.com/product-image.jpg

**Targeting**
- Audience: Adults, Dental Care Users
- Location: India

**Offer**
- Discount: 20%
- Valid From: 2026-05-01
- Valid Till: 2026-05-10

**Click URL**: https://example.com/product

**BigBox**
- Image: https://example.com/banner-300x250.jpg
- Headline: Get a Brighter Smile Today 😁
- CTA: Shop Now

**Leaderboard**
- Image: https://example.com/banner-728x90.jpg
- Headline: Limited Time Dental Deal 🦷
- CTA: Buy Now

Generating the JSON now...
```

This echo step is required — never skip it.

### Step 4 — Light validation (silent)

Quietly normalize:
- **Status casing**: "in stock" → "In Stock", "out of stock" → "Out of Stock", "limited stock" → "Limited Stock"
- **Discount**: if user wrote "20" or "20 percent", normalize to `"20%"`
- **Valid From**: ensure `YYYY-MM-DD` format. If malformed, omit the field rather than asking again.
- **Valid Till**: ensure `YYYY-MM-DD` format. If malformed, omit the field rather than asking again.
- **Click URL**: if the user provided one, use it for both banners. If not, fall back to the product URL from Step 0. If neither exists, omit `click_url` from each creative.
- **Product Usage**: rewrite the user's raw usage seed into one polished marketing line per Rule 2b before saving. The rewritten version (not the seed) is what goes into the JSON under `product.usage`. If the user skipped this field, omit `usage` from the JSON entirely.

### Step 5 — Generate the JSON

Produce a JSON file with this exact structure. Both BigBox and Leaderboard creatives are always included. Omit any keys whose values are missing or were skipped — do not emit empty strings or `null`.

```json
{
  "campaign": {
    "id": "",
    "name": "",
    "platform": ""
  },
  "product": {
    "name": "",
    "price": "",
    "status": "",
    "usage": "",
    "image_url": ""
  },
  "targeting": {
    "audience": "",
    "location": ""
  },
  "offer": {
    "discount": "",
    "valid_from": "",
    "valid_till": ""
  },
  "creatives": {
    "bigbox_300x250": {
      "headline": "",
      "cta": "",
      "banner_image": "",
      "click_url": ""
    },
    "leaderboard_728x90": {
      "headline": "",
      "cta": "",
      "banner_image": "",
      "click_url": ""
    }
  }
}
```

**Formatting rules:**
- 2-space indentation
- Keys appear in the exact order shown above (campaign → product → targeting → offer → creatives). Within each block, keys also follow the order shown.
- Inside `creatives`, BigBox always comes before Leaderboard.
- Missing/skipped fields are omitted from the JSON, not emitted as `""` or `null`.
- If an entire block (e.g. `targeting` or `offer`) has every field skipped, omit the block itself rather than emitting an empty object.
- The `product.usage` field always carries the **rewritten** marketing line, never the user's raw seed.
- Never emit `subheadline`, `dynamic_data`, `description`, or `defaults` — these are not part of the schema.

### Step 6 — Save location

Save the JSON file to `/mnt/user-data/outputs/` with a filename like `campaign-<campaign-id>.json` (e.g. `campaign-CMP12345.json`). If no Campaign ID was provided, use the product name slugified (e.g. `campaign-electric-toothbrush-pro.json`). If neither exists, use `campaign-banner-ads.json`.

Then call `present_files` so the user can download it.

## Example output (all-skipped variant)

When the user skips everything in the Offer section, the resulting JSON omits the entire `offer` block:

```json
{
  "campaign": {
    "id": "CMP12345",
    "name": "Summer Dental Sale",
    "platform": "AdButler"
  },
  "product": {
    "name": "Electric Toothbrush Pro",
    "price": "₹2,499",
    "status": "In Stock",
    "usage": "Brighter smiles, cleaner teeth, fresher confidence — every single day 😁",
    "image_url": "https://example.com/product-image.jpg"
  },
  "targeting": {
    "audience": "Adults, Dental Care Users",
    "location": "India"
  },
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

Notice: the `offer` block is omitted entirely (every field was skipped).

## Example walkthrough (section-by-section flow in action)

This is what the chat looks like when the skill actually runs. **No product URL given** in this example, so nothing is inferred — every section is asked one at a time.

> **User:** create json for my new dental campaign

> **Claude:** Got it — I'll walk you through the campaign section by section. Type "skip" next to any field you want to leave out. Reply with all the fields for each section in one message.
>
> **Campaign** — please send:
> - Campaign ID
> - Campaign Name
> - Platform

> **User:** what?

> **Claude:** **Campaign** — please send:
> - Campaign ID — something like `CMP12345` (the unique ID your ad platform uses)
> - Campaign Name — e.g. `Summer Dental Sale`
> - Platform — e.g. `AdButler`, `Google Ad Manager`

> **User:** CMP12345, Summer Dental Sale, AdButler

> **Claude:** **Product** — please send:
> - Product Name
> - Product Price
> - Product Status (In Stock / Out of Stock / Limited Stock)
> - Product Usage (a rough idea — I'll rewrite it into a marketing line)
> - Product Image URL

> **User:** Electric Toothbrush Pro, ₹2,499, In Stock, good for cleaning teeth daily, skip

> **Claude:** **Targeting** — please send:
> - Audience
> - Location

> **User:** Adults, dental care users / India

> **Claude:** **Offer** — please send:
> - Discount
> - Valid From (format: YYYY-MM-DD)
> - Valid Till (format: YYYY-MM-DD)

> **User:** 20%, 2026-05-01, 2026-05-10

> **Claude:** **Click URL** — what URL should both banners link to?

> **User:** https://example.com/product

> **Claude:** **BigBox (300x250)** — here are my suggestions:
> - Headline: Get a Brighter Smile Today 😁
> - CTA: Shop Now
>
> Please send:
> - Banner image URL
> - Headline (or "use it" to keep mine)
> - CTA (or "use it" to keep mine)

> **User:** https://example.com/banner-300x250.jpg, use it, use it

> **Claude:** **Leaderboard (728x90)** — here are my suggestions:
> - Headline: Limited Time Dental Deal 🦷
> - CTA: Buy Now
>
> Please send:
> - Banner image URL
> - Headline (or "use it" to keep mine)
> - CTA (or "use it" to keep mine)

> **User:** https://example.com/banner-728x90.jpg, use it, Buy Now

> **Claude:** *(posts the full echo summary from Step 3, then saves the JSON file)*

**Notes on what's happening above:**
- Each section is its own message; Claude waits for the full reply before moving on.
- When the user replies "what?" or "?", Claude re-asks the same section with concrete examples next to each field.
- "skip" inside a section reply silently drops that one field.
- Creative suggestions appear inline inside the BigBox and Leaderboard sections — the user can accept ("use it") or send their own.
- If a product URL had been given at the start, the Product section would have shown the inferred values up top and asked only for the missing fields (typically just Product Usage). The Click URL section would also default to the product URL.

## Edge cases

- **User provides only a product URL:** Run Step 0 to infer details, then start the section walkthrough. The Product section shows inferred values up top and asks only for missing fields. The Click URL section defaults to the product URL. Do not skip the walkthrough entirely.
- **URL fetch fails (timeout, blocked, 404):** Tell the user briefly and start the section walkthrough from Section 1 with no pre-filled product values.
- **Inferred values look wrong or partial:** Include what you have at the top of the relevant section, let the user correct in their reply (alongside the missing fields), then move on.
- **User accepts all creative suggestions verbatim:** Use them as-is. Don't second-guess.
- **User edits creative copy:** Use exactly what they wrote. Don't "improve" it.
- **User's Product Usage is rough, has typos, or sounds plain:** That's the expected case. Rewrite it into one polished marketing line per Rule 2b. Show both the seed and the rewrite in the echo summary so the user can confirm before the JSON is saved.
- **User's Product Usage already looks like polished marketing copy:** Apply only light cleanup (typos, capitalization) and pass it through. Don't rewrite a finished line into something different.
- **User skips Product Usage:** Omit `usage` from the JSON entirely. Never invent a usage line on the user's behalf.
- **User's reply to a section signals confusion** (e.g. "what?", "huh?", "?", "like what?", "example?"): Re-ask the same section with one concrete example next to each field. Do not skip the section. Do not move on until they actually answer (or say "skip" per field).
- **User answers only some fields in a section:** Capture what they sent and treat the missing ones as skipped. Don't re-ask for partial sections — moving forward keeps the flow fast.
- **User answers more than one section in one message** (e.g. they reply to Section 1 with all their Campaign + Product details): Accept all of it, mark those sections done, and jump ahead to the next un-answered section. Don't re-ask what they already gave you.
- **Missing fields after the walkthrough:** Omit them from the JSON. Do not re-ask.
- **User says "skip" for a field:** Drop that field from the JSON entirely. Mark it as `skipped` in the echo summary.
- **User skips an entire section (e.g. all of `targeting`):** Omit the whole block from the JSON, not an empty object.
- **User asks to add `subheadline`, `dynamic_data`, or `defaults`:** Politely note that these are not part of the schema and proceed without them.
- **User has multiple products in one campaign:** This skill produces JSON for one product at a time. Generate separate JSON files per product.
- **Status is "Limited Stock":** This is a valid status. Pass it through unchanged.
- **User provides extra fields (SKU, brand, alt text, etc.):** Ignore them — keep the schema stable for downstream consumers.
- **Different click URLs per banner:** The schema supports it (each creative has its own `click_url`). Section 5 asks for a single click URL by default, but if the user explicitly provides different ones in Sections 6 and 7, honor that.
- **Zero data provided:** If the user goes through the walkthrough but skips both Product Name AND Product URL, reply with a single line saying you need at least one of them, and stop. Don't generate empty JSON.
- **Conversation already has product data from earlier steps (e.g. Step 2 product verification or Step 4 banner generation):** Treat that data as inferred — show it at the top of the Product section and let the user confirm, then only ask the fields that are still missing. Never skip the section walkthrough entirely.
