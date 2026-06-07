# Claude-Skills-Sowingo
A collection of Claude skills for Sowingo marketing automation — product verification, creates attractive messages, banner ads, campaign JSON, and email building.

# Marketing Skills

A collection of custom [Claude](https://claude.com) skills that automate Sowingo's marketing workflow end-to-end — from live product verification to ready-to-send emails, ad creatives, and campaign files.

Each skill is a self-contained folder with a `SKILL.md` file (and supporting scripts/assets where needed). The skills are numbered as steps because they're designed to flow into one another: verify a product, write copy for it, turn that copy into banners and a campaign JSON, and finish with a marketing email.

---

## What's inside

| Step | Skill | What it does |
|------|-------|--------------|
| 1 | **sow-mkt-step-1-fetch-and-verify-products** | Fetches a product live from Sowingo (via URL or SKU) and verifies stock/availability, with uses, advantages, and disadvantages. |
| 2 | **sow-mkt-step-2-generate-messages** | Produces five punchy, emoji-rich one-line marketing messages for launches, drops, sales, and promos. |
| 3 | **sow-mkt-step-3-generate-banner-ads** | Builds banner ad variations (Leaderboard 728×90 and/or Big Box 300×250) as editable HTML, then recreates the chosen one as a native Canva design. |
| 4 | **sow-mkt-step-4-create-json-file** | Generates a structured campaign JSON (BigBox + Leaderboard) with metadata, targeting, offer, and per-banner copy — ready for AdButler / Google Ad Manager. |
| 5 | **sow-mkt-step-5-email-builder** | Multi-stage customer email builder that pitches concepts, builds a mobile-responsive HTML email on Sowingo's brand template, and can load it into Gmail as a draft. |

---

## Suggested workflow

1. **Verify** the product is live and in stock → *step 1*
2. **Write** short marketing copy for it → *step 2*
3. **Design** banner ads from that copy → *step 3*
4. **Package** the campaign as JSON for your ad server → *step 4*
5. **Build** a customer email to promote it → *step 5*

---

## How to use these skills

These are [Claude skills](https://docs.claude.com) and run inside Claude (Cowork mode or Claude Code).

1. Place each skill folder inside your Claude skills directory (typically `.claude/skills/`).
2. Open Claude and describe what you want — for example *"verify this product"*, *"write an attractive message"*, or *"build a discount email"*.
3. Claude detects the matching skill from its description and runs it.

Each `SKILL.md` contains its own trigger phrases, step-by-step logic, and validation rules.

---

## Repository structure

```
.
├── README.md
├── sow-mkt-step-1-fetch-and-verify-products/
│   └── SKILL.md
├── sow-mkt-step-2-generate-messages/
│   └── SKILL.md
├── sow-mkt-step-3-generate-banner-ads/
│   ├── SKILL.md
│   └── scripts/
├── sow-mkt-step-4-create-json-file/
│   └── SKILL.md
└── sow-mkt-step-5-email-builder/
    ├── SKILL.md
    ├── assets/
    └── references/
```

---

## Notes

- These skills are tailored to Sowingo's products and brand. Some `SKILL.md` files reference internal URLs or templates — review them before sharing publicly.
- Skills are modular: you can use any one on its own, or chain them together.

---

*Maintained by Partik Dahiya.*
