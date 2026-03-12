# GANTZ SimTracker JSON Preset Task for GPT Codex v3

## 0. Purpose of this document

This document is an **implementation handoff** for GPT Codex.

This is not a brainstorming document.
This is not permission to redesign the schema.
This is not an HTML task.

The goal is to implement a new SimTracker JSON preset for the GANTZ GM build with a corrected three-domain character model:

- **Dating / romance layer** = the main and richest layer
- **Combat layer** = compact and practical
- **World / bio layer** = the smallest layer, used for minimal identity grounding

This task concerns **JSON preset only**.
HTML, CSS, JS, and visual rendering are handled in a separate task.

---

## 1. Why this v3 rewrite exists

The previous handoff improved the direction, but it still had several weak points:

- wrapper-level preset compatibility was not explicit enough;
- `tracked_tier` vocabulary was not fully locked;
- player/world bio requirements did not fully reflect the latest project note;
- `World` scope risked drifting into mission-meta clutter;
- JSON/HTML handshake rules were not explicit enough.

This v3 version fixes those issues.

The corrected target is:

1. **Dating / romance / social disposition** as the richest layer
2. **Compact combat state** with only necessary battle continuity
3. **Minimal world / bio layer** for identity grounding

The latest user requirement for the smallest `World` layer is:
- who this person is,
- sex,
- short description,
- short appearance description,
- age,
- minimal bio/everyday grounding.

Do not lose that requirement.

---

## 2. What Codex must produce

Create one new preset JSON file:

- `gantz-simtracker-preset.json`

The file must:
- follow the general wrapper/metadata style of working SimTracker preset files,
- remain compatible with strict `disp` JSON output,
- encode the corrected three-layer state model,
- provide updater instructions for conservative continuity updates,
- explicitly track the player.

---

## 3. Required preset wrapper compatibility

This task is not only about the inner schema.
It must also produce a preset file that resembles working SimTracker preset structure.

### 3.1 Required wrapper-level keys

The resulting preset file should include the usual preset-style wrapper keys used by working tracker presets, such as:

- `templateName`
- `templatePosition`
- `tabsType`
- `htmlTemplate`
- `customFields`
- `extSettings`
- `trackerDesc`
- `sysPrompt`

Do not output a pure architecture-spec JSON that lacks preset wrapper structure.

### 3.2 HTML integration handshake

This JSON task must not invent HTML markup.
However, the preset must be designed to integrate with the separately implemented HTML template.

Use this policy:

- if the runtime supports external template file linking, reference `gantz-simtracker-template.html` through the appropriate preset setting;
- if the runtime requires embedded HTML, the final integration step may place the HTML inside `htmlTemplate`;
- do **not** fabricate placeholder HTML just to fill the field.

### 3.3 `disp` compatibility

The preset instructions must enforce:
- strict JSON inside `disp`
- one final tracker block only
- no text after the final tracker block

---

## 4. Non-negotiable architecture

### 4.1 Root model must remain

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
- merge player into `characters[]`,
- split the root into separate peaceful/combat arrays,
- create different root schemas for different sexes.

### 4.2 Per-entity domain model must be three-layered

For `playerState` and each item in `characters[]`, the core domain blocks must be:

- `social`
- `combat`
- `world`

### 4.3 Display and state must remain separate

Do not encode:
- card colors,
- animation flags,
- selected tab state,
- layout choices,
- visual theme tokens

as narrative state fields.

UI belongs to the HTML task.

---

## 5. Corrected policy on old dating-sim concepts

### 5.1 Important correction

Old fields like:
- `affinity`
- `desire`
- `connections`
- `belongings`
- `goals`
- `reactionIcon`

are **not invalid as concepts**.

What was wrong before was either:
- banning them too aggressively,
- or copying them one-to-one from the legacy template without redesign.

### 5.2 Correct migration policy

Use this migration logic:

- `affinity` -> valid romance/social meter in `social`
- `desire` -> valid romance/social meter in `social`
- `connections` -> migrate into structured `notable_links`
- `belongings` -> compact `world.belongings`
- `goals` -> compact `world.active_goals`
- `reactionIcon` -> controlled `social.reaction_icon`
- `bg` -> UI-only, never narrative state
- `inactiveReason` -> derive from `status`, `presence`, `health`, and context

### 5.3 What is still forbidden

Do not blindly reimport the old template schema.
Do not rebuild the preset as a re-skinned legacy dating tracker.
Do not revive legacy fields that existed only because the old schema was simplistic.

---

## 6. Global block: `worldData`

`worldData` stores current scene/world state, not per-character bio.

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

### 6.2 Allowed enums

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
- `active_cast` includes only directly present or actively influencing entities.
- `submode` is a controlled open `snake_case` string.
- if no countdown or mission timer exists, use `null`, not fake zero-values.
- if no mission exists, use `null` for mission identifiers and mission target.

---

## 7. Base entity shape

Each tracked entity must follow this top-level shape:

```json
{
  "id": "",
  "name": "",
  "sex": "",
  "ui_variant": "",
  "icon_key": "",
  "tracked_tier": "",
  "status": "",
  "presence": "",
  "social": {},
  "combat": {},
  "world": {}
}
```

### 7.1 Shared top-level enums

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
- NPCs must use only `major`, `support`, or `temporary`.

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

### 7.2 Naming policy

- `id` must be stable canonical snake_case English
- `icon_key` must be stable canonical snake_case English
- JSON keys must remain English
- enum values must remain English
- short human-facing descriptive text may be Russian

---

## 8. `playerState`

### 8.1 Invariants

- `id` must always be `player`
- `tracked_tier` must always be `primary`
- `playerState` must always exist
- player must never be duplicated inside `characters[]`

### 8.2 Top-level required fields

- `id`
- `name`
- `sex`
- `ui_variant`
- `icon_key`
- `tracked_tier`
- `status`
- `presence`
- `social`
- `combat`
- `world`

---

## 9. `playerState.social`

This block represents the player’s current social/romantic condition plus compact per-target links.
It is not a clone of NPC romance memory.

### 9.1 Required fields

- `stress`
- `loneliness`
- `openness`
- `arousal`
- `bond_need`
- `mask`
- `reaction_icon`
- `notable_links`

### 9.2 Optional fields

- `internal_thought`
- `social_flags`

### 9.3 Allowed `mask` enum

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

### 9.4 Allowed `reaction_icon` enum

- `none`
- `pleased`
- `neutral`
- `flustered`
- `jealous`
- `wary`
- `cold`
- `hostile`

### 9.5 `notable_links` entry shape

```json
{
  "target_id": "kei_kurono",
  "affinity": 42,
  "desire": 18,
  "trust": 31,
  "comfort": 22,
  "intimacy": 9,
  "jealousy": 0,
  "tension": 35,
  "attachment": 16,
  "bond_label": "acquaintance"
}
```

### 9.6 `bond_label` enum for player notable links

Use the same enum as NPC `social.bond_label`.

### 9.7 Rules

- player link entries are the per-target romance/disposition layer
- keep only 1–5 meaningful links
- do not create links for disposable extras

---

## 10. `characters[].social`

This is the richest layer for NPCs.
It should support romance, dating-sim style state, and scene social tension without bloating into nonsense.

### 10.1 Required fields

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

### 10.2 Optional fields

- `social_flags`
- `devotion`
- `interest`

### 10.3 Allowed `bond_label` enum

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

### 10.4 `notable_links` entry shape

```json
{
  "target_id": "rei_yoshino",
  "bond": "teammate",
  "affinity": 55,
  "trust": 48,
  "tension": 21
}
```

### 10.5 `notable_links[].bond` allowed enum

Use the same bond vocabulary family as `bond_label`, but keep it concise:

- `stranger`
- `acquaintance`
- `classmate`
- `teammate`
- `ally`
- `trusted`
- `protective`
- `dependent`
- `rival`
- `hostile`
- `fractured`

### 10.6 Rules

- NPC social block represents both disposition toward the player and current emotional/social state in scene
- this block is allowed to use `affinity` and `desire` because the active preset defines the semantics
- `reaction_icon` is allowed as a compact social summary field
- do not turn this into a giant fetish meter zoo

---

## 11. Shared `combat` block

Combat is intentionally smaller than the dating layer.
This is not the main personality engine.
It only needs practical mission-state continuity.

### 11.1 Required fields

- `health`
- `injuries`
- `weapon`
- `combat_readiness`
- `panic`
- `aggression`
- `mental_state`
- `downed`

### 11.2 Optional fields

- `last_combat_note`

### 11.3 Allowed `weapon` enum

- `none`
- `x_gun`
- `x_shotgun`
- `y_gun`
- `gantz_sword`
- `improvised`
- `unknown`

### 11.4 Allowed `mental_state` enum

- `stable`
- `stressed`
- `shaken`
- `panicked`
- `dissociated`
- `feral`
- `broken`

### 11.5 `injuries` entry shape

```json
{
  "key": "rib_fracture",
  "label": "трещина ребра",
  "severity": "moderate",
  "treated": false
}
```

### 11.6 `severity` enum

- `minor`
- `moderate`
- `severe`
- `critical`

### 11.7 Rules

- `injuries` must always be structured objects, never plain strings
- do not store suit simulation, ammo simulation, or exact coordinates in v3
- no automatic healing without scene justification

---

## 12. Shared `world` block

This is the smallest domain layer.
It is for stable bio/everyday/world identity, not for giant encyclopedic lore entries.

### 12.1 Required fields

- `role_label`
- `age`
- `bio_short`
- `appearance_short`

### 12.2 Optional fields

- `occupation`
- `group`
- `availability`
- `last_seen_location`
- `active_goals`
- `belongings`

### 12.3 Allowed `availability` enum

- `available`
- `busy`
- `missing`
- `isolated`
- `hospitalized`
- `dead`

### 12.4 Rules

- `age` may be integer, short string, or `null` if canonically unknown
- `bio_short` must remain short, 1–3 sentences max
- `appearance_short` must remain short, visual, and practical
- `active_goals` max 3 entries
- `belongings` max 5 compact entries
- do not expand `world` into a lore encyclopedia or a hidden gantz-meta module

---

## 13. Numeric policy

### 13.1 Social meters

All social meters use `0..100`.

Interpretation:
- `0-10` nearly absent
- `11-30` low
- `31-55` noticeable / working level
- `56-75` high
- `76-90` very high
- `91-100` extreme / dominant

### 13.2 Combat health

- `100` fully functional
- `75-99` light damage
- `50-74` significant damage
- `25-49` heavy degradation
- `1-24` critical state
- `0` dead / out of action

---

## 14. Lifecycle rules

### 14.1 Create NPC card only if

- the NPC has stable identity
- they entered the active scene
- they can recur
- they affect player, group, or arc
- they are not a disposable extra

### 14.2 Do not create cards for

- random passersby
- background students
- nameless crowds
- one-off extras with no ongoing relevance

### 14.3 Promotion path

- `temporary -> support -> major`

when the NPC survives, recurs, matters emotionally, or takes a stable role in continuity.

### 14.4 Offscreen retention

If an NPC leaves the current scene:
- keep the card
- usually set `presence = offscreen`
- set `status = inactive` only when justified

### 14.5 Death retention

If an NPC dies:
- keep the card
- set `status = dead`
- set `combat.health = 0`
- keep social/world identity intact for memory continuity

---

## 15. Updater contract

The preset must instruct the secondary updater model clearly.

### 15.1 Updater role

Updater is:
- conservative state updater
- continuity worker
- schema preserver

Updater is not:
- narrator
- lore writer
- improvisational designer

### 15.2 Hard updater rules

Updater must:
- read the most recent valid `disp` block as authoritative input
- preserve values unless the scene justifies change
- self-correct missing keys conservatively
- never invent new keys
- never silently delete still-relevant entities
- explicitly track `playerState`
- update only fields actually affected by the scene
- output one strict JSON `disp` block only

### 15.3 Field-speed discipline

#### Fast-changing
- `panic`
- `aggression`
- `stress`
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

### 15.4 Forbidden examples

Do not do this without strong scene justification:
- `affinity +20` from one pleasant exchange
- `desire +25` from a minor gesture
- `intimacy +30` from incidental contact
- `panic -> 0` immediately after slaughter
- `health +30` without treatment
- deleting a dead but narratively important NPC

---

## 16. Validation rules

The preset instructions must support validator-friendly output.

### 16.1 Validate

- root keys exist
- player exists exactly once
- no duplicate IDs
- enums are valid
- numeric values are capped
- no keys outside schema
- `injuries` are structured
- `active_goals` / `belongings` stay within limits

### 16.2 Key order policy

Use stable key order for root and entities.
Do not randomly shuffle field order between outputs.

Recommended root order:
1. `worldData`
2. `playerState`
3. `characters`

Recommended entity order:
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

### 16.3 Fallback

If the new tracker block is invalid:
1. attempt safe repair if reliable
2. otherwise return the previous valid state unchanged

Better a stable old state than a shiny broken one.

---

## 17. Acceptance criteria

The task is complete only if:

1. root schema remains exactly `worldData + playerState + characters`
2. player is explicitly tracked
3. each entity has exactly three domain blocks: `social`, `combat`, `world`
4. the dating layer is the richest one
5. the combat layer is compact
6. the world layer is small and bio-oriented
7. old dating concepts are migrated, not blindly rejected and not blindly copied
8. `tracked_tier` vocabulary is explicit and stable
9. `injuries` are structured objects only
10. dead/offscreen relevant entities are preserved
11. output remains strict-JSON `disp` compatible
12. wrapper-level preset structure is not omitted

---

## 18. Final instruction to Codex

Implement the new preset exactly as specified.

When a detail is underspecified:
- preserve root schema stability
- preserve player-explicit tracking
- preserve the three-layer entity design
- preserve continuity discipline
- preserve strict separation of state and display
- preserve minimal World/Bio scope
- prefer the most conservative implementation consistent with this handoff

Do not redesign the model.
Do not remove the dating/world layers.
Do not turn the tracker into combat-only minimalism.
Do not reintroduce legacy wrapper habits that break the new schema.
