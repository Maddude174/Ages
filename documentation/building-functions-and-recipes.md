# Building Functions and Recipes

The `function:` block decides what a registered building **does** on its timer. This is the most technical part of a building file.

```yaml
function:
  type: processor      # processor | spawner | tax | sell | none
  interval: 300        # seconds between cycles (minimum 1)
  mode: random         # random | first | all | selected
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
  - {money: 5}           # treasury money to the civilization
  - {item: FEATHER, min: 1, max: 3, chance: 0.7}   # items to the output container
```

Points and money go to the civilization, not to a container. Money is multiplied by `money_output_mult` and `economy.income_multiplier`. If the economy has `money_enabled: false`, money becomes RP at `money_to_points`.

## Function types

### `processor`

Consumes inputs from the **input container** and puts outputs into the **output container**. See the recipe format below.

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

Each cycle pays `per_resident * population` into the civilization treasury (population counts players and villagers). With `payout: owner` the income is stored on the building for its owner instead (legacy behaviour, capped by `max_stored`).

### `sell`

```yaml
function:
  type: sell
  prices: {WHEAT: 0.5}
  max_items: 64
```

Each cycle sells up to `max_items` items from the input container at `prices` and pays the total as money. Prices cannot be negative.

### `none`

No timer logic besides `always`. Used by cores, storage, defence and infrastructure buildings.

## Recipe modes (processors)

| Mode       | Behaviour each cycle                                                                                  |
| ---------- | ----------------------------------------------------------------------------------------------------- |
| `random`   | Recipes are shuffled. The **first one that can run** runs.                                            |
| `first`    | Recipes are tried in file order. The first that can run runs. Used by the progressive Mine.           |
| `all`      | **Every** recipe that can run, runs.                                                                  |
| `selected` | Only the recipe the owner selected in the building menu runs (the first available if none is chosen). |

A recipe is skipped if `require_level` exceeds the building's upgrade level, or `require_age` is later than the civilization's current age. If no recipe is unlocked the status is `No unlocked recipes`.

## Recipe format

```yaml
recipes:
  - id: forage              # needed for 'selected' mode, must be unique per building
    name: Forage
    interval: 240           # optional: the NEXT cycle after this recipe is this long
    optional: true          # dropped silently if items don't exist on this server version
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

### `consume` ingredients

| Form                          | Meaning                                                                                                                                |
| ----------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `{item: X, amount: N}`        | Take `N` of item `X`.                                                                                                                  |
| `{any_of: [A, B], amount: N}` | Take `N` of any one of the listed items.                                                                                               |
| `{group: g, amount: N}`       | Take `N` of any item in an item group from `groups.yml`.                                                                               |
| `{item: X, durability: D}`    | A **tool**: wears down by `D` per cycle and is destroyed when broken. The item must have durability and `D` must be below its maximum. |

An ingredient needs `amount` of at least 1 or `durability` of at least 1. If any input is missing, the recipe does not run and the building status reads `Needs ...`.

### `produce`

`produce` has a `pick` mode and a list of `entries`.

| `pick` | Behaviour                                                                                                                |
| ------ | ------------------------------------------------------------------------------------------------------------------------ |
| `one`  | Exactly **one** entry is chosen, weighted. Weights come from `percent` or `weight`. Any shortfall below 100% is nothing. |
| `all`  | **Each** entry rolls independently on its own `chance` (0 to 1, default always).                                         |

Entry kinds:

```yaml
- {item: SWEET_BERRIES, min: 1, max: 3, percent: 25}   # item, random quantity min..max
- {group: wool_items, min: 1, max: 3, chance: 0.8}     # a random item from a group
- {points: 2}                                          # RP (min/max allowed, 2 decimals)
- {money: 5}                                           # treasury money
- {item: AIR, percent: 55}                             # explicit "nothing"
```

For `pick: one`, percentages must total at most 100, and the remainder is nothing. You can use `weight:` instead of `percent:` (`{item: EGG, weight: 3}`, `{item: AIR, weight: 1}`).

If the recipe produces items but the output container is full, the recipe does **not** run and the building status reads `Output container is full`. Inputs are only consumed once the output is known to fit.

## Worked example: the Hunter's Hut

A barrel-core building (`logistics: {input: CORE, output: CORE}`) with `mode: random` and `interval: 240`.

| Recipe            | Consumes               | Produces (pick one)                                                        |
| ----------------- | ---------------------- | -------------------------------------------------------------------------- |
| Hunt with a bow   | Bow (wears 2), 1 arrow | Beef or porkchop 15% each, mutton 10%, rabbit 15%, chicken 10%, leather 5% |
| Hunt with a sword | Stone sword (wears 3)  | Porkchop 20%, mutton 15%, rabbit 10%                                       |
| Set snares        | 2 sticks, 1 string     | Rabbit 25%, chicken 20%                                                    |

Plus `always: {points: 0.4}` every cycle. Put a bow and arrows in the barrel, and meat appears in the same barrel.

## Worked example: the Marketplace

A `processor` whose recipes consume food and `produce` `{money: N}` with `pick: all`, turning goods into treasury money. For example, 4 bread produces 6 money. Money is subject to your economy modifiers, so under the Barter economy (`money_enabled: false`) it converts to RP.

## Tips

* Keep `interval` realistic. Production runs once per second in a scheduler, but lots of very short intervals add load.
* Use `optional: true` for recipes that need items missing on older Minecraft versions (copper tools before 1.21.9, for example).
* Make output containers large enough, or you will see `Output container is full` and production stops.
