---

name: Sowingo-Email-Generator

description: Multi-stage Sowingo customer marketing email builder with optional Gmail preview. First asks which language to write in (English, Hindi, or any language). STAGE 1 pitches 4 concepts (Welcome, Product Promotion, Discount Offer, Customer Feedback) with subject, preview text, objective, headline, and outline. STAGE 2 builds the chosen concept into a mobile-responsive, Outlook-safe HTML email using Sowingo's real brand template (hero, product showcase, primary + secondary CTAs). STAGE 3 (optional) loads the email into the user's Gmail as a DRAFT to preview and send themselves — never sends on its own. Auto-fetches product details from a Sowingo URL, SKU, or pasted info. Use whenever the user says "Write Email", "Customer Email", "Promo/Discount/Welcome/Feedback Email", "Draft customer email", "send me a preview", "draft it in Gmail", "put it in my email", or pastes a Sowingo product URL/SKU and wants marketing email copy or an HTML email — even without the word "email".

---
 
# Sow Mkt Step 6 — Email Builder
 
A skill for creating professional Sowingo customer marketing emails: first ask the user what language to write in,
then pitch 4 concepts, then build the chosen one into a complete, mobile-friendly, conversion-focused HTML email — 
all in the chosen language. Optionally, as a final stage, load the finished email into the user's Gmail as a
**draft** so they can preview the real rendering and send it themselves from their inbox.
 
---
 
## When to Use This Skill
 
Trigger this skill whenever the user:
- Says "Write Email", "Create Customer Email", "Draft customer email", or "email ideas for Product"
- Says "Promo Email", "Discount Email", "Welcome Email", or "Feedback Email"
- Asks for marketing email copy, an HTML customer email, or a ready-to-send email
- Says "send email to customers", "marketing email for this product", or similar
- Pastes a Sowingo product URL, SKU, or product details and wants an email
- Wants email outreach for a new launch, sale, discount, or feedback request
When triggered, **always start with Step 0 (language selection)** before anything else.
 
## What You Need
 
- Either a Sowingo product **URL**, **SKU**, OR raw **product details** (name, price, short description, image link)
- Sowingo brand assets (matched to Sowingo's real marketing emails — see `references/sowingo-email-design-system.md` for the full token list):
  - Text & headings: **#232f39** (dark slate)
  - Primary CTA button: **#398de3** (blue), white bold label, 6px radius
  - Link color: **#00a4bd** (teal)
  - Sale / price highlight: **#da741b** (orange), with **#4e6374** strikethrough on the old price
  - Divider: **#cad2d8**; content bg **#ffffff**; page bg **#f9fafb**
  - Sowingo logo URL: `https://hs-5545807.f.hubspotemail.net/hub/5545807/hubfs/Sowingo-colour-logo-canada.png` (or a `{{logo_url}}` placeholder if not given)
  - Footer text: "Sowingo • The smart way to manage your practice"
- The reusable HTML skeleton at `assets/sowingo-marketing-email-template.html`
- Access to the **sow-mkt-step-2-fetch-and-verify-products** skill (used when a URL/SKU is provided)
- For the optional Gmail preview stage: the user's **Gmail connector** must be connected (used only to create a draft — never to send)
---
 
## Steps
 
### Step 0 — Language Selection (ALWAYS FIRST)
1. Before doing anything else, ask the user:
   > "Which language should I write this email in? (e.g., English, Hindi, Spanish, French, or any other language you prefer)"
2. Wait for an explicit answer. Accept any language the user names — do not restrict to a fixed list.
3. Store the chosen language and use it for:
   - All Stage 1 concept output (concept names can stay in English as labels, but subject lines, preview text, headlines, and outlines must be in the chosen language)
   - All Stage 2 HTML email body copy (subject, preheader, headline, paragraphs, CTA labels, footer microcopy)
   - The product confirmation message you show in Step 2 (translate the "Should I move forward with this product?" prompt into the chosen language)
4. If the user has not yet provided a product, you may collect the language and the product details together in one turn — but the language question MUST be asked.
5. If the user explicitly skips the language question or says "any language is fine" / "you decide", default to **English**.
### Step 1 — Collect the Product
1. Ask: "Share the product URL, SKU, or product details (name, price, description, image)."
2. If a URL or SKU is provided, call the **sow-mkt-step-2-fetch-and-verify-products** skill to pull live product info (name, price, image URL, stock status, uses).
3. If only product details are typed, use them directly.
4. If the user says "the product I just sent" or refers to chat history, pull the most recent product context.
### Step 2 — Confirm the Product
5. Show a one-line product summary: name, price, stock status, image URL.
6. Ask: "Should I move forward with this product?" Wait for yes.
### Step 3 — Generate 4 Email Concepts (Stage 1)
7. Generate all 4 standard concepts at once, written in the **chosen language from Step 0**. For each, include:
   - **Concept name** (Welcome Email / Product Promotion / Discount Offer / Customer Feedback Email) — keep this label in English so the user can pick easily
   - **Subject line** — under 60 characters, punchy, spam-safe (avoid all-caps, multiple !!!, words like "FREE!!!"), with 1 emoji, in the chosen language
   - **Preview text** — under 90 characters, complements the subject, in the chosen language
   - **Campaign objective** — one short line stating the goal (e.g., "Drive first-purchase conversions from new sign-ups")
   - **Headline** — the main on-email headline, max 8 words, in the chosen language
   - **Outline** — 3–5 short bullets describing the sections of that email, in the chosen language
8. Present them in a clean numbered list (1–4) with clear visual separation between each concept.
### Step 4 — User Picks a Concept
9. Ask: "Which concept should I build into a full email? Reply with 1, 2, 3, or 4."
10. Wait for an explicit pick. If the user says something outside 1–4, ask again.
### Step 7B — Build the Full HTML Email (Stage 2)
11. **Start from the real Sowingo template.** Read `assets/sowingo-marketing-email-template.html` and use it as the structural base, and read `references/sowingo-email-design-system.md` for the exact color and type tokens. This template mirrors the layout, colors, fonts, CTA, and footer of Sowingo's actual sent marketing emails — match it rather than inventing a new look. Fill in the `{{placeholders}}` with the chosen concept's content. **All visible body copy, headlines, paragraphs, CTA button labels, footer microcopy, and the preheader must be written in the language chosen in Step 0.** Code, attributes, brand names ("Sowingo"), and URLs stay as-is. The template's sections, in order:
    - **Preheader (hidden)** — preview text, hidden but read by inbox clients
    - **Header** — Sowingo colour logo on white background (max-height 40px)
    - **Hero banner** — product image OR a colored block with the concept's headline
    - **Headline** — bold `#232f39`, attention-grabbing, max 8 words, 36px on desktop
    - **Marketing copy** — 2–3 short paragraphs (under 50 words each), persuasive and tailored to the chosen concept's tone
    - **Product showcase / product card** — image (300×300), product name, price (sale price in orange `#da741b` + strikethrough old price in `#4e6374` when there's a discount), short benefit line
    - **CTA hierarchy** — ONE primary CTA button (blue `#398de3`, white bold label, 6px radius) + ONE secondary CTA as a teal `#00a4bd` text link with a different action label (e.g., primary "Shop Now" + secondary "Explore the Catalog"; primary "Claim 20% Off" + secondary "Browse all deals"). Both in the chosen language.
    - **Footer** — Sowingo branding, social icons (Facebook, LinkedIn, Instagram), unsubscribe link (`#398de3`), copyright line
12. Technical requirements:
    - Build from `assets/sowingo-marketing-email-template.html`; do not drift from its palette or structure
    - **Inline CSS only** (no external sheets); tables for layout, no flex/grid
    - **Max width 600px** on desktop, full width on mobile
    - **Email-safe fonts:** Arial, Helvetica, sans-serif (16px body / 22px subheads / 36px hero / 13px footer)
    - **Palette (from the design system):** text `#232f39`, primary CTA `#398de3`, link `#00a4bd`, sale price `#da741b`, old-price strikethrough `#4e6374`, divider `#cad2d8`, content bg `#ffffff`, page bg `#f9fafb`
    - **Bulletproof button** (table-based, `bgcolor` + `border-radius:6px` + `mso-padding-alt:12px 18px`, no `<button>` tags) — works in Outlook
    - **Outlook compatibility:** keep the MSO conditional block from the template; use VML for backgrounds if needed
    - **Mobile-friendly:** stacks cleanly under 480px
    - Use `{{first_name}}` placeholder for personalization if the user hasn't given a name
    - For RTL languages set `{{dir}}` to `rtl` and `{{align}}` to `right`
    - No JavaScript, no web fonts, no tracking pixels
### Step 5 — Save & Share
13. Save the file as:
    `/sessions/sharp-fervent-newton/mnt/outputs/<concept-slug>-email-<product-slug>.html`
    Example: `discount-offer-email-titanium-implant.html`
14. Share a `computer://` link so the user can preview and download.
15. Show a 1-line summary: "Here's your [Concept Name] email for [Product] — open the file to preview."
### Step 6 — Preview in Gmail (Stage 3, OPTIONAL — only if the user wants it)
This stage loads the finished email into the user's Gmail as a **draft** so they can see how it renders in a real inbox and send it themselves. It NEVER sends the email.
 
16. After sharing the HTML file, offer the preview:
    > "Want me to drop this into your Gmail as a draft so you can preview it in your inbox and send it yourself?"
    Only continue this stage if the user says yes. If they decline, stop after Step 15 — the HTML file is the final deliverable.
17. Ask **who the preview draft should be addressed to**:
    > "What email address should I put in the To field? (Use your own address to send yourself a test preview.)"
    Default to a self-preview (the user's own Gmail address) when the user just wants to "see how it looks."
18. Confirm the Gmail connector is available. Before calling any Gmail tool, run `tool_search` (e.g. query "gmail create draft") to load the connector and get the exact parameter names — do NOT guess the schema. The relevant tool is **Gmail `create_draft`**.
19. Create the draft with:
    - **To:** the recipient address from Step 17
    - **Subject:** the exact subject line from the chosen concept (in the chosen language)
    - **Body:** the full generated **HTML** email as the message body, sent as HTML content (not plain text) so the rendering, brand color, and CTA buttons display correctly. If the Gmail tool needs a flag/field to mark the body as HTML, set it.
20. Tell the user the draft is now in **Gmail → Drafts**, and that they can open it, preview the real rendering, and press send themselves when ready. Do this in the chosen language if the rest of the conversation is in that language.
21. **NEVER send the draft.** Creating a draft is allowed; sending is always the user's own action from Gmail. Do not call any send/schedule endpoint, and do not "auto-send after creating the draft," even if the user earlier said "send it" — clarify that they should review and send from Gmail, or confirm explicitly per message before any send is attempted.
---
 
## Concept Templates (Used in Stage 1)
 
### 1. Welcome Email
- **Goal:** Welcome new customers, introduce Sowingo, suggest first products
- **Subject example:** "👋 Welcome to Sowingo — here's what's inside"
- **Outline bullets:** Greeting → why Sowingo → featured product → next steps → support
### 2. Product Promotion Email
- **Goal:** Highlight a featured product, drive clicks to the product page
- **Subject example:** "✨ Meet your new must-have for the clinic"
- **Outline bullets:** Hook → product story → 3 benefits → CTA → social proof
### 3. Discount Offer Email
- **Goal:** Drive purchases with a time-limited discount
- **Subject example:** "🔥 24 hours only — save 20% on [product]"
- **Outline bullets:** Urgency hook → offer details → product showcase → CTA → expiry reminder
### 4. Customer Feedback Email
- **Goal:** Request feedback or review on a recent purchase
- **Subject example:** "💬 How are we doing? We'd love your thoughts"
- **Outline bullets:** Thank you → ask for feedback → short survey link → reward incentive → sign-off
---
 
## Edge Cases & Validation
 
- **No language chosen / user skipped Step 0** → default to English and proceed (do not block the workflow indefinitely).
- **Right-to-left language chosen (Arabic, Hebrew, Urdu)** → add `dir="rtl"` to the `<body>` (or main wrapper `<table>`) and `text-align: right` on copy blocks. Keep the Sowingo logo top-left for brand consistency unless the user asks otherwise.
- **Mixed languages** (e.g., "Hindi but keep CTAs in English") → honor exactly what the user asks; don't override their preference.
- **Invalid URL / 404 / fetch fails** → tell the user, ask for a correct URL or typed details. Do NOT guess product details.
- **Out of stock** → warn the user before proceeding. Suggest pausing a promo email or switching to Welcome / Feedback concept.
- **Missing price or image** → ask the user to fill the gap before building HTML. For missing image, use a styled colored block placeholder with the product name.
- **User picks a number outside 1–4** → ask them to pick again. Do not assume.
- **User wants more than one concept built** → build them one at a time, separate files. Confirm each.
- **No product context at all** → cannot proceed. Ask for at least a product name and one detail.
- **Customer name not provided** → use `{{first_name}}` placeholder.
- **Discount offer concept without a discount amount** → ask for the % off and expiry date.
- **Gmail preview requested but connector not connected** → tell the user to connect Gmail (Settings → Connectors), then retry. Until then, the HTML file is the deliverable — do not fail the whole task.
- **Gmail draft creation fails / auth error** → report it plainly, keep the saved HTML file, and offer to retry after the user reconnects. Never silently drop the email.
- **User asks to actually send the email (not just draft)** → do not auto-send. Create the draft and explain they can review and send it from Gmail. Only attempt a real send if explicitly confirmed per message, and never as an automatic follow-on to draft creation.
- **No recipient given for the Gmail preview** → default to a self-preview to the user's own address; if that's unknown, ask once.
---
 
## Output Format
 
A single `.html` file in `/sessions/sharp-fervent-newton/mnt/outputs/` named:
`<concept-slug>-email-<product-slug>.html`
 
The file must:
- Render correctly in Gmail, Outlook, Apple Mail, and on mobile clients
- Be under 600px wide on desktop, full-width on mobile
- Use **inline CSS only**
- Include the Sowingo logo and the brand palette (text `#232f39`, blue CTA `#398de3`, teal links `#00a4bd`, orange sale price `#da741b`)
- Have exactly **one primary CTA button** per email
- Include a complete footer with Sowingo branding, social links placeholders, contact, and unsubscribe placeholder
- Pass a basic HTML email lint (no broken tags, no JavaScript, no external stylesheets)
**Optional secondary output (Stage 3, only when requested):** a Gmail **draft** containing the same email — subject pre-filled, HTML body, addressed to the recipient the user names. The draft sits in the user's Gmail Drafts for them to preview and send. The skill itself never sends.
 
---
 
## Rules
 
- ALWAYS ask the user what **language** to use BEFORE generating any concepts or HTML (Step 0). Never skip this. Default to English only if the user explicitly declines to choose.
- ALWAYS write subject lines, preview text, headlines, body copy, CTA labels, and footer microcopy in the **chosen language** — concept labels in Stage 1 can stay in English for picker clarity.
- ALWAYS show the 4 email concepts in Stage 1 first — never jump straight to building the full HTML
- ALWAYS wait for the user to explicitly pick a concept (1, 2, 3, or 4) before starting Stage 2
- ALWAYS build Stage 2 from `assets/sowingo-marketing-email-template.html` and the tokens in `references/sowingo-email-design-system.md` — primary CTA blue **#398de3** (6px radius), text **#232f39**, links **#00a4bd**, sale price **#da741b**. Do not revert to the old **#00A99D** / **#1A1A1A** values.
- ALWAYS use inline CSS for maximum email-client compatibility
- ALWAYS save the file to `/sessions/sharp-fervent-newton/mnt/outputs/` and share a `computer://` link
- ALWAYS include ONE primary CTA + ONE secondary CTA (text link or smaller button) — both in the chosen language
- ALWAYS keep subject lines spam-safe: no all-caps words, no multiple !!!, no "FREE!!!", no $$$ symbols, no clickbait
- NEVER use external stylesheets, JavaScript, web fonts, or `<button>` tags
- ALWAYS treat the Gmail stage (Step 6) as optional and only run it when the user asks for a preview / wants it in their mail
- MAY create a Gmail **draft** for preview when the user requests it — but NEVER send the email or hit any send/schedule endpoint on the user's behalf. Sending is always the user's own action from Gmail.
- BEFORE calling any Gmail tool, run `tool_search` to load the connector and use the exact parameter names it returns — never guess the schema
- NEVER add tracking pixels, third-party scripts, or hidden trackers
- NEVER hardcode real customer names — use `{{first_name}}` placeholder
- NEVER skip the language-selection step or the concept-selection step, even if the user is in a hurry
- Keep subject lines under 60 characters and preview text under 90 characters
- Keep marketing copy paragraphs under 50 words each
- Primary CTA must be clear and action-oriented in the chosen language (e.g., "Shop Now" / "अभी खरीदें" / "Comprar ahora")
---
 
## Integration Notes
 
- **Authentication:** No credentials needed for building the email. Product fetch uses the existing sow-mkt-step-2 skill which handles its own access. The optional Gmail preview uses the user's connected Gmail connector (OAuth handled by the connector — the skill never enters passwords or tokens).
- **Gmail preview (Stage 3):** Uses the Gmail `create_draft` tool, loaded via `tool_search` at runtime so the current parameter names are used. The HTML email becomes the draft body (HTML content type); subject = the concept's subject line; To = the recipient the user names (default: a self-preview). Draft only — sending is left to the user inside Gmail.
- **Failure handling:** If the product fetch skill fails or times out, fall back to asking the user to type product details manually. If Gmail isn't connected or the draft call fails, keep the saved HTML file and tell the user how to connect/retry.
- **Data validation:** Confirm product name, price, and image URL before generating HTML. If price is missing on a Discount Offer concept, ask before building.
