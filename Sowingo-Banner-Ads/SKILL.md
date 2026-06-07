---
name: Sowingo-generate-banner-ads
description: "Banner Ad Generator that builds 3 marketing banner ad variations as self-contained HTML banners 
(locked Roboto typography, inline CSS) in Leaderboard (728×90), Big Box (300×250), or both, shows them rendered,
and lists every banner in a NUMBERED menu so the user picks the one they want. Only the picked banner is then recreated as a native,
fully editable Canva design via generate-design + create-design-from-candidate, and the Canva edit link is shared.
Triggers when users ask for banner ads, ad creatives, a leaderboard/big-box banner, promo banners, or paste a product name or URL
and want creatives. Always asks which size first, then generates HTML previews, then waits for the user to pick a number before 
touching Canva. Copy is punchy with 1–2 emojis and a CTA like \"Shop Now\" or \"Book Now\"."

---
 
# Sow Mkt Step 4 — Generate Banner Ads (HTML-first → Canva-editable)
 
Build 3 banner ad variations as **self-contained HTML banners** in the size(s) the user picks (Leaderboard 728×90, Big Box 300×250, or both), show them rendered, then present a **numbered menu** of every banner. The user picks ONE number. Only that picked banner is recreated as a **native, fully editable Canva design** the user can open and edit.
 
The reason for this order: HTML previews are fast, free, and let the user compare every option side by side before any Canva call. Canva is reserved for the single design they actually chose — so the editable Canva design is exactly the one they want, not a pile of unused drafts.
 
---
 
## When to Use This Skill
 
- "Create banner ads for [product]"
- "Make 3 ad creatives"
- "Generate a leaderboard banner" / "I need a 300x250 banner"
- "Design banners for my product launch"
- "Promo banner for [product name]"
- User pastes a product page or product name and asks for ads/creatives
If a user wants *just* short text marketing copy (no banner design), use the `sow-mkt-step-3-generate-messages` skill instead. For a longer marketing post, use the blog/email skills.
 
---
 
## What You Need
 
**Required (ask in one message if missing):**
- **Product name** — e.g. "AquaPure Water Purifier" or "Surgical Room Booking"
- **Key benefit / hook** — the one thing that makes this product worth buying
**Asked separately (ALWAYS ask after collecting the basics, before generating):**
- **Banner size preference** — "Which size would you like — **Leaderboard (728×90)**, **Big Box (300×250)**, or **both**?" Wait for the answer.
**Optional:** price or offer, brand colour, CTA text, target audience or vibe.
 
---
 
## Workflow Overview
 
```
1. Collect product name + key benefit (ask once if missing)
2. Ask which size: Leaderboard / Big Box / both  → WAIT
3. Plan 3 angles + write copy for each
4. Build EVERY banner as a self-contained HTML file  (3 variations × chosen size(s))
5. Show them rendered + present a NUMBERED menu of all banners  → WAIT for a number
6. Recreate ONLY the picked banner as a native editable Canva design
   (Canva:generate-design → user picks closest candidate → create-design-from-candidate)
7. Share the Canva edit link + offer next steps
```
 
The two things that make this skill correct: **never call Canva before the user picks a number**, and **only ever send the one picked banner to Canva**.
 
---
 
## Steps
 
### 1. Detect intent and collect product basics
If product name + key benefit are missing, ask for both in one consolidated message.
 
### 2. Ask which banner size they want
ALWAYS ask before generating. Map answers: "leaderboard"/"lb"/"728"/"horizontal" → Leaderboard; "big box"/"bb"/"300x250"/"square" → Big Box; "both"/"all"/"either" → both.
 
### 3. Plan 3 distinct angles
- **Variation 1 — Bold & Benefit-driven:** lead with the biggest benefit, energetic emojis (🔥💥⚡).
- **Variation 2 — Offer / Urgency-driven:** lead with discount or scarcity, urgency emojis (⏰🛒💸).
- **Variation 3 — Lifestyle / Aspirational:** lead with how the product improves life, soft emojis (✨🌿💫).

### 4. Write copy for each variation
- Headline (max 6 words, with 1–2 emojis)
- Subhead (max 12 words)
- CTA button text (2–3 words)
- Punchy, scannable, active voice. Real copy — never placeholder text.

### 5. Build every banner as HTML (CORE BUILD STEP)
 
Use the bundled generator — **`scripts/build_banners.py`** — rather than writing the HTML by hand each time. It enforces the locked typography, scales sizes to fit each format, bakes in an on-brand SVG emblem, and produces both the individual banner files and the numbered gallery in one pass, so every run is consistent.
 
Write a `spec.json` with the product, the chosen size(s), and the 3 variations (angle + palette + copy you wrote in step 4), then run:
 
```bash
python scripts/build_banners.py --spec spec.json --out <session-outputs>/banner-ads
```
 
Spec shape (sizes ⊆ {`leaderboard`, `bigbox`}; exactly 3 variations):
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
 
The script writes one self-contained HTML file per banner (3 × the chosen size(s) → 3 or 6 files), plus `gallery.html` with a number badge on each banner, and **prints the numbered menu to stdout** — show that menu to the user verbatim in step 6. Use the `present_files` tool to share `gallery.html` first, then the individual files.
 
If for some reason the script can't run, fall back to building the same self-contained HTML by hand using the **Locked Typography** spec below — but prefer the script.
 
### 6. Present the NUMBERED menu and WAIT
 
After showing the previews, list every banner with a number so the user can pick the exact one they want. Number across variations and sizes. Example for "both":
 
```
Here are all 6 banner previews 🎨 — pick the ONE you want me to make editable in Canva:
 
1. Variation 1 · Bold        — Leaderboard 728×90
2. Variation 1 · Bold        — Big Box 300×250
3. Variation 2 · Offer       — Leaderboard 728×90
4. Variation 2 · Offer       — Big Box 300×250
5. Variation 3 · Lifestyle   — Leaderboard 728×90
6. Variation 3 · Lifestyle   — Big Box 300×250
 
Reply with a number and I'll recreate that one as a fully editable Canva design.
```
 
For a single size, the menu is just 1–3. **Do NOT call any Canva tool yet. Wait for the number.** This selection step is the whole point of the skill — it is what lets the user choose the image they actually want before anything is stored in Canva.
 
### 7. Recreate the picked banner as an editable Canva design
 
Once the user gives a number, take **only that banner** and recreate it natively in Canva so its text and colours are editable.
 
**7a. Call `Canva:generate-design`** with `design_type: "poster"` and a prompt that reproduces the picked HTML banner:
- The exact headline (with emoji), subhead, and CTA text from that variation
- The exact colour palette (hex codes) used in that HTML banner
- The locked typography hint (see below)
- Size hint: *"Leaderboard banner 728x90 pixels"* or *"Big Box format 300x250 pixels"* to match the picked banner
- The angle's aesthetic (bold / urgent / lifestyle) and any product-specific visual cue (e.g. "include a medical cross icon")
This returns several candidates with thumbnails.
 
**7b. Show the candidates and ask the user to pick the closest match** to their chosen HTML design (1, 2, 3…). Frame it as: *"Here are Canva's editable versions of your pick — choose the one closest to the preview (or say 'you pick')."* If they say "you pick", default to candidate 1 and say so.
 
**7c. Call `Canva:create-design-from-candidate`** with the chosen `candidate_id` + `job_id`. This returns the real `design_id` + `edit_url`. Without this call the design is NOT saved.
 
**7d. Share the `edit_url`** and confirm it's editable.
 
### 8. Wrap up
- One line describing the angle of the banner they chose
- The Canva `edit_url`
- Note they can edit text/colours/images directly in Canva
- Offer: recreate a different numbered banner too, tweak the copy, or export the HTML files.
---
 
## Locked Typography
 
Bake this into every HTML banner, and include it in the Canva `generate-design` prompt so the editable version matches.
 
**Headline:** Roboto Bold (700), 32px, 38px line-height. For Big Box, scale proportionally (≈22px / 26px). Never below 700 weight, never swap the font family.
 
**CTA button:** Roboto Medium (500), 16px, white (`#FFFFFF`) text on a solid colour fill, 6px border-radius, 12px vertical / 16px horizontal padding, 24px line-height. No gradients, shadows, or glows.
 
**Roboto font link (in every HTML file):**
```html
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@500;700&display=swap" rel="stylesheet">
```
 
**Headline CSS:**
```css
font-family: 'Roboto', sans-serif; font-size: 32px; font-weight: 700; line-height: 38px;
```
 
**CTA button CSS:**
```css
padding: 12px 16px; color: #FFFFFF; border: none; border-radius: 6px;
font-family: 'Roboto', sans-serif; font-weight: 500; font-size: 16px;
line-height: 24px; text-align: center;
```
Pick a button background per variation; the text stays `#FFFFFF`.
 
**Canva typography hint (paste into the generate-design prompt):**
> "Headline in Roboto Bold (700 weight), 32px, tight 38px line-height. CTA button in Roboto Medium (500 weight), 16px, white text on a solid colour fill, 6px border-radius, 12px/16px padding. No gradients, shadows, or glows."
 
Canva's AI won't honour this exactly, but it biases the output toward the locked system; the user can fix fonts in Canva afterward.
 
---
 
## Edge Cases
 
- **Missing inputs:** Ask once for product name + key benefit in one message.
- **Unclear size answer:** Re-ask once, else default to both and say so.
- **User picks before previews exist** (e.g. "just give me variation 2"): still build all HTML previews first so they can compare, then confirm their pick.
- **Invalid menu number** (e.g. "9" when only 1–6 exist): re-ask politely, restate the valid range.
- **User wants more than one made editable:** fine — recreate each picked number in Canva, one at a time.
- **User says "you pick" at the menu:** default to **1** (Variation 1) and say so.
- **`generate-design` fails:** retry once with a simpler prompt; if it still fails, tell the user the HTML files are ready to use and offer to retry Canva later.
- **`create-design-from-candidate` fails:** retry once, else share the candidate's preview URL so they can save it manually.
- **Restricted product** (weapons, adult, political, illegal): politely decline.
- **Only one variation requested:** still produce 3 — variety is the value.
- **Non-standard sizes:** build the standard one(s) first, then offer extras as a follow-up.
---
 
## Rules
 
**Always:**
- Produce exactly 3 distinct variations, each as real HTML banner(s).
- Ask which size BEFORE generating.
- Build HTML previews FIRST and present a NUMBERED menu of every banner.
- WAIT for the user's number before calling any Canva tool.
- Send ONLY the picked banner to Canva, recreated with its exact copy + hex palette + size + locked typography.
- Use real, punchy marketing copy — never lorem ipsum.
- Include 1–2 emojis per banner (not more) and a CTA on every banner.
- Share the Canva `edit_url` so the user can open and edit it.

**Never:**
- Call Canva before the user picks a number.
- Send unpicked variations to Canva.
- Skip `create-design-from-candidate` — without it the design isn't saved.
- Use placeholder text ("Lorem ipsum", "Your text here").
- Use more than 2 emojis on a single banner.
- Make medical / "100% guaranteed" claims unless the user provided them.
- Generate banners for restricted categories (weapons, adult, illegal, political).
- Skip the closing follow-up offer.
---
 
## Quick Reference: Tool Sequence
 
```
HTML phase (no Canva):
  scripts/build_banners.py --spec spec.json --out <out>   → N banner files + gallery.html
  present_files → show gallery + files
  show the script's printed numbered menu (1..N) → WAIT for user's number
 
Canva phase (picked banner only):
  1. Canva:generate-design
     └─ returns: job_id + candidates (candidate_id + thumbnail + preview URL)
  2. Show candidates → user picks closest match (default 1 if "you pick")
  3. Canva:create-design-from-candidate
     ├─ input: chosen candidate_id + job_id
     └─ returns: design_id + edit_url
  4. Share edit_url → offer to make another number editable
```
 ---
