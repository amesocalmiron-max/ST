# GANTZ SimTracker Codex JSON Task v5

## Scope
Use only these files as source of truth:
- `GANTZ_SIMTRACKER_HANDOFF_FOR_NEW_CHAT.md`
- `gantz-simtracker-preset_v4.json`

Goal: produce one final preset file `gantz-simtracker-preset_v5.json` for SimTracker.

---

## 1) Deliverable (exact)
Create exactly one JSON preset file:
- `gantz-simtracker-preset_v5.json`

It must be a real SimTracker preset wrapper (not a schema note), preserving strict `disp` output behavior.

---

## 2) Non-negotiable architecture

### Root model (must remain exact)
```json
{
  "worldData": {},
  "playerState": {},
  "characters": []
}
```

Root key order is fixed:
1. `worldData`
2. `playerState`
3. `characters`

Do not:
- remove `playerState`
- move player into `characters`
- split root model by mode
- create player/NPC schema split

### Per-entity domain model (exactly 3)
Every tracked entity (`playerState` and each item in `characters`) must contain:
- `social` (richest)
- `combat` (compact)
- `world` (smallest)

Do not add a fourth domain block.
Do not introduce a top-level `relations` block.
Relationship data lives in `social.notable_links` only.

---

## 3) Wrapper compatibility requirements
Preset wrapper must include:
- `templateName`
- `templateAuthor`
- `templatePosition`
- `tabsType`
- `htmlTemplate`
- `customFields`
- `extSettings`
- `trackerDesc`
- `sysPrompt`

HTML handshake:
- keep `htmlTemplate` empty
- set `extSettings.templateFile` to `gantz-simtracker-template.html`

`disp` contract in `sysPrompt` must enforce:
- exactly one final `disp` code block
- strict JSON inside
- no extra text after block

---

## 4) Entity top-level shape (shared for player and NPCs)
Use this top-level order:
1. `id`
2. `name`
3. `sex`
4. `ui_variant`
5. `icon_key`
6. `tracked_tier`
7. `status`
8. `presence`
9. `social`
10. `combat`
11. `world`

Enums:
- `sex`: `male | female | unknown`
- `ui_variant`: `male | female | neutral | custom`
- `tracked_tier`: `primary | major | support | temporary`
- `status`: `active | inactive | missing | down | dead | revived`
- `presence`: `onscreen | offscreen | room | mission | location_unknown`

Player invariants:
- `playerState.id = "player"`
- `playerState.tracked_tier = "primary"`
- player exists exactly once

---

## 5) `worldData` block (global scene state)
Required keys:
- `mode`
- `submode`
- `story_date`
- `story_time`
- `location`
- `sublocation`
- `active_cast`
- `call_status`
- `countdown_visible`
- `countdown_min`
- `mission_active`
- `mission_id`
- `mission_target`
- `mission_timer_min`
- `threat_level`
- `public_heat`
- `last_mission_result`
- `scene_focus`

Enums:
- `mode`: `daily | room | mission | aftermath`
- `call_status`: `silent | foreshadowed | called | in_room | deployed | returning`
- `threat_level`: `low | medium | high | extreme`
- `public_heat`: `low | medium | high | critical`
- `last_mission_result`: `none | survived | injured | failed | partial_success | wiped`
- `scene_focus`: `dating | combat | world | mixed`

Rules:
- use `null` for absent countdown/timer values
- if mission inactive: `mission_id = null`, `mission_target = null`

---

## 6) Shared `social` schema (single schema for all entities)
This is the richest domain layer.

Required numeric meters (0..100):
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

Required structured fields:
- `mask`
- `reaction_icon`
- `notable_links`

Optional:
- `internal_thought`
- `social_flags`

`mask` enum:
`closed | guarded | neutral | open | friendly | performative | cold | detached | composed | shaken | cracked`

`reaction_icon` enum:
`none | pleased | neutral | flustered | jealous | wary | cold | hostile`

`notable_links[].bond` enum:
`stranger | acquaintance | classmate | teammate | uneasy_ally | ally | trusted | protective | dependent | romantic_tension | crush | intimate | obsessed | rival | hostile | fractured`

`notable_links` entry shape:
```json
{
  "target_id": "kei_kurono",
  "bond": "acquaintance",
  "affinity": 42,
  "desire": 18,
  "trust": 31,
  "comfort": 22,
  "intimacy": 9,
  "jealousy": 0,
  "tension": 35,
  "attachment": 16
}
```

Limit links to 1..5 meaningful entries.

---

## 7) Shared `combat` schema (compact)
Required:
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

Enums:
- `weapon`: `none | x_gun | x_shotgun | y_gun | gantz_sword | improvised | unknown`
- `mental_state`: `stable | stressed | shaken | panicked | dissociated | feral | broken`

`injuries` entry shape:
```json
{
  "key": "rib_fracture",
  "label": "трещина ребра",
  "severity": "moderate",
  "treated": false
}
```

`severity` enum: `minor | moderate | severe | critical`

No ammo economy, coordinates, or tactical simulation layers.

---

## 8) Shared `world` schema (minimal bio/everyday)
Required:
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

`availability` enum:
`available | busy | missing | isolated | hospitalized | dead`

Limits:
- `age`: integer, short string, or null
- `bio_short`: 1..3 short sentences
- `active_goals`: max 3
- `belongings`: max 5

---

## 9) Updater discipline (must be in `sysPrompt`)
- Treat latest valid `disp` as authoritative state
- Preserve values unless scene justifies change
- Update only affected fields
- Never invent keys
- Never silently remove relevant entities
- Keep dead entities (do not delete)
- If dead: `status = dead` and `combat.health = 0`

Validation checklist before output:
- root keys/order valid
- player exists exactly once
- no duplicate IDs
- enums valid
- numeric ranges clamped
- injuries are structured objects
- goals/belongings limits respected

Fallback policy:
- attempt safe repair on invalid new state
- if repair unsafe, return previous valid state unchanged

---

## 10) Explicit contradictions resolved in v5
1. **No player/NPC schema split**: one shared entity schema and one shared social schema.
2. **No top-level relations block**: relationship semantics are only `social.notable_links`.
3. **Social-heavy restored**: social is richest; combat/world are intentionally smaller.
4. **No legacy blind copy**: preserve useful legacy ideas, but not old field layout.
5. **State/UI separation**: no UI-only fields in canonical narrative state.
