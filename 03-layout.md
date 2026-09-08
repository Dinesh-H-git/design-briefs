# Layout and space — foundation brief

The third of three. `01-design-system.md` says what things look like,
`02-workflows-app.md` says what the screens are; this says **how much room
everything takes and how it is arranged**.

It exists because the theme does not answer that question. Tokens are defined
for colour, radii, border widths, shadows, font sizes and weights, text styles
and keyframes — and **not** for spacing, sizing, breakpoints or stacking. Those
live as raw pixel values inside components. This file collects them so a design
does not have to invent a second set.

> [!important]
> Read §3 before spacing anything. Every gap in the build is one of **six
> values**. Reaching for a seventh is the single easiest way to produce a design
> the build cannot match.

---

## 1. The page skeleton

```
┌───────┬──────────────────────────────────────────────────┐
│       │  TopBar                                          │
│ rail  ├──────────────────────────────────────────────────┤
│       │                                                  │
│ 52 /  │  page content — 16px gutter on three sides       │
│ 207   │                                                  │
│       │                                                  │
└───────┴──────────────────────────────────────────────────┘
```

| Region | Size | Notes |
|---|---|---|
| Left rail, collapsed | **52px** | derived — a 16px inset either side of a 20px icon |
| Left rail, expanded | **207px** | the label fades in over 0.075s, half the rail's own transition |
| Rail item inset | 16px | |
| Top bar | content + 8px vertical padding, `thick` (2px) bottom border | its divider rule is 34px tall |
| Page gutter | **16px** left, right and top | **no bottom gutter** on the details page — the body card bleeds to the bottom edge |
| Section side padding | 16px | inside a details-page section band |
| Index column | **240px**, 16px padding all round | fixed width, does not flex |

### Content limits on wide screens

| Limit | Value | Applies to |
|---|---|---|
| Section content | **900px** | the trigger / condition / action cards on the details page |
| Form column | **932px** | the editor — derived, 900 + its own 2×16 gutter |
| Reading measure | **75ch** at `md` | the record description. In `ch` deliberately, so it tracks the type size instead of restating it in pixels |

**The band runs edge to edge; the content inside it stops.** A section's
background colour spans the full width and only its contents are capped. Do not
centre the page — the form column is left-aligned and the help panel stays
right-aligned as the window grows.

---

## 2. Two page templates

Above the spacing and below the app chrome sits a layer worth naming on its own:
**a record has a details page and an editor, and both are the same shape for
every feature.** Workflows is one instance. What changes between features is
what goes *in* the slots, not where the slots are.

The code says so already — `DetailHeader`'s own note reads "shared by every
details page — nothing here knows what record it's describing", and `HelpPanel`
is deliberately content-free "because the panel is the same surface whatever
screen it sits beside". Draw these two as templates and fill them per feature.

### 2.1 The record details page

```
┌──────────────────────────────────────────────────┐  ← 16px gutter
│ HEADER CARD                                      │
│   breadcrumb › › ›                          [⋮]  │
│   Title                                          │
│   description, clamped to 2 lines                │
│   [tag][tag][+N]              [ TAG ACTION ]     │
│ ──────────────────────────────────────────────── │
│   Label      Label      Label   Label    Label   │  ← equal columns
│   value      value      value   value    value   │
└──────────────────────────────────────────────────┘
                    ↕ 16px
┌──────────────────────────────────────────────────┐
│  Tab   Tab                                       │  ← pins on scroll
├──────────────────────────────────────────────────┤
│  the feature's own content                       │
│                                                  │  ← bleeds to the bottom
```

**Fixed by the template**

| | |
|---|---|
| Page | 16px gutter left, right and top; **no bottom gutter** — the body card bleeds to the bottom edge |
| Scroll | the **page** scrolls as one; the header card scrolls away |
| Card 1 | the header, `bg.content`, 3px radius, soft downward shadow, edge to edge |
| Card 2 | the tabbed body, edge to edge, at least tall enough to reach the bottom |
| Tab bar | pins to the top of the scroll area, keeping the page's 16px gutter above it |

**The header card is two stacked parts.** A header block — breadcrumb trail
(ancestors outermost, the title closing it), title at `3xl` with a kebab at the
trailing edge, description clamped to 2 lines at a **75ch measure** with a
tooltip carrying the rest, then a key/value tag rail with one action button
beside it. Then a `line.secondary` divider that belongs to the block, and a
metadata row of **equal-width columns**, each a bold label over its value, 24px
padding, 16px label to value.

**A metadata column can hold anything** — a live switch, an icon rail, an
avatar and name, plain text. The caller supplies them, so their number and
content are the feature's.

**Filled per feature** — the metadata columns, the tab set, and what is inside
each tab. In Workflows that inner content is an Index column beside Trigger /
Conditions / Actions, and a Logs table; none of that belongs to the template.

**Three states, always** — loading, error, and the record. The page draws one of
the three and fetches nothing itself, so all three are real designs, not
afterthoughts.

### 2.2 The record editor

Always **two segments side by side**: the thing being edited on the left, help
on the right.

```
┌────────────────────────────────┐ ┌─────────────────┐
│  Title       [____________]    │ │ Help and Guide  │
│  Description [____________]    │ │                 │  ← the template's
│  Owner       [____________]    │ │  Article        │    identity block,
│  Tags        [k][v][●]         │ │  paragraphs…    │    536px
│                                │ │                 │
│  ▾ Section title               │ │  Article        │  ← the feature's
│    …                    (full) │ │  paragraphs…    │    own sections
│  ▾ Section title               │ │                 │
│    …                           │ │                 │
├────────────────────────────────┤ │                 │
│ [ SAVE AND CONTINUE ] [CANCEL] │ │                 │  ← pinned
└────────────────────────────────┘ └─────────────────┘
        flexes, content capped         fixed 402px
```

**Fixed by the template**

| | |
|---|---|
| Split | form column + help column, **16px** apart, 16px page gutter |
| Help column | **402px, fixed** — a measured column; prose that reflows with the viewport stops being the design |
| Form column | **flexes**; its *content* stops at a max instead, so the two columns stay adjacent as the window grows rather than drifting apart across a void |
| Save bar | **pinned** as the last child of the form column, outside the scroll area, so the form runs out from under it and SAVE is reachable without scrolling to the end |
| Save bar rule | `line.secondary` — the lighter of the two, because the bar closes the form rather than separating two regions of the page |

**The form column opens with the record's identity, and that block is the
template's too.** Every feature edits the same four things about a record, in
this order, and they are not the feature's to rearrange:

| Field | Control | |
|---|---|---|
| **Title** | `Input`, `sm` | required |
| **Description** | `Textarea` | |
| **Owner** | `Select`, `sm` | required |
| **Tags** | key / value / colour rows | the same rows the details page's tag panel uses |

**These sit at 536px, narrower than the sections below them**, which run the
full column. A short field stretched the width of a form is mostly empty box
with its value stranded at one end; the identity fields are short and stop, and
the feature's own sections do not.

**Below them, a stack of section cards.** Each is a title with a chevron that
folds the section away, on a `bg.body`-to-gradient fill inside a 1px
`line.secondary` edge, 16px of padding and a 12px rhythm down the middle. Its
children are members of that rhythm, not a block inside it — which is what puts
every field, row and card in the editor on one grid.

**The help panel is a card of short articles**: a title over paragraphs, in three
shapes — plain, a bold lead-in before a colon, or a muted aside. It is sized by
its content and stretched to the column's height by the page, never by itself.

**Filled per feature** — the section cards *below* the identity block, and the
articles on the right. Nothing else. In Workflows those sections are the trigger
with its filters, and the ordered action list; both are that feature's, as is
the drag-to-reorder the action list carries.

**Errors sit beside the button that failed**, on the save bar, not above the
form: the sentence is about the act, and someone who just pressed SAVE is
looking there.

---
## 3. Spacing — a 4-point grid, six values

Every gap, pad and margin in the build is one of:

**4 · 8 · 12 · 16 · 24 · 32**

There is no seventh. Use the nearest of these; where a design needs something
between two of them, take the smaller and check whether the relationship it
carries survives.

| Value | Typically |
|---|---|
| **4** | inside a control — a dot to its label, a mark to the value it copies |
| **8** | inside a row — two controls answering one question, icon to label, popover padding |
| **12** | between rows in a stack, table cell vertical padding |
| **16** | the page gutter, card padding, section side padding, one field to the next |
| **24** | between major blocks, header card padding, a rule to the word it frames |
| **32** | the largest gap in use — a body indented past its own mark |

The larger values that appear (48, 120) are Storybook staging, not product.

**One deliberate exception:** a key/value tag pill is padded `8px / 2px`. Four
makes the pill 24px tall instead of 20, which breaks the overflow panel's
three-row cap and a rail of them on one line of a 73px table row. Everything
else is on the grid.

> [!note] This replaced a rhythm, and the replacement moved things
> The build previously ran on 6 / 12 / 16 / 18 / 24 with 3, 9, 10 and 15 in the
> tail — neither a 4- nor an 8-point grid. Snapping it moved real relationships:
> **18 → 16** unified the section gutter with the page gutter, **6 → 8** widened
> every in-row gap, and the collapsed rail narrowed from 56 to **52** because its
> width is derived from that inset. Design to the six above, not to a screenshot
> taken before the change.

## 4. Density constants

These are fixed and shared — every listing uses the same table metrics, every
marked row the same mark geometry. Treat them as given.

**Table**

| | |
|---|---|
| Header row | 40px, labels uppercased, sticky |
| Body row | **73px** |
| Cell padding | 16px horizontal, 12px vertical |
| Divider | 1px `line.secondary` |
| A cell whose content is a control | padding drops to 0 so the control fills it |

**A mark beside text**

| | |
|---|---|
| Frame | **24px** square, centred |
| Glyph | **20px** |
| Frame → text | **8px** |
| Body indent under a title | 32px (frame + gap) |

**List markers**

| | |
|---|---|
| Bullet heading a row of fields | 8px |
| Bullet marking an item in a list | 4px |
| Bullet → content | 8px |

**A control beside its label**

| | |
|---|---|
| Checkbox or radio → its words | **4px** |
| Switch → its words | 8px — a label beside a 26px track, not a 12px box |

**Cards**

| | |
|---|---|
| Panel card padding | 16px |
| Panel card radius | 8px |
| Header/body card radius | 3px |

**A rail of tags** — pills are `8px / 2px` on a 3px radius and 20px tall, **4px**
apart. What does not fit collapses into a `+N` whose panel stacks the rest 4px
apart inside 8px of padding, capped at three rows.

**Toast** — 782px wide, min 54px tall; 24px lead, 16px to the message, 16px
trailing; 30px mark; 12px between stacked toasts; top centre; max 3.

---

## 5. Who scrolls

The most consequential layout decision on each screen, and the one most often
got wrong.

**Listing pages — the table scrolls, the page does not.** The header, count bar
and footer are fixed; only the rows move, under a pinned table header. The
scroll box declares a size container so the table can reserve at least a
scrollport's height — a filtered result leaves empty space below the last row
rather than shortening the page.

**The details page — the page scrolls as one.** The header card scrolls away,
the tab bar pins with the 16px gutter above it, and the sections move beneath
it. Two exceptions inside that:

- The **Index column** pins under the tab bar and **scrolls itself** once it is
  taller than the space it is pinned in. Two columns of different lengths that
  travel independently.
- Switching tabs keeps the page-level position and rewinds only the panel: the
  scroll is pulled back to where the new panel's top sits under the bar, and
  never pushed down to meet it.

**A table inside a page that scrolls** — the Logs tab — is a third case, and
not the listing's. There is no inner scroll box to be fixed around: the footer
is *sticky at the bottom of the page's scrollport* while the rows travel under
it, the same way the table header is sticky at the top.

**Panels and drawers** scroll their body between a fixed header and a pinned
footer.

**Rule of thumb:** a scroll box must be *smaller* than what it scrolls. If a
region has a footer that must stay visible, the region above it scrolls; if the
whole page is the document, the page scrolls.

---

## 6. Stacking

Only three levels are in play, and low numbers deliberately:

| Level | What |
|---|---|
| 1 | sticky table header cells, a pinned table footer |
| 2 | a pinned secondary bar |
| 3 | the sticky tab bar |
| — | menus, tooltips, toasts, drawers and dialogs are portalled and sit above all of it |

Anything that floats over the page belongs in a portal, not in the stack.

---

## 7. Motion

Short, and only where something would otherwise jump.

| Duration | Where |
|---|---|
| **0.1s** | colour changes — hover, a mark coming up under the pointer |
| **0.15s** | the rail expanding, opacity fades, box-shadow, transform |
| 0.075s | the rail's label fade — half the rail's own duration, so it lands first |
| 120ms | small enter/exit transforms — a segment lifting under the pointer |
| 160ms | a card sliding aside to open the gap a dragged one will land in |
| 300ms | the Index's scroll to an anchor, eased (`easeOutCubic`), **skipped entirely under `prefers-reduced-motion`** |
| 5s | how long a toast stands |

A toast's own enter and exit are the component library's, not ours — the code
deliberately restates none of that recipe — so treat the slide-in as given
rather than specifying a number for it.

**Drag to reorder** — *not a template behaviour; it belongs to a feature whose
list is ordered, which in this app is the workflow's actions.* The other cards
move aside to open a gap where the card will land; dragging within 72px of an
edge scrolls the page, ramping from a crawl to 18px per frame at the edge
itself.

**Reserve the space before you animate into it.** A focus ring, a hover
background and an expanding block are all drawn so that nothing around them
moves.

---

## 8. Responsive — desktop only, and deliberately

**The target is a 13-inch MacBook Air, and that is the floor.** Roughly
1440–1470 logical pixels wide, ~800–860 tall once browser chrome is off. The app
is designed at that size and is not expected to work below it: no phone layout,
no tablet layout, and **no breakpoints are defined** — none in the theme, and no
component has a mode-per-width.

That is a position, not an omission. Design wide; do not invent a small-screen
variant unless it is asked for.

What the app does instead of breaking at widths:

- Everything is fluid, bounded by the content limits in §1.
- The rail collapses **on demand**, not at a width.
- Rails of tags and integration marks measure themselves and put the remainder
  behind a `+N`.
- Tables have a natural minimum width and pan sideways under a pinned header —
  except inside a panel, where the columns squeeze instead.

### How the three pages sit at the floor

Content width available = viewport − rail − 32px of gutters. On a 1470px
viewport that is **1231px** with the rail expanded, **1386px** collapsed.

| Page | Needs | At the floor |
|---|---|---|
| Details | 240 Index + 900 section + 32 padding = **1172px** | fits, either way |
| Editor | 932 form + 16 + 402 help = **1350px** | fits — the form column is a *max*, so it flexes down to ~813px with the rail expanded and reaches its full 932 when collapsed |
| Workflows listing | 48 + 1514 of columns = **1562px** | **pans horizontally**, by ~330px expanded and ~175px collapsed |

> [!note] The listing table pans at the target size, by design
> The header pins and travels with its columns, so this is the intended
> behaviour rather than a fault — but it does mean the full column set is never
> visible at once on the target machine. Two levers already exist: collapse the
> rail, or hide columns in the customize popup (dropping Tags alone, at 330px,
> brings it inside the expanded-rail width).
>
> If every column should be visible at 1470px without panning, that is a
> **column-width decision** — roughly 330px has to come out of the set — and it
> belongs in `02` §2.1, not here.
