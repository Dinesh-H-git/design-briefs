# Workflows — feature brief

The feature to design. Workflows is one feature of an incident-response
product, not the product — it automates what happens to an incident, and the
chrome around it (the rail, the top bar, the account) belongs to the product it
sits in.

**Read `01-design-system.md` first**: every screen here is assembled from
components in that file. Where this brief names a component in `code font`, use
the one that already exists rather than drawing a new one.

New components are a last resort. If a screen seems to need one, say so
explicitly and describe what it does that nothing in the system does.

---

## 1. What a workflow is

An automation on an incident-response product. Three parts, and the whole app
is about reading and editing them:

> **WHEN** something happens to an incident — **IF** it matches these
> conditions — **THEN** run these actions, in order.

**The trigger** — one of seven events, lettered as the app letters them:
*incident is Triggered* · *incident is Acknowledged* · *incident is Reassigned*
· *incident is Resolved* · *incident tags are updated* · *incident priority is
updated* · *a note is added to an incident*.

**The conditions** — a list joined by one word (**And** / **Or**), where an item
is either a single filter or a **bracket** of filters with its own inner word.
One level of nesting, no deeper. A filter is `property is|is not value`:

| Property | Value |
|---|---|
| Priority | P1–P5 or Unset |
| Alert Source | one of six: Amazon CloudWatch, AWS Lambda, DataDog, Firebase, Jira, Prometheus |
| Service Name | a service |
| Tag | a **key and a value** — two halves, drawn as a `KeyValueTag` |

A workflow must have **at least one condition**. A trigger with nothing under
it runs on every incident there is, and is not a workflow.

**The actions** — an ordered list, run one after the other. Seventeen kinds:

*Native* — Attach Runbook · Add Communication Channel · Add Incident
Note · Mark Incident as SLO Affecting · Trigger Manual Webhook · Make an HTTP
call · Send an email · Add Status Page Issue · Update Priority

*Slack* — Archive Incident Specific Slack Channel · Create Slack Channel · Send
Slack Message · Send Slack Direct Message

*Jira* — Create a Jira Ticket

*Microsoft Teams* — Create a Microsoft Teams Meeting · Send Microsoft Teams
Message · Send Microsoft Teams Direct Message

Two of them (**Archive Incident Specific Slack Channel**, **Create a Microsoft
Teams Meeting**) configure *nothing* — picking them adds the card immediately,
with no form.

Names are the catalog's, verbatim — they are what a card is lettered with.

---

## 2. Screens

### 2.1 Workflows listing — `/`

`Shell` › `PageHeader` › `ListingCountBar` › `DataTable` › `TableFooter`

Columns, in order, with their widths:

| Column | Width | Cell |
|---|---|---|
| Status | 77px | `Switch`, centred, filling the cell |
| Title | 300px | `TextLink`, clickable across the whole cell |
| Tags | 330px | `KeyValueTags` rail with `+N` overflow |
| Actions | 126px | `WorkflowActionIcons` — up to 2 marks, then `+N` |
| Owner | 192px | `Avatar` + linked name |
| Created | 192px | avatar + name over a date, date indented under the name |
| Updated | 192px | as Created |
| Executions | 105px | a number |

> [!warning] The Actions column is **not** integration logos
> It draws a tinted UI glyph per action kind — Attach Runbook, Update
> Priority — from the record's own action list, so the listing says what the
> details page says. The design's table drew Jira / AWS Lambda / DataDog
> logos here, and they were fixture decoration: identical on every row,
> because no action kind the details page attests has an integration.
> `IntegrationIcons` is the logo rail and is used on no product screen today.

Columns are **hideable and reorderable** through a customize control
(`CustomizeTable`), so design the header and the row to survive any subset.

**States to draw:** loading (header stays, body reserves a full page) · empty
("No workflows yet.") · error · a row mid-write (the Updated cell shows a
2-line skeleton while the server restamps it).

### 2.2 Workflow details — `/workflows/:id`

**An instance of the details template — `03` §2.1.** The two cards, the
breadcrumb-title-description-tags header, the metadata row and the pinned tab
bar are the template's and are the same on every feature's details page. What
follows is only what Workflows puts in the slots.

Two stacked cards on `bg.body` with a 16px gutter, page scrolls as one.

**Header card** (`DetailHeader`) — breadcrumb (`WorkFlows › title`), title at
`3xl` with a kebab menu at the trailing edge (**Edit**, **Delete**),
description clamped to 2 lines at a 75-character measure, a `KeyValueTags` rail
capped at half the card with an `UPDATE TAGS` button beside it. Below a divider,
five equal metadata columns: **Status** (a live switch) · **Actions** (the same
`WorkflowActionIcons` rail as the listing, capped at 2) · **Workflow type** ·
**Owner** · **# of Executions**.

**Body card** — `Tabs`: **Details** and **Logs**. The tab bar pins to the top of
the scroll area with the page's 16px gutter above it.

#### Details tab

A fixed **Index** column on the left and the record's sections on the right.

- **Index** — a `SearchBar` that really filters, then grouped links: *"Trigger
  and It's Properties"* (Trigger, then one entry per condition **row**, named as
  the row reads: "Priority is P1", "Alert Source is Firebase") and *"Actions"*
  (one entry per action card). Blue disc bullets. Entries are one line and clip.
  The column pins under the tab bar **and scrolls itself** when it is taller
  than the space it is pinned in. Clicking an entry scrolls the section into
  view and rings it.
- **Trigger section** (`bg.body` band) — a `PanelCard` reading
  "When **incident is Triggered**", then an `If` divider, then the condition
  items. **Each item is its own card**; a bracket is one card with several rows
  and `And` dividers inside it. Cards are joined by the list's own word.
- **Actions section** (a darker band, running to the bottom of the viewport) —
  one `ActionCard` per action, in order.

**An `ActionCard`** is a `PanelCard`: 24px mark, title (with a bracketed count
where the action has one — "Attach Runbook (2)"), then a body indented to the
title's left edge. Bodies vary by kind: bulleted links (runbooks, channels),
labelled lines ("Issue: Backend"), a priority badge, **rendered markdown** for
anything the editor wrote in markdown, or nothing at all for the two that
configure nothing.

#### Logs tab

`DataTable` + `TableFooter`, footer pinned to the bottom of the scrollport.

| Column | Cell |
|---|---|
| Execution Date & Time *(the reader's own timezone, named in the header)* | `2026-01-01 07:07:02 PM` |
| Log ID | the uuid, with a copy affordance — clicking anywhere on it copies, and the tooltip answers "Copied!" |
| Status | dot + **Successful** / **Failed** |
| Incident | "View Incident" link |
| Action Executions | a count |

### 2.3 Workflow editor — `/workflows/new`, `/workflows/:id/edit`

**An instance of the editor template — `03` §2.2.** Every edit flow is the same
two segments: the thing being edited on the left, Help and Guide fixed at 402px
on the right, and a pinned save bar. **Section 1 below is the template's** —
title, description, owner and tags are what every feature edits about a record.
Only sections 2 and 3, and the articles, are Workflows'.

A form column (max 932px) with a `HelpPanel` right-aligned beside it, and a
save bar pinned at the bottom (**SAVE AND CONTINUE** / **CANCEL**).

Three blocks — and only the last two are cards:

1. *The template's identity block.* Title, description, owner picker, tags
   (each tag row is a key, a value and a colour swatch), at 536px rather than
   the full column. **No heading and no card** — four fields at the top of the
   form.
2. **Set Workflow** *(Workflows only)* — **two titles, not one**: a field label
   reading *Set Workflow* above a collapsible card titled *Define Trigger and
   it's Filters*. Inside it, the trigger picker, then the filter list. A filter
   row is a bullet, the property name, then `Is`/`Is not`, then its value
   control — **two** controls for a Tag (key and value) **plus a colour
   swatch**. Rows are joined by an **And/Or select sitting on the divider
   between them**. `ADD FILTER` and `ADD GROUP` below; a group is a bordered box
   with its own inner join and its own `ADD FILTER`.
3. **Define Actions** *(Workflows only, drag-to-reorder included)* — each action
   is a strip ("Action 01" with a green tick, a drag handle, edit and delete
   buttons) above its `ActionCard`. **Actions reorder by dragging**: the others
   move aside to open a gap where the card will land. Below the list, a row of
   integration chips — *Incident Response, Slack, Jira, Microsoft Teams* — under
   "Select to add another action".

**Save is refused** until the workflow has a title and at least one complete
condition.

### 2.4 Action picker and forms

A right-hand `SlideOver`. Picking an integration lists its actions (name +
one-line description); picking an action opens its form in the same panel, with
`SAVE` / `CANCEL` pinned at the bottom.

Fifteen forms exist. The ones with real designs are **Attach Runbook(s)** (a
searchable, paginated table of runbooks with checkboxes), **Add Status Page
Issue** (page picker, title, state, component/impact rows, and a four-stage
timeline — *Investigating, Identified, Monitoring, Resolved* — as an
**accordion, one stage open at a time**, opening on Investigating; each stage
holds its own markdown editor and a button reading "ADD UPDATE" while the
stage is empty and "ADD ANOTHER UPDATE" after), and **Add Incident Note** (a
markdown editor).

The rest — SLO, webhook, HTTP call, email, Slack, Jira, Teams — are built from
`FormField` + `Input`/`Select`/`MarkdownEditor` and **have no design**. They are
the main thing worth designing next: see §4.

### 2.5 Overlays

- **Update Tags** — a side panel of key/value/colour rows.
- **Delete Workflow?** — a confirm dialog naming the workflow.
- **Discard Changes?** — asked when leaving a dirty form.
- **Toast** — top centre, on every status change: *"Workflow successfully
  running"* / *"Workflow successfully stopped"*, and a failure variant.

---

## 3. Behaviour worth designing for

- **Both modes.** Every screen, both. The screenshots that exist are dark.
- **The switch moves under the finger**, then the row catches up when the
  server answers — and moves back if it fails, with a toast saying so.
- **Nothing is drawn twice from two sources.** The Index and the section it
  jumps to are the same words; a table's mark and a card's mark are the same
  glyph.
- **Wide screens.** Section content caps at 900px and the header runs edge to
  edge; the editor's help panel stays right-aligned as the window grows.
- **Every list has three states** — loading, empty, error — and each says what
  happened rather than showing nothing.

---

## 4. What is missing, and would be the most useful thing to design

1. **One generic "configured action" card.** Of the seventeen action kinds,
   **three** are drawn in Figma (Attach Runbook, Update Priority, Add Status
   Page Issue) and **two** need no body at all (the ones that configure
   nothing). The remaining **twelve** follow a house pattern — labelled lines, a
   clamped excerpt, a bracketed count — that was inferred, not specified. A
   single node for *an action with a few labelled fields* settles all twelve.
2. **The long-text rule.** How much of a note or message a card shows, and
   whether there is a "show more".
3. **The remaining action forms** — SLO, webhook, HTTP call, email, and the
   Slack/Jira/Teams message forms.
4. **The light mode of the details and editor pages.** Both are only drawn dark.
5. **The Logs tab as designed** — the current one is built from a screenshot of
   the live product, not from a node.
