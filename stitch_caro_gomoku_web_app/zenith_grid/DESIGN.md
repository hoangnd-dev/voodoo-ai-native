---
name: Zenith Grid
colors:
  surface: '#faf8ff'
  surface-dim: '#d2d9f4'
  surface-bright: '#faf8ff'
  surface-container-lowest: '#ffffff'
  surface-container-low: '#f2f3ff'
  surface-container: '#eaedff'
  surface-container-high: '#e2e7ff'
  surface-container-highest: '#dae2fd'
  on-surface: '#131b2e'
  on-surface-variant: '#434655'
  inverse-surface: '#283044'
  inverse-on-surface: '#eef0ff'
  outline: '#737686'
  outline-variant: '#c3c6d7'
  surface-tint: '#0053db'
  primary: '#004ac6'
  on-primary: '#ffffff'
  primary-container: '#2563eb'
  on-primary-container: '#eeefff'
  inverse-primary: '#b4c5ff'
  secondary: '#904d00'
  on-secondary: '#ffffff'
  secondary-container: '#fe932c'
  on-secondary-container: '#663500'
  tertiary: '#943700'
  on-tertiary: '#ffffff'
  tertiary-container: '#bc4800'
  on-tertiary-container: '#ffede6'
  error: '#ba1a1a'
  on-error: '#ffffff'
  error-container: '#ffdad6'
  on-error-container: '#93000a'
  primary-fixed: '#dbe1ff'
  primary-fixed-dim: '#b4c5ff'
  on-primary-fixed: '#00174b'
  on-primary-fixed-variant: '#003ea8'
  secondary-fixed: '#ffdcc3'
  secondary-fixed-dim: '#ffb77d'
  on-secondary-fixed: '#2f1500'
  on-secondary-fixed-variant: '#6e3900'
  tertiary-fixed: '#ffdbcd'
  tertiary-fixed-dim: '#ffb596'
  on-tertiary-fixed: '#360f00'
  on-tertiary-fixed-variant: '#7d2d00'
  background: '#faf8ff'
  on-background: '#131b2e'
  surface-variant: '#dae2fd'
typography:
  headline-lg:
    fontFamily: Inter
    fontSize: 32px
    fontWeight: '600'
    lineHeight: 40px
    letterSpacing: -0.02em
  headline-md:
    fontFamily: Inter
    fontSize: 24px
    fontWeight: '600'
    lineHeight: 32px
    letterSpacing: -0.015em
  headline-sm:
    fontFamily: Inter
    fontSize: 18px
    fontWeight: '600'
    lineHeight: 24px
    letterSpacing: -0.01em
  body-lg:
    fontFamily: Inter
    fontSize: 16px
    fontWeight: '400'
    lineHeight: 24px
    letterSpacing: 0em
  body-md:
    fontFamily: Inter
    fontSize: 14px
    fontWeight: '400'
    lineHeight: 20px
    letterSpacing: 0em
  body-sm:
    fontFamily: Inter
    fontSize: 12px
    fontWeight: '400'
    lineHeight: 16px
    letterSpacing: 0.01em
  label-lg:
    fontFamily: Inter
    fontSize: 14px
    fontWeight: '600'
    lineHeight: 20px
    letterSpacing: 0.01em
  label-md:
    fontFamily: Inter
    fontSize: 12px
    fontWeight: '500'
    lineHeight: 16px
    letterSpacing: 0.02em
  label-sm:
    fontFamily: Inter
    fontSize: 11px
    fontWeight: '600'
    lineHeight: 14px
    letterSpacing: 0.04em
  glyph-board:
    fontFamily: Inter
    fontSize: 22px
    fontWeight: '700'
    lineHeight: 22px
    letterSpacing: 0em
  metric-timer:
    fontFamily: Inter
    fontSize: 28px
    fontWeight: '500'
    lineHeight: 32px
    letterSpacing: -0.02em
rounded:
  sm: 0.125rem
  DEFAULT: 0.25rem
  md: 0.375rem
  lg: 0.5rem
  xl: 0.75rem
  full: 9999px
spacing:
  gutter: 1.5rem
  margin: 2rem
  space-xs: 0.25rem
  space-sm: 0.5rem
  space-md: 1rem
  space-lg: 1.5rem
  space-xl: 2rem
---

## Brand & Style

The design system establishes a quiet, cerebral, and immediate environment tailored for desktop Gomoku (Caro). It eliminates decorative noise to prioritize spatial clarity, strategic contemplation, and decisive interaction. 

### Core Aesthetic: Disciplined Tactile Minimalism
- **Emotional Resonance:** Calm precision, intellectual clarity, zero friction, and high intentionality.
- **Target Audience:** Strategy enthusiasts, desktop web gamers, and purists seeking distraction-free head-to-head play.
- **Style Archetype:** Modern Structural Minimalism. The interface relies on architectural alignment, generous negative space, crisp hairline grids, and unmistakable semantic contrast rather than heavy drop shadows or ornamental depth.

## Colors

The palette leverages a neutral slate baseline, allowing the two competing tokens—Player X and Player O—to command absolute visual clarity without cognitive fatigue.

### Palette Architecture
- **Player X (Cobalt Azure):** `#2563EB` (Primary Interactive/Fill), `#1D4ED8` (Active/Stroke/Darkened). Communicates sharp focus and crystalline clarity.
- **Player O (Warm Ochre Amber):** `#D97706` (Secondary Interactive/Fill), `#B45309` (Active/Stroke/Darkened). Communicates warm tactical gravity and decisive counterplay.
- **Surfaces & Canvases:**
  - Base Canvas: `#F8FAFC` (Slate 50)
  - Surface Panel / Board Bed: `#FFFFFF` (Pure White)
  - Sub-surface Muted: `#F1F5F9` (Slate 100)
- **Borders & Dividers:** `#E2E8F0` (Slate 200) for grid dividers, panel edges, and low-contrast borders.
- **Text & Contrast Tiers:**
  - High Contrast (Primary Text): `#0F172A` (Slate 900)
  - Medium Contrast (Secondary Labels & Timers): `#475569` (Slate 600)
  - Low Contrast (Disabled/Watermark): `#94A3B8` (Slate 400)

### Dual-Signaling Requirement
Color must never be the sole differentiator for moves, turns, or victory conditions. Player representations must consistently pair color with explicit typographic glyphs (`X` and `O`).

## Typography

The type system is strictly unified under `Inter`, optimized for tabular precision, instant optical parsing, and balanced vertical metrics.

### Typographic Roles
- **Glyph Symbols (`glyph-board`):** Bold, optically balanced letterforms designed to sit centrally within the 15x15 intersections or cells. They retain proportional thickness to guarantee instant legibility at standard desktop viewing distances.
- **Metric Timers:** Displayed in tabular numbers (`tnum` font feature) to prevent horizontal jitter during active move countdowns.
- **Micro Labels:** Set with subtle uppercase trackings (`0.02em` to `0.04em`) to establish crisp section headers across match analytics and player panels.

## Layout & Spacing

The layout treats the 15x15 Gomoku grid as the geometric anchor of the application, utilizing a fixed-column side rail alongside an auto-centering play area.

### Spatial Architecture
- **Desktop Structure:** Dual-pane asymmetrical workspace.
  - **Main Canvas (Center-Left):** Houses the Gomoku 15x15 grid within a constrained bounding box (ideal dimensions: `640px` to `720px` square) flanked by equal generous padding.
  - **Match Rail (Right or Flanking):** Fixed `320px` utility column containing player cards, turn states, timers, and match controls.
- **Rhythm & Alignments:** Strict 4px/8px incremental base. Board grid cells must adhere to a rigid 1:1 aspect ratio (`40px` to `48px` per cell on desktop) with centered intersection points.
- **Outer Shell Margins:** Set to `margin` (2rem / 32px) to prevent screen edge collision and maintain breathing room around the active field.

## Elevation & Depth

Depth is established strictly through tonal layering and hair-thin architectural borders, rejecting heavy skeuomorphism and muddy ambient shadows.

### Elevation Tiers
- **Tier 0 (Base Canvas):** Solid background in `#F8FAFC`. Zero elevation.
- **Tier 1 (Panels & Board Bed):** Background in `#FFFFFF` with a single 1px solid border in `#E2E8F0`. Provides flat structural containment.
- **Tier 2 (Floating Popovers / Tooltips / Modals):** Background in `#FFFFFF`, border in `#CBD5E1`, with an ultra-subtle, clean ambient drop: `0px 4px 12px -2px rgba(15, 23, 42, 0.06), 0px 2px 4px -1px rgba(15, 23, 42, 0.04)`.
- **Active Board Intersection (Hover Ghost):** 1px dashed outline in `#94A3B8` or a 15% opacity tint of the active player's color, appearing instantly without elevation shifts to guarantee zero layout recalculation.

## Shapes

The visual language adheres to minimal, structural radius curves (`roundedness: 1`, 0.25rem / 4px base).

### Curvature Guidelines
- **Board Grid & Board Surface:** Crisp outer container rounded to `0.5rem` (8px). Individual cells and intersection targets remain geometric squares (`0px` radius) to maintain grid alignment accuracy.
- **Buttons & Control Chips:** `0.25rem` (4px) corner radius. This conveys a calibrated instrument aesthetic rather than a casual consumer toy.
- **Player Status Badges:** `0.25rem` (4px) structural tag style, reinforcing rigorous UI discipline.

## Components

### 1. The 15x15 Gomoku Board
- **Base Grid:** White `#FFFFFF` playing surface enclosed by a 1px `#CBD5E1` frame. Grid lines are continuous 1px `#E2E8F0` rules.
- **Star Points (Hoshi):** 4px solid `#64748B` round dots located at standard intersections (e.g., 4-4, 4-12, 8-8, 12-4, 12-12).
- **Intersection Cell Targets:** Square hitboxes (minimum `42x42px`) centered over grid lines.
- **Move Pieces (Glyphs):**
  - **X Token:** Centered glyph rendered in `#2563EB` using `glyph-board` font specs. Last played move displays a subtle 2px accent dot or hairline ring in `#1D4ED8`.
  - **O Token:** Centered glyph rendered in `#D97706` using `glyph-board` font specs. Last played move displays a subtle 2px accent dot or hairline ring in `#B45309`.
  - **Winning Sequence:** 5-in-a-row connections are underscored by an unbroken 2px line in the winning player's dark shade (`#1D4ED8` or `#B45309`) with a soft matching background tint (`5%` opacity) across the winning cells.

### 2. Buttons
- **Primary Button (Exactly One Style):** Solid, purposeful action (e.g., "New Match" or "Confirm Resign").
  - Background: `#0F172A` (Slate 900)
  - Text: `#FFFFFF`, `label-lg`
  - Padding: `0.5rem 1.25rem` (8px 20px)
  - Radius: `0.25rem` (4px)
  - Hover: `#1E293B` (Slate 800)
  - Focus Ring: 2px offset with 2px ring in `#2563EB`
- **Secondary / Utility Buttons (Quiet Text Actions):** Used for "Undo Request", "Resign", "Settings", "Rules".
  - Background: `transparent`
  - Text: `#475569` (Slate 600), `label-md`
  - Hover: `#0F172A` text with `#F1F5F9` background
  - Active: `#E2E8F0` background

### 3. Player Turn Cards
- **Structure:** Two symmetric cards representing Player X and Player O.
- **Active State:** Clean `#FFFFFF` panel with a prominent 2px solid left border matching the player's primary hue (`#2563EB` for X, `#D97706` for O), displaying player label, score, and the running timer.
- **Inactive State:** Flat `#F1F5F9` background, 1px `#E2E8F0` border, dimmed text (`#64748B`).

### 4. Interactive Feedback & Focus States
- **Focus Rings:** All keyboard-focused interactive items trigger a sharp `2px` focus ring in `#2563EB` with a `2px` white gap.
- **Empty Cell Hover:** When hovering over an unplayed intersection on the active turn, preview the respective letter glyph (`X` or `O`) at `35%` opacity in the active player's hue.