---
name: Serene Tabletop Caro
colors:
  surface: '#fff8f5'
  surface-dim: '#e0d8d5'
  surface-bright: '#fff8f5'
  surface-container-lowest: '#ffffff'
  surface-container-low: '#faf2ee'
  surface-container: '#f4ece8'
  surface-container-high: '#eee7e3'
  surface-container-highest: '#e9e1dd'
  on-surface: '#1e1b19'
  on-surface-variant: '#3f4941'
  inverse-surface: '#33302d'
  inverse-on-surface: '#f7efeb'
  outline: '#6f7a71'
  outline-variant: '#bec9bf'
  surface-tint: '#066c41'
  primary: '#006038'
  on-primary: '#ffffff'
  primary-container: '#1f7a4d'
  on-primary-container: '#aeffca'
  inverse-primary: '#82d8a3'
  secondary: '#ac3400'
  on-secondary: '#ffffff'
  secondary-container: '#fd6b36'
  on-secondary-container: '#5d1900'
  tertiary: '#0e602f'
  on-tertiary: '#ffffff'
  tertiary-container: '#2e7946'
  on-tertiary-container: '#b1ffbf'
  error: '#ba1a1a'
  on-error: '#ffffff'
  error-container: '#ffdad6'
  on-error-container: '#93000a'
  primary-fixed: '#9ef5be'
  primary-fixed-dim: '#82d8a3'
  on-primary-fixed: '#002110'
  on-primary-fixed-variant: '#00522f'
  secondary-fixed: '#ffdbd0'
  secondary-fixed-dim: '#ffb59d'
  on-secondary-fixed: '#390c00'
  on-secondary-fixed-variant: '#832600'
  tertiary-fixed: '#a6f4b5'
  tertiary-fixed-dim: '#8bd79b'
  on-tertiary-fixed: '#00210b'
  on-tertiary-fixed-variant: '#005226'
  background: '#fff8f5'
  on-background: '#1e1b19'
  surface-variant: '#e9e1dd'
typography:
  display-lg:
    fontFamily: Newsreader
    fontSize: 3rem
    fontWeight: '400'
    lineHeight: 3.5rem
    letterSpacing: -0.02em
  headline-lg:
    fontFamily: Newsreader
    fontSize: 2rem
    fontWeight: '400'
    lineHeight: 2.5rem
    letterSpacing: -0.015em
  headline-lg-mobile:
    fontFamily: Newsreader
    fontSize: 1.625rem
    fontWeight: '400'
    lineHeight: 2.125rem
  headline-md:
    fontFamily: Plus Jakarta Sans
    fontSize: 1.25rem
    fontWeight: '600'
    lineHeight: 1.75rem
    letterSpacing: -0.01em
  body-lg:
    fontFamily: Plus Jakarta Sans
    fontSize: 1rem
    fontWeight: '400'
    lineHeight: 1.625rem
  body-md:
    fontFamily: Plus Jakarta Sans
    fontSize: 0.875rem
    fontWeight: '400'
    lineHeight: 1.375rem
  label-lg:
    fontFamily: Plus Jakarta Sans
    fontSize: 0.875rem
    fontWeight: '600'
    lineHeight: 1.25rem
    letterSpacing: 0.01em
  label-md:
    fontFamily: Plus Jakarta Sans
    fontSize: 0.75rem
    fontWeight: '600'
    lineHeight: 1rem
    letterSpacing: 0.04em
  label-sm:
    fontFamily: Plus Jakarta Sans
    fontSize: 0.6875rem
    fontWeight: '500'
    lineHeight: 0.875rem
    letterSpacing: 0.08em
rounded:
  sm: 0.125rem
  DEFAULT: 0.25rem
  md: 0.375rem
  lg: 0.5rem
  xl: 0.75rem
  full: 9999px
spacing:
  gutter: 1.5rem
  gutter-mobile: 0.75rem
  margin: 2.5rem
  margin-mobile: 1rem
  space-xs: 0.25rem
  space-sm: 0.5rem
  space-md: 1rem
  space-lg: 1.5rem
  space-xl: 2.5rem
---

## Brand & Style

This design system embraces an organic, contemplative Zen tabletop aesthetic centered around the classic strategy game of Caro (Gomoku). It merges traditional Eastern board-game tactility—evocative of smooth river stones, fibrous washi paper, and aged paulownia wood—with refined Scandinavian minimalism. 

The experience targets players who seek mindful competition, strategic clarity, and digital respite. The interface avoids aggressive arcade gamification, skeuomorphic noise, or gaudy animations. Instead, it fosters quiet tension, patient pacing, and deliberate play through ample breathing room, balanced typographic rhythm, and physical precision.

## Colors

The palette is rooted in natural paper and mineral tones:

- **Canvas & Surfaces:**
  - Tabletop Background: `#F6F3EC` (quiet, warm ambient off-white)
  - Card & Panel Surface: `#FAF8F5` (luminous, soft fibrous paper)
  - Stone Board Base: `#EFE9DC` (warm cream pressed wood/raw linen)
  - Subtle Borders & Separators: `#E7E3D8`
  - Board Grid Lines & Crosshairs: `#D6CDBC`

- **Ink & Marks:**
  - Primary Typography & Player X Marks: `#1C1917` (deep sumi ink)
  - Muted Labels & Coordinates: `#78716C` (stone wash)
  - Subtle Inactive Dimming: `#A8A29E`

- **Accents & Player Identity:**
  - Primary Action / Garden Green: `#1F7A4D` (deep moss green), with `#166534` for hover/pressed states.
  - Player O (Opponent / Terracotta): `#C2410C` (warm fired clay / cinnabar seal).
  - Win Line Highlight & Focus: Soft emerald glow `#DCFCE7` surrounded by a sharp `#166534` boundary stroke.

## Typography

The type system balances the literary grace of **Newsreader** for primary titles, match outcomes, and ceremonial headers with the structural clarity of **Plus Jakarta Sans** for functional interface controls, timers, notations, and coordinates.

- Coordinate values (`A–O`, `1–15`) strictly use `label-sm` in tabular numbers with uppercase tracking to ensure optical alignment with the 15x15 intersection lines.
- Player move indicators and timers leverage tabular numbers (`font-variant-numeric: tabular-nums`) to prevent horizontal layout shift during countdowns.

## Layout & Spacing

The interface is engineered around an asymmetric desktop-first stage optimized for 1440x900 viewport real estate:

- **Desktop (1024px and up):** A centered 3-column layout where the board takes visual dominance.
  - Left column (260px–300px): Match info, captured marks, resignation controls, player profile cards.
  - Center stage (flex, min 640px): 1:1 square Caro 15x15 board enclosed by coordinate borders and balanced margins.
  - Right column (260px–300px): Move history notations, room controls, turn timers.
- **Mobile & Tablet (<1024px):** Reflows vertically. The 15x15 board takes edge-to-edge priority with horizontal panning enabled if square cell sizing dips below 24px touchable intersection targets. Player status bars consolidate into a compact dual-seat header above the grid.

## Elevation & Depth

Depth in this design system is expressed through physical materiality and fine tactile edge definitions rather than heavy diffuse drop shadows:

- **Layer 0 (Canvas):** Flat `#F6F3EC` tabletop ground.
- **Layer 1 (Card & HUD Panels):** Surface `#FAF8F5` bordered with a 1px solid `#E7E3D8` edge and an ultra-subtle contact shadow: `0 1px 2px rgba(28, 25, 23, 0.04)`.
- **Layer 2 (The Playing Board):** `#EFE9DC` board base with an inset structural shadow (`inset 0 1px 3px rgba(28, 25, 23, 0.05)`) and an exterior 1px framing border `#D6CDBC`.
- **Layer 3 (Stones & Marks):** Ink marks and terracotta stones sit on top of intersections with an authentic tactile stamp:
  - Deep Ink (X): Solid stamp, razor-sharp edge, micro ambient shadow (`0 2px 4px rgba(28, 25, 23, 0.12)`).
  - Terracotta (O): Rich mineral ring with smooth rounded caps and `0 2px 4px rgba(194, 65, 12, 0.15)`.
- **Layer 4 (Modals & Overlays):** `#FAF8F5` surface elevated by a calm boundary: `0 12px 32px rgba(28, 25, 23, 0.08)`.

## Shapes

The shape system adopts subtle, organic softness (`roundedness: 1`), honoring the look of hand-trimmed cardstock and polished wooden game blocks:

- Buttons, inputs, and badges use 4px (`0.25rem`) corner rounding.
- Surface cards and board framing use 8px (`0.5rem`) soft corners.
- Round indicators (turn halos, seat avatars, stone placeholders) are strictly circular (`rounded-full`).

## Components

### Buttons
- **Primary:** Solid `#1F7A4D` background, `#FAF8F5` label, 4px border radius. Hover: `#166534`. Active: subtle transform scale `0.98`.
- **Secondary / Outlined:** `#FAF8F5` paper surface, 1px border `#E7E3D8`, `#1C1917` text. Hover: `#F6F3EC` background, `#1C1917` border.
- **Destructive / Resign:** Text-only or ghost button, `#78716C` resting, transitioning to `#C2410C` on hover.

### 15x15 Caro Board & Intersections
- The board consists of a 15x15 grid yielding 225 intersections.
- Grid lines are drawn with 1px `#D6CDBC`. Star points (tengen and 4 corner hoshi marks) are rendered as crisp 4px ink dots `#1C1917`.
- Hovering over an empty intersection highlights a faint ghost stone (50% opacity of current turn mark).
- Last placed stone is highlighted with an understated 2px pulsing dot or ring matching the active stone color.

### Win Condition Highlights
- Winning 5-in-a-row lines are celebrated quietly: the 5 winning grid cells receive a soft translucent fill `#DCFCE7` linked by a continuous 2px botanical green stroke (`#166534`).

### Seat Status & Timers
- Dual player pods displayed in cards: Avatar dot (Sumi Black or Terracotta), player handle (`body-md`), status pip (online/thinking), and an oversized countdown timer in tabular figures.
- Active player is signified by an emerald underline accent bar (2px `#1F7A4D`) under their seat container.

### Chips & Move History Notation
- Notation moves (e.g., `1. H8`, `2. I7`) display inside minimalist chips: `#FAF8F5` surface with a 1px `#E7E3D8` border, using `label-sm` typography with deep ink numbers.

### Inputs & Room Codes
- 1px border `#E7E3D8`, `#FAF8F5` background, padded with `space-sm` vertically and `space-md` horizontally. Focus ring is an unobtrusive 1.5px `#1F7A4D` with no outer glow.