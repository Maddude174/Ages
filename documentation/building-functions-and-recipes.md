# Building Functions and Recipes

The `function:` block decides what a registered building **does** on its timer. This is the most technical part of a building file.

```yaml
function:
  type: processor      # processor | spawner | tax | sell | siege | none
  interval: 300        # seconds between cycles (minimum 1)
  mode: selected       # random | first | all | selected
  always: [{points: 1}]
  recipes: [...]
```

## Timing

* Each building has a **next-run timestamp** stored in the database, so timers survive restarts.
* A building with no timestamp waits one full interval.
* If the core's chunk is not loaded, the run is deferred by 15 seconds and retried.
* If the server was down or the chunk unloaded, missed cycles are mostly skipped. `production.max_catchup_cycles` (default `1`, meaning none) lets a building catch up that many.
* The effective interval is modified by `production_speed_mult` (government, economy, policies) and the additive `production_speed` bonus from buildings, capped by `production.max_speed_bonus`.
* A **disabled** building produces nothing and loses RP each interval (see [Buildings](buildings.md)).

## `always`

`always` entries fire **every cycle**, before the function type runs and whether or not a recipe succeeds.

```yaml
always:
  - {points: 1}          # resource points to the civilization
  - {money: 5}           # money, routed by the economy (see below)
  - {item: FEATHER, min: 1, max: 3, chance: 0.7}   # items to the output storage
```

Points go to the civilization. Money is multiplied by `money_output_mult` and `economy.income_multiplier`, then **routed**:

* If the economy has `money_enabled: false`, it becomes RP at `money_to_points`.
* Otherwise the **treasury share** is the economy's `income_to_civ` plus policy effects (0 to 1, or 1 under a government with `force_income_to_civ`). The rest is **stored on the building** until someone collects it into their own Vault balance.

See [Economies](economies.md) for the full rules.

## Function types

### `processor`

Consumes inputs from the **input storage** and puts outputs into the **output storage**. See the recipe format below.

### `spawner`

Spawns a persistent entity (usually a villager) inside the building.

```yaml
function:
  type: spawner
  interval: 900
  entity: VILLAGER
  cap: 3                  # maximum alive per building
  housing_limited: true   # stops when civilization housing is full
  consume: [{item: IRON_BLOCK, amount: 1}]   # optional cost per spawn
```

* Entities are tracked, persistent (never despawn when far away) and untradeable (`villagers.untradeable`).
* A death frees one slot, and the timer refills it. Entities are never duplicated.
* Iron golems are spawned as player-created: they defend but never attack players.
* Status messages: `Full (n/cap)`, `No free housing`, `No free space to spawn`, `Needs ...`.
* `entity` must be a valid entity type, `cap` at least 1, and a spawner without `entity` is a load error.

### `tax`

```yaml
function: {type: tax, interval: 600, per_resident: 0.4}
```

Each cycle earns `per_resident * population` as money (population counts players and villagers). That money is routed like any other building income, so with the default `income_to_civ` it may stay on the building. With `payout: owner` the income is always stored on the building for its owner (capped by `max_stored`).

### `sell`

```yaml
function:
  type: sell
  prices: {WHEAT: 0.5}
  max_items: 64
```

Each cycle sells up to `max_items` items from the input storage at `prices` and earns the total as money, routed like other building income. Prices cannot be negative.

### `siege`

The cannon's function. Each shell is a recipe that **only consumes** ammunition, so a recipe with `consume` and no `produce` is valid for this type.

```yaml
function:
  type: siege
  interval: 180
  mode: selected
  recipes:
    - id: standard_shell
      select_as: standard
      name: Standard shell
      consume:
        - {item: GUNPOWDER, amount: 3}
        - {item: COBBLESTONE, amount: 1}
siege:
  range: 32        # blocks (minimum 8)
  height: 8        # arc height (minimum 2)
  yield: 3.5       # explosion power (minimum 1.0)
  cooldown_seconds: 180   # overrides function.interval when set
```

Siege only runs while diplomacy is enabled, and only fires at civilizations you are at war with. The full targeting rules are in [Diplomacy and War](diplomacy-and-war.md). Ammunition comes from the cannon's storage, and a missing shell shows as `Need ammo (see shell recipes)`.

### `none`

No timer logic besides `always`. Used by cores, storage, defence and infrastructure buildings.

## Recipe modes (processors and siege)

| Mode       | Behaviour each cycle                                                                                                        |
| ---------- | --------------------------------------------------------------------------------------------------------------------------- |
| `random`   | Recipes are shuffled. The **first one that can run** runs.                                                                  |
| `first`    | Recipes are tried in file order. The first that can run runs. Used by the progressive Mine, Clay Pit, Sand Pit and Forager. |
| `all`      | **Every** recipe that can run, runs.                                                                                        |
| `selected` | The product the owner chose in the Production panel runs first. See below.                                                  |

A recipe is skipped if `require_level` exceeds the building's upgrade level, or `require_age` is later than the civilization's current age. Both are **minimums**, so a higher level or age still unlocks the recipe. If no recipe is unlocked the status is `No unlocked recipes`.

### `selected` mode and `select_as`

In `selected` mode the owner or a manager picks a **product** in the **Production** panel. Recipes that share a `select_as` key form one product, so tiers (better tools at higher levels) show up as one choice. For example every Woodcutter wood type is one product, and its wooden, stone, copper and iron axe tiers are the tiers.

How the choice resolves each cycle:

1. The recipes of the chosen product are tried **best tier first** (highest `require_level` first, preferring the rarer tool).
2. The first tier whose inputs are present and whose output fits runs.
3. If the chosen product cannot run this cycle (missing tool or ingredient, or full output), any other unlocked recipe that can run is used instead.
4. The status only reads `Needs ...` when no unlocked recipe could run.

Tiers stay hidden in the panel until the level and age allow them. A recipe without `select_as` is its own product, keyed by its `id`.

## Recipe format

```yaml
recipes:
  - id: forage              # unique per building
    select_as: berries      # optional: group tiers under one menu choice
    name: Forage
    interval: 240           # optional: the NEXT cycle after this recipe is this long
    require_level: 2
    require_age: medieval
    consume:
      - {item: ARROW, amount: 1}
      - {item: BOW, durability: 2}
      - {any_of: [COAL, CHARCOAL], amount: 1}
      - {group: wool_items, amount: 2}
    produce:
      pick: one
      entries:
        - {item: SWEET_BERRIES, min: 1, max: 3, percent: 25}
        - {item: BROWN_MUSHROOM, percent: 10}
```

The old `optional: true` flag is **no longer supported**. Every item name in a recipe must exist on your server, or the building is skipped. See [Compatibility and Requirements](compatibility-and-requirements.md).

### `consume` ingredients

| Form                          | Meaning                                                                                                                                                                                                  |
| ----------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `{item: X, amount: N}`        | Take `N` of item `X`.                                                                                                                                                                                    |
| `{any_of: [A, B], amount: N}` | Take `N` of any one of the listed items.                                                                                                                                                                 |
| `{group: g, amount: N}`       | Take `N` of any item in an item group from `groups.yml`.                                                                                                                                                 |
| `{item: X, durability: D}`    | A **tool**: wears down by `D` per cycle and is destroyed when broken. The item must have durability and `D` must be below its maximum. `any_of` works with `durability` too, so any listed tool will do. |

An ingredient needs `amount` of at least 1 or `durability` of at least 1. If any input is missing, the recipe does not run and the building status reads `Needs ...`.

### `produce`

`produce` has a `pick` mode and a list of `entries`. A recipe needs at least one `produce` entry or at least one `consume` entry.

| `pick` | Behaviour                                                                                                                |
| ------ | ------------------------------------------------------------------------------------------------------------------------ |
| `one`  | Exactly **one** entry is chosen, weighted. Weights come from `percent` or `weight`. Any shortfall below 100% is nothing. |
| `all`  | **Each** entry rolls independently on its own `chance` (0 to 1, default always).                                         |

Entry kinds:

```yaml
- {item: SWEET_BERRIES, min: 1, max: 3, percent: 25}   # item, random quantity min..max
- {group: wool_items, min: 1, max: 3, chance: 0.8}     # a random item from a group
- {points: 2}                                          # RP (min/max allowed, 2 decimals)
- {money: 5}                                           # money, routed by the economy
- {item: AIR, percent: 55}                             # explicit "nothing"
```

For `pick: one`, percentages must total at most 100, and the remainder is nothing. You can use `weight:` instead of `percent:` (`{item: EGG, weight: 3}`, `{item: AIR, weight: 1}`).

If the recipe produces items but the output storage is full, the recipe does **not** run and the building status reads `Output container is full`. Inputs are only consumed once the output is known to fit.

## Worked example: the Hunter's Hut

A building with `logistics: {input: CORE, output: CORE}`, `mode: selected` and `interval: 240`. It offers four products, and the owner picks one:

| Product           | Consumes                                          | Produces (pick one)                                                      |
| ----------------- | ------------------------------------------------- | ------------------------------------------------------------------------ |
| Hunt with a bow   | Bow (wears 2), 1 arrow                            | Beef 18%, porkchop 18%, mutton 14%, rabbit 16%, chicken 14%, leather 8%  |
| Hunt with a sword | Any stone, copper or iron sword (wears 3)         | Porkchop 24%, mutton 20%, rabbit 14%, beef 12%, leather 8%               |
| Hunt with a spear | Any wooden, stone, copper or iron spear (wears 3) | Beef 20%, porkchop 18%, mutton 16%, rabbit 14%, chicken 12%, leather 10% |
| Set snares        | 2 sticks, 1 string                                | Rabbit 30%, chicken 25%, rabbit hide 12%                                 |

Plus `always: {points: 1.4}` every cycle. Open the building's storage, put a bow and arrows in it, and meat appears in the same storage. The sword and spear recipes need copper or spears from newer Minecraft versions in their `any_of` lists. See [Compatibility and Requirements](compatibility-and-requirements.md).

## Worked example: progressive tiers (Woodcutter)

Each wood type is one product (`select_as: oak`, `birch`, and so on). The wooden-axe tier works at level 1 and produces 2 to 4 logs, a 30% chance of a sapling and a 40% chance of sticks. Upgrades unlock the stone, copper and iron axe tiers for the same wood type, and the building uses the best axe you put in. The Mine, Clay Pit, Sand Pit and the farming and industry buildings follow the same pattern.

## Tips

* Keep `interval` realistic. Production runs once per second in a scheduler, but lots of very short intervals add load.
* Make output storage large enough, or you will see `Output container is full` and production stops.
* Test a new building with `/ages give <building>` and an admin account before offering it in the catalogue.
