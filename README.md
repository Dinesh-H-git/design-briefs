# design-brief

Three markdown briefs written to be **fed to a design tool**, in this order:

1. **`01-design-system.md`** — the component library. Foundations (colour in
   both modes, type scale, radii, borders, elevation, iconography) and every
   component that exists in Storybook today, with its anatomy, sizes and
   states.
2. **`02-workflows-app.md`** — the feature. What a workflow is, then each
   screen described as an assembly of the components in (1), plus the
   behaviour and the states each screen has to cover.
3. **`03-layout.md`** — how much room everything takes: the page skeleton,
   the **two page templates** every feature's details and edit screens are
   built from, the spacing grid, the density constants, which region scrolls,
   stacking, motion. It is separate because the theme defines **no** spacing, sizing,
   breakpoint or z-index tokens — those values live as raw pixels inside
   components, and without them collected a design will invent a second set.

The second names components from the first in `code font`. That is deliberate:
the point of the pair is that the app is designed **out of the system**, not
alongside it. If a screen appears to need something new, the brief asks for it
to be called out rather than quietly drawn.

## Where the contents come from

Read out of the code, not from memory: token values from `app/src/theme/`, the
component list from the Storybook stories, dimensions from the components
themselves. Where a design node exists it is cited; where the current UI was
inferred rather than specified, `02` says so — §4 of that file is the list of
what has no design yet, which is the most useful place to start.

## Keeping them true

They describe the app as of the last commit that touched them. The parts most
likely to go stale first are the component inventory in `01` §2–§7, the column
tables in `02` §2, and the spacing values in `03` §3. All are cheap to
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

## Not vault notes

These live in the repo, not in the Obsidian vault, so they carry no frontmatter
and no `[[wikilinks]]` — a design tool reading them would only be confused by
either. The vault's own record of this project stays in `brain/` and
`reference/`.
