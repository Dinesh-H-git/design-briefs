# design-brief

Markdown briefs written to be **fed to a design tool**, and read in order.

**Two of them are shared; one is not.** The split is the point, and the
numbering carries it — the foundations come first because a feature is
assembled out of them.

### Foundations — every feature reuses these unchanged

1. **`01-design-system.md`** — the component library. Colour in both modes,
   type scale, radii, borders, elevation, iconography, and every component that
   exists in Storybook today with its anatomy, sizes and states.
2. **`02-layout.md`** — how much room everything takes: the page skeleton, the
   **two page templates** every feature's details and edit screens are built
   from, the spacing grid, the density constants, which region scrolls,
   stacking, motion. It is a separate file because the theme defines **no**
   spacing, sizing, breakpoint or z-index tokens — those live as raw pixels
   inside components, and without them collected a design invents a second set.

### The feature

3. **`03-workflows.md`** — Workflows, one feature of an incident-response
   product. What a workflow is, then each screen as an assembly of the
   components in (1), sized by the rules in (2), plus the behaviour and states
   each screen has to cover.

A second feature adds `04-<name>.md` beside the third and touches neither
foundation. If it needs something the foundations do not have, that is a change
to a foundation, made once — not a paragraph in a feature brief.

The feature brief names components from the first in `code font`. That is
deliberate: the point is that a screen is designed **out of the system**, not
alongside it. If a screen appears to need something new, the brief asks for it
to be called out rather than quietly drawn.

## Where the contents come from

Read out of the code, not from memory: token values from `app/src/theme/`, the
component list from the Storybook stories, dimensions from the components
themselves. Where a design node exists it is cited; where the current UI was
inferred rather than specified, `03` says so — §4 of that file is the list of
what has no design yet, which is the most useful place to start.

## Keeping them true

They describe the app as of the last commit that touched them. The parts most
likely to go stale first are the component inventory in `01` §2–§7, the spacing
values in `02` §3, and the column tables in `03` §2. All are cheap to
re-derive:

```bash
ls app/src/components/*.stories.tsx      # the documented component surface
grep -r "export const" app/src/theme/    # the token values
grep -rhoE '(p|px|py|gap)="[0-9]+px"' app/src/components/*.tsx \
  | grep -oE '[0-9]+px' | sort | uniq -c | sort -rn | head   # the spacing grid
```

## Why colour is not its own file

Because a token and the place it is used have to stay on the same line. Split
them and you get two documents that must agree, which is how token docs go
stale — the same drift this codebase has hit more than once. `01` §1.1 keeps
each semantic colour, its value in both modes, and what it is for on one row.

There is also no raw palette worth publishing. The system is two-tier —
primitives, then semantics — and a design should only ever use the semantic
layer. Naming the primitives would invite reaching past it.

## Publishing

This repository is private; the briefs are not. They are mirrored to a public
repo so the raw markdown can be linked, and the mirror is made by script rather
than by hand — it drifted four commits once, while the type scale changed
underneath it.

```bash
./design-brief/publish.sh "what changed"
```

Run it whenever a brief changes. It mirrors exactly, so a renamed file does not
survive in the mirror as a second copy.

## Not vault notes

These live in the repo, not in the Obsidian vault, so they carry no frontmatter
and no `[[wikilinks]]` — a design tool reading them would only be confused by
either. The vault's own record of this project stays in `brain/` and
`reference/`.
