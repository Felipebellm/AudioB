---
colors:
  canvas: "#0d1117"
  canvas-subtle: "#161b22"
  canvas-inset: "#010409"
  border: "#30363d"
  border-muted: "#21262d"
  fg: "#e6edf3"
  fg-muted: "#9198a1"
  fg-subtle: "#6e7681"
  fg-on-emphasis: "#ffffff"
  accent: "#58a6ff"
  accent-emphasis: "#1f6feb"
  success: "#3fb950"
  success-emphasis: "#238636"
  danger: "#f85149"
  danger-emphasis: "#da3633"
  attention: "#d29922"
  attention-emphasis: "#9e6a03"
  record-armed: "#f85149"
  record-active: "#da3633"
  solo-active: "#d29922"
  mute-active: "#6e7681"
  playhead: "#58a6ff"
  selection: "#1f6feb"
  meter-low: "#3fb950"
  meter-mid: "#d29922"
  meter-high: "#f85149"

typography:
  ui:
    fontFamily: "system-ui, -apple-system, 'Segoe UI', 'Noto Sans', Helvetica, Arial, sans-serif"
    fontSize: "13px"
    fontWeight: "400"
    lineHeight: "1.5"
  ui-strong:
    fontFamily: "system-ui, -apple-system, 'Segoe UI', 'Noto Sans', Helvetica, Arial, sans-serif"
    fontSize: "13px"
    fontWeight: "600"
    lineHeight: "1.5"
  ui-small:
    fontFamily: "system-ui, -apple-system, 'Segoe UI', 'Noto Sans', Helvetica, Arial, sans-serif"
    fontSize: "11px"
    fontWeight: "400"
    lineHeight: "1.4"
  numeric:
    fontFamily: "ui-monospace, 'SF Mono', Menlo, Consolas, 'Liberation Mono', monospace"
    fontSize: "13px"
    fontWeight: "400"
    lineHeight: "1.2"
    letterSpacing: "0"
  numeric-display:
    fontFamily: "ui-monospace, 'SF Mono', Menlo, Consolas, 'Liberation Mono', monospace"
    fontSize: "20px"
    fontWeight: "500"
    lineHeight: "1.1"

spacing:
  xs: "4px"
  sm: "8px"
  md: "16px"
  lg: "24px"
  xl: "32px"
  xxl: "48px"

rounded:
  sm: "3px"
  md: "6px"
  lg: "12px"
  full: "9999px"

components:
  panel:
    background: "{colors.canvas}"
    border: "1px solid {colors.border}"
    radius: "{rounded.md}"
    padding: "{spacing.md}"
  panel-header:
    background: "{colors.canvas-subtle}"
    foreground: "{colors.fg}"
    borderBottom: "1px solid {colors.border}"
    padding: "{spacing.sm} {spacing.md}"
    typography: "{typography.ui-strong}"
  track-header:
    background: "{colors.canvas-subtle}"
    border: "1px solid {colors.border-muted}"
    foreground: "{colors.fg}"
    padding: "{spacing.xs} {spacing.sm}"
    typography: "{typography.ui}"
  button:
    background: "{colors.canvas-subtle}"
    foreground: "{colors.fg}"
    border: "1px solid {colors.border}"
    radius: "{rounded.md}"
    padding: "{spacing.xs} {spacing.sm}"
  button-primary:
    background: "{colors.success-emphasis}"
    foreground: "{colors.fg-on-emphasis}"
    border: "1px solid {colors.border}"
    radius: "{rounded.md}"
    padding: "{spacing.xs} {spacing.sm}"
  transport-clock:
    background: "{colors.canvas-inset}"
    foreground: "{colors.fg}"
    border: "1px solid {colors.border}"
    radius: "{rounded.md}"
    typography: "{typography.numeric-display}"
    padding: "{spacing.sm} {spacing.md}"
---

# AudioB Design System

## Overview

AudioB is a professional digital audio workstation. Its visual language takes direct inspiration from the **GitHub portal** — specifically GitHub's Primer design system in dark mode.

That reference is not arbitrary. GitHub solves the same core problem a DAW has: presenting an enormous amount of dense, structured information without exhausting the person reading it. It does this through a calm near-monochrome canvas, hairline borders instead of shadows, and color reserved almost entirely for meaning rather than decoration. A DAW needs exactly that discipline, because a DAW screen carries far more simultaneous information than a GitHub page ever does.

**Personality: precise, calm, professional.** Nothing playful, nothing skeuomorphic. No brushed metal, no faux-analog knobs, no drop shadows imitating physical hardware. AudioB should look like a serious instrument for long sessions, not a toy.

The interface is **dark-first and dark-only**. This is not a stylistic preference — engineers and producers work long hours in dim rooms, and a bright UI beside a waveform display causes real fatigue. A light theme is explicitly out of scope.

## Colors

The palette is Primer's dark theme, adopted directly so that values are proven rather than invented.

**Surfaces** form a three-step depth scale within a very narrow luminance range:

- `canvas` `#0d1117` — the main application background, the default for editor and arrangement areas
- `canvas-subtle` `#161b22` — raised surfaces: panel headers, track headers, mixer strips, toolbars
- `canvas-inset` `#010409` — recessed surfaces: clock displays, waveform wells, text inputs

**Foreground** carries a strict hierarchy. Never use more than three text weights in one region:

- `fg` `#e6edf3` — primary text, track names, values
- `fg-muted` `#9198a1` — labels, units, secondary information
- `fg-subtle` `#6e7681` — disabled states, inactive controls

**Semantic colors are where a DAW must diverge from GitHub, and this matters.** On GitHub, red means danger and green means success. In a DAW those meanings are already taken by audio conventions that musicians have internalized over decades. AudioB therefore re-maps them deliberately:

| Token | GitHub meaning | AudioB meaning |
|---|---|---|
| `danger` `#f85149` | error / destructive | **record arm** |
| `attention` `#d29922` | warning | **solo** |
| `success` `#3fb950` | success | **signal present / healthy level** |
| `accent` `#58a6ff` | links, interactive | **playhead, selection, focus** |

Meter colors follow the universal audio convention — green below nominal, amber approaching, red at clipping — and are **not negotiable**. Musicians read meters by muscle memory; restyling them for visual consistency would actively harm usability. These are the one place where function overrides the design system.

## Typography

Use the **system font stack** for all interface chrome, exactly as GitHub does. It renders crisply on every OS, costs nothing to load, and keeps AudioB feeling native on Linux rather than like a web app in a window.

`ui` at 13px is the workhorse. `ui-small` at 11px is for track headers, meter scales and other dense areas — the only places where density genuinely justifies smaller text. `ui-strong` at weight 600 marks section headers and active states.

**All numeric readouts must use the `numeric` monospace stack.** This is a functional requirement, not an aesthetic one: timecode, BPM, sample positions and gain values change constantly during playback, and proportional digits cause the display to visibly jitter as numbers update. Monospace keeps every digit in a fixed column. `numeric-display` at 20px is reserved for the primary transport clock.

## Layout

GitHub's layout discipline translates to AudioB as follows:

**Regions are defined by 1px borders, never by shadows or gradients.** GitHub separates its entire interface using `border` `#30363d` hairlines against nearly identical background values. This is what makes a dense page feel calm. AudioB's editor, mixer, browser and transport regions should be delineated the same way.

**The 8px grid governs all spacing.** Use `spacing.sm` (8px) as the default gap between related controls, `spacing.md` (16px) between groups, `spacing.lg` (24px) between major regions. `spacing.xs` (4px) is permitted only inside dense clusters such as track header buttons.

**Density is earned, not assumed.** GitHub is information-dense but never cramped, because it spends its space on separation between groups rather than padding inside them. Apply this directly: tighten within a control cluster, breathe between clusters.

**Panel structure follows GitHub's page structure** — a persistent header strip (transport), a primary content column (arrangement/editor), and a secondary sidebar (browser/properties) that can collapse. Users should be able to hide any panel and have the remaining layout reflow sensibly.

## Elevation & Depth

**There are no drop shadows in AudioB.** Depth is communicated entirely through the surface scale and borders, exactly as on GitHub.

- Recessed (`canvas-inset`) — things you read *into*: clocks, waveform areas, inputs
- Base (`canvas`) — the working surface
- Raised (`canvas-subtle`) — things that sit *on* the surface: headers, strips, toolbars

Floating elements (menus, dialogs, plugin windows) are the sole exception and may use a subtle shadow to separate from the layer beneath, since they genuinely overlap rather than tile.

## Shapes

Radius follows Primer exactly: `rounded.sm` 3px for small inline elements such as badges and tags, `rounded.md` 6px as the default for buttons, inputs and panels, `rounded.lg` 12px for dialogs and floating windows.

6px is the signature — soft enough to feel modern, sharp enough to stay serious. Avoid fully rounded (pill) shapes except for genuine toggle switches.

Note a practical constraint: Ardour's canvas-drawn elements (regions, automation lanes, meters) are painted directly rather than composed from widgets, so radius may not be achievable there. Apply radius to widget-based chrome and accept square edges on canvas elements rather than fighting the renderer.

## Components

Token definitions live in the front matter above. Behavioral notes:

**Buttons** default to the subtle surface with a hairline border — GitHub's pattern. Reserve filled/emphasis backgrounds for genuinely primary actions. A screen full of filled buttons has no hierarchy at all.

**Toggle controls** (record, solo, mute) are the exception to restraint: they must read unambiguously at a glance across dozens of tracks. Use the full semantic color as a filled background when active, and the default subtle surface when inactive. Never rely on a border or subtle tint alone to indicate an armed track — that is a mistake with real consequences during a session.

**The transport clock** uses `canvas-inset` with `numeric-display` typography. It is the single most-read element in the application and should be the most legible thing on screen.

**Track headers** use `canvas-subtle` with `border-muted` between adjacent tracks — a lighter separator than region borders, so that a list of forty tracks reads as a list rather than forty boxes.

## Do's and Don'ts

**Do**
- Use borders to separate regions
- Reserve saturated color for state and meaning
- Use monospace for every number that updates during playback
- Keep the three-step surface scale consistent throughout
- Let users collapse any panel
- Follow audio convention over design consistency when the two conflict on meters

**Don't**
- Don't add drop shadows to tiled (non-floating) elements
- Don't introduce a light theme
- Don't use gradients, bevels, or skeuomorphic hardware imitation
- Don't use color decoratively — if a color doesn't encode meaning, it shouldn't be saturated
- Don't restyle meter colors away from green/amber/red
- Don't use proportional fonts for timecode or numeric values
- Don't introduce hex values that aren't in the token list
- Don't use more than three foreground weights in a single region

---

## Implementing this in AudioB

**The tooling in the `apply-design-md` workflow does not apply to this project.** That workflow generates Tailwind configs and CSS variables; AudioB is a GTK2 C++ application forked from Ardour, with no npm, CSS or Tailwind anywhere in the stack. This file is still the source of truth for design decisions — only the code-generation step differs.

Ardour's UI is themed through XML color files in `gtk2_ardour/themes/*.colors`, in the form:

```xml
<Color name="neutral:background" value="202020ff"/>
```

Values are 8-digit hex — `RRGGBBAA`, no leading `#`, alpha last. Translating a token means dropping the `#` and appending `ff` for full opacity: `#0d1117` becomes `0d1117ff`.

The practical path is to copy an existing theme as a starting point rather than authoring from scratch:

```bash
cp gtk2_ardour/themes/dark-ardour.colors gtk2_ardour/themes/audiob-ardour.colors
```

Then map the tokens onto Ardour's existing color names. The key anchors in that file:

| Ardour color name | Purpose (per source comments) | AudioB token |
|---|---|---|
| `theme:bg` | gtk_background | `canvas-subtle` |
| `theme:bg2` | ruler base | `canvas-inset` |
| `neutral:background` | audio track base | `canvas` |
| `neutral:backgroundest` | border color | `border` |
| `neutral:foreground` | gtk_foreground | `fg` |
| `neutral:foreground2` | marker track | `fg-muted` |
| `neutral:midground` | grid line minor | `fg-subtle` |
| `theme:contrasting` | play head | `playhead` |
| `meter color0-9` | level meter ramp | **leave unchanged** |

Note that `meter color0` through `color9` already implement the green→yellow→red ramp and should be left alone, per the Do's and Don'ts above.

Typography and spacing are **not** controlled by the `.colors` files — those live in GTK rc files and widget code, so they will require actual source changes rather than theming. Treat the color theme as phase one; it is achievable without touching C++ and is a genuinely good first contribution for learning the codebase.

## Sources

- [UI color system — Primer](https://primer.style/foundations/color/overview/)
- [Colors — Primer Primitives](https://primer.style/primitives/colors/)
- [Typography — Primer](https://primer.style/foundations/typography)
- [Primer design tokens guide — GitHub](https://github.com/primer/primitives/blob/main/DESIGN_TOKENS_GUIDE.md)
- [How Primer's color system makes GitHub more inclusive — GitHub Blog](https://github.blog/2023-05-11-unlocking-inclusive-design-how-primers-color-system-is-making-github-com-more-inclusive/)
- [design.md specification — Google Labs](https://github.com/google-labs-code/design.md/blob/main/docs/spec.md)
- Primary source: `gtk2_ardour/themes/dark-ardour.colors`, local Ardour clone
