# Buildings

Buildings are the heart of Ages. A building is a **core block** surrounded by a **structure** that satisfies a list of block requirements. Once registered it can produce, tax, trade, house villagers, extend your claim or provide passive bonuses.

There are **64 shipped buildings** in eight categories. See the [Building Catalogue](building-catalogue.md). Production recipes are explained in [Building Functions and Recipes](building-functions-and-recipes.md).

## Lifecycle

1. **Buy.** The cost is modified by `building_cost_mult` from the government, economy and policies. It is paid as follows:
   * **RP** from the civilization (needs `SPEND_POINTS`).
   * **Items** from the buyer's inventory. Item costs are never scaled.
   * **Money** from the buyer's **Vault balance** by default, or from the **treasury** (needs `SPEND_TREASURY`) when the economy has `charge_purchases_to_civ: true` or the government has `force_treasury_purchases: true`.
   * In an economy **without money** (Barter) money costs are dropped and RP costs are multiplied by `economy.no_money_purchase_points_mult` (default 2.0).
2. **Build.** Construct the structure inside the building's `scan` box.
3. **Place the core.** The scan box is centred on the core. Ages counts blocks per `requirements`, then checks limits, conflicts, age, placement and claim rules.
4. **Running.** The building appears in `/ages buildings`. Its function runs on a timer that survives restarts. Right-click the core to open its menu.
5. **Collect income.** Money the building earned and kept shows in its core menu as `Collect income`. Clicking pays it into your own Vault balance. See [Economies](economies.md).
6. **Disabled.** If the structure is broken below its requirements, the building is disabled. It stops producing, and 50% of its per-cycle `always` points are subtracted from the civilization as ongoing decay. Members who can manage buildings, as well as the owner, are told when this happens. Restore the structure to repair it. Repairs cost 10% of the purchase price, multiplied by `repair_cost_mult`. Disabled buildings still count toward **age requirements**.
7. **Upgrade or remove.** Upgrades add on top of the base definition. Removing a building needs `REMOVE_BUILDINGS` for your own, `MANAGE_BUILDINGS` for others'.

## The building menu

The core menu shows structure status and requirements, upgrades, storage access, collected income and relocation. Buildings that run recipes have a separate **Production** panel (a crafting table in the core menu) that shows recipes, timing and status. In `selected` mode the owner or a manager picks the active recipe there.

## Scan box

```yaml
scan: {x: 5, z: 5, up: 3, down: 1}
```

Radii are measured from the core and mirrored on each horizontal axis: `x: 5` makes the box 11 blocks wide. `z` defaults to `x`, and `up` and `down` are separate. Every value must be at most `buildings.max_scan_radius` (default 20, a 41 x 41 x 41 box), or the building is skipped at load.

## Requirements

```yaml
requirements:
  - {group: wall_material, min: 24}
  - {group: door, min: 2}
  - {material: CAULDRON, min: 1}
```

Each entry needs either `group` (from `groups.yml`) or `material`, and `min` of at least 1. An unknown group is a load error that skips the building. Beds and doors count two blocks each. `requirements: []` (the Fireplace) means no structure.

## Placement

| `placement`        | Rule                                                                                                                                                                                        | Claims                           |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------- |
| `inside` (default) | The core must be inside your own civilization's land.                                                                                                                                       | Counts toward claim growth.      |
| `anywhere`         | Wilderness or your own land is allowed. It is **refused on another civilization's land**. Used by mines, quarries, pits, the Forager, Fisher's Hut, Hunter's Hut, Herbalist and Woodcutter. | Does **not** expand claims.      |
| `foreign`          | Must be inside **another** civilization's borders, never in wilderness and never on your own land. Used by the Embassy.                                                                     | Does **not** expand your claims. |

Any other value is a load error (`placement must be inside, anywhere, or foreign`).

## Limits

Three layers are checked together, and **the strictest non-zero cap wins**:

1. **The building itself**: `limits.max_per_town` and `limits.max_per_player` (`per_town` and `per_player` also work), plus the age's `building_limits`.
2. **Shared tag pools**: a building lists `tags: {housing: 2}` and counts toward each tag with that weight. Caps live in `settings.yml` (`limits.tags`) and in the age files (`tag_limits`). A tag with no cap anywhere is unlimited.
3. **Per-civilization caps**, so a big civilization cannot dodge limits by having every member place the maximum.

The `tag_limit_add.<tag>` effect raises tag caps. For a per-town cap the full bonus is added. For a per-player cap, half the bonus (at least 1) is added, when the bonus is positive.

`0` or absent means unlimited at that level.

## Conflicts, replacements and aliases

* `conflicts: [id, ...]`: cannot be owned together with the listed buildings. The scope is set by `buildings.conflict_scope` (`town` or `player`).
* `replaces: [id, ...]`: placing this building over buildings of those types retires them **transactionally**. This is how the Townhouse merges Cottages and each age core replaces the previous one.
* `aliases: [id, ...]`: old ids in saved data and older configs resolve to this building, and they count for age requirements. The shipped `mine` lists `coal_mine`, `copper_mine`, `iron_mine`, `gold_mine`, `emerald_mine` and `stone_quarry`.

## Age visibility

| Field            | Effect                                                                              |
| ---------------- | ----------------------------------------------------------------------------------- |
| `age`            | Earliest age at which it appears in the catalogue. **Required.**                    |
| `obsolete_in`    | From this age on it can no longer be bought or placed. Existing ones keep working.  |
| `hide_after_age` | Hidden from the shop at and after this age, but still giveable. Existing ones stay. |
| `shop: false`    | Not sold at all, given by command or recipe only (for example the Fireplace).       |

A building whose `age` does not exist, or was skipped, is itself skipped. Ages are skipped when their core building or a required building is missing, and that cascades.

## Containers and logistics

Processors and spawners read inputs from, and write outputs to, containers:

```yaml
logistics: {input: CORE, output: CORE}
```

* `CORE`: the core block itself, which must be a storage block (chest, barrel and similar). Output and input can both be the core. The Woodcutter's core is a **chest**, so it can use `CORE`.
* A material name (`BARREL`, `CHEST`): the nearest such container block found **inside the building area**. If input and output are the same type, the two nearest are used.
* `logistics.stores: any | food | goods`: what kind of goods a storage hub parks.

The core must be a storage block when you use `CORE`, or the file is skipped with an error.

## Passive bonuses (`provides`)

| Key                      | Effect                                                                              |
| ------------------------ | ----------------------------------------------------------------------------------- |
| `housing: N`             | Villager housing capacity. Spawners stop at the civilization's total.               |
| `respawn_point: true`    | Members can respawn here (see `respawn.*`).                                         |
| `claim_bonus: N`         | Extra claim radius.                                                                 |
| `storage: N`             | Marks a logistics hub: rail logistics park surplus here and pull shortages from it. |
| `logistics_capacity: N`  | Extra items per logistics run on its network.                                       |
| `production_speed: 0.10` | Civilization-wide speed bonus (additive, capped by `production.max_speed_bonus`).   |
| `withdraw_limit: N`      | Raises the daily treasury withdrawal limit.                                         |

Disabled buildings do not provide bonuses.

## Upgrades

```yaml
upgrades:
  - label: "II"
    age: medieval
    cost: {points: 8400, money: 7900}
    description: ["First line shows in the catalogue."]
    scan: {x: 6, z: 6, up: 4, down: 1}
    requirements: [{group: furnace_any, min: 2}]
    function:
      interval: 240
      recipes: []
      always: [{points: 1}]
    provides: {housing: 2}
```

Upgrades are **cumulative** on top of the previous level:

* `age` is the earliest age for the level, and may not go backwards between levels. An upgrade that names an unknown age gets its building skipped.
* `scan` replaces the scan box.
* `requirements`: the same group is raised to the new minimum, new groups are added.
* `function` overrides `interval`, `cap`, `max_items`, `per_resident`, `max_stored` and `prices`. `recipes` and `always` are **added** to the previous ones.
* `provides` values are **added**.
* A cannon's `siege:` block is replaced per level.
* Recipes can be gated with `require_level` (the building's upgrade level) and `require_age`. Both are **minimums**: a higher level or age still runs the recipe.

## Building file reference

```yaml
id: example_forager             # REQUIRED. lowercase, matches the file name
name: "&aExample Forager"
description: ["Line 1", "Line 2"]
category: food                  # a key in shop.yml, unknown ones are auto-created
age: tribal                     # REQUIRED
core_block: BARREL              # REQUIRED. any placeable block
icon: SWEET_BERRIES             # defaults to core_block
shop: true
obsolete_in: industrial
hide_after_age: medieval
conflicts: [rival_building_id]
aliases: [old_forager_id]
tags: {housing: 2}
scan: {x: 5, z: 5, up: 3, down: 1}
requirements:
  - {group: wall_material, min: 16}
cost: {points: 400, money: 0, treasury: 0, items: {WHEAT: 4}}
limits: {max_per_town: 2, max_per_player: 1}
placement: inside               # inside | anywhere | foreign
replaces: [tribal_hut]
logistics: {input: CORE, output: CORE}
function: {type: processor, interval: 300, mode: random}
provides: {housing: 4}
upgrades: []
```

| Field           | Required | Notes                                                                                                                           |
| --------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------- |
| `id`            | yes      | Missing id skips the file. Must match the file name.                                                                            |
| `age`           | yes      | Age id.                                                                                                                         |
| `core_block`    | yes      | Must be a placeable block, otherwise load error.                                                                                |
| `cost.points`   | no       | RP from the civilization. Spending needs `SPEND_POINTS`.                                                                        |
| `cost.treasury` | no       | Treasury money. Spending needs `SPEND_TREASURY`.                                                                                |
| `cost.money`    | no       | Money. Paid from the buyer's Vault, or from the treasury in treasury-purchase economies. Nearly every shipped building uses it. |
| `cost.items`    | no       | Items taken from the buyer's inventory. Each amount at least 1.                                                                 |

Costs cannot be negative.

{% hint style="info" %}
Copy `buildings/_example.yml` as a starting point. A definition with an error is skipped with a clear console message and everything else still loads. Errors you may meet include `unknown block group`, `scan radius exceeds buildings.max_scan_radius`, `core_block ... is not a placeable block`, `placement must be inside, anywhere, or foreign`, `logistics CORE needs a storage block`, and `unknown function type`.
{% endhint %}

## Siege (cannons)

The Cannon carries a `siege:` block: `range`, `height`, `cooldown_seconds` and `yield`. See [Diplomacy and War](diplomacy-and-war.md).
