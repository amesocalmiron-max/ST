# GANTZ SimTracker Handoff Brief

## 0. Зачем этот файл

Это **handoff-документ для нового чата**, чтобы продолжить работу без потери контекста.

Цель проекта сейчас:
- спроектировать **новый SimTracker preset** для GANTZ GM-бота;
- использовать SimTracker **не как декоративный UI-блок**, а как **authoritative state-layer / continuity layer**;
- подготовить **жёсткое ТЗ для GPT Codex**, который уже будет собирать:
  - `JSON preset`
  - `HTML template`
- не допустить schema drift, UI/state-каши и слепого копирования старого dating template.

---

## 1. Главная архитектурная идея

Новый GANTZ SimTracker должен быть:
- continuity anchor;
- runtime mode anchor;
- state-carrier для игрока, мира и важных NPC;
- источником истины для secondary updater model;
- частью GM-архитектуры, но **не заменой ядра**.

### Важное разделение ролей

- **GM Kernel Core** отвечает за:
  - attempt → outcome,
  - agency protection,
  - continuity,
  - causality,
  - NPC autonomy.

- **Mode pack** отвечает только за:
  - tone,
  - pacing,
  - initiative profile,
  - explicitness profile.

- **SimTracker preset** отвечает за:
  - текущее состояние,
  - continuity,
  - активный режим сцены,
  - tracked entities,
  - state update contract.

### Output contract

Финальный tracker block:
- идёт **строго последним**;
- это **strict JSON** в кодблоке `disp`;
- после него **нет текста**.

---

## 2. Что мы хотим в итоге

Нужен новый SimTracker preset для GANTZ-бота, где каждая tracked сущность имеет **3 доменных слоя**:

### 2.1 Dating / Social layer (главный слой)
Это **самый насыщенный слой**. Он отвечает за:
- романтику;
- привязанность;
- social/dating динамику;
- повседневные эмоциональные сдвиги;
- напряжение, ревность, комфорт и т.д.

Это **не optional cosmetic layer**, а одна из основ ролевой динамики.

### 2.2 Combat layer (небольшой)
Это **компактный боевой слой**, без overkill-симуляции.

Нужно хранить только минимум:
- здоровье;
- травмы;
- оружие;
- readiness;
- panic/aggression;
- психическое состояние;
- downed.

### 2.3 World / Bio layer (самый маленький)
Это **маленький персонажный био/мирный слой**, не глобальный world-lore.

Нужно хотя бы:
- кто это;
- пол;
- возраст;
- короткое описание;
- короткое описание внешности;
- возможно роль/занятие/группа;
- немного everyday-данных.

То есть это **минимальное BIO**, а не энциклопедия и не ERP-система.

---

## 3. Каноническая root-модель

Корень state-модели должен оставаться:

```json
{
  "worldData": {},
  "playerState": {},
  "characters": []
}
```

### Почему это сохраняем

Потому что это уже было правильно и совпадает с нашей целью:
- мир ≠ персонаж;
- игрок ≠ обычный NPC;
- не все сущности обязаны иметь одинаковую важность.

---

## 4. Что хранится на верхнем уровне

## 4.1 `worldData`
Глобальное состояние сцены/мира:
- текущий режим сцены;
- дата/время;
- локация;
- sublocation;
- active_cast;
- mission/call/threat meta;
- scene_focus.

Это **не заменяет персонажный world-layer**, а отвечает за **глобальное состояние сцены**.

## 4.2 `playerState`
Игрок tracked explicitly.

Это важное решение: в GANTZ preset игрок **обязан** быть tracked entity, в отличие от некоторых старых NPC-only build-ов.

## 4.3 `characters`
Массив tracked NPC.

Трекать только:
- именованных;
- релевантных;
- повторяемых;
- влияющих на игрока, группу или арку.

Не плодить статистов.

---

## 5. Каноническая структура tracked entity

Для `playerState` и каждого `characters[]` нужна **единая базовая форма сущности**.

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
  "world": {},
  "relations": {}
}
```

### Важно

Раньше была попытка строить схему как:
- `social`
- `combat`
- `gantz`
- `story`
- `relations`

Но после последних правок фокус сместился на:
- **Dating/Social** как главный слой;
- **Combat** как маленький слой;
- **World/Bio** как маленький слой.

Поэтому в текущем ТЗ правильнее думать в модели:
- `social`
- `combat`
- `world`
- `relations`

Если потом понадобится, отдельные mission/gantz-детали можно вернуть аккуратно, но сейчас нельзя раздувать схему без нужды.

---

## 6. Что было неправильным в ранних версиях ТЗ

Вот список ошибок, которые уже были обнаружены в ходе работы.

### 6.1 Ошибка: слишком агрессивно вырезали legacy dating/world concepts

Изначально было сказано, что такие поля как:
- `affinity`
- `desire`
- `reactionIcon`
- `connections`
- `belongings`
- `goals`
- `bg`
- `inactiveReason`

это «мусор».

Это **было ошибкой формулировки**.

### Что на самом деле верно

Эти вещи **не мусор как идеи**.

Неправильно было бы:
- тащить их **один-в-один как legacy-реализацию** из старого шаблона.

Но как **концепты** они нужны:
- `affinity/desire` полезны для dating/social layer;
- `connections` полезны как связи;
- `belongings` полезны как лёгкий everyday/world слой;
- `goals` полезны как текущие цели персонажа.

### Исправленная позиция

Надо запрещать не сами идеи, а:
- **слепой copy-paste legacy schema**.

---

### 6.2 Ошибка: JSON-ТЗ раньше было перекошено в боёвку

Ранний JSON package слишком сильно упирался в:
- health;
- suit integrity;
- readiness;
- panic/aggression;
- mission state.

Это противоречило твоей актуальной постановке:
- dating/social = главный слой;
- combat = маленький;
- world/bio = маленький.

### Исправление

Теперь social должен быть **самым богатым доменным блоком**.

---

### 6.3 Ошибка: HTML-ТЗ долго фиксировало только 2 main tabs

Раньше HTML-пакет был построен вокруг:
- `Social`
- `Combat`

Это уже не соответствовало новой модели.

### Исправление

Сейчас правильные **3 главные вкладки**:
- `Dating`
- `Combat`
- `World`

---

### 6.4 Ошибка: world-layer был слишком слабым или загрязнялся mission-meta

Ранние версии:
- либо почти не давали персонажу normal world/bio layer;
- либо пытались пихать туда mission/gantz мусор (`room_status`, `score`, `revivable`).

### Исправление

Теперь `world` должен быть:
- маленький;
- биографический;
- everyday-oriented;
- без mission-overload.

---

### 6.5 Ошибка: Codex выдавал концептуально сломанные альтернативы

Когда были запрошены 2 версии preset-а, одна из них оказалась реально плохой:
- ломала enums;
- смешивала stress/panic/aggression в social;
- урезала status/presence;
- уводила combat не туда.

Вывод был такой:
- одна версия была salvageable;
- вторая была концептуально сломана.

На их базе потом был собран `gantz-simtracker-preset_v4.json` как лучшая текущая база.

---

## 7. Текущая правильная концепция social / combat / world

## 7.1 Social = главный слой

Сейчас social-layer должен быть **богатым**, почти в духе dating-sim/relationship sim, но не тупой копией старого шаблона.

### Что в social хотим держать
Минимально это должно покрывать:
- affinity;
- desire;
- trust;
- comfort;
- intimacy;
- jealousy;
- tension;
- attachment;
- openness;
- dependency;
- loneliness;
- bond_need;
- arousal.

### Важно

Эти поля нужны **как полноценный dating/social state**, а не как display gimmick.

---

## 7.2 Combat = маленький слой

Combat должен быть компактным.

### Что там нужно
- `health`
- `injuries`
- `weapon`
- `combat_readiness`
- `panic`
- `aggression`
- `mental_state`
- `downed`

### Чего там не надо
- подробной баллистики;
- координатной тактики;
- ammo economy;
- сложной spatial sim.

---

## 7.3 World = маленький bio/everyday слой

### Что там нужно минимум
- `sex`
- `age`
- `role_label`
- `bio_short`
- `appearance_short`

### Что ещё допустимо как лёгкий everyday-слой
- `occupation`
- `group`
- `availability`
- `active_goals`
- `belongings`

### Чего там не надо
- mission-score мусора;
- тяжёлой глобальной world-sim;
- full equipment sim;
- полноразмерной базы знаний.

---

## 8. Lifecycle rules

Эти правила остаются в силе.

### 8.1 Карточка создаётся, если персонаж:
- именован;
- вошёл в активную сцену;
- имеет шанс повториться;
- влияет на игрока/группу/арку;
- не является disposable extra.

### 8.2 Tier progression
- `temporary -> support -> major`

Для игрока:
- `tracked_tier = primary`

### 8.3 Уход в фон
Если NPC ушёл из сцены:
- карточка не удаляется;
- сохраняется;
- может стать `inactive`;
- `presence = offscreen`.

### 8.4 Смерть
Если персонаж умер:
- `status = dead`
- карточка сохраняется
- не удалять silently.

---

## 9. Updater philosophy

Secondary model нужна как:
- state updater;
- normalizer;
- conservative continuity worker.

### Updater должен
- читать последний валидный `disp` block как authoritative state;
- менять только то, что реально затронуто сценой;
- не invent keys;
- не сбрасывать показатели случайно;
- не удалять важные сущности;
- выдавать один strict JSON `disp` block.

### Preserve unless justified
Если сцена не даёт основание для изменения поля:
- значение сохраняется.

---

## 10. Что сейчас делаем practically

Сейчас задача уже не в том, чтобы спорить о философии, а в том, чтобы собрать **рабочий пакет для Codex**.

### Нам нужны 2 основных handoff-пакета:

## A. JSON preset package
Что должен сделать Codex:
- собрать **реальный preset JSON** в формате, совместимом с SimTracker;
- не просто schema-doc, а preset-wrapper в духе рабочего примера;
- встроить или правильно связать HTML template;
- реализовать schema:
  - `worldData`
  - `playerState`
  - `characters`
- дать 3 доменных блока на сущность:
  - `social`
  - `combat`
  - `world`

## B. HTML template package
Что должен сделать Codex:
- сделать **современный, красивый, tech-pron UI**;
- one icon per entity;
- иконка открывает карточку сущности;
- красивые вкладки;
- нормальные анимации;
- тёмный современный стеклянный/techwear стиль;
- без clown-RGB и без каши.

---

## 11. HTML/UI vision сейчас

### Основная идея

Справа вертикальный rail:
- одна иконка на персонажа;
- игрок тоже имеет иконку;
- клик по иконке открывает красивую карточку.

### Главные вкладки теперь должны быть
- `Dating`
- `Combat`
- `World`

### Dating tab
Это главный экран персонажа:
- романтические/социальные статы;
- bonds;
- chemistry / attraction / trust / comfort / jealousy / intimacy и т.д.

### Combat tab
Компактный боевой экран:
- здоровье;
- травмы;
- оружие;
- психическое состояние;
- готовность;
- panic/aggression;
- downed.

### World tab
Небольшой bio/everyday экран:
- кто это;
- пол;
- возраст;
- короткое описание;
- краткая внешность;
- минимальные everyday поля.

### Визуальный стиль
Нужен:
- красивый;
- современный;
- тёмный;
- слегка glassy / sci-fi;
- с плавной анимацией;
- но без кричащего неона и без бессмысленного мусора.

---

## 12. Что уже было создано в этом чате

В процессе были созданы/обсуждались:

### 12.1 Handoff md-файлы
Были версии:
- `GANTZ_SIMTRACKER_CODEX_JSON_TASK.md`
- `GANTZ_SIMTRACKER_CODEX_HTML_TASK.md`
- `GANTZ_SIMTRACKER_CODEX_JSON_TASK_v2.md`
- `GANTZ_SIMTRACKER_CODEX_HTML_TASK_v2.md`
- `GANTZ_SIMTRACKER_CODEX_JSON_TASK_v3.md`
- `GANTZ_SIMTRACKER_CODEX_HTML_TASK_v3.md`

### 12.2 Генерировались Codex/preset JSON файлы
Были варианты:
- `gantz.json`
- `gantz-simtracker-preset (1).json`
- `gantz-simtracker-preset (2).json`
- `gantz-simtracker-preset_v4.json`

### 12.3 Важный вывод по двум версиям preset-а
Из двух альтернатив:
- `(1)` был относительно живой базой;
- `(2)` оказался концептуально плохим.

После этого был сделан `gantz-simtracker-preset_v4.json` как **лучшая текущая рабочая база**, но он тоже должен рассматриваться не как “священный артефакт”, а как **рабочая база для следующей итерации**.

---

## 13. Что нужно проверить/решить в новом чате

Вот правильный список следующих шагов.

## Шаг 1. Проверить текущий `gantz-simtracker-preset_v4.json`
Нужно внимательно пройтись по нему и проверить:
- wrapper-level preset compatibility;
- не поломаны ли legacy expectations SimTracker;
- правильно ли встроен/связан HTML;
- достаточно ли богат `social`;
- не раздут ли `world`;
- не конфликтуют ли enums.

## Шаг 2. Перепроверить и, возможно, переписать JSON handoff one more time
С учётом финальной постановки:
- dating/social = главный слой;
- combat = компактный;
- world = маленький bio.

## Шаг 3. Сделать финальный HTML handoff
HTML должен соответствовать уже **новой 3-tab модели**, а не старым 2-tab версиям.

## Шаг 4. Получить от Codex новый clean implementation
Нужно попросить Codex:
- собрать финальный JSON preset;
- собрать финальный HTML template;
- не менять schema;
- не выдумывать лишние поля;
- не откатываться к старой dating-sim schema один-в-один;
- не превращать UI/state в кашу.

## Шаг 5. После этого сделать final audit
Проверить:
- schema correctness;
- visual correctness;
- tab mapping;
- state/display separation;
- compatibility with actual runtime.

---

## 14. Самое важное, что нельзя забыть в новом чате

### 14.1 Мы не делаем просто боевой модуль
Это критично.

Модель должна быть:
- social-heavy,
- combat-light,
- world-light.

### 14.2 Legacy ideas не мусор, но legacy schema нельзя копировать слепо
То есть:
- старые идеи useful;
- старый literal field layout не обязательно годится.

### 14.3 HTML должен быть красивым
Это не мелочь.

Пользователь хочет:
- tech-pron;
- современный интерфейс;
- красивые вкладки;
- анимацию;
- one icon per character;
- визуальное удовольствие, а не только бухгалтерию.

### 14.4 SimTracker всё ещё authoritative state layer
То есть это не просто декоративная игрушка, а реальный continuity/state carrier.

---

## 15. Рекомендуемая формулировка для нового чата

Ниже короткая версия, которую можно вставить в новый чат как старт.

---

# START BRIEF FOR NEW CHAT

Мы продолжаем проект нового **GANTZ SimTracker preset** для GM-бота.

### Главная цель
Сделать SimTracker не декоративным UI-блоком, а **authoritative state-layer / continuity layer**.

### Root model
Оставляем:
- `worldData`
- `playerState`
- `characters`

### Player
Игрок tracked explicitly.

### На каждую tracked сущность нужны 3 доменных слоя
1. **Dating / Social** — главный и самый богатый слой
2. **Combat** — небольшой боевой слой
3. **World / Bio** — самый маленький слой, минимальное BIO / everyday info

### Social layer должен быть богатым
Нужны romance / relationship / dating-style meters, а не только trust + comfort.

### Combat layer должен быть компактным
Нужны только:
- health
- injuries
- weapon
- readiness
- panic/aggression
- mental state
- downed

### World layer должен быть маленьким
Нужно минимум:
- кто это
- пол
- возраст
- короткое описание
- короткое описание внешности
- минимальные everyday поля

### HTML vision
Нужен красивый современный right-side UI:
- одна иконка на персонажа;
- клик по иконке открывает карточку;
- красивые анимированные вкладки;
- 3 main tabs:
  - `Dating`
  - `Combat`
  - `World`
- современный dark / glass / sci-fi look;
- без clown-RGB.

### Important correction
Мы НЕ считаем dating/world concepts мусором.
Ошибка раннего ТЗ была в том, что мы слишком агрессивно вырезали legacy concepts.
Правильно: не копировать старую schema один-в-один, но сохранить полезные идеи social/world layers.

### Current state
Были сделаны несколько handoff md-файлов и несколько preset JSON вариантов.
Из двух альтернативных preset-версий:
- первая была salvageable,
- вторая была концептуально плохой.

После этого была сделана рабочая база:
- `gantz-simtracker-preset_v4.json`

Но её тоже нужно ещё раз проверить как рабочую основу, а не считать финальным идеалом.

### What we need now
1. Перепроверить handoff под финальную 3-layer модель
2. Перепроверить `gantz-simtracker-preset_v4.json`
3. Подготовить или улучшить финальный Codex task package
4. Дожать финальный JSON preset
5. Дожать финальный HTML template
6. Сделать final audit на совместимость и логику

---

