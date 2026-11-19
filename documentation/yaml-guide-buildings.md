# YAML Guide: Buildings

A building file defines the **structure** players must build, what it **costs**, where it can go, and what it **does** on a timer. Put it in `plugins/Ages/buildings/<category>/<id>.yml` and run `/ages reload`. The shipped `buildings/_example.yml` is the quick reference. This page explains every key.

## Minimum viable building

Only three keys are required: `id`, `age` and `core_block`. This is a valid, if useless, building:

```yaml
id: myserver_marker
age: neolithic
core_block: LODESTONE
```

Everything else has a default. The sections below build it up.

## The complete annotated template

This file shows **every** option once. Delete what you do not need. Comments say what a line does, its default and its limits.

```yaml
# ---------------------------------------------------------------- identity
id: myserver_example            # REQUIRED. Unique. Lowercase. Match the file name.
name: "&aExample Building"      # Shown in menus. Defaults to the id. Quote it when it has & codes.
description:                    # Lines shown in the catalogue and the building menu.
  - "&7What it does, in one line."
category: community             # Catalogue tab. A key in shop.yml. Unknown keys are created
                                #   automatically. Default: community.
age: tribal                     # REQUIRED. Earliest age it can be bought. Must exist.
obsolete_in: industrial         # Optional. From this age on it can no longer be bought or
                                #   placed. Existing ones keep working.
hide_after_age: medieval        # Optional. Hidden from the catalogue from this age on.
shop: true                      # false = not sold. Give it with /ages give or a recipe. Default true.

# ---------------------------------------------------------------- look
core_block: BARREL              # REQUIRED. Any placeable block. The block the player places.
icon: SWEET_BERRIES             # Menu icon. Defaults to core_block.

# ---------------------------------------------------------------- relationships
aliases: [old_example_id]       # Old ids that now mean this building (saves, other files).
replaces: [tribal_hut]          # Placing this over those buildings retires them atomically.
conflicts: [rival_building]     # Cannot be owned together with these. Scope: buildings.conflict_scope.

# ---------------------------------------------------------------- limits
tags: {housing: 2}              # Shared limit pools. The number is the weight in the pool.
limits:
  max_per_town: 4               # 0 or missing = unlimited. Also accepted: per_town.
  max_per_player: 2             # Also accepted: per_player.

# ---------------------------------------------------------------- structure
scan:                           # The box around the core that is checked.
  x: 5                          # Half-width east and west. 5 = 11 blocks wide. Default 0.
  z: 5                          # Half-width north and south. Defaults to x.
  up: 3                         # Blocks above the core. Default 0.
  down: 1                       # Blocks below the core. Default 0.
  # y: 2                        # Shorthand that sets both up and down.
requirements:                   # Every entry must be met inside the scan box.
  - {group: wall_material, min: 16}   # A block group from groups.yml...
  - {material: CAULDRON, min: 1}      # ...or one exact block. min defaults to 1.

# ---------------------------------------------------------------- price
cost:
  points: 400                   # Resource points. From the civilization.
  money: 250                    # Money. Vault by default, or the treasury (see Economies).
  items: {WHEAT: 4}             # Items from the buyer's inventory. Each at least 1.

# ---------------------------------------------------------------- where
placement: inside               # inside (your land) | anywhere (not on foreign land)
                                #   | foreign (only on another civilization's land).

# ---------------------------------------------------------------- containers
logistics:
  input: CORE                   # CORE = the building's built-in storage, or a container block.
  output: CORE                  # BARREL or CHEST = the nearest one inside the scan box.
  stores: any                   # Storage hubs only: any | food | goods.

# ---------------------------------------------------------------- behaviour
function:
  type: processor               # processor | spawner | tax | sell | siege | none. Default none.
  interval: 300                 # Seconds between cycles. Default 300. Minimum 1.
  mode: random                  # random | first | all | selected. Default random.
  always:                       # Happens every cycle, whether or not a recipe runs.
    - {points: 0.5}
  recipes: []                   # See the recipe section.

# ---------------------------------------------------------------- passive bonuses
provides:
  housing: 4
  claim_bonus: 2

# ---------------------------------------------------------------- upgrades
upgrades:
  - label: "II"                 # Shown in menus. Default is a roman numeral.
    age: medieval               # Earliest age for this level. Default: the building's age.
    cost: {points: 1500, money: 900}
```

## Identity and appearance

| Key              | Required | Default      | Notes                                                                                                                                                                                                                                                                    |
| ---------------- | -------- | ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `id`             | yes      | none         | Lowercase. A blank id skips the file. Duplicate ids are skipped.                                                                                                                                                                                                         |
| `name`           | no       | the id       | Supports `&` colour codes.                                                                                                                                                                                                                                               |
| `description`    | no       | none         | A list of lines.                                                                                                                                                                                                                                                         |
| `category`       | no       | `community`  | Must be a catalogue category. The shipped ones are `government`, `community`, `food`, `resources`, `industry`, `trade`, `military` and `infrastructure`. A new name creates a new tab automatically with a chest icon. Add it to `shop.yml` to choose the icon and slot. |
| `age`            | **yes**  | none         | The building is skipped if this age does not exist.                                                                                                                                                                                                                      |
| `obsolete_in`    | no       | never        | Stops purchase and placement from this age on.                                                                                                                                                                                                                           |
| `hide_after_age` | no       | never        | Hides it from the catalogue but it can still be given.                                                                                                                                                                                                                   |
| `shop`           | no       | `true`       | `false` hides it from the catalogue entirely.                                                                                                                                                                                                                            |
| `core_block`     | **yes**  | none         | Must be a placeable block. Any block works, because the building's storage is built in.                                                                                                                                                                                  |
| `icon`           | no       | `core_block` | Any item.                                                                                                                                                                                                                                                                |

## Structure: `scan` and `requirements`

The **scan box** is a box centred on the core. `scan: {x: 5, z: 5, up: 3, down: 1}` checks 11 x 11 blocks horizontally, 3 above and 1 below, plus the core. Without a `scan` the radius is 0 and only the core block is checked. Every value must be at most `buildings.max_scan_radius` (20 by default).

Each requirement is either a **block group** (see `groups.yml` for the list) or one **exact block**:

```yaml
requirements:
  - {group: wall_material, min: 24}   # any of the blocks in the group, 24 or more
  - {group: door, min: 2}             # a door counts 2 blocks (top and bottom)
  - {material: LECTERN, min: 1}
```

* `min` must be at least 1.
* Beds and doors are two blocks each, so `min: 2` means one bed or one door.
* An unknown group or material skips the building.
* Players can run `/ages particles` to see the scan box, so choose a size they can actually build.

{% hint style="info" %}
**Best practice:** make the box slightly larger than the smallest valid build. Players like some room, and a tight box makes the requirements feel like a puzzle. Use groups instead of exact blocks so players can choose their materials.
{% endhint %}

## Price: `cost`

| Key                     | Meaning                                                                                                                                                             |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `points`                | Resource points from the civilization. Needs `SPEND_POINTS`.                                                                                                        |
| `money` (or `treasury`) | Money. Paid from the buyer's Vault balance, or from the treasury where the economy or government says so. Dropped under Barter, where RP costs are doubled instead. |
| `items`                 | Items taken from the buyer's inventory. Never scaled by cost modifiers.                                                                                             |

Costs are never negative. `building_cost_mult` from the government, economy and policies scales `points` and `money`.

## Limits and tags

```yaml
limits: {max_per_town: 4, max_per_player: 2}
tags: {housing: 2}
```

* `limits` caps this building on its own. `0` or missing means no cap at this level.
* `tags` put the building into a **shared pool**. The number is how much each building counts. Pool caps are set per age in `tag_limits` and globally in `settings.yml`. The strictest non-zero cap wins.
* `tags` can be a map (`{housing: 2}`), a list (`[housing]`, weight 1) or a single word.
* The tag `aimable` is special: it makes cannons prefer this building as a target.

## Where it can go: `placement`

| Value      | Rule                                                                                         |
| ---------- | -------------------------------------------------------------------------------------------- |
| `inside`   | In your own land. Counts toward claim growth.                                                |
| `anywhere` | In the wilderness or your own land, never on another civilization's land. Never claims land. |
| `foreign`  | Only inside **another** civilization's borders. Never claims land.                           |

## Containers: `logistics`

Processors, spawners, sellers and cannons read ingredients from an **input** and write products to an **output**.

| Value                                         | Meaning                                                                                                        |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| `CORE`                                        | The building's own built-in 27-slot storage. Players open it with **Open storage**. Works with any core block. |
| `BARREL`, `CHEST`, and other container blocks | The nearest real container of that type inside the scan box. It must be part of the player's build.            |

* Using the same material for input and output picks the two nearest.
* A building that **consumes** something needs an input. One that outputs **items** needs an output. The loader tells you if one is missing.
* `stores` matters only for storage hubs (`any`, `food`, `goods`).

## Behaviour: `function`

| Key               | Default    | Applies to          | Meaning                                                        |
| ----------------- | ---------- | ------------------- | -------------------------------------------------------------- |
| `type`            | `none`     | all                 | `processor`, `spawner`, `tax`, `sell`, `siege` or `none`.      |
| `interval`        | `300`      | all                 | Seconds between cycles. Minimum 1. Speed modifiers shorten it. |
| `mode`            | `random`   | processor, siege    | `random`, `first`, `all` or `selected`. See below.             |
| `always`          | none       | all                 | Entries that fire every cycle.                                 |
| `recipes`         | none       | processor, siege    | The recipes.                                                   |
| `entity`          | none       | spawner             | An entity type such as `VILLAGER`. Required for spawners.      |
| `cap`             | `1`        | spawner             | Maximum alive at once. Minimum 1.                              |
| `housing_limited` | `false`    | spawner             | Stop spawning when civilization housing is full.               |
| `consume`         | none       | spawner             | Ingredients taken per spawn.                                   |
| `per_resident`    | `0`        | tax                 | Money per resident per cycle.                                  |
| `max_stored`      | `1000`     | all that earn money | Cap on income kept on the building.                            |
| `payout`          | `treasury` | tax                 | `owner` always stores all income on the building.              |
| `prices`          | none       | sell                | A map of item to money each.                                   |
| `max_items`       | `64`       | sell                | Most items sold per cycle.                                     |

A **processor** must have at least one recipe or one `always` entry. `mode: selected` needs at least one recipe.

### Recipe modes

| Mode       | Each cycle                                                                                           |
| ---------- | ---------------------------------------------------------------------------------------------------- |
| `random`   | Shuffle the recipes and run the first that can.                                                      |
| `first`    | Try recipes in file order and run the first that can. Good for tiers where the best tool should win. |
| `all`      | Run every recipe that can.                                                                           |
| `selected` | Run the product the owner chose in the Production panel, with fallbacks.                             |

## Recipes

```yaml
recipes:
  - id: gather_herbs           # Unique in this building. Default r1, r2, ... Needed for selected mode.
    select_as: herbs           # Optional. Recipes with the same value form one menu product (tiers).
    name: Gather herbs         # Shown in the Production panel. Defaults to the id.
    interval: 240              # Optional. Seconds until the NEXT cycle after this recipe.
    require_level: 1           # Minimum building level. Default 1. Higher levels still run it.
    require_age: village       # Minimum age. Higher ages still run it.
    consume:
      - {item: BONE_MEAL, amount: 1}
    produce:
      pick: one                # one (default) or all
      entries:
        - {item: BROWN_MUSHROOM, min: 1, max: 3, percent: 40}
        - {item: AIR, percent: 60}
```

A recipe needs at least a `consume` or a `produce` entry. A recipe with only `consume` is valid, which is how cannon shells work. Recipe ids must not repeat.

### `consume` ingredients

| Form                          | Meaning                                                                                                                                              |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| `{item: X, amount: N}`        | Take `N` of one item. Amount defaults to 1 and is at most 2304.                                                                                      |
| `{any_of: [A, B], amount: N}` | Take `N` of any one of the listed items.                                                                                                             |
| `{group: g, amount: N}`       | Take `N` of any item in an item group from `groups.yml`.                                                                                             |
| `{item: X, durability: D}`    | A **tool**: it wears by `D` each cycle. The item must have durability, and `D` must be below its maximum. Use `any_of` to accept several tool tiers. |

### `produce` entries

| Entry                        | Meaning                                                               |
| ---------------------------- | --------------------------------------------------------------------- |
| `{item: X, min: 1, max: 3}`  | A random quantity of an item. `min` defaults to 1 and `max` to `min`. |
| `{group: g, min: 1, max: 3}` | A random item from an item group.                                     |
| `{points: 2}`                | Resource points (`min` and `max` allowed).                            |
| `{money: 5}`                 | Money, routed by the economy.                                         |
| `{item: AIR}`                | An explicit "nothing".                                                |

For `pick: one` an entry is chosen by weight. Give every entry a `percent`, or give some a `weight` and leave `percent` out. **Do not mix them.** Percentages must total at most 100, and the remainder becomes "nothing". For `pick: all`, every entry rolls on its own `chance` (0 to 1) and `percent` is not allowed.

Products need an output container. Inputs and ingredients need an input container.

### `always`

`always` uses the same entry forms and every entry rolls independently with `chance`. Use it for a steady trickle of points or money:

```yaml
always:
  - {points: 1.4}                       # every cycle
  - {item: FEATHER, min: 1, chance: 0.3} # a 30% chance of a feather
```

## Function types in depth

### `processor`

The general case: ingredients in, products out. See the Herb Garden and Stone Yard examples.

### `spawner`

```yaml
function:
  type: spawner
  interval: 900
  entity: VILLAGER       # any entity type
  cap: 2                 # at most 2 alive from this building
  housing_limited: true  # stop when civilization housing is full
  consume:
    - {item: BREAD, amount: 1}   # optional cost per spawn
```

Spawned entities are tracked and persistent. Iron golems are spawned as player-created, so they defend but never attack players.

### `tax`

```yaml
function: {type: tax, interval: 600, per_resident: 0.3, max_stored: 5000}
```

Earns `per_resident` times the civilization's population every cycle as money, routed like any building income.

### `sell`

```yaml
function:
  type: sell
  interval: 300
  max_items: 32
  prices: {COD: 0.4, SALMON: 0.6}
```

Sells up to `max_items` from the input storage each cycle and pays the total as money. Prices cannot be negative.

### `siege`

A cannon. Shells are recipes that only `consume`:

```yaml
logistics: {input: CORE}
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
siege:               # a separate top-level block
  range: 32          # blocks. Minimum 8.
  height: 8          # arc height. Minimum 2.
  yield: 3.5         # explosion power. Minimum 1.0.
  cooldown_seconds: 180   # replaces function.interval. Minimum 10.
```

The `siege:` block is read **only at the top level** of the building. A `siege:` block inside an upgrade is ignored. Upgrade levels change a cannon in two ways: an upgrade's `function.interval` shortens the reload, and the plugin itself adds a level bonus of **+16 range and +4 arc height for each level above I, and +0.5 yield per level**. See [Diplomacy and War](diplomacy-and-war.md).

## Passive bonuses: `provides`

| Key                      | Effect                                                                 |
| ------------------------ | ---------------------------------------------------------------------- |
| `housing: N`             | Villager housing.                                                      |
| `respawn_point: true`    | Members can respawn here.                                              |
| `claim_bonus: N`         | Extra claim radius.                                                    |
| `storage: N`             | Makes it a logistics hub.                                              |
| `logistics_capacity: N`  | Extra items per rail run.                                              |
| `production_speed: 0.05` | Civilization-wide speed bonus, capped by `production.max_speed_bonus`. |
| `withdraw_limit: N`      | Raises the daily treasury withdrawal limit.                            |

Booleans count as 1 or 0. Unknown keys are stored but do nothing. Disabled buildings provide nothing.

## Upgrades

Upgrades are numbered from level 2. They are **cumulative**: a level keeps everything from the levels below.

| Key                                                                             | Behaviour                                                                |
| ------------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| `label`                                                                         | Menu label. Defaults to a roman numeral.                                 |
| `age`                                                                           | Earliest age for this level. Defaults to the building's age. Must exist. |
| `cost`                                                                          | Price of this upgrade, same keys as the building cost.                   |
| `scan`                                                                          | Replaces the scan box. Leave out any axis you do not want to change.     |
| `requirements`                                                                  | The same group is raised to the new minimum. New groups are added.       |
| `function.recipes`                                                              | **Added** to the previous recipes. Only for processors.                  |
| `function.always`                                                               | **Added** to the previous entries.                                       |
| `function.interval`, `cap`, `max_items`, `per_resident`, `max_stored`, `prices` | Replace the previous value.                                              |
| `provides`                                                                      | Values are **added**.                                                    |
| `description`                                                                   | Lines shown for the upgrade. The first appears in the catalogue.         |

An upgrade can only change a function the building already has.

{% hint style="warning" %}
Because `always` entries are **added**, an upgrade that repeats `{points: 0.5}` makes the building earn `1.0` in total, not `0.5`. Only list the **extra** amount.
{% endhint %}

## Worked examples

Each file below is valid for 0.0.59 and uses only blocks and items that exist on every supported version.

### A simple gatherer: Herb Garden

```yaml
id: herb_garden
name: "&aHerb Garden"
description:
  - "&7A small plot that turns bone meal into herbs and points."
category: food
age: village
core_block: COMPOSTER
icon: COMPOSTER
tags: {farming: 1}                 # counts toward the farming pool
scan: {x: 4, z: 4, up: 3, down: 1}
requirements:
  - {group: farmland, min: 6}
  - {group: wall_material, min: 8}
  - {group: light_source, min: 1}
cost:
  points: 600
  money: 400
limits: {max_per_town: 3, max_per_player: 1}
placement: inside
logistics: {input: CORE, output: CORE}   # built-in storage for both
function:
  type: processor
  interval: 300
  mode: random
  always:
    - {points: 0.5}                # a small steady income
  recipes:
    - id: gather_herbs
      name: Gather herbs
      consume:
        - {item: BONE_MEAL, amount: 1}
      produce:
        pick: one                  # exactly one outcome per cycle
        entries:
          - {item: BROWN_MUSHROOM, min: 1, max: 3, percent: 40}
          - {item: RED_MUSHROOM, min: 1, max: 2, percent: 25}
          - {item: SWEET_BERRIES, min: 2, max: 4, percent: 25}
          - {item: AIR, percent: 10}       # 10% of cycles produce nothing
upgrades:
  - label: "II"
    age: medieval
    cost: {points: 1500, money: 1000}
    scan: {x: 5, z: 5, up: 3, down: 1}
    function:
      interval: 240                # faster
      always:
        - {points: 0.5}            # ADDED: total is now 1.0 per cycle
```

### Tiers with one menu choice: Stone Yard

```yaml
id: stone_yard
name: "&7Stone Yard"
description:
  - "&7Quarries cobblestone. Better pickaxes in the storage mean more stone."
category: resources
age: village
core_block: STONECUTTER
tags: {mining: 1}
scan: {x: 4, z: 4, up: 3, down: 1}
requirements:
  - {group: natural_stone, min: 8}
  - {group: support_beam, min: 4}
  - {group: light_source, min: 1}
cost: {points: 900, money: 600}
limits: {max_per_town: 2, max_per_player: 1}
placement: anywhere                # may be built in the wilderness
logistics: {input: CORE, output: CORE}
function:
  type: processor
  interval: 240
  mode: selected                   # the owner picks a product
  recipes:
    - id: cobble_stone_pick
      select_as: cobble            # both tiers appear as one product: Cobblestone
      name: Cobblestone
      require_level: 1
      consume:
        - {item: STONE_PICKAXE, durability: 2}   # a tool: wears by 2 per cycle
      produce:
        pick: one
        entries:
          - {item: COBBLESTONE, min: 4, max: 8, percent: 100}
    - id: cobble_iron_pick
      select_as: cobble
      name: Cobblestone
      require_level: 2             # unlocked by the level II upgrade
      consume:
        - {item: IRON_PICKAXE, durability: 2}
      produce:
        pick: one
        entries:
          - {item: COBBLESTONE, min: 10, max: 16, percent: 100}
upgrades:
  - label: "II"
    age: medieval
    cost: {points: 4000, money: 2500}   # unlocks the iron tier through require_level
```

The building uses the best pickaxe it finds. If the owner stocks only stone pickaxes it keeps working at tier 1.

### Housing that trains villagers: Hostel

```yaml
id: hostel
name: "&6Hostel"
description: ["&7Houses and trains villagers, one per cycle, for a loaf of bread each."]
category: community
age: village
core_block: LECTERN
tags: {housing: 1}
scan: {x: 4, z: 4, up: 3, down: 1}
requirements:
  - {group: wall_material, min: 20}
  - {group: door, min: 2}
  - {group: bed, min: 4}
  - {group: light_source, min: 2}
cost: {points: 1200, money: 800}
limits: {max_per_town: 4, max_per_player: 2}
logistics: {input: CORE}           # bread is taken from the built-in storage
provides: {housing: 4}             # adds 4 to civilization housing
function:
  type: spawner
  interval: 900
  entity: VILLAGER
  cap: 2                           # at most two alive from this building
  housing_limited: true            # stops when housing is full
  consume:
    - {item: BREAD, amount: 1}
```

### A tax building: Customs House

```yaml
id: customs_house
name: "&eCustoms House"
description: ["&7Collects a small tax from every resident."]
category: trade
age: medieval
core_block: BELL
scan: {x: 4, z: 4, up: 3, down: 1}
requirements:
  - {group: masonry, min: 20}
  - {group: door, min: 2}
  - {group: banner, min: 1}
cost: {points: 5000, money: 3000}
limits: {max_per_town: 1}
function:
  type: tax
  interval: 600
  per_resident: 0.3                # money per resident per cycle
  max_stored: 5000                 # most income kept on the building
```

### A market: Fish Market

```yaml
id: fish_market
name: "&bFish Market"
description: ["&7Buys fish from its storage and pays money."]
category: trade
age: village
core_block: BARREL
scan: {x: 4, z: 4, up: 3, down: 1}
requirements:
  - {group: wall_material, min: 16}
  - {group: roof, min: 6}
cost: {points: 800, money: 500}
limits: {max_per_town: 2}
logistics: {input: CORE}
function:
  type: sell
  interval: 300
  max_items: 32                    # at most 32 fish per cycle
  prices: {COD: 0.4, SALMON: 0.6}  # money each
```

## Balancing and pricing notes

* **Work out the payback.** Price in RP divided by RP per cycle, times the interval, gives seconds to repay. A 600 RP building earning 0.5 RP every 300 s repays in 100 hours from `always` alone, so the recipes should carry most of the value.
* **Compare with contributions.** `/ages contribute list` shows what a stack of items is worth in RP. A recipe that turns cheap items into expensive ones is a printing press.
* **Put strong buildings behind a limit** (`limits`, a `tags` pool, or an age `building_limits`).
* **Gate with age and level.** Use `age`, `require_age` and `require_level` instead of raising prices.
* **Use `first` mode for tool tiers** when the best tool should always win, and `selected` with `select_as` when the owner should choose what to make.
* **Keep scan boxes buildable.** `/ages particles` shows players the box.
* **Do not rely on `optional`.** Every item name must exist on the server version you support.

## Messages you may see

| Console message                                       | Fix                                                 |
| ----------------------------------------------------- | --------------------------------------------------- |
| `missing 'id'`                                        | Add an `id`.                                        |
| `unknown material 'X'`                                | Fix the name or remove the item.                    |
| `core_block X is not a placeable block`               | Pick a block, not an item.                          |
| `unknown block group 'x'`                             | Use a group from `groups.yml`.                      |
| `scan radius exceeds buildings.max_scan_radius`       | Lower the scan or raise the setting.                |
| `limits cannot be negative`                           | Use 0 or more.                                      |
| `placement must be inside, anywhere, or foreign`      | Fix the spelling.                                   |
| `logistics.input X is not a container block`          | Use `CORE`, `BARREL`, `CHEST` or another container. |
| `unknown function type` / `unknown recipe mode`       | Use one of the listed values.                       |
| `duplicate recipe id`                                 | Make recipe ids unique.                             |
| `percent values total N%, which is more than 100%`    | Lower the percentages.                              |
| `percent must be given on every entry or on none`     | Add or remove `percent` consistently.               |
| `do not mix weight and percent`                       | Use one style.                                      |
| `spawner function needs an 'entity'`                  | Add `entity:`.                                      |
| `processor has no recipes and no 'always' output`     | Add a recipe or an `always` entry.                  |
| `inputs/consumption need a logistics.input container` | Add `logistics.input`.                              |
| `item outputs need a logistics.output container`      | Add `logistics.output`.                             |
| `an upgrade uses age 'x' which does not exist`        | Fix the upgrade's `age`.                            |
