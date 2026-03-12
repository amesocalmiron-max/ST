# GANTZ SimTracker HTML Template Task for GPT Codex v5

## 0. Purpose

This document is an implementation handoff for GPT Codex for the HTML template of the GANTZ SimTracker.

This is not a JSON-schema task.
This is not an updater-logic task.
This is not permission to redesign the state model for frontend convenience.

Your job is to implement a beautiful, modern, controlled UI for the canonical three-domain entity model:

- `Dating` = main and richest tab
- `Combat` = compact and practical tab
- `World` = smallest identity/bio tab

The tracker UI must present authoritative state clearly.
It must not invent state.

---

## 1. Required source of truth

Before editing, read these files in the workspace and use them as the only relevant references:

- `GANTZ_SIMTRACKER_HANDOFF_FOR_NEW_CHAT.md`
- `gantz-simtracker-preset_v4.json`
- `GANTZ_SIMTRACKER_CODEX_JSON_TASK_v5.md`

If you see ambiguity between older notes and this file, follow this v5 HTML task.
This v5 task supersedes earlier contradictory HTML notes.

---

## 2. Deliverable

Create exactly one HTML template file:

- `gantz-simtracker-template.html`

The file must:
- be compatible with the canonical JSON schema,
- use a right-side entity icon rail,
- render exactly one icon per entity,
- open a polished animated drawer or panel on icon click,
- support exactly three main tabs: `Dating`, `Combat`, `World`,
- remain readable at low, medium, and high cast sizes,
- avoid schema inventions and renderer-side fantasy fields.

Minimal inline JS is allowed only if genuinely needed for:
- icon selection,
- drawer switching,
- tab switching,
- compact section toggles,
- responsive card state.

---

## 3. Non-negotiable UI constraints

### 3.1 UI must not redesign the schema

Do not:
- rename fields,
- invent new narrative fields for rendering convenience,
- create fallback fantasy fields,
- remap the schema back into legacy `stats.*` semantics,
- reintroduce `bg`, `inactiveReason`, or other old display-era shortcuts as if they were canonical state.

### 3.2 One icon per entity

This is mandatory.

Rules:
- one entity = one icon
- player also has an icon
- click icon -> open that entity card
- active icon and active card must stay synchronized
- do not collapse multiple characters under one icon

### 3.3 Exactly three main tabs only

Top-level tabs must be exactly:
- `Dating`
- `Combat`
- `World`

Do not reduce this back to two tabs.
Do not expand it into five or six tabs.

### 3.4 Beautiful but controlled presentation

Target feel:
- premium dark sci-fi,
- clean glassy surface design,
- polished motion,
- strong readability,
- restrained techwear / terminal-luxury vibe.

Do not create:
- clown RGB circus,
- unreadable glow soup,
- toy dashboard aesthetics,
- bloated gimmick panels.

---

## 4. Schema handshake rules

### 4.1 Read the JSON task exactly

The HTML template must assume the canonical JSON entity model:
- `social`
- `combat`
- `world`

Do not infer a fourth domain.
Do not expect a top-level `relations` block.
Relationship data exists only through `social.notable_links`.

### 4.2 Shared social schema only

This is the most important correction.

The renderer must assume one shared `social` schema for both:
- `playerState`
- `characters[]`

Do not implement separate field assumptions for player and NPC.
Do not treat player as a different schema species.

Different visual emphasis is allowed.
Different underlying schema is forbidden.

### 4.3 State/display separation

The renderer may:
- hide empty optional sections,
- condense low-information cards,
- reorder visual emphasis,
- use icons / chips / bars / pills,
- use compact summaries for inactive or offscreen characters.

The renderer may not:
- mutate schema,
- create derived persistent state,
- require fields that do not exist in the canonical JSON,
- assume old legacy fields from the disposition template.

---

## 5. Overall layout model

Preserve this high-level interaction model:

### 5.1 Right-side rail

Use a vertical rail on the right side.
It must contain:
- exactly one icon button per entity,
- a visible active-state marker,
- compact but readable labels or tooltips,
- smooth hover and active feedback.

### 5.2 Drawer / panel behavior

Clicking an icon must open the corresponding character panel.
The panel may slide in, expand, or switch state, but it must feel modern and stable.

Required behavior:
- one active card at a time,
- active rail icon synchronized with active card,
- stable animation,
- no chaotic layout jumps,
- no full-screen takeover unless viewport is extremely narrow.

### 5.3 Responsive behavior

It must remain usable for:
- low cast size,
- medium cast size,
- high cast size.

On narrower widths, compact mode is allowed.
Compact mode may reduce visual density, but must not remove domain access.

---

## 6. Card architecture

Each entity card must have a clear structure.

Recommended high-level card layout:
1. compact identity header
2. top-level status strip
3. tab row
4. tab content area
5. optional micro-footer for minimal meta summary

### 6.1 Header should expose

Use top-level entity fields as the identity header basis:
- `name`
- `sex`
- `tracked_tier`
- `status`
- `presence`
- optional visual identity from `ui_variant`
- stable icon/avatar keyed by `icon_key`

Do not invent biography paragraphs in the header.
Keep it crisp.

### 6.2 Status strip

Expose state in a compact but readable way.
Examples of appropriate header-adjacent chips:
- `active / inactive / dead`
- `onscreen / offscreen / mission`
- `major / support / temporary`

Use tasteful chip or pill styling.
Avoid clutter.

---

## 7. `Dating` tab requirements

This is the primary tab.
It should feel like the main personality / relationship surface.

### 7.1 Data source

Render from the shared `social` schema only.

Required readable coverage:
- `affinity`
- `desire`
- `trust`
- `comfort`
- `intimacy`
- `jealousy`
- `tension`
- `attachment`
- `openness`
- `dependency`
- `loneliness`
- `bond_need`
- `arousal`
- `mask`
- `reaction_icon`
- `notable_links`

Optional:
- `internal_thought`
- `social_flags`

### 7.2 Visual presentation

Recommended approach:
- hero cluster for the most important social meters,
- secondary meter grid for the rest,
- clear textual surface for `mask` and `reaction_icon`,
- compact relationship list for `notable_links`,
- optional thought / hidden-state block if `internal_thought` exists.

### 7.3 Relationship list rules

For `social.notable_links`:
- show target identity clearly,
- show bond label clearly,
- show a small subset of key relational numbers,
- do not turn the relationship list into an unreadable wall of numbers,
- keep it compact and scannable.

A good default is:
- target name/id,
- bond,
- 2-4 important metrics,
- optional expand for deeper view.

### 7.4 Player vs NPC visual emphasis

Allowed:
- player card may visually emphasize self-state first,
- NPC cards may visually emphasize relation-facing cues first,
- order and density may differ.

Forbidden:
- assuming different underlying fields,
- separate player-only schema rendering branch,
- NPC-only schema expectations.

---

## 8. `Combat` tab requirements

This tab must be compact, practical, and lower-noise than `Dating`.

### 8.1 Data source

Render from `combat` only.

Required readable coverage:
- `health`
- `injuries`
- `weapon`
- `combat_readiness`
- `panic`
- `aggression`
- `mental_state`
- `downed`

Optional:
- `last_combat_note`

### 8.2 Visual presentation

Recommended approach:
- health as the most visually legible metric,
- readiness / panic / aggression as compact meters or chips,
- weapon and mental state as labeled rows,
- injuries as structured list entries,
- downed state must be unmistakable.

### 8.3 Injury rendering

Each injury is a structured object.
Show:
- `label`
- `severity`
- `treated`

Do not assume injuries are plain strings.
Do not flatten away severity/treatment.

---

## 9. `World` tab requirements

This is the smallest tab.
It is for identity and everyday grounding, not for hidden mission-meta sprawl.

### 9.1 Data source

Render from:
- top-level `sex`
- `world`

Required readable coverage:
- `role_label`
- `age`
- `bio_short`
- `appearance_short`

Optional:
- `occupation`
- `group`
- `availability`
- `last_seen_location`
- `active_goals`
- `belongings`

### 9.2 Visual presentation

Keep this tab clean and compact.
Recommended structure:
- identity summary block,
- short bio / appearance block,
- everyday metadata list,
- optional mini-lists for goals and belongings.

### 9.3 Forbidden drift

Do not let `World` become:
- a hidden mission dashboard,
- a global lore encyclopedia,
- a storage dump for random metadata,
- a substitute for `worldData`.

---

## 10. Visual style requirements

### 10.1 Core aesthetic

The UI should feel:
- dark,
- modern,
- slightly glassy,
- premium,
- subtle sci-fi,
- motion-polished,
- highly readable.

### 10.2 Surface language

Use:
- restrained depth,
- frosted panels,
- crisp spacing,
- subtle active glows,
- soft gradients,
- thin separators,
- tasteful shadows.

Avoid:
- excessive blur,
- cartoon neon,
- pointless decorative clutter,
- noisy gradients that kill readability.

### 10.3 Animation rules

Animations should be:
- smooth,
- short,
- stable,
- informative.

Do not use:
- chaotic bouncing,
- flashy flicker,
- laggy elastic movement,
- pointless animated noise.

---

## 11. Compact-mode rules

The template must degrade gracefully for larger casts or smaller viewports.

Compact mode may:
- reduce rail label density,
- collapse secondary sections,
- switch some meter clusters to denser rows,
- hide optional sections until expanded,
- reduce vertical spacing.

Compact mode must not:
- remove one of the three tabs,
- remove player access,
- hide status/presence,
- require different schema data,
- break rail-card synchronization.

---

## 12. Implementation rules

### 12.1 Allowed technologies

Inside the HTML template, you may use:
- HTML
- CSS
- minimal JS
- the runtime templating primitives already expected by SimTracker

### 12.2 Avoid overengineering

Do not build:
- framework-scale code,
- giant state machines,
- brittle layout hacks,
- renderer logic that duplicates the updater logic.

### 12.3 Defensive rendering

The template should handle:
- missing optional fields,
- empty arrays,
- low-information temporary characters,
- offscreen entities,
- dead entities,
- high cast counts.

Do this through graceful rendering, not schema mutation.

---

## 13. Legacy reference policy

The old right-side template is useful only as a mechanical reference for:
- right rail,
- icon-button logic,
- slide drawer model,
- general interaction feel.

Its old semantic field model is not canonical.
Do not reintroduce:
- `stats.*` assumptions,
- legacy `connections` rendering,
- legacy `bg` color state,
- legacy narrative inactivity hacks as if they were current schema.

If you borrow mechanics, rebind them cleanly to the new canonical schema.

---

## 14. Acceptance criteria

This task is complete only if all statements below are true:

1. The template reads the canonical three-domain model
2. It assumes one shared `social` schema for player and NPCs
3. It does not assume a top-level `relations` block
4. It renders exactly one icon per entity
5. It uses a right-side rail
6. It provides exactly three top-level tabs: `Dating`, `Combat`, `World`
7. `Dating` is visually the richest tab
8. `Combat` is compact and practical
9. `World` remains minimal and identity-oriented
10. Player can be visually privileged without becoming a separate schema species
11. The UI stays dark / glassy / premium / controlled
12. The renderer does not reintroduce legacy schema assumptions

---

## 15. Final instruction

Implement the HTML template exactly as specified.

When a detail is underspecified:
- preserve schema compatibility,
- preserve shared social rendering,
- preserve three-tab structure,
- preserve state/display separation,
- preserve readability,
- prefer the most conservative modern UI solution consistent with this document.

Do not redesign the data model.
Do not split player and NPC schemas.
Do not smuggle renderer convenience fields into the canonical state.
Do not rebuild the old disposition template under a cooler coat of paint.
