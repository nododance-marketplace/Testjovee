# Jovée Link — Codebase Summary

> Snapshot as of commit `54d146c` (Apr 2026) — the "booking app" version, after reverting the Jovée Voice pivot.

---

## 1. Project Overview

**Jovée Link** is a two-sided marketplace mobile app connecting nail artists with clients. This codebase is the **single-page marketing landing site** that lives at `joveelink.com`. Its sole job: convert nail artists and salon owners into app downloads / waitlist signups.

- **Audience (primary):** Nail artists & salon owners
- **Audience (secondary):** Clients looking for nail services
- **Markets:** US-based, with significant Vietnamese-speaking community → **English + Vietnamese** are first-class

---

## 2. Tech Stack & Architecture

| Layer | Choice | Rationale |
|---|---|---|
| Markup | Plain HTML5 | Zero build step, fast deploys |
| Styling | Vanilla CSS in `<style>` block | Single-file portability, no PostCSS / Tailwind needed |
| Scripting | Vanilla JS, single IIFE | No framework, no bundle, ~100ms TTI |
| Fonts | Google Fonts — Plus Jakarta Sans (400/500/600/700/800) | Brand spec |
| Hosting | Vercel (auto-deploy from `main` branch) | Static-site deploy, no build config needed |
| Repo | GitHub: `nododance-marketplace/Testjovee` | |

**Everything lives in one file** (`index.html`, ~2,000 lines). No bundler, no `package.json`, no dependencies. This was an intentional tradeoff for simplicity and load speed.

---

## 3. File Structure

```
New Website/
├── index.html                  ← Entire site (HTML + CSS + JS)
├── .gitignore
├── CODEBASE.md                 ← This file
├── assets/
│   ├── logo-icon.png           ← Octagon mark only (favicon)
│   ├── logo-dark.png           ← Full logo for light backgrounds (nav)
│   ├── logo-white.png          ← Full logo for dark backgrounds (footer)
│   └── HeaderVideo.mp4         ← Higgsfield-generated 4:3 video, hero loop
├── .skills/                    ← Reference design skills (gitignored)
│   ├── gorgeous-websites/
│   └── taste-skill/
└── .claude/                    ← Claude Code settings (gitignored)
```

---

## 4. Design System

### 4.1 Colors (CSS custom properties on `:root`)

Full 50–900 scales for both brand colors, plus a navy/neutral ramp.

**Primary — Teal**
```
--teal-50:  #E6F7F7    --teal-500: #2A9D9D
--teal-100: #B3ECEC    --teal-600: #1F8080
--teal-200: #80E0E0    --teal-700: #166363
--teal-300: #5CD2D2    --teal-800: #0D4747
--teal-400: #3DBFBF    --teal-900: #062D2D
```

**Secondary — Blush / Pink**
```
--blush-50:  #FDF2F5   --blush-500: #D46D86
--blush-100: #F9DDE4   --blush-600: #B85470
--blush-200: #F2C4CE   --blush-700: #943F58
--blush-300: #EBA8B7   --blush-800: #6F2D41
--blush-400: #E08A9E   --blush-900: #4A1D2B
```

**Neutrals**
```
--navy-900: #0C1A30    --gray-700: #4A4A4A
--navy-800: #111F3A    --gray-500: #777777
--navy:     #1A2B4A    --gray-300: #BBBBBB
--navy-600: #243B60    --gray-100: #F0F0F0
--charcoal: #2D2D2D    --offwhite: #F8F6F2
```

**Semantic aliases:** `--teal`, `--teal-dark`, `--blush`, `--blush-dark` map to the most-used shades.

### 4.2 Typography
- **Family:** Plus Jakarta Sans (Google Fonts), with `-apple-system` fallback
- **Weights:** 400, 500, 600, 700, 800
- **Headlines:** 800 weight, navy on light bg / white on dark bg, `letter-spacing: -2.5px` on hero
- **Body:** 400, 1.65 line-height
- **Hero clamp:** `clamp(50px, 6.5vw, 76px)` — fluid scaling
- **Section titles clamp:** `clamp(32px, 4.2vw, 46px)`

### 4.3 Spacing & Radius
- **Container:** `max-width: 1200px`, `padding: 0 24px`
- **Section padding:** 96px vertical (desktop), reduced on mobile
- **Radius scale:** `--radius-sm: 12px`, `-md: 16px`, `-lg: 24px`, `-xl: 32px`

### 4.4 Shadows
```
--shadow-card:       0 8px 32px rgba(0,0,0,0.06)
--shadow-card-hover: 0 16px 48px rgba(0,0,0,0.10)
--shadow-nav:        0 4px 24px rgba(0,0,0,0.06)
```

### 4.5 Motion
- **Easing:** `--ease-out-expo: cubic-bezier(0.16, 1, 0.3, 1)` is used everywhere (no `ease-in-out`)
- **Spring:** `--ease-spring: cubic-bezier(0.34, 1.56, 0.64, 1)` available but not currently applied
- **Durations:** 0.3s (nav/hover), 0.4–0.5s (interactions), 0.8s (scroll reveals)
- **GPU-safe rule:** only `transform` and `opacity` are animated — never `width/height/top/left`

---

## 5. Page Structure (8 Sections)

| # | Section | ID | Purpose |
|---|---|---|---|
| 1 | Navigation | `#nav` | Sticky pill with blur backdrop on scroll |
| 2 | Hero | `#hero` | Video bg + headline + dual store CTAs |
| 3 | Pain banner | — | Full-width navy/teal strip with the "Instagram DMs?" line |
| 4 | How it works | `#how-it-works` | Tab toggle (Artist / Client) → 3 steps |
| 5 | Features | `#features` | 3×2 grid of feature tiles with alternating teal/blush icons |
| 6 | Testimonials | `#testimonials` | 3 cards with star ratings, gradient avatars |
| 7 | Download CTA | `#download` | Dark navy section with white store buttons |
| 8 | Footer | — | Logo, link nav, social icons, language toggle |

Sections are visually separated by **SVG wave dividers** (curved, mirrored alternately).

---

## 6. Key Components & Patterns

### 6.1 Navigation
- Floating pill, blurs/whitens on scroll past 50px
- **Two logos**: `logo-dark.png` shown over scrolled (white) bg, `logo-white.png` over hero video
- Auto-swaps via `.nav.scrolled` class CSS
- Hamburger morph: 3 lines → X via `rotate(45deg)` + opacity fade
- Mobile menu = full-screen frosted overlay with staggered link reveals

### 6.2 Hero Video
- `<video autoplay muted loop playsinline preload="auto">` with `aria-hidden="true"`
- `object-position: center 20%` — pushes the frame down so the nav/logo isn't cropped
- **Two-layer overlay system:**
  - Desktop: linear gradient darkest on the left (where text sits), fading right
  - Mobile: vertical gradient — top/bottom darkened, middle clearer
- Decorative `hero-blob-*` divs add radial glows on top of the video

### 6.3 Tab Toggle (How It Works)
- Default tab: **"I'm a Nail Artist"** (primary audience for this page)
- Same `.tab-btn[data-tab]` selector works in nav, mobile, anywhere it appears
- On switch: animations are re-triggered by removing/re-adding `.is-visible` and forcing reflow with `void el.offsetWidth`

### 6.4 Step Cards
Each card has a **colored top accent bar** (gradient stripe), and the **icon background + color alternates** between teal-50/teal-500 and blush-50/blush-500 via `:nth-child` — keeps the brand palette balanced rather than mono-teal.

### 6.5 Feature Tiles
6 tiles in a 3-col grid (collapses to 2-col, then 1-col).
- Odd tiles → teal icon background, teal icon
- Even tiles → blush icon background, blush icon
- Hover: lift + alternating teal/blush border color

### 6.6 Testimonials
- Each card: blush quote mark, teal star ratings, italic body, avatar with initials
- Three avatar gradient styles: `.avatar-teal`, `.avatar-blush`, `.avatar-navy`
- Names are realistic (Linh T., Maya R., Sophia N.) with U.S. cities

### 6.7 Buttons
| Class | Use |
|---|---|
| `.btn-nav-cta` | Pill-shaped teal CTA (top nav + mobile menu) |
| `.btn-store` | Dark/white app store buttons (hero) — adapts to bg |
| `.btn-store-light` | White-on-navy variant (download CTA section) |

All have `transform: scale(1.03)` on hover and `scale(0.98)` on active — physical "press" feedback.

---

## 7. Internationalization (i18n)

### 7.1 Mechanism
- `data-i18n="key"` attribute on every translatable element
- All copy lives in a `translations` object inside the `<script>` block: `{ en: {...}, vi: {...} }`
- Click handler on any `.lang-btn` calls `setLanguage(lang)`, which:
  1. Iterates `[data-i18n]` elements
  2. Sets `textContent` (or `innerHTML` if the value contains a `<span>` — used for the "Instagram DMs" highlight)
  3. Sets `<html lang="…">` for accessibility
  4. Toggles `.active` on all matching `.lang-btn`s (nav + mobile + footer stay in sync)

### 7.2 Coverage
**Every visible string** has a translation key — nav, hero, pain banner, how-it-works steps (artist + client), all 6 features, all 3 testimonials, download CTA, footer links, store button subcopy. ~50 keys total per language.

### 7.3 Vietnamese Quality Notes
- All Vietnamese strings use Unicode escape sequences (`á`, `ạ`, etc.) for byte-safe storage in HTML attribute values that quote the JS literally.
- Translations were drafted by Claude — should be reviewed by a native VN speaker before going to a wider Vietnamese audience.

### 7.4 Persistence
**Currently NOT persisted.** Refresh resets to EN. If desired later, add `localStorage.setItem('lang', lang)` + read on init.

---

## 8. JavaScript Modules (single IIFE)

All in one self-executing function for scope isolation. Five blocks:

1. **TRANSLATIONS** — the `translations` object + `setLanguage()` + binding all `.lang-btn` clicks
2. **STICKY NAV** — `scroll` listener (passive) toggles `.scrolled` past 50px
3. **HAMBURGER MENU** — opens overlay, locks body scroll, closes on link click
4. **TABS** — handles `data-tab` switching + animation re-trigger
5. **SCROLL ANIMATIONS** — single `IntersectionObserver` adds `.is-visible` to all `.animate-on-scroll`
6. **SMOOTH SCROLL** — anchor links use `scrollIntoView({behavior: 'smooth'})`

No third-party JS. No event listeners on `scroll` other than the sticky-nav one (passive, cheap).

---

## 9. Animations & Interactions

| What | How |
|---|---|
| Hero video | Native `<video loop autoplay muted>` |
| Decorative blobs | CSS `@keyframes blobPulse` (8/10/12s loops) |
| Floating phone (deprecated, removed when video added) | CSS `@keyframes float` still defined but unused |
| Scroll reveals | IntersectionObserver + `.animate-on-scroll` + `.stagger-N` delay classes (1–6) |
| Tab content fade | CSS `@keyframes fadeUp` + `.tab-content.active` |
| Map pin pulse (in old phone mockup, also unused now) | `blobPulse` reused |
| Nav blur on scroll | `backdrop-filter: blur(20px) saturate(1.8)` only when `.scrolled` |

---

## 10. Responsive Breakpoints

| Breakpoint | Behavior |
|---|---|
| `≤ 900px` | Hero collapses to single column (video spacer hidden); features grid → 2 col |
| `≤ 768px` | Hamburger appears, nav CTA + lang toggle hidden; footer stacks centered |
| `≤ 600px` | Features grid → 1 col |
| `≤ 480px` | Hero CTAs stack vertically full-width |

Hero uses `min-height: 100dvh` (not `100vh`) to avoid iOS Safari viewport-jump bug.

---

## 11. Asset Inventory

| File | Size | Use |
|---|---|---|
| `index.html` | ~92 KB | Everything |
| `HeaderVideo.mp4` | ~14 MB | Hero loop |
| `logo-icon.png` | small | Favicon, possible future use |
| `logo-dark.png` | small | Nav (over white bg) |
| `logo-white.png` | small | Footer + nav (over video) |

Total page weight: ~14 MB (dominated by video). Consider adding a `poster` attribute to the `<video>` and a smaller mobile-optimized variant if Lighthouse scores matter.

---

## 12. Deployment

- **GitHub:** `https://github.com/nododance-marketplace/Testjovee`, `main` branch is canonical
- **Vercel:** Connected to GitHub, auto-deploys on push to `main`
- **Production domain:** `joveelink.com` (set up via Vercel → Settings → Domains)
- **Build command:** none (static site)
- **No env vars** required

### Git history
```
54d146c   Revert "Rebuild Jovée Voice"   ← current
9702e38   Rebuild Jovée Voice            ← preserved (recoverable)
bdb3d8d   Add video hero background
f34ddbb   Initial commit
```

---

## 13. Known Gaps & Future Work

| Item | Notes |
|---|---|
| Store buttons link to `#` | Need real App Store / Play Store URLs once apps are live |
| Footer links go to `#` | About, Privacy Policy, Terms of Service, etc. need real pages |
| Vietnamese translations | Drafted by AI — get a native review before heavy VN promotion |
| Language preference | Not persisted across page loads |
| Video weight | 14 MB MP4. Consider WebM + smaller mobile variant + `poster` |
| SEO | Has `<meta description>` but no Open Graph / Twitter cards / `og:image` |
| Analytics | None installed (Vercel Analytics easy add) |
| Form / waitlist capture | Not present — only deep links to app stores |
| Social icons | Currently link to `#` — need real Instagram, TikTok, Facebook URLs |
| Floating phone CSS | Dead code (`@keyframes float`, `.phone-*` styles) — could be deleted |

---

## 14. Brand & Copy Quick Reference

- **Headline:** "Nails. Connected." — "Connected." has a teal-to-blush gradient
- **Subheadline:** "Find nail artists near you. Book instantly. No DMs. No guessing."
- **Pain hook:** "Still managing bookings through Instagram DMs? There's a better way."
- **Social proof:** "Trusted by nail artists across the U.S."
- **Default tab:** "I'm a Nail Artist" (artist-first messaging)
- **Tone:** Beauty meets tech. Warm, modern, women-led, confident.
- **Comparable brands referenced in spec:** Booksy, StyleSeat, Glossier, The Cut

---

*Generated for handoff / internal documentation. Update this file when major sections, copy, or architecture change.*
