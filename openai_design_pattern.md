# OpenAI.com — Design Pattern Analysis

Captured via Playwright inspection of `https://openai.com/` on 2026-07-18.

## 1. Design Philosophy

Minimalist, **content-first** monochrome UI chrome that lets colorful AI-generated "art cards" pop. No shadows, no borders, no card backgrounds — just **typography + imagery + space**. The site reads as a calm editorial publication, not a SaaS marketing site.

## 2. Tech Stack

- **Tailwind CSS** (utility classes throughout: `flex`, `min-w-0`, `flex-col`, `gap-20`, `px-6`, `max-w-container`)
- **Tailwind Container Queries** (`@container`, `@md:`, `@lg:`, `@xl:` prefixes) — sections respond to their own width, not the viewport
- **Custom design tokens** exposed as CSS variables and Tailwind theme extensions:
  - `--header-h: 4rem` (64px)
  - `--max-w-container` → `max-width: 1440px`
  - Easing curves: `ease-curve-a`, `ease-curve-c`
  - Duration tokens: `duration-short`, `duration-medium`, `duration-250`
- **Custom font**: `"OpenAI Sans", "OpenAI Sans Variable Scripts", sans-serif`

## 3. Typography

| Element        | Size      | Weight | Line-height |
|----------------|-----------|--------|-------------|
| Body           | 17px      | 400    | ~28px (1.65)|
| Nav links      | 17px      | 400    | —           |
| Section H2     | ~21px     | 500    | —           |
| CTA H2         | ~42px     | 500    | —           |
| "Skip" / micro | 13px      | 400    | —           |

Notable: **headings use weight 500 (medium), not 700 (bold)**. This is the single biggest signal of the "calm editorial" feel.

## 4. Color

- Body: `#000` on `#FFF` (pure black on pure white)
- All section/nav/card backgrounds: `transparent`
- No accent color in the UI chrome — color comes only from imagery
- Dark mode is supported via `prefers-color-scheme` (auto, no toggle)

## 5. Layout Structure

```
<header> 64px, transparent, static (sticky on scroll)
  nav: Research | Products ▾ | Business | Developers | Company | Foundation
  right: Log in · [Try ChatGPT] CTA
  desktop: mask-gradient fade on the right edge

<main>
  <article class="flex flex-col gap-20 @md:gap-30">   // vertical rhythm ~80–120px

    1. HERO — ChatGPT prompt input
       "What can I help with?" + textarea + suggestion chips
       min-height: 400px, centered, calc-based height subtracts header

    2. FEATURED CAROUSEL — GPT-5.6 announcement
       Full-bleed art card, ~1130px tall, large headline overlay

    3. RECENT NEWS — horizontal list
       Each row: 185×185 art card | text link (category · date)
       "View more" pill at top right

    4. STORIES — same row pattern, photo-led

    5. LATEST RESEARCH — same row pattern

    6. OPENAI FOR BUSINESS — same row pattern, customer logos

    7. GET STARTED CTA — large ~42px heading + Download button

  </article>
</main>

<footer> ~650px, multi-column link grid, transparent
  Research | Products | API Platform | Business | Developers | Company | Support | More | Terms
  Social row + "OpenAI © 2015–2026"
```

## 6. Spacing System

- Container max-width: **1440px**
- Horizontal page padding: **32px** (`px-6`)
- Vertical section gap: **80px** mobile / **120px** `@md` (`gap-20` → `gap-30`)
- Card grid uses container queries, not viewport breakpoints

## 7. Key Components

### Art Card (image thumbnail)
- 185 × 185 px
- `border-radius: 6.08px` (subtle, ~6px)
- No border, no shadow
- Image fills card, slight scale transition on hover (`ease-curve-c`, `duration-medium`)

### News List Row
- Flex row: `[art card] [text block]`
- Text block: title (medium weight) → category · date (smaller, muted)
- Hover: text color transition, `duration-250`, `ease-curve-a`

### "View more" Pill
- `border-radius: 2.5rem` (fully rounded, 40px)
- `min-height: 32px`
- `text-nowrap`, inline flex
- No background fill — text-only pill

### Hero Prompt Input
- Transparent background, no border
- Textarea (not input) — multi-line ready
- Placeholder uses `--primary-60` (muted black at 60% alpha)
- Suggestion chips below: "Quiz me on vocabulary", "Plan a surf trip to …"

### Navigation
- 64px tall, transparent, static by default
- Desktop: right-edge mask gradient fades nav items out
- "Try ChatGPT" is the only filled CTA in the nav
- Mobile: hamburger, same item set

## 7. Motion

- **Custom easings**: `ease-curve-a` (UI), `ease-curve-c` (imagery)
- **Duration tokens**: `duration-short`, `duration-medium`, `duration-250`
- Hover transitions only — no scroll-triggered animations observed
- Subtle: image scale, text color shift. No fade-in-up, no parallax.

## 8. Reproducing the Pattern

To clone this aesthetic:

1. **Tailwind + container queries** (`@tailwindcss/container-query` plugin)
2. Custom theme:
   ```js
   theme: {
     extend: {
       fontFamily: { sans: ['"OpenAI Sans"', 'system-ui', 'sans-serif'] },
       maxWidth: { container: '1440px' },
       borderRadius: { card: '6.08px' },
       transitionTimingFunction: {
         'curve-a': 'cubic-bezier(0.4, 0, 0.2, 1)',
         'curve-c': 'cubic-bezier(0.16, 1, 0.3, 1)',
       },
       transitionDuration: {
         short: '150ms', medium: '300ms', '250': '250ms',
       },
       colors: { primary: { 60: 'rgba(0,0,0,0.6)', 100: '#000' } },
     }
   }
   ```
3. Body: `text-black bg-white`, font-weight 400, headings 500
4. Sections: `flex flex-col gap-20 @md:gap-30`, container `max-w-[1440px] mx-auto px-6`
5. Cards: image + text row, `rounded-[6px]`, no shadow/border
6. Pills: `rounded-[2.5rem] min-h-8`
7. Let imagery carry all the color. UI stays monochrome.
