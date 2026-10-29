# Configuration Overview

Ages is almost entirely data-driven. Ages, buildings, governments, economies and policies are plain YAML files that you can edit, add to or delete.

## Loading rules

* **Every definition lives in one file.** The `id` must be lowercase and match the file name for buildings.
* **Files starting with `_` are ignored.** They are references.
* **A broken definition is skipped, not fatal.** The console prints the file and the reason. Everything else loads, and the console reports how many definitions were skipped.
* **Unknown materials** are reported at load time (`unknown material` warnings).
* **Buildings load recursively** from `buildings/<category>/`. A new building appears in the catalogue by itself.
* **Apply changes with `/ages reload`** (needs `ages.admin`) or by restarting.

{% hint style="info" %}
The plugin refuses to start only for fatal registry errors, for example when it cannot read the config at all. Watch the console on first start.
{% endhint %}

## DefaultsSync: how upgrades treat your files

Shipped defaults are kept in step with your data folder **without overwriting your edits**.

| Situation                                                                             | What happens                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Fresh install                                                                         | Every shipped file is installed.                                                                                                                                                                                                   |
| New shipped files in a later release                                                  | Added automatically on the next start. They show up in the catalogue with no code change.                                                                                                                                          |
| A shipped file you deleted on purpose                                                 | Remembered in `defaults.manifest` and **not** restored.                                                                                                                                                                            |
| Shipped defaults change incompatibly (internal content version bump, currently **5**) | Your old default files are **backed up to `plugins/Ages/backup/`**, then replaced. Obsolete defaults listed in `obsolete-defaults.txt` are removed. An old `town.yml` is migrated to `settings.yml` with your values carried over. |
| Files you created yourself                                                            | Never touched.                                                                                                                                                                                                                     |

{% hint style="warning" %}
If you edited a shipped file directly, copy your edit into a differently named file or re-apply it after a content version bump. Look in `backup/` for your previous version.
{% endhint %}

## Cross-file references

| In file              | Field                                               | Refers to                                                      |
| -------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| building             | `age`, `obsolete_in`, `hide_after_age`              | an age `id`                                                    |
| building             | `category`                                          | a category in `shop.yml` (unknown categories are auto-created) |
| building             | `requirements[].group`, `produce`/`consume` `group` | a block group or item group in `groups.yml`                    |
| building             | `replaces`, `conflicts`, `aliases`                  | other building ids                                             |
| age                  | `core_building`                                     | a building id                                                  |
| age                  | `requirements[].building`, `retire`                 | building ids                                                   |
| government           | `policies`                                          | policy ids                                                     |
| economy              | `policies`                                          | policy ids                                                     |
| government / economy | `min_age`                                           | an age id                                                      |
| settings             | `defaults.government`, `defaults.economy`           | government and economy ids                                     |
| settings             | `logistics.min_age`                                 | an age id                                                      |

## Names, colours and text

`name` and `description` fields accept `&` colour codes (`&6Gold`, `&c`, and so on). Descriptions are lists of lines. The first line of an upgrade description is shown in the catalogue.

## Material and entity names

All materials are Bukkit `Material` names (`OAK_LOG`, `IRON_INGOT`). Ages resolves them through `Materials`, which has built-in aliases for historical renames (for example `GRASS` becomes `SHORT_GRASS`). To add your own, create `plugins/Ages/materials-aliases.yml` with one `OLD_NAME: NEW_NAME` pair per line. It is loaded at startup if present.

## Block groups (`groups.yml`)

Groups are reusable sets of blocks that buildings reference with `{group: wall_material, min: 24}`. A group can combine:

* `materials:` exact Bukkit materials
* `tags:` vanilla block tags such as `minecraft:planks` (new blocks in later Minecraft versions are covered automatically)
* `groups:` other groups, to nest

| Group                                                                      | Contents                                                                                                  |
| -------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| `wall_material`                                                            | planks, logs, wool, masonry, hay block, packed mud, bone block, terracotta                                |
| `masonry`                                                                  | cobblestone, stone, stone bricks, bricks, deepslate and blackstone bricks, sandstone, mud bricks and more |
| `roof`                                                                     | stairs and slabs                                                                                          |
| `light_source`                                                             | torches, lanterns, glowstone, campfire, candles, redstone lamp and more                                   |
| `bed`, `door`, `carpet`, `banner`                                          | vanilla tags (beds and doors count 2 blocks each)                                                         |
| `support_beam`                                                             | logs, fences, planks                                                                                      |
| `natural_stone`                                                            | `minecraft:base_stone_overworld` (mines)                                                                  |
| `chest_any`                                                                | chest, trapped chest, barrel                                                                              |
| `furnace_any`                                                              | furnace, blast furnace, smoker                                                                            |
| `rail`                                                                     | `minecraft:rails`                                                                                         |
| `ore_coal`, `ore_iron`, `ore_copper`, `ore_gold`, `ore_gem`, `ore_emerald` | ore blocks, including deepslate variants                                                                  |

The file also defines leaf, crop and furniture groups (`oak_leaves`, `farmland`, `wheat_crop`, `anvil`, `cauldron`, `bookshelf`, `loom`, `brewing`, `redstone_comp` and others) and an `item_groups:` section (for example `wool_items`, which expands to all wool colours) used in recipe `produce` and `consume` entries.

## Catalogue categories (`shop.yml`)

`shop.yml` only configures the **catalogue layout**, not prices. Each category has a `name`, `icon`, an optional main-menu `slot` (10 to 16 and 19 to 25) and a `description`. Shipped categories: `government`, `community`, `food`, `resources`, `industry`, `trade`, `military`, `infrastructure`. A building that names a category missing from the file gets one created automatically with a generic icon.

## Town dumps

Each civilization is mirrored to `plugins/Ages/Towns/<name>.yml` as a human-readable snapshot. The database is the source of truth. Do not edit the dumps.

## Known issues in 0.0.3A

* `buildings.conflict_scope` and `protection.tnt_requires_online` are listed in `settings.yml` but the loader does **not** read them, so their built-in defaults (`town` and `true`) always apply. See [settings.yml Reference](/broken/pages/settings-yml-reference).
* `plugin.yml` lists BlueMap as a soft dependency, but no BlueMap integration ships in this release. Only dynmap is implemented.
* The helper scripts under `tools/` in the source zip still refer to an older layout (`blockgroups.yml` and a flat `buildings/` folder), so `check_config.py` does not match the current file layout without edits.
