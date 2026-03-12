# GANTZ SimTracker JSON Preset Task for GPT Codex v5

## 0. Purpose

This document is an implementation handoff for GPT Codex.

This is not a brainstorming document.
This is not permission to redesign the schema.
This is not an HTML task.

Your job is to create the canonical JSON preset for the GANTZ SimTracker build.
The preset must preserve a strict, validator-friendly, continuity-first state model.

The tracker is not decorative UI.
It is an authoritative state-layer / continuity-layer for the GANTZ GM build.

---

## 1. Required source of truth

Before editing, read these existing files in the workspace and use them as the only relevant references:

- `GANTZ_SIMTRACKER_HANDOFF_FOR_NEW_CHAT.md`
- `gantz-simtracker-preset_v4.json`

Interpret them conservatively.
If they contain ambiguity, follow this v5 task document.
This v5 task supersedes earlier contradictory notes.

---

## 2. Deliverable

Create exactly one new preset file:

- `gantz-simtracker-preset_v5.json`

The result must:
- keep working preset-wrapper structure,
- remain compatible with strict `disp` JSON output,
- preserve one canonical entity shape,
- preserve one shared social schema for player and NPCs,
- preserve compact combat,
- preserve minimal world/bio,
- preserve conservative updater behavior.

Do not create multiple alternatives.
Do not create exploratory drafts.
Produce one final canonical preset.

---

## 3. Non-negotiable wrapper compatibility

The output must be a real preset JSON, not a schema-only note.
It must keep the usual wrapper-level preset structure used by working SimTracker presets.

### 3.1 Required wrapper-level keys

Include wrapper-level keys of this family:

- `templateName`
- `templateAuthor`
- `templatePosition`
- `tabsType`
- `htmlTemplate`
- `customFields`
- `extSettings`
- `trackerDesc`
- `sysPrompt`

Keep the wrapper practical and close to the working `v4` style.
Do not strip it down into abstract architecture JSON.

### 3.2 External HTML handshake

This JSON task must not implement HTML.
However, it must correctly handshake with the external template.

Use this policy:
- keep `htmlTemplate` empty unless runtime embedding is explicitly required,
- keep `extSettings.templateFile = "gantz-simtracker-template.html"`,
- do not fabricate placeholder HTML,
- do not move visual rules into narrative state.

### 3.3 Strict `disp` compatibility

The preset instructions must enforce:
- exactly one final `disp` code block,
- strict JSON inside the block,
- no comments,
- no trailing commas,
- no markdown inside the JSON,
- no text after the final tracker block.

---

## 4. Canonical architecture

### 4.1 Root model must remain exact

The root tracker state must remain exactly:

```json
{
  "worldData": {},
  "playerState": {},
  "characters": []
}
```

Do not:
- remove `playerState`,
- move player into `characters`,
- split the root by mode,
- create sex-specific root schemas,
- create separate combat/social root branches.

### 4.2 Stable root key order

Root key order must remain:
1. `worldData`
2. `playerState`
3. `characters`

### 4.3 Per-entity domain model must be exactly three blocks

Every tracked entity must use exactly these three domain blocks:
- `social`
- `combat`
- `world`

No fourth domain block.
No top-level `relations` block.
No `gantz` block.
No `story` block.

Relationship data must live only in `social.notable_links`.
Do not duplicate relationship semantics in a separate container.

### 4.4 State and display must remain separate

Do not encode the following as narrative state:
- card colors,
- animation state,
- selected tab,
- open/closed drawer state,
- layout choices,
- glass/neon theme tokens,
- per-card UI visibility toggles.

UI belongs to the HTML task.

---

## 5. Canonical entity shape

For `playerState` and every item in `characters[]`, use exactly this top-level order:

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

### 5.1 Top-level enums

#### `sex`
- `male`
- `female`
- `unknown`

#### `ui_variant`
- `male`
- `female`
- `neutral`
- `custom`

#### `tracked_tier`
- `primary`
- `major`
- `support`
- `temporary`

Rules:
- `primary` is reserved for the player.
- NPCs may use only `major`, `support`, or `temporary`.

#### `status`
- `active`
- `inactive`
- `missing`
- `down`
- `dead`
- `revived`

#### `presence`
- `onscreen`
- `offscreen`
- `room`
- `mission`
- `location_unknown`

### 5.2 Naming policy

- `id` must be stable canonical snake_case English.
- `icon_key` must be stable canonical snake_case English.
- JSON keys must remain English.
- Enum values must remain English.
- Short human-facing descriptive text may be Russian.

---

## 6. `worldData` block

`worldData` stores current scene/world state.
It is not a substitute for per-character `world`.

### 6.1 Required fields

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

### 6.2 Enums

#### `mode`
- `daily`
- `room`
- `mission`
- `aftermath`

#### `call_status`
- `silent`
- `foreshadowed`
- `called`
- `in_room`
- `deployed`
- `returning`

#### `threat_level`
- `low`
- `medium`
- `high`
- `extreme`

#### `public_heat`
- `low`
- `medium`
- `high`
- `critical`

#### `last_mission_result`
- `none`
- `survived`
- `injured`
- `failed`
- `partial_success`
- `wiped`

#### `scene_focus`
- `dating`
- `combat`
- `world`
- `mixed`

### 6.3 Rules

- `story_date` and `story_time` are in-world chronology fields.
- `active_cast` includes only directly present or immediately influential entities.
- `submode` is a controlled open snake_case string.
- If no countdown exists, use `null`, not fake zeros.
- If no mission is active, use `null` for `mission_id` and `mission_target`.
- Keep `worldData` global and scene-focused.
- Do not bloat `worldData` with encyclopedic lore.

---

## 7. Player invariants

`playerState` must always exist.

Required invariants:
- `id` is always `player`
- `tracked_tier` is always `primary`
- player exists exactly once
- player must never be duplicated inside `characters[]`

Player is not a separate schema species.
Player uses the same canonical top-level entity shape and the same shared domain schemas.

---

## 8. Shared `social` block

This is the richest domain layer.
It must be identical in schema for player and NPCs.

Do not define one schema for `playerState.social` and another for `characters[].social`.
That contradiction is forbidden in v5.

### 8.1 Required numeric meters (all 0..100)

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

### 8.2 Required categorical / structured fields

- `mask`
- `reaction_icon`
- `notable_links`

### 8.3 Optional fields

- `internal_thought`
- `social_flags`

### 8.4 `mask` enum

- `closed`
- `guarded`
- `neutral`
- `open`
- `friendly`
- `performative`
- `cold`
- `detached`
- `composed`
- `shaken`
- `cracked`

### 8.5 `reaction_icon` enum

- `none`
- `pleased`
- `neutral`
- `flustered`
- `jealous`
- `wary`
- `cold`
- `hostile`

### 8.6 Bond vocabulary for `social.notable_links[].bond`

- `stranger`
- `acquaintance`
- `classmate`
- `teammate`
- `uneasy_ally`
- `ally`
- `trusted`
- `protective`
- `dependent`
- `romantic_tension`
- `crush`
- `intimate`
- `obsessed`
- `rival`
- `hostile`
- `fractured`

### 8.7 `social.notable_links` entry shape

Use one stable shape only:

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

### 8.8 Rules

- keep only 1-5 meaningful links,
- do not create links for disposable extras,
- do not invent a second relationship structure elsewhere,
- if a field is less relevant in a given moment, keep the field and use stable low/neutral values rather than deleting it,
- do not reintroduce `stress`, `devotion`, `interest`, or other special-case social fields as schema branches,
- do not add top-level `bond_label` outside `notable_links`.

---

## 9. Shared `combat` block

Combat is intentionally smaller than social.
It exists for practical continuity, not for simulation fetishism.

### 9.1 Required fields

- `health`
- `injuries`
- `weapon`
- `combat_readiness`
- `panic`
- `aggression`
- `mental_state`
- `downed`

### 9.2 Optional fields

- `last_combat_note`

### 9.3 `weapon` enum

- `none`
- `x_gun`
- `x_shotgun`
- `y_gun`
- `gantz_sword`
- `improvised`
- `unknown`

### 9.4 `mental_state` enum

- `stable`
- `stressed`
- `shaken`
- `panicked`
- `dissociated`
- `feral`
- `broken`

### 9.5 `injuries` entry shape

```json
{
  "key": "rib_fracture",
  "label": "трещина ребра",
  "severity": "moderate",
  "treated": false
}
```

### 9.6 `severity` enum

- `minor`
- `moderate`
- `severe`
- `critical`

### 9.7 Rules

- `injuries` must always contain structured objects, never plain strings,
- do not store ammo simulation,
- do not store exact coordinates,
- do not store complex tactics state,
- no automatic healing without scene justification.

---

## 10. Shared `world` block

This is the smallest domain layer.
It is for compact bio / identity / everyday grounding.
It must not turn into mission-meta clutter.

### 10.1 Required fields

- `role_label`
- `age`
- `bio_short`
- `appearance_short`

### 10.2 Optional fields

- `occupation`
- `group`
- `availability`
- `last_seen_location`
- `active_goals`
- `belongings`

### 10.3 `availability` enum

- `available`
- `busy`
- `missing`
- `isolated`
- `hospitalized`
- `dead`

### 10.4 Rules

- `age` may be integer, short string, or `null`,
- `bio_short` must stay 1-3 short sentences,
- `appearance_short` must remain concise and practical,
- `active_goals` max 3,
- `belongings` max 5,
- keep it bio/everyday-oriented,
- do not place mission-score or revive-score junk here.

---

## 11. Migration policy from useful legacy concepts

Old dating/world concepts are not invalid as concepts.
Blind literal legacy schema copying is invalid.

Use this migration logic:
- `affinity` -> valid `social` meter
- `desire` -> valid `social` meter
- `connections` -> `social.notable_links`
- `belongings` -> `world.belongings`
- `goals` -> `world.active_goals`
- `reactionIcon` -> `social.reaction_icon`
- `bg` -> UI-only, never narrative state
- `inactiveReason` -> derive from `status`, `presence`, `health`, and context

Do not recreate the legacy template under a new file name.

---

## 12. Numeric policy

- all `social` meters are `0..100`
- `health` is `0..100`
- `combat_readiness`, `panic`, `aggression` are `0..100`
- clamp all numeric values into valid ranges before output

---

## 13. Lifecycle rules

- Keep cards for offscreen but relevant NPCs.
- Do not create cards for nameless crowds or disposable extras.
- Promotion path: `temporary -> support -> major`.
- If dead: keep the card, set `status = dead`, keep continuity, set `combat.health = 0`.
- Do not silently delete dead or still-relevant entities.

---

## 14. Updater discipline

The preset must instruct the updater model to behave conservatively.

Required hard rules:
- read the latest valid `disp` block as authoritative state,
- preserve values unless the scene justifies change,
- update only fields touched by scene events,
- never invent new keys,
- never silently remove still-relevant entities,
- self-repair missing required keys conservatively,
- prefer stable continuity over dramatic volatility.

### 14.1 Change tempo buckets

#### Fast-changing
- `panic`
- `aggression`
- `arousal`
- `combat_readiness`
- `health`

#### Medium-changing
- `comfort`
- `tension`
- `openness`
- `dependency`
- `loneliness`
- `bond_need`

#### Slow-changing
- `affinity`
- `desire`
- `trust`
- `intimacy`
- `attachment`
- `world.active_goals`

### 14.2 Explicit forbidden jumps

Do not do the following without strong scene justification:
- `affinity +20` from one pleasant exchange
- `desire +25` from a minor gesture
- `intimacy +30` from incidental contact
- `panic -> 0` immediately after slaughter
- `health +30` without treatment
- deleting a dead but narratively important entity

---

## 15. Validation rules

The preset instructions must be validator-friendly.

Validate before output:
- root keys exist and are ordered,
- player exists exactly once,
- no duplicate IDs,
- enums are valid,
- numeric values are capped,
- no out-of-schema keys,
- `injuries` are structured,
- `active_goals` and `belongings` respect limits,
- only one canonical entity shape is used,
- no top-level `relations` block exists.

### 15.1 Fallback policy

If the new tracker state fails validation:
1. attempt safe repair if reliable
2. otherwise return the previous valid state unchanged

A stable old state is better than a fresh broken one.

---

## 16. `sysPrompt` requirements

When encoding the final preset, make sure the `sysPrompt` clearly contains:
- strict final `disp` output contract,
- exact root model,
- exact entity shape,
- shared `social` schema for both player and NPCs,
- compact shared `combat`,
- minimal shared `world`,
- lifecycle rules,
- validation rules,
- fallback policy,
- updater discipline,
- forbidden jumps.

Do not make the `sysPrompt` vague.
Do not compress away critical invariants.

---

## 17. Acceptance criteria

This task is complete only if all statements below are true:

1. Root schema remains exactly `worldData + playerState + characters`
2. Player is explicitly tracked
3. One canonical entity shape is used
4. Player and NPCs share the same `social` schema
5. There is no top-level `relations` block
6. Each entity has exactly three domain blocks: `social`, `combat`, `world`
7. Social is the richest layer
8. Combat is compact
9. World is minimal bio/everyday grounding
10. Relationship data exists only in `social.notable_links`
11. Dead/offscreen relevant entities are preserved
12. Strict `disp` compatibility is preserved
13. Wrapper-level preset structure is preserved
14. `extSettings.templateFile` still points to `gantz-simtracker-template.html`

---

## 18. Final instruction

Implement the preset exactly as specified.

When a detail is underspecified:
- preserve schema stability,
- preserve player-explicit tracking,
- preserve the three-domain design,
- preserve shared social schema,
- preserve conservative updater behavior,
- preserve strict state/display separation,
- prefer the most conservative implementation consistent with this document.

Do not redesign the model.
Do not split player and NPC social schemas.
Do not reintroduce a top-level `relations` block.
Do not turn the tracker into combat-only minimalism.
Do not smuggle UI state into narrative state.
