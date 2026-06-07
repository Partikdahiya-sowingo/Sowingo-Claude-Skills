# Generate Messages

A Claude skill that turns a product into **5 short, punchy marketing message variations** — perfect for launches, new arrivals, restocks, sales, and trending products.

Every message is a single line with emojis, written in simple English, and built to drive excitement, FOMO, or curiosity.

---

## What it does

Given a product, the skill produces exactly **five** one-line messages, each using a different marketing angle so you always have variety to choose from:

1. **Excitement / launch** — "just dropped", "now live"
2. **Benefit / value** — the main reason to buy
3. **Urgency / scarcity** — "only 10 left", "today only"
4. **Curiosity / hook** — a surprising or intriguing line
5. **Social proof / trend** — "everyone's talking about it"

---

## When to use it

Trigger this skill when you want short marketing copy for a product, including:

- Phrases like *"write attractive message"*, *"write launch message"*, *"create marketing message"*, *"promo line"*, *"hype message"*, *"trending product caption"*
- Providing a product name, category, or link and asking for a caption, tagline, hook, or hype message
- Mentioning a launch, drop, sale, deal, discount, restock, or "back in stock"
- Asking for Instagram, WhatsApp, SMS, push notification, banner, or email subject-line copy

---

## Inputs

| Input | Required | Notes |
|-------|----------|-------|
| Product name | Yes | The only required input |
| Category | Optional | e.g. skincare, dental, electronics |
| Key benefit | Optional | The #1 reason to buy |
| Offer / discount | Optional | e.g. "20% off", "Buy 1 Get 1" |
| Target audience | Optional | e.g. dentists, parents, gamers |

If only a product name is given, the skill proceeds with smart defaults — it won't over-ask.

---

## Output format

```
Variation 1:
<one-line message with emojis>

Variation 2:
<one-line message with emojis>

Variation 3:
<one-line message with emojis>

Variation 4:
<one-line message with emojis>

Variation 5:
<one-line message with emojis>
```

No preamble, no explanation — just the five variations.

---

## Example

**Input:** *"Write attractive message for our new whitening toothpaste, 20% off this week"*

**Output:**

```
Variation 1:
✨ Just dropped: the whitening toothpaste everyone's smiling about — 20% off this week only! 🦷

Variation 2:
😁 Brighter smile in days, not weeks — grab it now at 20% off! 💎

Variation 3:
⏰ Hurry! Our viral whitening toothpaste is 20% off — sale ends Sunday! 🚨

Variation 4:
🤔 What if your smile could turn heads in just 7 days? Try it now at 20% off! 😍

Variation 5:
🔥 Trending everywhere — the whitening toothpaste fans love is 20% off this week! 🌟
```

---

## Rules

**Always:**

- Keep every message to **one line**.
- Use simple, clear English anyone can read.
- Add 1–3 relevant emojis per line.
- Focus on **benefits and feelings**, not technical features.
- Create urgency or excitement.
- Produce exactly **5 distinct variations**.

**Never:**

- Write paragraphs or multi-line messages.
- Use jargon or corporate-speak ("synergy", "leverage", "best-in-class").
- Make false claims, fake discounts, or fake scarcity.
- Copy a competitor's tagline.
- Use more than 3 emojis in one line.

---

*Part of the [Sowingo Marketing Skills](../README.md) suite — Step 3.*
