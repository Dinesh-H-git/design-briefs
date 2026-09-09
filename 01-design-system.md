# Design system — component brief

> **Foundation — shared by every feature.** Nothing here is Workflows';
> a second feature reuses this file unchanged.

The component library every feature is built from — not Workflows'. Every
component listed here exists in Storybook today; the values are taken from the
code, not from memory.

Read this first, then `02-layout.md` for how much room things take, then
`03-workflows.md`, which composes screens **out of these components** rather
than inventing new ones.

**Design in Mulish, render in Roboto.** The Figma source is set in Mulish; the
app renders Roboto at the same sizes and weights. Sizes below are the rendered
ones.

**Two modes, always.** Every screen exists in Light and Dark. A colour
below with two values changes by mode; one value means it is an *identity*
(a status hue, a chart colour) and must read the same in both.

---

## 1. Foundations

### 1.1 Colour

Semantic names only — never a raw hex in a design spec. The hexes are here so
you can see them, not so you can reuse them by value.

| Token | Light | Dark | Used for |
|---|---|---|---|
| `bg.body` | `#F8F8F8` | `#212121` | the page under everything |
| `bg.content` | `#FFFFFF` | `#2F2F2F` | cards, panels, the surface content sits on |
| `bg.control` | `#FFFFFF` | `#1F1F1F` | fields, buttons, the inner card |
| `bg.tooltip` | `#4F4F4F` | `#4F4F4F` | tooltip only |
| `text.default` | `#4F4F4F` | `#F8F8F8` | body and headings |
| `text.secondary` | `#11111180` | `#FFFFFF80` | dates, captions, labels above a value |
| `text.placeholder` | `#BFBFBF` | `#6A6A6A` | empty field text |
| `text.link` | `#2D5893` | `#0196CC` | links |
| `text.inverse` | `#FFFFFF` | `#292929` | text on a filled control |
| `line.default` | `#D6D6D6` | `#4F4F4F` | field borders |
| `line.secondary` | `#1111111A` | `#FFFFFF1A` | dividers, table rules |
| `line.strong` | `#CCCCCC` | `#6A6A6A` | outlined button borders |
| `active.alternate` | `#750856` | `#BA84AB` | the selected/active accent (magenta) |
| `focus` | `#9CD8EF` | `#2BACBE` | focus ring |

**Status hues — identical in both modes.**

| Token | Value | Meaning |
|---|---|---|
| `semantic.ok` | `#97BD4C` | success, healthy |
| `semantic.critical` | `#DD2C00` | failure, destructive |
| `semantic.warning` | `#F7C41B` | warning |
| `semantic.info` | `#0196CC` | information |

Each also has a `.subtle` (a tint for a banner's fill) and a `.text` variant.

**Chart ramp** — for anything that must read as *several distinct things*
rather than as one stripe: avatars, tag pills, donut segments.

`one #2BBDBE` · `two #B55182` · `three #EFB118` · `four #4269D0` ·
`five #FF8AB7` · `six #97BBF5` · `seven #A463F2` · `eight #FF725C` ·
`nine #9C6B4E` · `ten #9498A0`

> A colour in this ramp is **assigned, never chosen** — derived from a stable
> key (a person's id, a tag's key) so the same person is the same colour on
> every screen. `two` and `five` are reserved for two specific people and are
> excluded from automatic assignment.

### 1.2 Surfaces and the hover rule

Three surfaces — `body`, `content`, `control` — and a rule that goes with them:
**a hover state is always "the other surface"**, because a fixed highlight
colour reads as a highlight on one background and as nothing on another.

- on `bg.body` → hover paints `bg.control`
- on `bg.content` → hover paints `bg.body`
- on `bg.control` → hover paints `bg.body`

Design hover states as *the contrasting surface*, not as a specific grey.

### 1.3 Type scale

**One typeface: Roboto.** There is no second face and no pairing — hierarchy is
carried by size and weight alone. The only other family the app names anywhere
is the generic `monospace` keyword, for inline code and the JSON editor.

**Whole pixels.** The ramp was 10.5 / 12.5 / 14.5 — the design's own values, and
half a pixel is not a size a screen can draw. Rounded to nearest, ties down:
rounding up would put `sm` at 15 against `lg`'s 16, and two steps a pixel apart
is not a scale.

| Style | Size | Line height | Weight |
|---|---|---|---|
| `xs` | 10px | 1 | 400 |
| `sm` | 12px | 1 | 400 |
| `md` | 14px | 1.5 | 400 |
| `lg` | 16px | 1.5 | 400 |
| `xl` | 20px | 1.5 | 400 |
| `3xl` | 27px | 1.5 | 400 |

Weight variants: `-bold` (700) on `xs`/`sm`/`md`; `-strong` (800) on
`xs`/`sm`/`md`/`lg`. Weight 600 exists as a token but is unused.

**Where each is used:** `3xl` page and record titles · `lg` toast copy and
markdown body · `md` everything in a row, cell or field · `md-strong` field
labels, card titles, section headings · `sm` breadcrumbs and tooltips ·
`sm-strong` table column headers (uppercased) · `xs-bold` priority badges.

### 1.4 Radii, borders, elevation

- **Radii** — `control 3px` (fields, buttons, tooltips, **both kinds of
  chip**), `controlLg 4px` (32px icon buttons and the bars they sit in — top-bar
  controls, the table footer, a toast, an editor section), `panel 8px` (the
  trigger / condition / action cards). Circles use `full`. One-offs stay inline:
  the checkbox box (2px at `md`, 1.5px at `sm`), the sidebar logo tile at 16px.
- **Borders** — `hairline 0.75px` (tag pill stroke), `base 1px` (everything),
  `focus 1.5px`, `thick 2px`.
- **Focus** — the field's own border changes to `focus` **and keeps its width**;
  the extra half-pixel is an outline drawn outside the box. A focus ring must
  never move the thing it is on.
- **Shadows** — `panel`, `card`, `modal`, `popover`, `tabActive`. Tooltips and
  toasts are deliberately **flat**: a tint and a 1px edge separate them.

### 1.5 Iconography

Two kinds, drawn by different mechanisms, and the difference is visible:

- **UI icons** — monochrome, tinted by `color`. Never multi-colour.
- **Integration logos** — keep their own colours (Slack, Jira, MS Teams,
  DataDog, Firebase, AWS Lambda, Amazon CloudWatch, Prometheus).

**A mark in a row is a 20px glyph centred in a 24px frame, 8px before the
text.** The frame matters: glyphs are not all the same shape, and without it a
column of them has an edge that moves per row.

Never borrow a glyph that means something else. A card whose action has no icon
draws its title alone — a missing mark reads as missing, a wrong one reads as a
fact.

---

## 2. Controls

| Component | Anatomy | Sizes | States |
|---|---|---|---|
| **PrimaryButton** | filled label | `listing` · `form` · `small` | rest / hover / active / disabled |
| **SecondaryButton** | outlined label | the same three | rest / hover / active / disabled |
| **TertiaryButton** | ghost label, no border | the same three | rest / hover / active / disabled |
| **SecondaryIconButton** | outlined square, icon at half the box | 24px (12px icon) | rest / hover / active / disabled |
| **TertiaryIconButton** | ghost square, no border | 24px (12px icon) | rest / hover(3A overlay) / active(5A overlay) / disabled |
| **DeleteIconButton** | ghost square in `semantic.critical` | 24px | rest / hover / disabled |
| **Input** | label + field + optional error | full width | rest / focus / error / disabled |
| **Textarea** | resizable field | min-height set by caller | rest / focus / error / disabled |
| **Select** | field + chevron, opens a `MenuList` | width by caller | closed / open / disabled / placeholder |
| **Dropdown** | multi-select field with chips | full width | closed / open / loading |
| **Checkbox** | rounded box + tick | `md` 16px box / `sm` 12px | unchecked / checked / indeterminate / disabled |
| **Radio** | circle + dot | default | unchecked / checked / disabled |
| **Switch** | 26×16 pill | default | off / on / disabled |
| **SearchBar** | magnifier + field + clear | full width | empty / typing / with value |
| **ToggleChip** | selectable pill | default | off / on |
| **TagColorPicker** | swatch + chevron, opens the 10-colour ramp | 24px | closed / open / disabled |

**One size sheet for all three buttons** — `listing` (45px min-width, `sm`
label), `form` (58px, `md`), `small` (31px, `xs`). A button changes variant, not
geometry, so a row of mixed variants lines up.

**Disabled is one treatment everywhere:** 45% opacity, `not-allowed` cursor.

**Hover and press are mutually exclusive.** Scope hover to `:not(:active)` so a
pressed control never shows its hover colour.

---

## 3. Display

| Component | What it is |
|---|---|
| **Avatar** | initials on a chart colour, circular; sizes `xs` 18 / `sm` 24 |
| **KeyValueTag** | two-part pill: key segment (tinted border + text on `bg.body`) then value segment (filled with the chart colour, `bg.body` text). 0.75px border, 3px radius. Key ellipsis-clips and returns in full on hover |
| **KeyValueTags** | a rail of the above that fits as many as the width allows and puts the rest behind a `+N` that opens a panel |
| **Chip** | small labelled pill |
| **Priority** | 26px-min badge `P1`–`P5` (or `-` for unset) with per-level fill; optionally followed by its name. **P1 red → P5 grey**, severity descending |
| **IncidentStatus** | 9px colour dot + name. The dot is the second channel — status never depends on text alone |
| **TextLink** | link in `text.link`; hover → purple, active → `active.alternate`. Truncates with the full text on hover. No pointer cursor unless it actually has a destination |
| **AppIcon / AppLogo** | see §1.5 |
| **IntegrationIcons** | a row of third-party logos with a `+N` overflow; each names itself on hover. **In the library, on no product screen today** |
| **WorkflowActionIcons** | the same rail, holding a tinted UI glyph per action kind instead of a logo. This is what the listing's Actions column and the details header draw |
| **Loader** | ring spinner drawn as a 293.72° arc on a bare track; sizes `sm` 9 / `md` 15 / `lg` 30 / `xl` 60 / `xxl` 90 |
| **SkeletonText** | pulsing lines that occupy exactly the line box the real text will |
| **Bullet** | list marker; 8px heading a row of fields, 4px marking an item in a list. Takes the colour of what it marks |
| **FavoriteStar** | outline / filled toggle |
| **ExpandCollapseToggle** | two-state chevron button |

---

## 4. Feedback and overlays

| Component | Spec |
|---|---|
| **Tooltip** | `bg.tooltip` surface, white 12px/1.5 centred text, 3px radius, 8px padding, arrow that flips to the available side. **No shadow.** 200ms open delay, closes immediately |
| **Toast** | 782px wide (capped to the viewport), min 54px tall, `controlLg` radius, `semantic.*.subtle` fill on a 1px `semantic.*` border, **no shadow**. 30px circular mark (a disc with the glyph knocked out), message at `lg`, ghost close button at the trailing edge. Enters from the **top centre**, max 3 stacked, 12px apart, 5s each |
| **ConfirmDialog** | modal: title, sentence naming the thing, destructive confirm + cancel |
| **DiscardChangesDialog** | modal asking before abandoning edits |
| **SidePanel / SlideOver** | right-hand panel: header with title + close, scrolling body, pinned footer with the CTA pair |
| **OverflowPanel** | the popover a `+N` opens |
| **MenuList** | popover list; rows are 1 line (or 2 for a person: name over handle). Caps at **5 rows** then scrolls, so it is always cut between rows |
| **PageMessage** | centred message for a page-level empty/error state |

---

## 5. Data table

One table serves every listing. **Structural properties belong to the table;
only the columns differ.**

- **Header row** 40px, labels `sm-strong` **uppercased**, sticky, opaque in
  whatever surface it was dropped onto, bottom rule `line.secondary`.
- **Body row** 73px, divider `line.secondary`, hover paints the contrasting
  surface across the full row width.
- **Cell padding** 16px horizontal, 12px vertical, against a row height of
  73px. A column whose content *is* a control (a switch, a checkbox) gives up
  both, so the control fills the cell and the whole cell is clickable.
- **Optional leading checkbox column** with select-all.
- **Loading** replaces the body only — the header stays, and the body reserves
  the height of the page being fetched so nothing collapses and snaps back.
- **TableFooter** — "Show rows per page" + a size select on the left; first /
  prev / next icon buttons on the right, faded to 50% when unavailable.

---

## 6. Editors

- **MarkdownEditor** — toolbar (block-style select, bold, italic, ordered and
  unordered list, link, table, code, strikethrough, quote, rule) plus preview
  and expand toggles; a textarea that swaps for rendered markdown in preview.
- **Prose** — rendered markdown in the app's own type scale: headings, both
  list kinds, blockquote, rule, links, tables, inline code, fenced code on a
  tinted panel.
- **CodeEditor** — CodeMirror for JSON payloads.

---

## 7. Chrome

- **Shell** — left rail, **52px collapsed / 207px expanded**, 0.15s transition.
  Rail items are a 20px icon at a 16px inset plus a label that fades. Brand
  tile and account at the top, product logo and a collapse control at the
  bottom. The selected item carries a rounded active marker.
- **TopBar** — team picker, search, theme toggle, on-call summary, avatar.
- **PageHeader** — title, description, primary action.
- **ListingCountBar** — "N Workflows." plus filter/search controls.
- **Tabs** — a flex row over a continuous `line.secondary` rule. The selected
  tab is a **3-sided magenta box with an open bottom** that breaks the rule and
  merges into the panel. Drawn with an inset shadow so switching tabs shifts
  nothing.
- **HelpPanel** — right-hand guidance column beside a form.
- **DetailHeader / DetailSection / EditorSection / PanelCard** — the page
  furniture §2 of the app brief composes.

---

## 8. Rules a design has to keep

1. **Every screen exists in both modes.** Never spec a colour that only works in
   one.
2. **A control that can't act says so** — disabled, not silently inert.
3. **A control never shows a value it doesn't produce.** If a picker displays a
   colour, that is the colour the thing will be.
4. **Never two sources for one fact.** A label, an icon and a colour that
   describe the same thing come from one place, or they drift.
5. **Clipping needs a way back** — anything ellipsised returns in full on hover,
   and only when it is actually clipped.
6. **Loading has a shape.** A pending region reserves the size of what is
   coming; it never collapses and re-expands.
7. **A frame, not padding, aligns a column of marks.**
