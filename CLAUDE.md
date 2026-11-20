# Animated Icon Library — project brief for Claude Code

This project started in a claude.ai chat and moved here. Read this file before changing anything.

## Talk to the user
- Reply in Egyptian Arabic. The page is bilingual: English is the default (LTR), Arabic (Egyptian, RTL) is one click away.
- The user is a designer-minded manager: show results (screenshots, previews) before long explanations.
- When the user drops files in `references/`, study them first and match their timing/easing/feel. Use paid libraries (e.g. Lordicon) only as style/timing references; never copy their drawings or exact animations.

## What it is
A single self-contained page, `index.html` (~250 KB), that hosts a library of **748 animated icons** in **30 categories** and **6 styles**, with per-icon and bulk export to **Figma (SVG), Lottie (JSON) and GIF**.

- Icons: Lucide **0.460.0** (UMD from jsDelivr, pinned) + a `CUSTOM` object of extra icons:
  - from Lucide 1.59.0 (copied node data, ISC): `Mosque`, `SaudiRiyal` (official 2025 symbol), `CirclePoundSterling`
  - hand-drawn on the 24×24 Lucide grid: `Lantern`, `PrayerRug`, `Quran`, `Kaaba`, `Pyramids`, `Camel`, `Dallah`, and the insurance set `CarShield`, `HouseShield`, `HeartShield`, `GlobeShield`, `FileClaim`, `FilePolicy`
  - hand-drawn insurance set v2 (25, `ins-*` names, `Ins*` keys): family, life, health card, medical approval, hospital network, dental, vision, maternity, car crash, tow truck, windshield, house fire, flood, valuables, flight delay, lost luggage, phone, pet, business, cyber, marine, claim approved, renewal, premium, 24/7 support. Many share the small top-right shield badge (pop + check DRAW).
  - hand-drawn Arab-countries set (category `arab`, one landmark per country, 22): `ArabEgypt` (Sphinx), `ArabSaudi` (Kingdom Centre), `ArabUae` (Burj Al Arab), `ArabKuwait` (Kuwait Towers), `ArabQatar` (Museum of Islamic Art), `ArabBahrain` (Pearl Monument), `ArabOman` (khanjar), `ArabYemen` (Shibam), `ArabJordan` (Petra Treasury), `ArabPalestine` (Dome of the Rock), `ArabLebanon` (cedar), `ArabSyria` (Palmyra arch), `ArabIraq` (Malwiya), `ArabMorocco` (Koutoubia), `ArabAlgeria` (Maqam Echahid), `ArabTunisia` (Sidi Bou Said door), `ArabLibya` (Arch of Marcus Aurelius), `ArabSudan` (Meroë pyramids), `ArabMauritania` (Chinguetti minaret), `ArabSomalia` (aqal hut), `ArabDjibouti` (Lake Abbé chimneys), `ArabComoros` (crescent + 4 stars). Labels are the country names; the landmark name is in the recipe `t` so search finds it.
  - insurance journeys (`jr-*`, 21: quote, compare, coverage, add driver/member, instalments, policy active/expiring/expired/cancel, claim photo/upload/track/review/rejected, damage, ID scan, selfie, OTP, secure login, tap to pay) and screen states (`state-*`, 8: offline, server down, no results, empty, maintenance, session expired, update, sync), all hand-drawn.
  - sets for partner apps: `design` (Canva-like editor: 15 Lucide + `ds-bg-remove`, `ds-magic-resize`), `nfc` (digital NFC cards: 6 Lucide + `dc-nfc-card`, `dc-tap-share`, `dc-business-card`, `dc-profile-qr`), `delivery` (food delivery: 6 Lucide + `fd-scooter`, `fd-food-bag`, `fd-burger`, `fd-order-track`). `XCAT` adds existing icons to a set without duplicating them in "All" (`inSet`, `catCount`).
  - 83 more Lucide icons were added using the Phosphor Icons catalogue (phosphoricons.com) as a list of missing concepts. Phosphor drawings themselves are NOT used: they are single filled compound paths (256 grid), which break the stroke styles, DRAW trim paths and part articulation. Pick concepts from Phosphor, draw/animate them with Lucide-style stroked parts.
  - custom icons are merged with `lucide.icons` and passed to `lucide.createIcons({icons: ICONS})` (also for the second call in the style UI).
- Fonts: IBM Plex Sans Arabic (UI) + Lalezar (headline) from Google Fonts.
- Lazy libs (cdnjs, pinned): lottie-web 5.12.2 (Lottie preview), JSZip 3.10.1 (bulk ZIP).

## Page frame and design (v3)
- Inspired by the big icon sites (Lucide, Phosphor, Tabler, Lordicon): sticky `.nav` (brand, ⌘K / "/" search `#q`, `#langBtn`, `#themeBtn`, GitHub), hero with dotted backdrop and the animated showcase card, then `.layout` = sticky `.side` (categories `#chips` as a vertical list with counts) + `.content` (sticky `.tools`: segmented `#styles`, `#size` slider → `--ico` on `#grid`, play all, export, `#duo` row) and the grid.
- Below 900px the sidebar becomes a draggable chip row (`dragRow`), the toolbar keeps styles scrollable next to icon-only buttons. Hero stacks below 600px. Short landscape screens unpin the toolbar. `horizontal-viewport-segments:2` keeps hero/layout off a hinge. Tested at 280 (Galaxy Fold cover) → 1440 with no horizontal scroll.
- Always starts in LIGHT mode (`<html data-theme="light">`); the toggle is remembered only for the tab (sessionStorage), on purpose.
- Contrast: all text passes WCAG AA in light and dark (audit script: walk text nodes, blend background, check 4.5/3). Use `--accent-ink` for text on `--accent-soft`, `--line-strong` for control borders; never fade text with opacity.
- Share preview: `og.png` (1200×630, rendered from an HTML mock), Open Graph + Twitter tags in `<head>`, `favicon.svg`, `apple-touch-icon.png`. Re-render `og.png` when the icon count changes. WhatsApp caches previews per URL.
- Local preview: `../.claude/launch.json` runs a tiny Node static server (macOS blocks Xcode's python from Downloads).

## Search
- `filter()` ranks, it does not just match: `nrm()` normalises (lowercase, strips diacritics/tatweel, أإآ→ا, ة→ه, ى→ي), each token is scored against name words (100), current-language label (95), other-language label (85), `CONCEPTS` synonym tags (72, e.g. "money/فلوس" → wallet, card, riyal…), descriptions (40, whole words only: Egyptian verbs all start with بي…) and category (30). Order: exact > word prefix > typo (Damerau-Levenshtein ≤1 for 4+ letters, ≤2 for 7+) > inside a word.
- All tokens must match; otherwise the closest partial matches are shown with a "closest" heading. Results are ordered with CSS `order` on the tiles; the heading shows the count; typing scrolls the grid into view. Add new synonyms to `CONCEPTS`.

## Hover (desktop)
- Every icon and control reacts to the mouse (`@media (hover:hover) and (pointer:fine)`): tiles play their recipe; toggle icons (heart, star, menu…) get `peek()`, a wiggle that never flips their state; the hero stage and the sheet preview play too; UI icons use small CSS keyframes (`ui-flip`, `ui-turn`, `ui-drop`, `ui-twinkle`, `ui-beat`, `ui-x`, `ui-wiggle`, `ui-pop`). All off under reduced motion.

## Language (i18n)
- `LANG` (default `"en"`, `?lang=ar|en`, remembered in localStorage `lang`). `loc(key,...args)` reads the `UI.en` / `UI.ar` dictionary (do NOT name it `tx`: that is the translateX motion helper).
- Static markup uses `data-t` (text), `data-t-aria`, `data-t-ph`, `data-t-alt`; `applyLang()` re-labels everything in place, flips `dir`, and runs `filter()`. The `#langBtn` toggles.
- Data stays Arabic-first. English comes from `CAT_EN` (categories), `STN_EN` (styles), `LBL_EN` + `human(name)` (icon labels; `lbl(i)`), and `TEN` (Arabic recipe `t` → English; `desc(t)`). Every new recipe needs a `TEN` entry; missing ones fall back to Arabic.
- `ar(n)` prints Arabic-Indic digits only in Arabic mode.

## Data model
- `CATS = [[key, arabicName, "lucide-name:Arabic label,..."], ...]` → parsed into `I = [[name, label, catKey], ...]`.
- Categories: local (محلي وعربي), arab (دول عربية), insurance (تأمين), ui, arrows, status, text, layout, talk, people, media, files, shop, biz, time, nature, tech, dev, travel, home, food, health, sport, edu, security.

## Motion system (the core)
Every icon has a recipe. `rec(name, svg)` returns `R[name]` (hand-made, ~440 icons) or `auto(name, svg)` (pattern families: arrows, chevrons, charts, text lines, calendars, `-off` slash, badges, files, users, signal bars, faces…). There are **no generic fallbacks left**: keep it that way when adding icons.

Recipe shape:
```js
name: { t: "Arabic description of the motion",
        p: [[target, keyframes, easing, opts], ...],   // parts
        fx: [[effectName, arg, delayMs], ...],          // optional DOM/SVG particles
        tog: 1, on: [...], off: [...], c: "hot", fill: [1] } // toggles (heart, star, menu…)
```
- `target`: `0` = whole svg, `n` or `[n, m]` = child by **original index** (`data-o`, 1-based). Children are tagged `data-o` before any reordering, so indices stay stable.
- easing: `"lin"` (per-keyframe easings inside frames), `"sp" | "sps" | "spb"` = generated spring `linear()` easings (`spring(k, c)`), or any CSS easing (`E.o`, `E.io`, `E.sm`, `E.in`, `E.ant`).
- opts: `d` duration, `dl` delay, `st` stagger, `it` iterations, `o` transform-origin, `box: "view-box"` (origin in icon px), `keep` (commit end state; toggles keep automatically).
- Helpers: `K, W, POP, HOP, SQ, J, OUT, FLY, DRAW, DRAWR, FADEPOP, NUDGE, SL, GROW, STEAM, JELLY, rot, tx`.
- Principles kit (v2), prefer these for new work: `DV(a,n,decay)` damped-oscillation values (follow-through, e.g. `rot(DV(11,4,.5))`), `BLINK(at)` eyelid, `PUSH(x,y)` anticipation → thrust → spring settle, `LEAP(h,lean)` crouch/launch/fall/land squash, `DRAG(a)` a dangling part that lags and overshoots. Articulate parts (overlap with small `dl` offsets) instead of moving the whole svg as one block; whole-svg-only motion is reserved for single-path icons.
- `DRAW` uses `pathLength="1"` + dasharray/dashoffset.
- FX (page only, not exported): burst, sparkle, ripple, rings, glow, land, snap, trail, confetti, dust, splash, drip, notes, zzz, embers, coins, dropin, buzz, tint, scanline, bubbles, typing, mercury, screen, rec, slash, scribble.
- Playback runs on the Web Animations API. A click while animating queues one replay (`box._again`) instead of cutting the animation.

## Styles
`outline, light (1.2), bold (2.5), sharp (square caps / miter), duo (tone fill 25%), solid (filled + knockouts)`.
Duo colours: the `#duo` row (visible only in duo) shows 8 curated duotone pairs as live heart previews (dark stroke + same-family tone at 25%, each with a lighter stroke for dark mode) and sets them via `setColors(ink,tone,save,darkInk)`, which drives the page (`--duo-ink-l` / `--duo-ink-d`, `--tone` on body), the export pickers and `EX` (so SVG/Lottie/GIF match), and remembers the pair in localStorage (`duoColors`). Preset `null,null` = theme colours.
`classify(svg)` tags children: `sh` = closed shape not inside another, `ish` = closed shape inside a filled one, `iln` = open line inside a filled one (knocked out in solid), `tf` = toggle fill. Containment = bbox inside **and** `isPointInFill` at the centre (handles concave shapes like the crescent). Filled containers are moved behind their inner details so knockouts paint on top.

## Export pipeline
1. `sample(i, fps)`: clone the icon into the hidden `#lab`, run its real recipe paused, step `currentTime` frame by frame and read computed `transform`, `opacity`, `fill-opacity`, dash → trim range.
2. **Lottie** (`lottieOf`): one shape layer per part, root transform baked into each layer (no parenting), trim paths for drawing, 60 fps, comp 128×128 with a 4-unit margin (`LK = 4`). SVG paths → Lottie beziers by `dToSubs` (full path parser incl. arcs).
3. **GIF** (`gifOf` + `encodeGIF` + `lzw`): frames rendered from the sampled transforms into SVG → canvas, own palette + LZW encoder, 33 fps, 256 px, white / transparent / dark background, loop with an end hold.
4. **Figma**: `staticSvg(i)` with explicit colours; bulk "sheet" puts every visible icon in a named grid.
5. Downloads: inside claude.ai the page uses the `downloads` runtime capability; when self-hosted it falls back to normal browser downloads.

## Gotchas already solved (don't reintroduce)
- Footer `footer.credit`: "Made with ♥ by Ahmed Elminshawy" linking to https://www.linkedin.com/in/minsh/ (bilingual via `madeWith`/`madeBy`; the heart is a CSS lub-dub beat, off under reduced motion). The grid's bottom padding moved to the footer so the open sheet never covers the last row.
- The visible credit line was removed at the user's request; the Lucide ISC notice stays as an HTML comment in `<main>` and in README. Keep it.
- `parseM` must strip the `matrix3d(` prefix before matching numbers (the "3" used to shift every value) and project perspective with the Jacobian at the origin. Before the fix every `perspective()` icon (cards, coins, calendars, laptop, book, fridge, door) exported broken to Lottie/GIF.
- `transform-box: fill-box` must be scoped to `svg.ic *`. A global `svg *` rule breaks lottie-web's own transforms.
- Elements with `display: flex/grid` ignore `[hidden]`: add explicit `[hidden]{display:none}` rules.
- The page is RTL: don't park hidden helpers at `left: -9999px` (it creates sideways scroll). `#lab` sits at `top:0; right:0; opacity:0`.
- lottie-web mutates `animationData`: pass a copy.

## Testing
- Use Playwright: open `index.html`, click every tile (catches runtime errors), take screenshots per style.
- Lottie fidelity check: render `lottieOf(i)` with lottie-web at frame k next to the sampled frame k and compare.
- Motion review: render each icon as a frame strip (8–10 frames from `sample(i,60)` + an onion-skin cell) to judge motion visually. `staticSvg(i)` returns an SVG element, not a string.

## Good next steps
- Use `references/` to retune motions (Lottie JSON references are the most precise; for video/GIF, extract frames with ffmpeg).
- Higher-quality GIF with gifski, plus MP4/WebM via ffmpeg.
- Split into source files (icons, recipes, export) with a build step that still outputs one `index.html`.
- Package as an npm library / React components, or a Figma plugin.
