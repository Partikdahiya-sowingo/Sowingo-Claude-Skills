# Generate Banner Ads (HTML-first → Canva-editable)

A Claude skill that builds **3 banner ad variations** as self-contained HTML previews, lets you pick the one you like from a numbered menu, and then recreates **only that pick** as a fully editable native Canva design.

The HTML-first flow is deliberate: previews are fast and free, so you can compare every option side by side before any Canva call. Canva is reserved for the single banner you actually choose — no pile of unused drafts.

---

## What it does

1. Collects the product name and key benefit.
2. Asks which size you want — **Leaderboard (728×90)**, **Big Box (300×250)**, or **both**.
3. Plans 3 distinct angles and writes punchy copy for each.
4. Builds every banner as a self-contained HTML file (locked Roboto typography, inline CSS) via the bundled generator script.
5. Shows them rendered and presents a **numbered menu** of all banners.
6. Waits for you to pick a number — then recreates that one banner as an editable Canva design and shares the edit link.

---

## When to use it

- *"Create banner ads for [product]"*
- *"Make 3 ad creatives"*
- *"Generate a leaderboard banner"* / *"I need a 300×250 banner"*
- *"Promo banner for [product name]"*
- Pasting a product page or name and asking for ads/creatives

For *just* short text copy (no design), use **Step 3 — Generate Messages** instead.

---

## Inputs

| Input | Required | Notes |
|-------|----------|-------|
| Product name | Yes | e.g. "AquaPure Water Purifier" |
| Key benefit / hook | Yes | The one thing that makes it worth buying |
| Banner size | Asked before generating | Leaderboard, Big Box, or both |
| Price / offer, brand colour, CTA text, audience | Optional | Refines the copy and look |

---

## The three angles

| Variation | Angle | Style |
|-----------|-------|-------|
| 1 | **Bold & Benefit-driven** | Leads with the biggest benefit; energetic emojis (🔥💥⚡) |
| 2 | **Offer / Urgency-driven** | Leads with discount or scarcity; urgency emojis (⏰🛒💸) |
| 3 | **Lifestyle / Aspirational** | Leads with how life improves; soft emojis (✨🌿💫) |

Each banner has a headline (≤6 words), subhead (≤12 words), and a 2–3 word CTA button.

---

## The generator script

Banners are built by the bundled **`scripts/build_banners.py`**, which enforces the locked typography, scales each format, bakes in an on-brand SVG emblem, and produces both the individual files and a numbered gallery in one pass.

Write a `spec.json` describing the product, chosen size(s), and 3 variations, then run:

```bash
python scripts/build_banners.py --spec spec.json --out <outputs>/banner-ads
```

**Spec shape** (sizes ⊆ `leaderboard`, `bigbox`; exactly 3 variations):

```json
{
  "product": "Product Name",
  "sizes": ["leaderboard", "bigbox"],
  "variations": [
    {"angle": "Bold & Benefit", "bg": "#0B3D91", "accent": "#FF5722", "cta_bg": "#FF5722",
     "headline": "🔥 ...", "subhead": "...", "cta": "Shop Now"},
    {"angle": "Offer & Urgency", "bg": "#B71C1C", "accent": "#FFC107", "cta_bg": "#E53935",
     "headline": "⏰ ...", "subhead": "...", "cta": "Shop the Sale"},
    {"angle": "Lifestyle & Aspirational", "bg": "#0F9D8F", "accent": "#FFFFFF", "cta_bg": "#0B6B61",
     "headline": "✨ ...", "subhead": "...", "cta": "Discover More"}
  ]
}
```

It writes one HTML file per banner (3 or 6 files), plus `gallery.html` with a number badge on each, and prints the numbered menu to stdout.

---

## Locked typography

Baked into every banner and passed to Canva so the editable version matches:

- **Headline:** Roboto Bold (700), 32px / 38px line-height (≈22px / 26px scaled for Big Box). Never below 700, never a different font.
- **CTA button:** Roboto Medium (500), 16px, white text on a solid fill, 6px radius, 12px/16px padding. No gradients, shadows, or glows.

Font link used in every file:

```html
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@500;700&display=swap" rel="stylesheet">
```

---

## Tool sequence

```
HTML phase (no Canva):
  build_banners.py --spec spec.json --out <out>  → N banner files + gallery.html
  present gallery + files
  show numbered menu (1..N)  → WAIT for user's number

Canva phase (picked banner only):
  1. generate-design            → job_id + candidates
  2. user picks closest match   (default 1 if "you pick")
  3. create-design-from-candidate → design_id + edit_url
  4. share edit_url
```

---

## Rules

**Always:**

- Produce exactly 3 distinct variations as real HTML banners.
- Ask which size **before** generating.
- Build HTML previews first and present a numbered menu.
- Wait for the user's number before calling any Canva tool.
- Send **only** the picked banner to Canva, with its exact copy, hex palette, size, and typography.
- Use real, punchy copy and 1–2 emojis per banner with a CTA.

**Never:**

- Call Canva before the user picks a number, or send unpicked variations.
- Skip `create-design-from-candidate` (without it the design isn't saved).
- Use placeholder text or more than 2 emojis per banner.
- Make medical / "100% guaranteed" claims unless the user provided them.
- Generate banners for restricted categories (weapons, adult, illegal, political).

---

*Part of the [Sowingo Marketing Skills](../README.md) suite — Step 4.*
