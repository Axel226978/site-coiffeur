# Prompt: men's barber scroll hero ("L'art du geste précis.")

Build a single-file **scroll-driven barbershop site** in plain HTML, CSS and vanilla JS, with no framework and no build step. There is **no video**. All motion is driven by scroll position: a sticky, full-viewport black stage where a giant gold monogram draws itself, a photo is revealed through a widening "razor-line" mask, and a slow zoom pulls back from a close-up of a barber's hands to the whole chair. Over it the content scrolls normally: a big 3-line headline, a mirrored services block arranged along a vertical gold axis, a horizontal-scrolling price menu, the hours, a text-only testimonial section, and a booking footer.

The site is for a real men's salon. **All visible copy is in French.** Never invent facts about the salon: wherever a value is shown as `[LIKE THIS]`, output it literally as a visible placeholder so the owner can fill it in.

## Salon data

- **Name:** `[NOM DU SALON]` (the shop sign reads "HB — Coiffure For Men", with a crowned HB crest and a bearded-man medallion; confirm the exact spelling before publishing).
- **Monogram:** the letters "HB", drawn as an original serif monogram (do not copy the shop's logo; leave a slot `[LOGO SVG]` for the real one).
- **Address:** `[RUE ET NUMÉRO], [CODE POSTAL] [VILLE]`, plus a "Voir l'itinéraire" link `[LIEN GOOGLE MAPS]`.
- **Phone:** `[TÉLÉPHONE]`. **Booking link:** `[LIEN DE RÉSERVATION]`.
- **Hours** (real, keep exactly):
  - Lundi: Fermé
  - Mardi to Samedi: 09:00–19:00
  - Dimanche: 09:00–18:00
- **Services:** "Coupe homme" `[PRIX] €` is confirmed. Add these rows marked as optional, each with `[PRIX] €` and `[DURÉE]`, and a code comment `<!-- à confirmer -->`: "Taille de barbe", "Coupe + barbe", "Dégradé / fade", "Coupe enfant".

## Media

- **No video anywhere.** Nothing auto-plays.
- **Photos (placeholders):** use `<img>` tags with `src="[PHOTO_X].jpg"` and a neutral dark `#141210` fill with a thin gold frame while missing. Required shots, all landscape 2400×1600 unless noted:
  1. `[PHOTO_HERO]`: very close, top-down shot of a barber's hands, clipper or straight razor at the nape, warm side light, black background. The zoom-out starts here.
  2. `[PHOTO_CHAIR]`: the whole chair and mirror, wide, same light.
  3. `[PHOTO_DETAIL_1..3]`: portrait 1200×1600, finished fades and beard lines, client faces turned away or cropped.
  4. `[PHOTO_INTERIOR]`: the salon interior.
- **Rule:** photos are only transformed (scale, translate, clip-path) by scroll. No filters animated over time, no carousels, no autoplay.
- Do not use Google Street View captures or any third-party photos.

## Type and colour

- **Headline / display:** Cormorant Garamond 600, uppercase, tight tracking (−0.01em). Italic 500 for one accent word per headline.
- **UI text:** Manrope 700 for titles, eyebrows and buttons (uppercase, tracking .22em for eyebrows), 400 for body.
- **Colours:**
  - ink black `#0B0A09` (page and stage)
  - warm black `#141210` (cards, placeholders)
  - gold `#C9A45C`, and a brighter gold `#E6C98A` for highlights and the drawn monogram stroke
  - cream `#F3EDE2` (headline, titles)
  - body text cream at 78%
  - background after the stage `#EFE8DC` (paper cream) with ink `#16130F`
- **Gold rules:** 1px lines in gold at 55%. No gradients on text. Optional very subtle film-grain overlay (an inline SVG noise at 4% opacity, static).
- **Shadows:** soft, e.g. headline `0 10u 30u rgba(0,0,0,.55)`.

## Scale and structure

- **Unit:** the design is drawn on a 1280×800 frame. `u = min(100vh/800, 100vw/716)`. Vertical positions are written as `N/8 vh`.
- **Track:** `.track` (relative, height set by JS). Inside it:
  - `.stage`: `position:sticky; top:0; height:100vh` (100lvh). Layers, back to front:
    1. `.photo-hero` (`[PHOTO_HERO]`, object-fit cover)
    2. `.photo-chair` (`[PHOTO_CHAIR]`, starts hidden)
    3. `.mask`: the razor-line reveal (see below)
    4. `.monogram`: inline SVG "HB", 420u tall, centred, gold stroke 1.2u, no fill
    5. `.axis`: a vertical 1px gold line on the centre axis
    6. a light vignette (black at 0% centre to 45% edges)
  - `.content`: an absolute layer the full height of the track, holding the scrolling blocks.
- **Header (fixed):** 64px tall, transparent. Left: small monogram + `[NOM DU SALON]` in Manrope 700 12px tracking .3em. Right: a gold-outlined pill button "RÉSERVER" linking to `[LIEN DE RÉSERVATION]`. The header gains a `#0B0A09` 80% blurred background after the stage ends.

## Stage choreography (driven by progress `p`, 0 → 1 over the track)

| p range | What happens |
|---|---|
| 0 – 0.18 | Black stage. The HB monogram strokes draw in (`stroke-dashoffset` from full length to 0). The axis line grows from the centre to full height. |
| 0.10 – 0.38 | Razor-line reveal: `.mask` is a black layer with a horizontal slit cut through the middle using `clip-path: inset()`. The slit opens from 0 to 100% height, revealing `[PHOTO_HERO]`. A 1px bright-gold line sits on both edges of the slit, like a razor-sharp contour. The monogram fades to 18% opacity and stays as a watermark. |
| 0.30 – 0.70 | Slow pull-back: `[PHOTO_HERO]` scales from 1.35 to 1.0 and translates Y from 0 to −4%. |
| 0.62 – 0.78 | Cross-reveal: `[PHOTO_CHAIR]` is revealed with a vertical wipe from the axis outward (`clip-path: inset(0 50% 0 50%)` to `inset(0 0 0 0)`), so the image splits open along the gold axis. |
| 0.78 – 1 | The stage darkens to 70% black, the axis fades, and the cream section rises from below (see "After the stage"). |

Everything is reversible: scrolling back plays it backwards exactly.

## Scrolling blocks (desktop, positions from the top of the track)

- **Intro, top 900/8 vh.** Centred, 1280u wide:
  - eyebrow "BARBIER — COIFFURE HOMME": 7.4u, gold, tracking .3em
  - headline "L'ART / DU GESTE / *précis.*": 64u on a 64u line, one span per line, nowrap, the last word in italic gold
  - a 13u line under it in cream at 78%: "Coupe, dégradé et barbe, au millimètre."
  - Each headline line rises in with a 12u translateY and fades in, staggered by 40 design px of scroll.
- **Services along the axis:** each block is 300u wide, mirrored across the vertical gold axis. Each has:
  - a 30u gold line icon, 26u above the title, on the inner side near the axis
  - a short gold tick (24u horizontal rule) linking the block to the axis
  - an uppercase title, 19.6u on a 20u line, two lines max
  - a body of 12.3u on a 15.4u line, three lines max
- **Rows:**

  | Row | Top | Left column | Right column |
  |---|---|---|---|
  | 1 | 1500/8 vh | right-aligned, right edge 78u left of the axis | left-aligned, 78u right of it |
  | 2 | 1759/8 vh | right-aligned, 88u left of the axis | left-aligned, 88u right of it |
  | 3 | 2063/8 vh | the fifth block, centred on the axis | |

  1. **La coupe sur mesure** (scissors icon): "On regarde votre visage, vos épis et / votre rythme de vie avant de prendre / les ciseaux."
  2. **Dégradés nets** (three stepped bars, like a fade): "Du skin fade au dégradé bas, des / transitions propres qui tiennent / plusieurs semaines."
  3. **La barbe dessinée** (straight razor icon): "Taille, contours au rasoir et / finitions soignées pour une barbe / qui a de la tenue."
  4. **Prenez votre temps** (clock icon): "Ouvert du mardi au dimanche. / Un créneau réservé, c'est un / créneau respecté."
  5. **Un salon pour vous, messieurs** (crown icon): "Une ambiance calme, un café, et / quelqu'un qui écoute ce que vous / voulez vraiment."

## Scroll → stage mapping (JS)

- **Track length:** `end = lastBlock.bottom + 0.75 × viewport`, track height = `end + viewport`.
- **Progress:** `p = clamp((scrollY − track.top) / end)`.
- **Two-part mapping** (same idea as a camera that only moves fast once the text is gone): `knee = lastBlock.bottom / end`. While `p < knee`, stage progress = `p / knee × 0.70`. After that it runs from 0.70 to 1.
- **Smoothing:** each frame, `cur += (target − cur) × 0.18`; stop the rAF loop when `|target − cur| < 0.0005` and restart on scroll.
- **Writes:** set one CSS custom property `--p` on `.stage` and derive every transform in CSS with `calc()` and `clamp()` per range, or compute per-layer values in JS. Only animate `transform`, `opacity`, `clip-path` and `stroke-dashoffset`.
- **Reduced motion:** with `prefers-reduced-motion: reduce`, skip all scrubbing: show the monogram drawn, the hero photo fully revealed at scale 1, and let the blocks appear without movement.
- **Recorder mode (`?rec=1`):** a 6.5 s Web Animation drives the progress: hold to 3%, one ease (cubic-bezier .45,.05,.55,.95) to 94%, then hold, running 0.9 viewport past the track so the next section scrolls in. Expose `window.__recProgress(t)` that scrolls to that progress, so a frame-by-frame recorder is deterministic.

## After the stage

### 1. Carte des prestations (horizontal scroll)

- A `<section class="menu">` on `#EFE8DC`, pulled up 30vh over the end of the stage, starting with a 30vh gradient from transparent to `#EFE8DC`.
- A sticky inner panel (100vh) whose row of cards translates horizontally as you scroll vertically (section height = cards width + 100vh). Ink `#16130F`.
- Left fixed title: "LA / CARTE" in Cormorant 600 at clamp(40px, 6vw, 88px), "CARTE" in italic gold.
- Cards: 360×480px, `#16130F` background, cream text, 1px gold border. Each card: a large serif number (01, 02…), the service name, `[DURÉE]`, and the price `[PRIX] €` in gold. First card is "Coupe homme". Then the optional rows listed in Salon data.
- Between cards, insert the portrait shots `[PHOTO_DETAIL_1..3]` at 360×480 with a slow inner parallax (image translateX −6% to 6%).
- Under the row: a cream button with ink text "RÉSERVER UN CRÉNEAU" → `[LIEN DE RÉSERVATION]`.

### 2. Horaires

- Two-column grid (1fr and 1fr), max 1120px, 64px gutters, 120px vertical padding.
- Left: "NOS / HORAIRES" in display 600, and below it the address block and "Voir l'itinéraire".
- Right: one row per day (Lundi → Dimanche) with a 1px rule (ink at 16%). Day in Manrope 700 13px uppercase, hours right-aligned in Cormorant 500 22px. "Fermé" in grey `#8F877C`.
- Highlight today's row in gold with a small "Aujourd'hui" tag (computed with JS in the Europe/Brussels time zone), and show "Ouvert maintenant" / "Fermé maintenant" next to it.

### 3. Avis clients (text only)

- Same container. Header grid (1fr and 300px):
  - Left: "ILS NOUS / CONFIENT / LEUR TÊTE" in display 600 at clamp(34px, 4.4vw, 60px), line-height 1; the last line grey `#8F877C`.
  - Right: a 13px note at 70% ink: "Des clients fidèles, une coupe après l'autre."
  - A 1px rule under the header.
- Three rows, each a grid of 260px and 1fr, 44px vertical padding, 1px rule under each. No photos.
  - Left: `[PRÉNOM + INITIALE]` in 13px bold uppercase, then "Avis Google" in 12px at 60% ink with five small gold stars.
  - Right: `[TEXTE DE L'AVIS RÉEL]` in Cormorant 500 italic at clamp(19px, 1.8vw, 24px), line-height 1.4, max 680px.
  - Only real reviews copied with permission may replace the placeholders. Do not write fake reviews.

### 4. Footer / réservation

- Full-width `#0B0A09` block, 70vh. Centre: the HB monogram drawn again (stroke draws in when it enters the viewport via IntersectionObserver), then "VOTRE PROCHAINE COUPE / COMMENCE ICI." and two buttons: "RÉSERVER" (gold fill, ink text) and "APPELER" (gold outline) → `tel:[TÉLÉPHONE]`.
- Bottom bar: name, address, hours summary "Mar–Sam 9h–19h · Dim 9h–18h · Lun fermé", and `© [ANNÉE] [NOM DU SALON]`.
- Mobile only: a sticky bottom bar with "Réserver" and "Appeler", 56px tall, appearing after the stage.

## Portrait (≤ 609px wide, or any portrait aspect)

- **Unit:** `u = min(100vw/390, 1.6px)`.
- **Stage:** same choreography, monogram 260u tall; the razor slit opens vertically instead of horizontally.
- **Content:** one centred column with 112vh top padding:
  - headline at clamp(38px, 52u, 76px)
  - each service centred with its icon above, the first 24vh below the headline, then 16vh apart; the axis tick is hidden
  - title lines and body lines flow inline
- **Menu:** the horizontal scroll becomes a normal vertical list of cards (full width, 22px gutters).
- **Hours / reviews:** stack to one column (18px and 14px gaps), 22px side gutters.
- **Overflow:** no sideways scroll anywhere.

## SEO and accessibility

- `<html lang="fr">`, a real `<title>` "[NOM DU SALON] — Coiffeur barbier homme à [VILLE]" and a meta description.
- JSON-LD `HairSalon` / `BarberShop` with name, address, phone and the exact opening hours above (placeholders stay as placeholders).
- All images have French `alt` text; the monogram SVG has `aria-hidden="true"`. Buttons reachable by keyboard with a visible gold focus ring. Contrast at least AA.

## Acceptance check (1440×960)

- **On load:** black stage, the HB monogram drawing itself in gold, the gold axis growing.
- **Reveal:** scrolling opens the razor slit onto the close-up of the barber's hands, which slowly pulls back.
- **Headline:** rises along the gold axis and exits the top.
- **Services:** the 2×2 block and the centred fifth block scroll through, mirrored across the axis, while the photo pulls back and then splits open onto the chair.
- **Menu:** the cream section rises over the stage; vertical scroll moves the cards sideways.
- **Hours:** today's row is highlighted with the correct open/closed status.
- **Scrubbing:** scrolling back reverses everything smoothly.
- **Content:** every unknown fact is a visible `[PLACEHOLDER]`; no invented prices, reviews or brands.
