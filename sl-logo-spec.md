# SL Chat Bubble Logo — Spec & Implementation Guide

## Shape

Speech bubble: rounded rectangle (200×158px, rx=22) with a bottom-left tail.
SVG viewBox: `0 0 215 208`

Path:
```
M 29,7 H 185 A 22,22 0 0,1 207,29 V 143 A 22,22 0 0,1 185,165 H 70 L 9,198 L 29,165 A 22,22 0 0,1 7,143 V 29 A 22,22 0 0,1 29,7 Z
```

- Bubble body: x=7–207, y=7–165
- Tail: emerges bottom edge between x=29–70, tip at (9, 198)
- Center of bubble body: (107, 86)

## Colors (from Shortlist site palette)

| Name   | Hex       |
|--------|-----------|
| Cyan   | `#00e5ff` |
| Pink   | `#ff2d78` |
| Yellow | `#f5e642` |
| Text yellow | `#ffe600` |
| Background | `#0b0c1a` |

## Static version (favicon / icon use)

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 215 208">
  <defs>
    <linearGradient id="border-grad" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%"   stop-color="#00e5ff"/>
      <stop offset="40%"  stop-color="#ff2d78"/>
      <stop offset="80%"  stop-color="#f5e642"/>
      <stop offset="100%" stop-color="#00e5ff"/>
    </linearGradient>
    <linearGradient id="text-grad" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%"   stop-color="#f5e642"/>
      <stop offset="45%"  stop-color="#ff2d78"/>
      <stop offset="100%" stop-color="#00e5ff"/>
    </linearGradient>
  </defs>
  <path d="M 29,7 H 185 A 22,22 0 0,1 207,29 V 143 A 22,22 0 0,1 185,165 H 70 L 9,198 L 29,165 A 22,22 0 0,1 7,143 V 29 A 22,22 0 0,1 29,7 Z"
    fill="#0b0c1a" stroke="url(#border-grad)" stroke-width="5" stroke-linejoin="round"/>
  <text x="107" y="86" text-anchor="middle" dominant-baseline="central"
    font-family="-apple-system,BlinkMacSystemFont,'Segoe UI',sans-serif"
    font-size="128" font-weight="700" fill="url(#text-grad)">SL</text>
</svg>
```

## Animated version (web / inline SVG)

### How the animation works

- **Total perimeter length**: ~745px
- **Three colored bands** (cyan, pink, yellow) travel the perimeter using `stroke-dashoffset` CSS animation
- **Two layers per band**: a thick blurred "aura" (outer glow) and a thinner blurred "liquid band" (visible moving color)
- **Blur via SVG `<filter>`** — NOT CSS `filter: blur()`. CSS blur on SVG elements breaks on iOS Safari. Always use `<feGaussianBlur>` in a `<defs>` filter.
- **Spacing**: three bands equally spaced using `animation-delay` (0s, -3s, -6s for a 9s cycle)

### CSS

```css
/* Overall bubble pulse (very mild) */
.sl-bubble {
  animation: softPulse 3.5s ease-in-out infinite;
  overflow: visible;
}
@keyframes softPulse {
  0%, 100% { filter: drop-shadow(0 0 4px rgba(180,240,255,0.18)); }
  50%       { filter: drop-shadow(0 0 10px rgba(180,240,255,0.34)); }
}

/* Outer aura — thick, heavy blur, behind fill */
.aura-band   { stroke-dasharray: 370 375; animation: travelBand 9s linear infinite; opacity: 0.5; }
.aura-cyan   { animation-delay: 0s; }
.aura-pink   { animation-delay: -3s; }
.aura-yellow { animation-delay: -6s; }

/* Liquid band — thinner, lighter blur, on top of fill */
.liquid-band   { stroke-dasharray: 310 435; animation: travelBand 9s linear infinite; }
.liquid-cyan   { animation-delay: 0s; }
.liquid-pink   { animation-delay: -3s; }
.liquid-yellow { animation-delay: -6s; }

@keyframes travelBand {
  from { stroke-dashoffset: 0; }
  to   { stroke-dashoffset: -745; }
}

/* Text glow */
.sl-text {
  filter: drop-shadow(0 0 5px rgba(255,230,0,0.7)) drop-shadow(0 0 14px rgba(255,230,0,0.28));
}
```

### SVG markup

```svg
<svg class="sl-bubble" viewBox="0 0 215 208" xmlns="http://www.w3.org/2000/svg" style="overflow:visible">
  <defs>
    <!-- IMPORTANT: use SVG filters for blur, not CSS filter:blur() — CSS blur on SVG breaks on iOS Safari -->
    <filter id="f-aura" x="-30%" y="-30%" width="160%" height="160%">
      <feGaussianBlur stdDeviation="11"/>
    </filter>
    <filter id="f-liq" x="-15%" y="-15%" width="130%" height="130%">
      <feGaussianBlur stdDeviation="4"/>
    </filter>
  </defs>

  <!-- Outer aura (behind fill — glow bleeds outward) -->
  <path class="aura-band aura-cyan"   filter="url(#f-aura)" fill="none" stroke="#00e5ff" stroke-width="14" stroke-linejoin="round" d="M 29,7 H 185 A 22,22 0 0,1 207,29 V 143 A 22,22 0 0,1 185,165 H 70 L 9,198 L 29,165 A 22,22 0 0,1 7,143 V 29 A 22,22 0 0,1 29,7 Z"/>
  <path class="aura-band aura-pink"   filter="url(#f-aura)" fill="none" stroke="#ff2d78" stroke-width="14" stroke-linejoin="round" d="M 29,7 H 185 A 22,22 0 0,1 207,29 V 143 A 22,22 0 0,1 185,165 H 70 L 9,198 L 29,165 A 22,22 0 0,1 7,143 V 29 A 22,22 0 0,1 29,7 Z"/>
  <path class="aura-band aura-yellow" filter="url(#f-aura)" fill="none" stroke="#f5e642" stroke-width="14" stroke-linejoin="round" d="M 29,7 H 185 A 22,22 0 0,1 207,29 V 143 A 22,22 0 0,1 185,165 H 70 L 9,198 L 29,165 A 22,22 0 0,1 7,143 V 29 A 22,22 0 0,1 29,7 Z"/>

  <!-- Dark fill (covers inside, clips inner half of aura) -->
  <path fill="#0b0c1a" d="M 29,7 H 185 A 22,22 0 0,1 207,29 V 143 A 22,22 0 0,1 185,165 H 70 L 9,198 L 29,165 A 22,22 0 0,1 7,143 V 29 A 22,22 0 0,1 29,7 Z"/>

  <!-- Liquid bands (on top of fill — blur bleeds ~2px inside border) -->
  <path class="liquid-band liquid-cyan"   filter="url(#f-liq)" fill="none" stroke="#00e5ff" stroke-width="4" stroke-linejoin="round" stroke-linecap="round" d="M 29,7 H 185 A 22,22 0 0,1 207,29 V 143 A 22,22 0 0,1 185,165 H 70 L 9,198 L 29,165 A 22,22 0 0,1 7,143 V 29 A 22,22 0 0,1 29,7 Z"/>
  <path class="liquid-band liquid-pink"   filter="url(#f-liq)" fill="none" stroke="#ff2d78" stroke-width="4" stroke-linejoin="round" stroke-linecap="round" d="M 29,7 H 185 A 22,22 0 0,1 207,29 V 143 A 22,22 0 0,1 185,165 H 70 L 9,198 L 29,165 A 22,22 0 0,1 7,143 V 29 A 22,22 0 0,1 29,7 Z"/>
  <path class="liquid-band liquid-yellow" filter="url(#f-liq)" fill="none" stroke="#f5e642" stroke-width="4" stroke-linejoin="round" stroke-linecap="round" d="M 29,7 H 185 A 22,22 0 0,1 207,29 V 143 A 22,22 0 0,1 185,165 H 70 L 9,198 L 29,165 A 22,22 0 0,1 7,143 V 29 A 22,22 0 0,1 29,7 Z"/>

  <!-- Crisp white outline (top, for definition) -->
  <path fill="none" stroke="rgba(232,234,246,0.7)" stroke-width="1.5" stroke-linejoin="round" d="M 29,7 H 185 A 22,22 0 0,1 207,29 V 143 A 22,22 0 0,1 185,165 H 70 L 9,198 L 29,165 A 22,22 0 0,1 7,143 V 29 A 22,22 0 0,1 29,7 Z"/>

  <!-- SL text -->
  <text class="sl-text" x="107" y="86"
    text-anchor="middle" dominant-baseline="central"
    font-family="-apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif"
    font-size="128" font-weight="700" fill="#ffe600">SL</text>
</svg>
```

## Key implementation notes

- **Font**: `-apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif` — matches the Shortlist app UI font stack
- **Text size**: `font-size="128"` in a 215-wide viewBox fills most of the bubble interior
- **Blur on mobile**: Always use SVG `<filter>`+`<feGaussianBlur>` for blur effects on SVG paths. CSS `filter: blur()` on SVG `<use>` elements silently fails on iOS Safari.
- **No `<use>` elements**: Inline each `<path>` separately to avoid the iOS `<use>`+filter rendering bug
- **`overflow: visible`**: Required on the SVG element so the outer aura glow isn't clipped at the viewBox edge
- **`prefers-reduced-motion`**: Add `*, *::before, *::after { animation-duration: 0.01ms !important; }` inside a `@media (prefers-reduced-motion: reduce)` block to disable animation for accessibility
