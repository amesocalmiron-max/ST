# GANTZ SimTracker Codex HTML Task v5

## Scope
Use only these files as source of truth:
- `GANTZ_SIMTRACKER_HANDOFF_FOR_NEW_CHAT.md`
- `gantz-simtracker-preset_v4.json`
- `GANTZ_SIMTRACKER_CODEX_JSON_TASK_v5.md`

Goal: implement `gantz-simtracker-template.html` for the canonical schema.

---

## 1) Deliverable (exact)
Create exactly one HTML file:
- `gantz-simtracker-template.html`

Template must:
- be compatible with canonical root model (`worldData`, `playerState`, `characters`)
- render one icon per tracked entity (including player)
- use right-side icon rail
- open/switch active entity card on icon click
- provide exactly three top-level tabs: `Dating`, `Combat`, `World`

---

## 2) Non-negotiable schema handshake

### One shared schema for player and NPCs
Renderer must assume the same entity structure for:
- `playerState`
- `characters[]`

No player/NPC schema split.
No separate social field map for player vs NPC.

### No top-level relations block
Do not expect `relations` at entity top level.
Relationship data source is only:
- `social.notable_links`

### Three domain blocks only
Render and bind to:
- `social`
- `combat`
- `world`

Do not assume `gantz`, `story`, or other extra domain blocks.

---

## 3) Layout and interaction model

### Right rail
- Vertical rail on the right
- Exactly one icon button per entity
- Active icon state clearly visible
- Hover/active transitions smooth and restrained

### Card behavior
- Clicking icon activates that entity card
- One active card at a time
- Active icon and active card always synchronized
- Smooth panel transition; no chaotic layout jumps

### Responsive behavior
- Must remain usable with low/medium/high cast sizes
- Compact mode allowed for small viewports
- Compact mode must keep access to all 3 tabs

---

## 4) Card structure
Recommended structure:
1. identity header
2. status/presence strip
3. tab row (`Dating`, `Combat`, `World`)
4. tab content

Header should expose:
- `name`
- `sex`
- `tracked_tier`
- `status`
- `presence`
- visual variant cues from `ui_variant`
- stable avatar/icon from `icon_key`

---

## 5) Tab requirements

### `Dating` tab (richest)
Data source: shared `social`.
Must cover:
- `affinity`, `desire`, `trust`, `comfort`, `intimacy`, `jealousy`, `tension`, `attachment`, `openness`, `dependency`, `loneliness`, `bond_need`, `arousal`
- `mask`
- `reaction_icon`
- `notable_links`
- optional `internal_thought`, `social_flags`

Presentation:
- this is the densest/primary tab
- use readable meter groups/chips
- relationship list must stay compact and scannable

### `Combat` tab (compact)
Data source: `combat` only.
Must cover:
- `health`
- `injuries`
- `weapon`
- `combat_readiness`
- `panic`
- `aggression`
- `mental_state`
- `downed`
- optional `last_combat_note`

Injuries are structured objects; show `label`, `severity`, `treated`.

### `World` tab (smallest)
Data source: top-level `sex` + `world`.
Must cover:
- `role_label`
- `age`
- `bio_short`
- `appearance_short`
- optional `occupation`, `group`, `availability`, `last_seen_location`, `active_goals`, `belongings`

Keep compact and identity/everyday oriented.

---

## 6) Visual style requirements
Target style:
- dark modern sci-fi
- subtle glassy surfaces
- restrained premium motion
- high readability

Avoid:
- clown RGB
- glow overload
- noisy gimmick panels

Animation:
- short, smooth, informative
- no bouncy/flicker-heavy effects

---

## 7) Renderer behavior rules
Allowed:
- graceful handling of missing optional fields
- compacting low-information cards
- collapsing optional sections

Forbidden:
- mutating schema
- inventing persistent data fields
- requiring legacy fields not in canonical schema
- reintroducing old `stats.*` assumptions

State vs display:
- UI state (selected tab, open card, transitions) remains renderer-local
- narrative state remains canonical JSON only

---

## 8) Acceptance criteria
Complete only if all are true:
1. One icon per entity in right rail (player included)
2. Exactly 3 top-level tabs: `Dating`, `Combat`, `World`
3. `Dating` is visually richest
4. Shared social schema for player and NPCs (no split)
5. No top-level `relations` assumptions
6. Renderer reads only canonical domains (`social`, `combat`, `world`)
7. UI remains dark/glassy/modern and readable
8. No schema drift or legacy semantic fallback

---

## 9) Explicit contradictions resolved in v5
1. Replaced old 2-tab model with fixed 3-tab model (`Dating`, `Combat`, `World`).
2. Removed player-vs-NPC schema divergence in renderer assumptions.
3. Removed any expectation of top-level `relations`; use `social.notable_links` only.
4. Preserved social-first emphasis while keeping combat compact and world minimal.
5. Enforced strict separation between canonical narrative state and UI-only behavior.
