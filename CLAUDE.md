# CLAUDE.md — Frontend Website Rules

## Always Do First
- **Invoke the `frontend-design` skill** before writing any frontend code, every session, no exceptions.

## Reference Images
- If a reference image is provided: match layout, spacing, typography, and color exactly. Swap in placeholder content (images via `https://placehold.co/`, generic copy). Do not improve or add to the design.
- If no reference image: design from scratch with high craft (see guardrails below).
- Screenshot your output, compare against reference, fix mismatches, re-screenshot. Do at least 2 comparison rounds. Stop only when no visible differences remain or user says so.

## Local Server
- **Always serve on localhost** — never screenshot a `file:///` URL.
- Start the dev server: `node serve.mjs` (serves the project root at `http://localhost:3000`)
- `serve.mjs` lives in the project root. Start it in the background before taking any screenshots.
- If the server is already running, do not start a second instance.

## Screenshot Workflow
- Puppeteer is installed globally. Chrome cache is at `~/.cache/puppeteer/`.
- **Always screenshot from localhost:** `node screenshot.mjs http://localhost:3000`
- Screenshots are saved automatically to `./temporary screenshots/screenshot-N.png` (auto-incremented, never overwritten).
- Optional label suffix: `node screenshot.mjs http://localhost:3000 label` → saves as `screenshot-N-label.png`
- `screenshot.mjs` lives in the project root. Use it as-is.
- After screenshotting, read the PNG from `temporary screenshots/` with the Read tool — Claude can see and analyze the image directly.
- When comparing, be specific: "heading is 32px but reference shows ~24px", "card gap is 16px but should be 24px"
- Check: spacing/padding, font size/weight/line-height, colors (exact hex), alignment, border-radius, shadows, image sizing

## Output Defaults
- Single `index.html` file, all styles inline, unless user says otherwise
- Tailwind CSS via CDN: `<script src="https://cdn.tailwindcss.com"></script>`
- Placeholder images: `https://placehold.co/WIDTHxHEIGHT`
- Mobile-first responsive

## Brand Assets
- Always check the `brand_assets/` folder before designing. It may contain logos, color guides, style guides, or images.
- If assets exist there, use them. Do not use placeholders where real assets are available.
- If a logo is present, use it. If a color palette is defined, use those exact values — do not invent brand colors.

## Images – Pexels (Automatic)
- **Never ship a finished website with `placehold.co` placeholder images.** Always replace them with real photos before pushing to GitHub.
- Use the Pexels free library: search via WebFetch/WebSearch on pexels.com. No API key required for browsing.
- Direct CDN URL format: `https://images.pexels.com/photos/{ID}/pexels-photo-{ID}.jpeg?auto=compress&cs=tinysrgb&w=1200` (use `w=1920` for hero backgrounds)
- Pexels license: commercial use allowed, no attribution required. Do not add credits to impressum.
- Search queries: use the trade/craft in German + English (e.g. "HVAC technician", "Klimaanlage", "Dachdecker roof", "Maler painting wall")
- **Always verify image IDs** match the trade before using. Fetch the Pexels photo URL and confirm the subject. A photo of food, landscapes, or unrelated objects must be rejected and replaced.
- **Hero image approach:** Use the Pexels photo as the actual background (opacity: 1), then two overlays: first a fixed dark layer `rgba(0,0,0,0.45)` for guaranteed contrast, then a brand-color gradient `linear-gradient(135deg, rgba(darkPrimary,0.60), rgba(primary,0.50))` for the color wash. Never use a solid gradient hero with the image ghosted at low opacity (< 0.3) — looks flat and artificial.
- **Contrast — mandatory:** White text must always be legible against the hero background. Never use a brand color as overlay if it's too light (perceived brightness > 100/255). Auto-darken light colors to ~35% of their original brightness before use. In addition, always add a fixed `rgba(0,0,0,0.45)` dark layer beneath the brand color overlay so text remains readable even if the brand color is very light or unexpected.
- Add a dark gradient overlay on hero images: `position:relative` wrapper + `<div style="position:absolute;inset:0;background:linear-gradient(to top,rgba(0,0,0,0.6),transparent)"></div>`
- Use `object-fit:cover` and `object-position:center` on all `<img>` tags inside fixed-height containers

## Anti-Generic Guardrails
- **Colors:** Never use default Tailwind palette (indigo-500, blue-600, etc.). Pick a custom brand color and derive from it.
- **Shadows:** Never use flat `shadow-md`. Use layered, color-tinted shadows with low opacity.
- **Typography:** Never use the same font for headings and body. Pair a display/serif with a clean sans. Apply tight tracking (`-0.03em`) on large headings, generous line-height (`1.7`) on body.
- **Gradients:** Layer multiple radial gradients. Add grain/texture via SVG noise filter for depth.
- **Animations:** Only animate `transform` and `opacity`. Never `transition-all`. Use spring-style easing.
- **Interactive states:** Every clickable element needs hover, focus-visible, and active states. No exceptions.
- **Images:** Add a gradient overlay (`bg-gradient-to-t from-black/60`) and a color treatment layer with `mix-blend-multiply`.
- **Spacing:** Use intentional, consistent spacing tokens — not random Tailwind steps.
- **Depth:** Surfaces should have a layering system (base → elevated → floating), not all sit at the same z-plane.

## SEO — Automatically Applied to Every Page
Every HTML page must include all of the following without being asked.

### `<head>` Meta Tags
```html
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>[Keyword-reicher Titel] | [Firmenname] – [Ort]</title>
<meta name="description" content="[150–160 Zeichen, enthält Hauptkeyword + Ort + CTA]">
<meta name="robots" content="index, follow">
<link rel="canonical" href="https://[domain]/[page]">

<!-- Open Graph (Social Sharing) -->
<meta property="og:title" content="...">
<meta property="og:description" content="...">
<meta property="og:type" content="website">
<meta property="og:url" content="...">
<meta property="og:image" content="...">

<!-- Geo (Local SEO) -->
<meta name="geo.region" content="DE-[Bundesland]">
<meta name="geo.placename" content="[Ort]">
```

### Heading Hierarchy (per page)
- Exactly **one `<h1>`** per page — contains the primary keyword + city
- `<h2>` for main sections (Leistungen, Über uns, Kontakt)
- `<h3>` for sub-items within sections
- Never skip heading levels (h1 → h3 without h2)
- Example for a craftsperson: `<h1>Dachdecker in Osnabrück – Reparatur & Neubau | Meier Bedachungen</h1>`

### Semantic HTML Structure
```html
<header> — Logo, Navigation
<main>
  <section aria-label="Hero"> — H1 here
  <section aria-label="Leistungen"> — H2 here
  <section aria-label="Über uns">
  <section aria-label="Bewertungen">
  <section aria-label="Kontakt">
</main>
<footer> — Adresse, Links, Copyright
```

### Images
- Every `<img>` needs a descriptive `alt` attribute with keyword: `alt="Dachdecker bei der Arbeit in Osnabrück"`
- Use `loading="lazy"` on all images below the fold
- Hero image: `loading="eager"` and `fetchpriority="high"`

### LocalBusiness Schema (JSON-LD)
Add to every page inside `<head>` — critical for local craftspeople Google ranking:
```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "LocalBusiness",
  "name": "[Firmenname]",
  "description": "[Kurzbeschreibung]",
  "url": "https://[domain]",
  "telephone": "[Telefonnummer]",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "[Straße]",
    "addressLocality": "[Ort]",
    "postalCode": "[PLZ]",
    "addressCountry": "DE"
  },
  "areaServed": "[Einzugsgebiet]",
  "priceRange": "€€",
  "openingHours": "Mo-Fr 07:00-18:00"
}
</script>
```

### Performance (affects Google ranking)
- Inline critical CSS — no external stylesheets that block rendering
- Fonts via `<link rel="preconnect">` + `display=swap`
- No unused JavaScript on page load
- Images: use correct dimensions, never scale down large images via CSS

### Internal Linking
- Every subpage links back to the homepage
- Footer contains links to all main pages
- Service subpages link to the contact page with a clear CTA

## Animations – Standard auf jeder Website
Jede Website bekommt diese 4 Effekte automatisch – kein Extra-Auftrag nötig:

1. **Page fade-in:** `body { animation: pageFadeIn 0.5s ease both; }` – Seite blendet beim Laden sanft ein.
2. **Hero slide-up:** Headline, Subtext und CTA-Buttons mit `hero-reveal`-Klassen (delay 0.1s / 0.25s / 0.4s / 0.55s) animiert reinfahren.
3. **Shiny CTA-Button:** Den primären Haupt-CTA-Button im Hero mit `.btn-glow` versehen – ein weißer Lichtschein sweept alle 2.4s von links nach rechts über den Button (kein Pulsieren). Umsetzung via `::after` Pseudo-Element mit `linear-gradient(90deg, transparent, rgba(255,255,255,0.65), transparent)` + `animation: shine`.
4. **Scroll reveal:** Alle Cards, Sections und Content-Blöcke unterhalb der Folds bekommen `.reveal` (+ `.reveal-d1/d2/d3` für gestaffelte Delays). IntersectionObserver triggert `.visible`-Klasse beim Einrollen.
5. **Counter-Animation:** Zahlen-Stats (z.B. 100%, 24h, 50+) mit `data-counter="[Zahl]" data-suffix="[%/+]"` versehen – zählen beim Sichtbarwerden hoch (Ease-out, ~1.4s).
6. **Card-hover lift:** Service- und USP-Karten mit `transform: translateY(-8px)` + brand-farbigem `box-shadow` beim Hover.
- Animationen nur auf `transform` und `opacity` – niemals `transition-all`.
- Easing: immer `cubic-bezier(0.16,1,0.3,1)` (spring-style) für natürliches Gefühl.

## Footer – Pflichtinhalt
- Unten links: © Jahr + Firmenname des Kunden
- Unten rechts (klein, dezent): `gebaut von <a href="https://meisterimnetz.de" target="_blank">Meister im Netz</a>`
- Link öffnet in neuem Tab, Farbe: #6b7280 (grau), Underline, hover → weiß

## Hard Rules
- Do not add sections, features, or content not in the reference
- Do not "improve" a reference design — match it
- Do not stop after one screenshot pass
- Do not use `transition-all`
- Do not use default Tailwind blue/indigo as primary color

## Batch Deploy (Netlify API)
- Site names must be **deterministic** — derived from the company name via a hash suffix, never random. This ensures re-runs update the same site instead of creating duplicates.
- Before creating a site, always check if it already exists (`GET /api/v1/sites?filter=all&name={siteName}`). If it exists, deploy directly to the existing site_id — no new create needed.
- Deploys to existing sites: 5s delay between runs. New site creation: 25s delay to respect Netlify's rate limit (~3 creates/min on free plan).
- Always save `site_id` in the results CSV so future runs can skip the lookup entirely.
- Always include a `_headers` file in the ZIP with `Content-Type: text/html; charset=UTF-8` so Netlify serves HTML correctly.
- Never re-deploy without checking for existing sites first — avoids duplicate site proliferation.

## GitHub & Deploy
- Lokal entwickeln, lokal testen.
- Erst auf explizite Anweisung ("push to GitHub") committen und pushen.
- Commit-Messages auf Englisch, kurz und beschreibend.
- Remote: https://github.com/meisterimnetz/website-test
- Netlify deployed automatisch sobald Code auf GitHub ist — nichts manuell deployen.
