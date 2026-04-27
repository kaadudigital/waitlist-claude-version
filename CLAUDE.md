# Kaadu — Shopify Theme: Developer Guide

This is a customised version of Shopify's **Shape** theme. It has been significantly redesigned to match the **Kaadu Design System** — a bold, protest-aesthetic brand identity built around the tagline *"Justice Never Tasted So Sweet."*

The live shop is locked behind a waitlist (`/pages/waitlist`). All non-waitlist, non-policy pages redirect there via a script in `layout/theme.liquid`.

---

## Local development

```bash
shopify theme dev --store esyumr-dw.myshopify.com
```

Run this from the project root. Shopify CLI will upload the theme as a development
theme, then print a local preview URL. Changes sync to the store in real time.
Authenticated as `digital@kaadu.ch`.

---

## Project layout

```
assets/           Compiled JS/CSS bundles + kaadu-ds.css (design system)
config/           settings_schema.json, settings_data.json
kaadu-design-system/  Source-of-truth design prototypes (NOT deployed)
layout/           theme.liquid (one layout for all pages)
sections/         Liquid section files
snippets/         Reusable Liquid partials
templates/        Page/product/collection JSON templates
```

---

## Design System

### Source files
`kaadu-design-system/project/` — HTML/CSS prototypes created with Claude Design.
Read `kaadu-design-system/project/ui_kits/website/Kaadu Website.html` to understand
the target visual language. **Do not deploy these files.** They are reference only.

The canonical CSS token file is:
`kaadu-design-system/project/colors_and_type.css`

### Deployed design system
`assets/kaadu-ds.css` — the live design system file loaded by Kaadu-branded sections.

It exposes CSS custom properties prefixed `--kds-*` and reusable component classes:

| Class | What it does |
|---|---|
| `.kds-btn` | Base pill button (border, shadow, hover lift) |
| `.kds-btn--display` | Fagies font, cream bg, red text — primary CTA |
| `.kds-btn--label` | Banter Grotesk, cream bg, black text — secondary CTA |
| `.kds-btn--inverse` | Transparent bg, cream border — for dark sections |
| `.kds-badge` | Pill badge / tag |
| `.kds-badge--lime` | Lime green badge |
| `.kds-badge--red` | Red badge, cream text |
| `.kds-ticker` | Scrolling marquee strip (border top/bottom) |
| `.kds-ticker__track` | Inner animated track (4 copies, -50% translateX loop) |
| `.kds-ticker__item` | Individual ticker text span |
| `.kds-card` | Cream card with black border and md shadow |
| `.kds-image-frame` | Organic blob-shaped image container |

Load it in any section:
```liquid
{{ 'kaadu-ds.css' | asset_url | stylesheet_tag }}
```

---

## Color tokens

### `--kds-*` custom properties (in kaadu-ds.css)

| Token | Hex | Use |
|---|---|---|
| `--kds-red` | `#AE0000` | Headlines, CTAs, primary brand |
| `--kds-violet` | `#542A76` | Dark sections, eyebrows, scheme3 bg |
| `--kds-pink` | `#FFC5FF` | Hero backgrounds, scheme1 bg |
| `--kds-orange` | `#FF781E` | Accents, product labels |
| `--kds-cream` | `#FFFFF5` | Light backgrounds, button fill, text on dark |
| `--kds-lime` | `#ACFF64` | Tickers, badges, scheme5 bg |
| `--kds-yellow` | `#EDF731` | Highlights |
| `--kds-sky` | `#B7D9FF` | Section backgrounds |
| `--kds-black` | `#000000` | Borders, text |

### Theme color schemes (from settings_data.json)

Used by standard Shape theme sections via `data-color-scheme="schemeN"`.

| Scheme | Background | Text | Use |
|---|---|---|---|
| `scheme1` | `#FFC5FF` (pink) | `#AE0000` (red) | Waitlist hero, default |
| `scheme2` | `#AE0000` (red) | `#FFFFF5` (cream) | CTAs, sale banners |
| `scheme3` | `#542A76` (violet) | `#FFFFF5` (cream) | Dark impact sections |
| `scheme4` | `#FF781E` (orange) | `#FFFFF5` (cream) | Accent sections |
| `scheme5` | `#ACFF64` (lime) | `#AE0000` (red) | Tickers, highlights |
| `scheme6` | `#FFFFFF` (white) | `#000000` (black) | Neutral / fallback |

Kaadu-branded sections use `--kds-*` tokens directly instead of the theme's
`rgb(var(--color-scheme-*))` pattern. Do not mix the two systems in the same
visual element.

---

## Typography

### How fonts are loaded

Fonts are declared globally in `snippets/css-bridge.liquid` using **direct Shopify CDN
URLs** (already uploaded). They load on every page — no section needs to declare
`@font-face` itself.

| CSS name | Weight | Use |
|---|---|---|
| `fagies` | 400 | Display / H1 headlines |
| `bantergroteskregular` | 400 | Body copy |
| `bantergrotesk` | 400 | Semibold variant (heavier body) |
| `thickhead` | 400 | Legacy display fallback (avoid for new work) |

### Theme CSS variables (set in theme.liquid)

```css
--heading-font-stack: fagies
--main-font-stack:    bantergroteskregular
```

### Design system font variables (in kaadu-ds.css)

```css
--kds-font-display: var(--heading-font-stack)  /* → fagies */
--kds-font-heading: var(--main-font-stack)      /* → bantergroteskregular */
--kds-font-body:    var(--main-font-stack)      /* → bantergroteskregular */
```

**Always use `var(--kds-font-display)` / `var(--kds-font-heading)` in design-system
sections.** Never hardcode `font-family: 'Fagies'` — the capitalisation `'Fagies'`
does not match the declared name `fagies`, and will silently fall back to serif.

### Type scale

| Role | Font | Size (desktop) | Transform |
|---|---|---|---|
| H1 / display | `var(--kds-font-display)` | 64–72px | uppercase |
| H2 / section heading | `var(--kds-font-heading)` | 24–32px | uppercase |
| Eyebrow label | `var(--kds-font-heading)` | 13px | uppercase, ls 0.1em |
| Body copy | `var(--kds-font-body)` | 18px | — |
| Button label | `var(--kds-font-heading)` | 16–20px | uppercase |
| Ticker | `var(--kds-font-display)` | 28px | uppercase |

---

## Waitlist pages

The shop is currently in waitlist mode. Key pages:

| Template | URL pattern | Section used |
|---|---|---|
| `templates/page.waitlist-3.json` | `/pages/waitlist*` | `sections/waitlist-hero.liquid` |
| `templates/page.waitlist-success.json` | `/pages/waitlist-success` | `sections/waitlist-success.liquid` |
| `templates/page.waitlist-2.json` | Legacy — leave untouched | Old `rich-text-with-image-custom` |

### Form submit → redirect flow

`kds-email-signup.liquid` uses a Shopify Contact form (`form_type: customer`, `contact[tags]: waitlist`).
On success Shopify redirects back with `?customer_posted=true`. An inline script
detects this and replaces the URL with the `success_handle` setting value
(default: `waitlist-success`).

Do not change the `action="/contact#contact_form"` or the hidden input values —
they are required by Shopify's contact form API.

### Hero → Email form scroll link

`kds-hero` has a `button_scroll_target` setting (default: `kds-email-signup`).
`kds-email-signup` has a static `id="kds-email-signup"` on its `<section>` tag.
The CTA button in the hero renders as `href="#kds-email-signup"` and smooth-scrolls
down the page. `html { scroll-behavior: smooth; }` is set in `kaadu-ds.css`.

### Accessing images set in the schema

The hero and success sections have `image_picker` settings. To assign the existing
product images (`shopify://shop_images/Group_18_1.webp`, `Group_21.webp`), set them
via the Shopify theme customizer (Online Store → Customize → select the page →
select the section → upload/select the image).

---

## KDS section library

These are the reusable Kaadu-branded building blocks. Each is a standalone
Shopify section that loads `kaadu-ds.css` and requires no extra dependencies.
Combine them in any order inside a template JSON.

| Section file | Schema name | Purpose |
|---|---|---|
| `sections/kds-hero.liquid` | KDS — Hero | Two-column image + text hero. Configurable image position (left/right), background colour, CTA that scroll-links to any element ID. |
| `sections/kds-value-prop.liquid` | KDS — Value Prop | Full-width coloured strip with an eyebrow label and body copy. Default: red bg, cream text. Use between hero and email form to communicate the offer. |
| `sections/kds-email-signup.liquid` | KDS — Email Signup | Centred email capture form (pill input + display-font button). Handles the Shopify contact-form submit and redirects to the success page. Has a static `id="kds-email-signup"` for the hero scroll link. |
| `sections/kds-ticker.liquid` | KDS — Ticker | 56 px scrolling marquee strip. Drop between any two sections. Default: lime bg, red text. |
| `sections/waitlist-success.liquid` | Waitlist Success | Post-signup confirmation page. Mirrors the hero layout without a form. |

### Current waitlist page section order (`templates/page.waitlist-3.json`)

```
kds-hero          pink bg   — headline + body + image + CTA button
kds-ticker        lime bg   — "ITS NOT FAIR!"
kds-value-prop    red bg    — "Pioniere profitieren" + offer text
kds-email-signup  red bg    — email form → redirects to waitlist-success
```

---

## How to build new Kaadu-branded sections

### Minimal template

```liquid
{{ 'kaadu-ds.css' | asset_url | stylesheet_tag }}
{% render 'kaadu-fonts' %}  {%- comment -%} no-op: documents font intent {%- endcomment -%}

<section class="my-section" data-color-scheme="{{ section.settings.color_scheme }}">
  <div class="my-section__container">
    <h2 class="my-section__heading">{{ section.settings.heading }}</h2>
    <div class="my-section__body">{{ section.settings.body }}</div>
    <a href="{{ section.settings.cta_url }}" class="kds-btn kds-btn--display">
      {{ section.settings.cta_label }}
    </a>
  </div>
</section>

<style>
  .my-section {
    background-color: var(--kds-pink);
    padding: var(--kds-s16) 0;
  }
  .my-section__container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 var(--kds-s8);
  }
  .my-section__heading {
    font-family: var(--kds-font-display);
    font-size: 64px;
    text-transform: uppercase;
    color: var(--kds-red);
  }
  .my-section__body {
    font-family: var(--kds-font-body);
    font-size: 18px;
    color: var(--kds-black);
  }
  @media (max-width: 767px) {
    .my-section__heading { font-size: 44px; }
  }
</style>

{% schema %}
{
  "name": "My Section",
  "settings": [ ... ],
  "presets": [{ "name": "My Section" }],
  "disabled_on": { "groups": ["header", "footer", "aside"] }
}
{% endschema %}
```

### Ticker strip

```liquid
{%- if section.settings.show_ticker -%}
<div class="kds-ticker" style="background-color: {{ section.settings.ticker_bg }};" aria-hidden="true">
  <div class="kds-ticker__track">
    {%- for i in (1..4) -%}
      <span class="kds-ticker__item" style="color: {{ section.settings.ticker_color }};">
        {{ section.settings.ticker_text }}
      </span>
    {%- endfor -%}
  </div>
</div>
{%- endif -%}
```

Four copies are required — the animation moves `-50%` of the total width to create
a seamless loop.

### CSS class naming convention

- Kaadu Design System reusable classes: `kds-*`
- Section-specific classes: `section-name__element` (BEM, no kds- prefix)
- Do not use or override the theme's `bg-scheme-*`, `text-scheme-*`,
  `py-section-vertical-spacing` in custom sections — those belong to the Shape
  theme's pattern and conflict with the kds- approach.

---

## Key files quick-reference

| File | Purpose |
|---|---|
| `assets/kaadu-ds.css` | Design system tokens + component classes |
| `snippets/css-bridge.liquid` | Global font-face + all CSS custom properties (color schemes, spacing, button vars). Rendered in `<head>`. |
| `snippets/kaadu-fonts.liquid` | Documentation-only no-op. Fonts already in css-bridge. |
| `layout/theme.liquid` | Single layout. Waitlist redirect script lives here. Sets `--heading-font-stack` and `--main-font-stack`. |
| `sections/kds-hero.liquid` | KDS hero — image + text, configurable position |
| `sections/kds-value-prop.liquid` | KDS coloured text strip — eyebrow + body |
| `sections/kds-email-signup.liquid` | KDS email capture form — handles submit + redirect |
| `sections/kds-ticker.liquid` | KDS scrolling marquee strip |
| `sections/waitlist-success.liquid` | Post-signup confirmation page |
| `templates/page.waitlist-3.json` | Active waitlist page template |
| `templates/page.waitlist-success.json` | Success page template |
| `kaadu-design-system/project/colors_and_type.css` | Design token source of truth |
| `kaadu-design-system/project/ui_kits/website/Kaadu Website.html` | Visual reference prototype |

---

## Gotchas

1. **Font capitalisation matters.** The font is registered as `fagies` (lowercase)
   in css-bridge.liquid. Using `'Fagies'` (capital F) fails silently and falls
   back to serif. Always use `var(--kds-font-display)` or `var(--heading-font-stack)`.

2. **`base.bundle.css` is compiled — do not edit it.** It contains the Shape theme's
   Tailwind output. CSS overrides belong in section `<style>` blocks or in
   `assets/kaadu-ds.css`.

3. **Waitlist redirect is unconditional.** `layout/theme.liquid` redirects every
   page that isn't `/pages/waitlist*`, `/policies/*`, or `/challenge` to the
   waitlist. When working on other templates (e.g. product, collection), either
   temporarily disable the redirect script or use the Shopify theme preview
   with `?preview_theme_id=`.

4. **Color schemes use `rgb()` wrapper.** The Shape theme stores colors as comma-
   separated RGB channels: `--color-scheme-background: 255, 197, 255`. Always
   access them via `rgb(var(--color-scheme-background))`, never as hex. Kaadu
   sections avoid this pattern by using `--kds-*` hex values directly.

5. **Template JSON `"disabled": true` on `main-page`.** Every waitlist template
   disables the default `main-page` section (which renders page.content). Keep
   this — otherwise Shopify renders an empty page content area above the custom
   sections.

6. **Section IDs in template JSON must be unique strings.** When duplicating a
   template JSON, rename the section key (e.g. `"waitlist_hero"`) — it becomes
   the section ID passed to `section.id`.

7. **`{% render %}` cannot access the outer scope.** Snippets rendered with
   `{% render 'snippet' %}` are sandboxed. Pass variables explicitly:
   `{% render 'snippet', param: variable %}`.

---

## Design principles to maintain

- **Bold borders.** 2–3px solid black on buttons, cards, image frames.
- **Box shadows.** Offset (not blurred) shadows: `6px 8px 0 #000` on media,
  `0 4px 4px rgba(0,0,0,0.25)` on cards/buttons.
- **Pill shapes.** All buttons and badges use `border-radius: 30px`.
- **Uppercase everywhere** for display type and labels.
- **Section rhythm.** Alternate backgrounds using the color token set:
  pink → lime ticker → sky/violet → lime ticker → cream.
- **Organic image frames.** Product images use a blob border-radius:
  `60% 40% 55% 45% / 50% 60% 40% 50%`.
- **No drop shadows on text.** The design uses text color contrast, not shadow,
  for legibility.
