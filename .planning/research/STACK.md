# Technology Stack: Handled Agency Prospect Homepages

**Project:** Handled Agency — 23 Personalized Cold Outreach Prospect Homepages
**Researched:** 2026-03-25
**Research Mode:** Ecosystem (Stack dimension)
**Overall Confidence:** HIGH

---

## Recommended Stack

### Core Technologies

| Technology | Version / Spec | Purpose | Why |
|------------|---------------|---------|-----|
| HTML5 | Semantic (2025 baseline) | Document structure | Single-file constraint; semantic elements (`<header>`, `<section>`, `<nav>`) improve screen reader compatibility and give CSS clean hooks without class bloat |
| CSS3 (embedded `<style>`) | Custom Properties, Grid, Flexbox | All layout + visual design | Embedded in `<head>` eliminates a network round-trip; no FOUC risk since styles parse before render |
| Vanilla JS (inline `<script>`) | ES2020+ | Intersection Observer scroll animations only | No framework overhead; one small script block handles all animation toggling |
| Google Fonts v2 API | `fonts.googleapis.com/css2` | Oswald + Inter typefaces | Only legitimate external dependency per project constraints; v2 API supports variable font weights and `display=swap` in a single URL |

---

### Supporting Techniques (CSS)

#### 1. Intersection Observer API — Scroll Animations

**Confidence: HIGH** — 97.06% global browser support (Chrome 58+, Firefox 55+, Safari 12.1+, Edge 16+, iOS Safari 12.2+). Baseline Widely Available since March 2019.

**Pattern:**
```js
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      entry.target.classList.add('visible');
      observer.unobserve(entry.target); // fire once, not on every re-enter
    }
  });
}, { threshold: 0.15, rootMargin: '0px 0px -40px 0px' });

document.querySelectorAll('[data-animate]').forEach(el => observer.observe(el));
```

**Why this pattern:**
- `threshold: 0.15` — triggers when 15% of element is visible; prevents premature fires for tall elements
- `rootMargin: '0px 0px -40px 0px'` — 40px negative bottom margin delays trigger until element is comfortably in view
- `observer.unobserve()` after trigger — animations fire once; avoids re-triggering on scroll-back
- Toggle via CSS class (`.visible`), not inline JS style — keeps animation logic in CSS where it belongs

**CSS counterpart:**
```css
[data-animate] {
  opacity: 0;
  transform: translateY(24px);
  transition: opacity 0.55s ease, transform 0.55s ease;
}
[data-animate].visible {
  opacity: 1;
  transform: translateY(0);
}
```

**What NOT to use:** `scroll` event listeners — fire on every pixel of scroll, block main thread, degrade battery life on mobile. `animation-timeline` / CSS scroll-driven animations — Firefox has zero support as of March 2026; requires polyfill; not production-ready for cold outreach pages that must work everywhere.

---

#### 2. Layered Box-Shadow — Depth / Premium Feel

**Confidence: HIGH** — universally supported; zero browser caveats.

**Why layered:** A single shadow looks flat and artificial. Stacking 3-5 shadows at different offsets/blurs matches how real-world light scatters, producing the "elevated card" feel seen on premium sites.

**Standard pattern (3-layer):**
```css
box-shadow:
  0 1px 2px rgba(0,0,0,0.07),   /* contact shadow — anchors to surface */
  0 4px 12px rgba(0,0,0,0.08),  /* mid shadow — main lift */
  0 16px 40px rgba(0,0,0,0.06); /* ambient halo — soft outer glow */
```

**Performance:** Layered shadows are GPU composited and do not trigger layout. Avoid animating box-shadow directly (causes repaint). If shadow must change on hover, use `opacity` transitions on a pseudo-element instead.

---

#### 3. `backdrop-filter: blur()` — Frosted Glass Panels

**Confidence: MEDIUM** — 95% global support, but Firefox disables it by default (requires user flag in `about:config` through at least early 2026).

**Implementation with progressive enhancement:**
```css
.glass-panel {
  background: rgba(255, 255, 255, 0.12); /* fallback for Firefox */
  -webkit-backdrop-filter: blur(12px) saturate(180%);
  backdrop-filter: blur(12px) saturate(180%);
  border: 1px solid rgba(255,255,255,0.18);
}
```

**Why include despite Firefox gap:** Cold outreach recipients are typically business owners on Chrome/Edge/Safari (Chromium + WebKit = ~85%+ of real-world desktop share). Firefox users see a semi-transparent panel — still readable and functional. The visual degradation is graceful, not broken.

**Performance note:** Each `backdrop-filter` element creates a separate GPU compositing layer. Limit to 2-3 glass elements per page. Avoid on large full-bleed areas.

---

#### 4. CSS Custom Properties — Per-Site Palette System

**Confidence: HIGH** — universally supported in all modern browsers.

**Why:** The project requires unique palettes per site. CSS variables defined in `:root` let you swap an entire color system by changing 5-6 lines, keeping the main CSS block identical across all 23 files.

```css
:root {
  --brand-primary: #1A3C5E;
  --brand-accent:  #E87722;
  --brand-surface: #F5F3EE;
  --brand-text:    #1C1C1C;
  --brand-muted:   rgba(28,28,28,0.55);
  --shadow-color:  28,28,28; /* for rgba() construction */
}
```

---

#### 5. CSS Grid + Flexbox — Layout

**Confidence: HIGH** — both Baseline Widely Available; no caveats for 2025+ projects.

**Grid for:** Page-level sections, overlapping panels (negative margins + `grid-area`), image/text split layouts.
**Flexbox for:** Navigation, button groups, icon + label pairs, inline alignment within components.

**Overlapping panel technique (no JS required):**
```css
.hero {
  display: grid;
  grid-template-rows: 1fr;
  grid-template-columns: 1fr;
}
.hero__bg, .hero__content {
  grid-area: 1 / 1; /* both occupy same cell */
}
.hero__content {
  align-self: end;
  margin-top: -80px; /* pulls content into image area */
}
```

---

#### 6. CSS `clamp()` — Fluid Typography (No Media Queries)

**Confidence: HIGH** — Baseline Widely Available; all modern browsers since 2020.

**Why:** Eliminates per-breakpoint font size overrides. Scales smoothly between mobile and desktop. Keeps files under 320 lines by removing 30-50 lines of `@media` font rules.

```css
h1 { font-size: clamp(2rem, 5vw + 1rem, 4.5rem); }
h2 { font-size: clamp(1.5rem, 3.5vw + 0.75rem, 2.75rem); }
p  { font-size: clamp(1rem, 1.5vw + 0.5rem, 1.2rem); }
```

**Accessibility note:** Always combine `vw` with a `rem` addend (as shown above) so text scales correctly when users increase browser font size — meets WCAG 1.4.4 (Resize text).

---

#### 7. CSS Background Parallax — Approach

**Confidence: MEDIUM** — `background-attachment: fixed` works on all desktop browsers but is **disabled on iOS Safari and Android Chrome** for performance reasons. Apple and Google both made this decision intentionally.

**Recommended approach — CSS-only desktop parallax with mobile fallback:**
```css
.parallax-section {
  background-image: url('...');
  background-attachment: fixed;   /* desktop parallax */
  background-size: cover;
  background-position: center;
}

@media (max-width: 768px) {
  .parallax-section {
    background-attachment: scroll; /* disable on mobile — fixed is broken on iOS */
  }
}
```

**Alternative for full cross-device parallax:** JavaScript-driven `transform: translateY()` on a pseudo-element using scroll position. More reliable but adds JS complexity — only use if parallax is essential on mobile. Given the 320-line budget, CSS-only with the media query fallback is the correct tradeoff.

---

#### 8. CSS Texture / Pattern Overlays

**Confidence: HIGH** — Pure CSS, no external dependencies, universally supported.

**Technique:** Use `::before` or `::after` pseudo-elements with `background-image: repeating-linear-gradient()` or `background-image: url("data:image/svg+xml,...")` for inline SVG patterns. This keeps texture in CSS with zero additional HTTP requests.

```css
/* Subtle diagonal line texture */
.textured-section::before {
  content: '';
  position: absolute;
  inset: 0;
  background-image: repeating-linear-gradient(
    45deg,
    transparent,
    transparent 2px,
    rgba(0,0,0,0.02) 2px,
    rgba(0,0,0,0.02) 4px
  );
  pointer-events: none;
}

/* Noise grain texture via inline SVG data URI */
.grain-overlay::after {
  content: '';
  position: absolute;
  inset: 0;
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.65' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='0.04'/%3E%3C/svg%3E");
  opacity: 0.4;
  pointer-events: none;
}
```

---

### Google Fonts Loading Strategy

**Confidence: HIGH** — Verified against current best practices (2025 sources).

**Required implementation:**
```html
<!-- Two preconnect links: one plain, one with crossorigin (CORS required for font files) -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>

<!-- v2 API, display=swap, both weights in one request -->
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Oswald:wght@400;600;700&family=Inter:wght@400;500;600&display=swap">
```

**Why this exact pattern:**
- `preconnect` to both domains saves 100-300ms by pre-establishing DNS + TCP + TLS before the CSS parser hits the `<link>` tag
- `crossorigin` on `fonts.gstatic.com` is **required** — font files are CORS resources; without it the preconnect is wasted
- `display=swap` ensures body text uses the system fallback font immediately; Oswald/Inter swap in when ready (no invisible text flash)
- Single `<link>` for both families = one request, not two
- **Never use `@import` for Google Fonts** — forces sequential parsing; the font load can't start until the parent stylesheet finishes parsing

---

### Unsplash Image Integration

**Confidence: HIGH** — `source.unsplash.com` confirmed deprecated and non-functional.

**Use direct CDN URLs with Imgix parameters:**
```html
<!-- Pattern: images.unsplash.com/photo-{ID}?w={width}&q={quality}&fit=crop&auto=format -->
<img
  src="https://images.unsplash.com/photo-1568702846914-96b305d2aaeb?w=1400&q=80&fit=crop&auto=format"
  alt="Downtown Greenville, SC"
  loading="lazy"
  decoding="async"
>
```

**Key imgix parameters for these pages:**
| Parameter | Value | Why |
|-----------|-------|-----|
| `w` | 1400 | Caps download size; sufficient for full-bleed hero on desktop |
| `q` | 80 | Quality/size sweet spot; imperceptible difference vs 100 |
| `fit=crop` | — | Ensures image fills target area without distortion |
| `auto=format` | — | Imgix serves WebP/AVIF to supporting browsers automatically |

**Important:** The `ixid` parameter in Unsplash photo URLs **must be preserved** — it's required by Unsplash's API terms and tracks photo views for attribution to photographers. Strip all other auto-generated parameters and add the optimized set manually.

**For CSS background images (hero overlays):**
```css
.hero {
  background-image:
    linear-gradient(to bottom, rgba(0,0,0,0.3) 0%, rgba(0,0,0,0.6) 100%),
    url('https://images.unsplash.com/photo-1568702846914-96b305d2aaeb?w=1600&q=75&fit=crop&auto=format');
  background-size: cover;
  background-position: center;
}
```

---

### GPU-Optimized Animation Properties

**Confidence: HIGH** — verified against MDN performance documentation and 2025 sources.

**Animate ONLY these properties** (GPU composited, zero layout recalculation):
- `opacity`
- `transform` (translate, scale, rotate)
- `filter` (blur, brightness — composited in Chromium)

**Never animate** (triggers layout recalc = jank):
- `width`, `height`, `top`, `left`, `margin`, `padding`
- `background-color` (acceptable in short bursts but not on scroll)
- `box-shadow` directly (use pseudo-element opacity trick instead)

**`will-change` usage:**
```css
/* Apply only to actively animating elements, remove after animation completes */
[data-animate] {
  will-change: opacity, transform; /* hint GPU layer creation */
}
[data-animate].visible {
  will-change: auto; /* release the layer after animation settles */
}
```

---

## Alternatives Considered

| Category | Recommended | Alternative Considered | Why Not |
|----------|-------------|----------------------|---------|
| Scroll animations | Intersection Observer API | `scroll` event listener | Fires on every pixel; blocks main thread; 10x more expensive |
| Scroll animations | Intersection Observer API | CSS `animation-timeline` | Firefox has zero support as of 2026; requires polyfill; too new for cold outreach |
| Font loading | `<link>` with preconnect | `@import` in CSS | Forces sequential load; font request blocked until parent CSS finishes |
| Image source | `images.unsplash.com` direct URLs | `source.unsplash.com` | Deprecated and non-functional as of 2024 |
| Parallax | CSS `background-attachment: fixed` + media query fallback | JS scroll parallax | Adds JS complexity; 320-line budget is tight; desktop-only CSS is sufficient |
| Frosted glass | `backdrop-filter` with `rgba` fallback | Pure opacity/blur JS | backdrop-filter is GPU composited and requires zero JS |
| Color system | CSS custom properties in `:root` | Separate `.css` file per site | Violates single-file constraint; custom properties achieve same effect inline |
| Typography scaling | `clamp()` fluid sizing | Per-breakpoint `@media` font rules | Media queries add 30-50 lines per file; clamp() handles all viewports in one line |
| CSS textures | `repeating-linear-gradient` + SVG data URIs | External texture images | Extra HTTP requests; data URIs keep everything in-file |

---

## What NOT to Use

| Technology | Category | Reason |
|------------|----------|--------|
| `source.unsplash.com` | Image source | Deprecated, non-functional |
| CSS `animation-timeline` / scroll-driven animations | Scroll FX | Firefox unsupported (March 2026); not production-ready without polyfill |
| `@import` for Google Fonts | Font loading | Sequential parse blocks; always use `<link>` |
| Any CDN-loaded JS library (GSAP, ScrollReveal, AOS) | Animation | Violates "no CDNs" constraint; adds 30-100KB; overkill for simple fade-ins |
| Tailwind CDN or any CSS framework CDN | Styling | Violates constraints; adds 100KB+ CSS for features that won't be used |
| `background-attachment: fixed` without mobile fallback | Parallax | Broken on iOS Safari and Android Chrome by design; must pair with `scroll` fallback |
| Animating `width`, `height`, `top`, `left` | Animation | Triggers layout recalculation on every frame; causes jank on mid-range mobile |
| Overusing `will-change` | Performance | Promotes elements to GPU layers permanently; increases VRAM consumption; use sparingly |
| `backdrop-filter` on large areas | Performance | Each element creates a compositing layer; > 3 glass elements risks frame drops on mid-range phones |
| `window.scrollY` scroll listeners for animations | Scroll FX | Main thread blocking; use Intersection Observer instead |

---

## Development Tooling

No build pipeline. No Node. No npm. This is intentional.

| Tool | Role |
|------|------|
| Code editor (VS Code) | Author `.html` files directly |
| Browser DevTools | Validate CSS, test Intersection Observer callbacks, check mobile responsive layout |
| Chrome DevTools > Rendering > Paint Flashing | Verify animations don't trigger unintended repaints |
| Chrome DevTools > Performance tab | Confirm 60fps scroll on simulated mid-range mobile |
| `prefers-reduced-motion` media query | Must be respected in all animation CSS |

**Reduced motion pattern (required):**
```css
@media (prefers-reduced-motion: reduce) {
  [data-animate] {
    opacity: 1;
    transform: none;
    transition: none;
  }
}
```

---

## Line Budget Allocation Guide

Given the 320-line constraint per file:

| Block | Est. Lines | Notes |
|-------|-----------|-------|
| `<!DOCTYPE>` + `<head>` (meta, fonts, title) | 10-12 | Fixed boilerplate |
| CSS `:root` variables + reset | 15-20 | Palette + box-sizing reset |
| Typography styles | 20-25 | h1-h4, p, a — leverages clamp() |
| Navigation | 20-25 | Mobile hamburger adds ~10 lines |
| Hero section CSS | 25-30 | Background, overlay, positioning |
| Content sections CSS (3-4 sections) | 60-80 | ~20 lines each |
| Card / feature grid CSS | 20-25 | Reusable grid + card styles |
| CTA / footer CSS | 15-20 | — |
| Intersection Observer JS | 20-25 | One small `<script>` block |
| HTML body structure | 60-80 | Semantic markup for all sections |
| **Total** | **265-312** | Fits within 320-line ceiling |

Avoid: verbose `@media` blocks per section. Use `clamp()` for type, `min()` / `max()` for spacing, and Grid's `auto-fit` / `minmax()` for responsive column counts — these collapse 3-4 breakpoints into 1 line each.

---

## Sources

- Intersection Observer browser support: [caniuse.com/intersectionobserver](https://caniuse.com/intersectionobserver) — 97.06% global usage
- Intersection Observer API: [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) — Baseline Widely Available since March 2019
- Scroll animation techniques 2025: [MROY Club](https://mroy.club/articles/scroll-animations-techniques-and-considerations-for-2025)
- `backdrop-filter` frosted glass: [Josh W. Comeau](https://www.joshwcomeau.com/css/backdrop-filter/)
- CSS scroll-driven animations browser support: [MDN — animation-timeline](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/animation-timeline)
- `background-attachment: fixed` mobile issues: [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/background-attachment), [LogRocket](https://blog.logrocket.com/create-parallax-scrolling-css/)
- Google Fonts loading strategy: [Request Metrics](https://requestmetrics.com/web-performance/5-tips-to-make-google-fonts-faster/), [DebugBear](https://www.debugbear.com/blog/preload-web-fonts)
- `source.unsplash.com` deprecation: [MUI GitHub Issue #42736](https://github.com/mui/material-ui/issues/42736), [Paul's Weblog](https://paul.af/reimplementing-unsplash-source)
- Unsplash + Imgix URL parameters: [Unsplash API Documentation](https://unsplash.com/documentation)
- CSS `clamp()` fluid typography: [Smashing Magazine](https://www.smashingmagazine.com/2022/01/modern-fluid-typography-css-clamp/), [web.dev](https://web.dev/articles/baseline-in-action-fluid-type)
- Layered shadows technique: [Josh W. Comeau — Designing Shadows](https://www.joshwcomeau.com/css/designing-shadows/)
- GPU animation properties: [Smashing Magazine — GPU Animation](https://www.smashingmagazine.com/2016/12/gpu-animation-doing-it-right/), [lexo.ch](https://www.lexo.ch/blog/2025/01/boost-css-performance-with-will-change-and-transform-translate3d-why-gpu-acceleration-matters/)
- CSS performance — which properties trigger layout: [MDN — CSS Performance](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Performance/CSS)
