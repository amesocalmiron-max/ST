# GANTZ SimTracker HTML Template Task for GPT Codex v3

## 0. Purpose of this document

This document is an **implementation handoff** for GPT Codex for the HTML template of the new GANTZ SimTracker.

This is not a JSON-schema task.
This is not an updater-logic task.
This is not permission to redesign the state model for frontend convenience.

The goal is to implement a beautiful, modern, animated UI for the corrected three-domain character model:

- **Dating** tab = the main and richest tab
- **Combat** tab = compact and practical
- **World** tab = the smallest tab, used for minimal identity/bio grounding

This task concerns **HTML / CSS / JS / visual rendering only**.
The JSON preset is handled separately and must not be changed here.

---

## 1. Why this v3 rewrite exists

The previous HTML handoff was close, but several points still needed tightening:

- the `World` tab needed to reflect the latest minimal-bio requirement more explicitly;
- schema handshake with the JSON task needed to be clearer;
- optional World content needed trimming so it would not swell into a hidden meta-panel;
- top-level entity fields like `sex` needed to be accounted for in the `World` presentation.

This v3 version fixes those issues.

The corrected tracker UI is a **three-domain character panel**:
1. **Dating / romance / disposition**
2. **Combat**
3. **World / minimal bio**

The old right-side template remains useful only as a mechanical reference:
- right rail
- icon buttons
- slide drawer
- smooth transforms

Its old semantic model must not be restored.

---

## 2. What Codex must produce

Create one new HTML template file:

- `gantz-simtracker-template.html`

The file must:
- be compatible with the new JSON schema,
- use a right-side entity icon rail,
- render exactly one icon per entity,
- open a beautiful animated drawer on icon click,
- support exactly three main tabs: `Dating`, `Combat`, `World`,
- remain readable at low, medium, and high cast sizes.

Minimal inline JS is allowed if genuinely needed for:
- icon selection
- drawer switching
- tab switching
- section toggles

---

## 3. Non-negotiable UI constraints

### 3.1 UI must not redesign the schema

Do not:
- rename fields
- invent fallback fantasy fields
- smuggle new state fields into the renderer
- remap the new schema back into old dating-template semantics

### 3.2 One icon per entity

This is mandatory.

- one entity = one icon
- player also has an icon
- click icon -> open that entity card
- active icon and active card must stay synchronized

### 3.3 Exactly three main tabs only

Top-level tabs must be exactly:
- `Dating`
- `Combat`
- `World`

Do not reduce this back to two tabs.
Do not explode it into five or six tabs either.

### 3.4 Beautiful but controlled presentation

The user wants something close to tech-pron in presentation quality.
That means:
- premium dark sci-fi feel
- polished motion
- rich but controlled surface design

That does **not** mean:
- childish neon overload
- random RGB circus
- bloated gimmick UI
- unreadable glow soup

---

## 4. Schema handshake rules

### 4.1 Read the JSON task exactly

The HTML template must assume the JSON task’s three-domain entity model:
- `social`
- `combat`
- `world`

It must also honor top-level entity fields such as:
- `name`
- `sex`
- `ui_variant`
- `icon_key`
- `tracked_tier`
- `status`
- `presence`

### 4.2 Old dating concepts may be rendered if they exist in the new schema

The new JSON task allows romance/disposition concepts such as:
- affinity
- desire
- trust
- intimacy
- jealousy
- reaction icon
- goals
- belongings

Render them only if they exist in the new schema.

### 4.3 Do not render legacy-only fields that are not part of the new schema

Do not assume:
- `bg`
- `inactiveReason`
- old `stats.*` naming
- old section structure

---

## 5. Core UX concept

## 5.1 Main interaction model

**One tracked entity = one icon in the right-side rail.**

On icon click:
- the corresponding card opens
- the card animates into view
- the selected icon becomes active
- the main card tabs switch between `Dating`, `Combat`, and `World`

### 5.2 Desired feel

The interface should feel like:
- premium dark side-panel HUD
- elegant ST module
- techwear / sci-fi drawer
- beautiful and modern, not tacky

---

## 6. Global layout structure

### 6.1 Main container

Requirements:
- right-side placement
- icon rail and drawer visually linked
- layered composition
- no layout jitter
- card area should not wreck the chat layout

### 6.2 World HUD

A persistent world summary block must exist.
It should stay visible whenever the tracker panel is open.

### 6.3 Icon rail

There must be a vertical icon rail.
It must be scrollable if cast size grows.

### 6.4 Card drawer

The selected entity card must open as a sliding drawer/panel.

---

## 7. World HUD

### 7.1 Required displayed fields

Render:
- `mode`
- `submode`
- `story_date`
- `story_time`
- `location`
- `sublocation`
- `call_status`
- `countdown_min` when countdown is visible
- `mission_timer_min` when mission is active
- `threat_level`
- `public_heat`
- `scene_focus`

### 7.2 Design rules

The HUD must be:
- compact
- clearly separated from entity cards
- legible
- modern
- not too tall

### 7.3 Accent logic

Use controlled semantic accents:
- calm info -> muted cyan / teal
- social warmth -> violet / indigo accents
- pressure -> amber
- danger / dead / critical -> crimson
- neutral metadata -> graphite / steel / gray

---

## 8. Icon rail

### 8.1 Mandatory behavior

- one icon per entity
- player included
- right-side rail
- hover state required
- active state required
- dead/down/offscreen differences must be visible
- scroll when cast size grows

### 8.2 Sorting order

1. player
2. major onscreen NPCs
3. support onscreen NPCs
4. temporary onscreen NPCs
5. offscreen active NPCs
6. inactive NPCs
7. dead NPCs

### 8.3 Icon content

The icon may be:
- initials badge
- icon_key-based glyph
- stylized minimal avatar badge

Rules:
- must work without custom images
- player must be visually distinguished
- dead/down states must affect icon styling

---

## 9. Card drawer

### 9.1 Required behavior

When an icon is clicked:
- the matching card opens
- the previous one closes cleanly
- motion stays smooth
- icon/card sync is preserved

### 9.2 Style direction

The card should feel like:
- layered dark glass panel
- crisp premium sidebar card
- subtle blur, depth, and polish
- not like a spreadsheet or forum relic

### 9.3 Motion direction

Motion must be:
- smooth
- quick but controlled
- transform/opacity based
- no stupid bounce
- no carnival effects

---

## 10. Card structure

Each entity card must contain:

### 10.1 Header
- name
- icon/avatar badge
- tier badge
- status badge
- presence badge

### 10.2 Meta row
Use compact meta such as:
- role label
- bond label
- age
- group tag
- one small critical hint if needed

### 10.3 Main tabs
Exactly three:
- `Dating`
- `Combat`
- `World`

### 10.4 Optional collapsible sections inside tabs
Allowed where useful:
- `Links`
- `Goals`
- `Belongings`
- `Thought`

Do not create a second layer of giant tab systems.
Keep collapsibles tasteful and compact.

---

## 11. `Dating` tab

This is the richest tab and the visual priority tab for most scenes.

### 11.1 Player presentation

For player, render:
- `stress`
- `loneliness`
- `openness`
- `arousal`
- `bond_need`
- `mask`
- `reaction_icon`
- `notable_links`

### 11.2 NPC presentation

For NPCs, render:
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
- `mask`
- `reaction_icon`
- `bond_label`
- `internal_thought`
- `notable_links`

### 11.3 Visual treatment

Dating tab should feel the most polished and information-rich.
Use:
- elegant meters
- readable labels
- chips/pills for bond labels and states
- tasteful reaction icon display
- clean relation rows for `notable_links`

Do not make it look like a hospital dashboard.
This is the main character chemistry tab.

---

## 12. `Combat` tab

Combat is intentionally smaller.
It should be fast to read.

### 12.1 Render

- `health`
- `injuries`
- `weapon`
- `combat_readiness`
- `panic`
- `aggression`
- `mental_state`
- `downed`

### 12.2 Visual treatment

- strong but compact danger signaling
- injuries as chips / compact alert blocks
- readable weapon / mental state labels
- health emphasis without giant arcade bars

Do not overbuild this tab.
It is not the main dramatic layer.

---

## 13. `World` tab

This is the smallest tab.
It is for quick identity grounding and minimal bio.

### 13.1 Required rendering

Render:
- `sex`
- `role_label`
- `age`
- `bio_short`
- `appearance_short`

### 13.2 Optional rendering

If present, render compactly:
- `occupation`
- `group`
- `availability`
- `last_seen_location`
- `active_goals`
- `belongings`

### 13.3 Visual treatment

World tab should feel minimal, elegant, and grounded.
Think:
- concise profile panel
- compact bio capsule
- not a lore encyclopedia

Do not swell this tab into a hidden gantz-meta dashboard.

---

## 14. Default opening logic

The template must respect `scene_focus`.

### If `scene_focus = dating`
Open `Dating` by default.

### If `scene_focus = combat`
Open `Combat` by default.

### If `scene_focus = world`
Open `World` by default.

### If `scene_focus = mixed`
Use these priorities:
- if `downed = true` or severe/critical injury exists -> `Combat`
- else if strong relationship/drama pressure is present -> `Dating`
- else -> `World`

---

## 15. Player card special treatment

Player card must feel privileged without breaking consistency.

Requirements:
- player always first in rail
- slightly stronger visual emphasis
- clear identity marker
- same schema-driven renderer, not a separate bespoke UI species

---

## 16. Compact mode

The template must support compact rendering for entities with:
- `tracked_tier = temporary`
- or `presence = offscreen`
- or `status = inactive`
- or `status = dead`

### Compact mode should still show
- icon
- name
- status/presence
- one or two important cues

Do not hide them completely.
Dead/offscreen memory must remain visible.

---

## 17. Visual state mapping

### By `status`
- `active` -> full readable state
- `inactive` -> dimmed but readable
- `missing` -> muted / uncertain
- `down` -> danger emphasis
- `dead` -> grayscale / memorial / low-saturation
- `revived` -> special but controlled accent

### By `presence`
- `onscreen` -> full emphasis
- `offscreen` -> reduced emphasis
- `room` -> room-accent
- `mission` -> combat-accent
- `location_unknown` -> obscured / uncertain

---

## 18. Animation contract

The user explicitly wants the UI to feel beautiful and modern.
So yes, give it motion. Just don’t make it idiotic.

### 18.1 Required animations

- icon hover animation
- icon active-state transition
- card slide-in / slide-out
- tab switch animation
- collapsible section expand/collapse animation
- subtle opacity / transform transitions

### 18.2 Style rules

Animations must be:
- smooth
- premium-feeling
- slightly futuristic
- restrained
- readable

### 18.3 Forbidden motion behavior

- bounce spam
- excessive glow spam
- jittery panels
- childish cyberpunk carnival nonsense
- motion that harms legibility

---

## 19. Visual design direction

### 19.1 Theme

Recommended direction:
- obsidian / graphite base
- muted steel surfaces
- controlled glassmorphism
- subtle blur
- sharp but elegant edges

### 19.2 Mood target

The interface should feel like:
- premium dark HUD
- stylish ST sidebar module
- modern anime/sci-fi UI with taste
- tech-pron, but for adults with eyesight

### 19.3 Typography

- readable primary text
- clean labels
- clear hierarchy
- no decorative nonsense fonts

---

## 20. Data mapping rules

The template must read the new schema exactly as defined in the JSON task.

### Do not
- invent fallback fields
- remap new data into old `stats.*` naming
- assume the old dating template’s structure
- ignore the three-layer model

### Do
- render `playerState` separately from `characters`
- gracefully hide missing optional fields
- preserve state/display separation
- read `sex` from top-level entity fields when rendering the `World` tab

---

## 21. Acceptance criteria

The task is complete only if:

1. exactly one icon is rendered per tracked entity
2. clicking an icon opens the matching card
3. player card exists and is visually privileged
4. there are exactly three main tabs: `Dating`, `Combat`, `World`
5. `Dating` is the richest tab
6. `Combat` is compact
7. `World` is the smallest, minimal-bio tab
8. dead/offscreen/inactive entities remain visible
9. compact mode works
10. animations look modern and smooth without becoming clownish
11. the template reads the agreed schema without redefining it

---

## 22. Final instruction to Codex

Implement the HTML template exactly as specified.

When a detail is underspecified:
- preserve schema compatibility
- preserve right-side icon rail UX
- preserve one-icon-per-entity interaction
- preserve the three-tab model
- preserve elegant readability
- preserve minimal `World` scope
- preserve strict separation of state and display
- prefer the most conservative beautiful implementation

Do not redesign the data model.
Do not collapse the UI back into only two tabs.
Do not turn the interface into a gimmick showcase.
Make it beautiful, modern, animated, and controlled.
