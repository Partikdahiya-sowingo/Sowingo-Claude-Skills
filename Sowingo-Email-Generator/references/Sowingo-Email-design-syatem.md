# Sowingo Marketing Email — Design System
 
Extracted from Sowingo's real HubSpot marketing emails (the "Test send" preview
emails in the Sowingo inbox). Use these tokens so every email the skill builds
matches the brand's actual sent emails. When a value here conflicts with older
notes elsewhere in the skill, **this file wins** — it reflects what Sowingo
actually ships.
 
## Color tokens
 
| Role | Hex | Where it's used |
|------|-----|-----------------|
| Text / headings | `#232f39` | All body copy, headlines, product names (dark slate) |
| Muted text / old price | `#4e6374` | Strikethrough original price, secondary captions |
| Primary CTA button bg | `#398de3` | Main "Shop Now" style button, bold link emphasis (blue) |
| Link color | `#00a4bd` | Inline text links, "view in browser", secondary links (teal) |
| Sale / price highlight | `#da741b` | Discounted price, "As low as $X", urgency accents (orange) |
| Divider / border | `#cad2d8` | Hairline rules between sections |
| Content background | `#ffffff` | Inner 600px content card |
| Page background | `#f9fafb` | Outer body wrapper behind the card |
 
Notes:
- The CTA button is **blue `#398de3`**, NOT teal. Button label text renders in white.
- Price treatment: discounted price in **orange `#da741b`**, immediately followed
  by the original price with `text-decoration: line-through; color:#4e6374`.
- Older versions of this skill referenced `#00A99D` / `#1A1A1A`. Those are
  superseded — use the table above.
## Typography
 
- Font stack everywhere: `Arial, Helvetica, sans-serif`
- Body text: `16px`, `line-height:150%`
- Subheads / price line: `22px`
- Hero headline: `36px` (scale down on mobile)
- Footer / fine print: `13px`
- Weight: bold for headlines, prices, and CTA labels; normal for body
## Layout
 
- Doctype: `XHTML 1.0 Transitional` with Microsoft Office VML namespaces in `<html>`
- Outer wrapper background `#f9fafb`; centered content table `max-width:600px`
- Outlook conditional block in `<head>` (`<!--[if gte mso 9]> ... OfficeDocumentSettings ... <![endif]-->`)
- Meta: `x-apple-disable-message-reformatting`, UTF-8, `viewport width=device-width, initial-scale=1.0`
- Hidden preheader: `display:none; font-size:1px; color:#f9fafb; line-height:1px; max-height:0; max-width:0; opacity:0; overflow:hidden;` followed by padding entities (`&#847; &zwnj; &nbsp;` repeated) so the preview text isn't padded by body copy
## Components
 
### Header
- White background, Sowingo colour logo top, centered or left-aligned
- Logo asset: `https://hs-5545807.f.hubspotemail.net/hub/5545807/hubfs/Sowingo-colour-logo-canada.png`
  (use this real logo, or a `{{logo_url}}` placeholder if building generically)
- Often a "View in browser" link above/below the logo in `#00a4bd`
### Hero
- Full-width hero/collage image OR a colored block with the headline
- Headline 36px bold `#232f39`
### Product showcase / row
- Product image, product name (16–22px bold `#232f39`)
- Price line: `As low as $X` in `#da741b` 22px, then strikethrough original price in `#4e6374`
- Short benefit line in 16px `#232f39`
### CTA button (bulletproof, table-based — copy this pattern)
```html
<table role="presentation" border="0" cellpadding="0" cellspacing="0">
  <tr>
    <td align="center" valign="middle" bgcolor="#398de3"
        style="font-family:Arial, sans-serif; font-size:16px; color:#ffffff;
               word-break:break-word; border-radius:6px; cursor:auto;
               background-color:#398de3; mso-padding-alt:12px 18px;">
      <a href="{{cta_url}}" target="_blank"
         style="display:inline-block; padding:12px 18px; color:#ffffff;
                font-family:Arial, sans-serif; font-size:16px; font-weight:bold;
                text-decoration:none; border-radius:6px;">{{cta_label}}</a>
    </td>
  </tr>
</table>
```
- Primary CTA: blue `#398de3`, white bold label, 6px radius, 12px/18px padding
- Secondary CTA: a text link in `#00a4bd` (teal), bold, underlined — not a filled button
### Footer
- Light background, social icons row: Facebook, LinkedIn, Instagram (circle color icons)
- Unsubscribe link styled `color:#398de3` with `data-unsubscribe="true"` placeholder
- Sowingo address / copyright line in 13px `#4e6374`
## Email-client safety (carried over, still required)
- Inline CSS only; tables for layout; no flit/grid; no `<button>`; no JS; no web fonts
- VML conditional comments for Outlook where buttons/backgrounds need them
- Stacks cleanly under 480px; images with explicit width and `alt` text
