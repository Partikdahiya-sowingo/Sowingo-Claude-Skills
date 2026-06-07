# Sowingo Email Builder

A multi-stage Claude skill that creates professional Sowingo customer marketing emails — in any language — and optionally drops the finished email into your Gmail as a **draft** for preview. It never sends on its own.

This is **Step 6** in the Sowingo marketing workflow.

---

## What it does

The skill runs in clear stages:

1. **Language** — asks which language to write the email in (English, Hindi, or any language).
2. **Product** — fetches product details from a Sowingo URL/SKU (via the Step 2 verify skill) or uses pasted details.
3. **Stage 1 — Concepts** — pitches **4 email concepts**, each with a subject line, preview text, objective, headline, and outline.
4. **Stage 2 — Build** — turns the chosen concept into a mobile-responsive, Outlook-safe HTML email built on Sowingo's real brand template.
5. **Stage 3 — Gmail preview (optional)** — loads the email into your Gmail as a draft so you can preview and send it yourself.

---

## When to use it

Trigger when you say *"Write Email"*, *"Customer Email"*, *"Promo / Discount / Welcome / Feedback Email"*, *"Draft customer email"*,
*"send me a preview"*, *"draft it in Gmail"*, or paste a Sowingo product URL/SKU and want email copy — even without the word "email".

---

## Inputs

- A Sowingo product **URL**, **SKU**, or raw **product details** (name, price, description, image).
- For the optional Gmail stage: a connected **Gmail connector** (used only to create a draft).

The skill uses Sowingo's brand assets, defined in `references/sowingo-email-design-system.md`, and builds from the skeleton at `assets/sowingo-marketing-email-template.html`.

---

## The four concepts

| # | Concept | Goal |
|---|---------|------|
| 1 | **Welcome Email** | Welcome new customers, introduce Sowingo, suggest first products |
| 2 | **Product Promotion** | Highlight a featured product, drive clicks to the product page |
| 3 | **Discount Offer** | Drive purchases with a time-limited discount |
| 4 | **Customer Feedback** | Request feedback or a review on a recent purchase |

You pick one (1–4) to build into a full HTML email.

---

## Brand palette

| Token | Hex | Use |
|-------|-----|-----|
| Text / headings | `#232f39` | Dark slate |
| Primary CTA | `#398de3` | Blue button, white bold label, 6px radius |
| Links | `#00a4bd` | Teal |
| Sale price | `#da741b` | Orange (with `#4e6374` strikethrough on old price) |
| Divider | `#cad2d8` | — |
| Content / page bg | `#ffffff` / `#f9fafb` | — |

---

## Technical requirements

- **Inline CSS only**, table-based layout (no flex/grid), max width **600px**.
- Email-safe fonts (Arial / Helvetica / sans-serif) and bulletproof, Outlook-compatible CTA buttons (no `<button>` tags).
- One primary CTA + one secondary CTA, both in the chosen language.
- Mobile-friendly stacking under 480px; RTL support for Arabic/Hebrew/Urdu.
- No JavaScript, web fonts, or tracking pixels.

---

## Output

A single `.html` file named `<concept-slug>-email-<product-slug>.html` (e.g. `discount-offer-email-titanium-implant.html`), shared for preview and download.

Optionally, a Gmail **draft** of the same email — subject pre-filled, HTML body, addressed to the recipient you name (defaults to a self-preview). The draft sits in your Gmail Drafts for you to review and send.

---

## Safety notes

- **Language and concept selection are never skipped** — the skill always asks before building.
- **The skill never sends email.** It only creates a Gmail draft when you ask; sending is always your own action from Gmail.
- Subject lines are kept spam-safe (no all-caps, no `!!!`, no "FREE!!!", under 60 characters).
- Customer names use a `{{first_name}}` placeholder — never hardcoded.

---
