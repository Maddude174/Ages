# Configuration Overview

Ages is almost entirely data-driven. Ages, buildings, governments, economies, policies and contribution values are plain YAML files that you can edit, add to or delete.

## Loading rules

* **Every definition lives in one file.** The `id` must be lowercase and match the file name for buildings.
* **Files starting with `_` are ignored.** They are references with every option explained.
* **A broken definition is skipped, not fatal.** The console prints the file and the reason. Everything else loads, and the console reports how many definitions were skipped.
* **Skips cascade.** An age that references a missing building is skipped, and every building that belongs to a skipped age is skipped with it.
* **A few errors are fatal.** The plugin refuses to start when there are no valid ages, when `settings.yml` or `contributions.yml` cannot be read, or when `contributions.yml` names an unknown material.
* **Unknown tags or materials in `groups.yml`** are `(ignored)` warnings, so a group still loads with the entries that do exist.
* **Buildings load recursively** from `buildings/<category>/`. A new building appears in the catalogue by itself.
* **Apply changes with `/ages reload`** (needs `ages.admin`) or by restarting.

## Defaults: how upgrades treat your files

Shipped defaults carry a content version, currently **31**. What happens depends on what is already in your data folder.

| Situation                                           | What happens                                                                                                                                                                                                                                            |
| --------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Fresh install                                       | Every shipped file is installed.                                                                                                                                                                                                                        |
| Same defaults version, new shipped files            | Files not yet in `defaults.manifest` are added on the next start. They show up in the catalogue with no code change.                                                                                                                                    |
| A shipped file you deleted on purpose               | Remembered in `defaults.manifest` and **not** restored, as long as the defaults version has not changed.                                                                                                                                                |
| **Defaults version bump** (any older version to 31) | Every shipped file, **including `settings.yml` and `contributions.yml`**, is backed up to `backup/defaults-v<old>-<timestamp>/` and **replaced** from the jar. Values are **not merged**. Obsolete files listed in `obsolete-defaults.txt` are removed. |
| Legacy `town.yml`                                   | Backed up and deleted. It is not migrated into `settings.yml`.                                                                                                                                                                                          |
| Files you created yourself                          | Never touched.                                                                                                                                                                                                                                          |

{% hint style="danger" %}
If you edited a shipped file, your edit disappears from the live folder on the first start after a defaults version bump. Re-apply it from the backup, or keep your changes in new files with new ids (for example a copy of a building under a different id).
{% endhint %}

### Restoring shipped files (`/ages admin regen`)

Admins can restore shipped files at any time, which is the quickest way to undo a bad edit:

```
/ages admin regen buildings
/ages admin regen governments
/ages admin regen mine
/ages admin regen buildings/resources/mine.yml
/ages admin regen settings
/ages admin regen all
```

Categories are `ages`, `buildings`, `governments` (`gov`), `economies` (`eco`), `policies`. Root files are `settings`, `contributions`, `groups` and `shop`. `all` restores every one of those. A single file can be named by id (`mine`) or by path. If an id matches more than one file, you are asked to use the full path. The existing file is copied to `backup/regen-<timestamp>/` first, and you must run `/ages reload` afterwards.

## Cross-file references

| In file              | Field                                               | Refers to                                                      |
| -------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| building             | `age`, `obsolete_in`, `hide_after_age`              | an age `id`                                                    |
| building             | `category`                                          | a category in `shop.yml` (unknown categories are auto-created) |
| building             | `requirements[].group`, `produce`/`consume` `group` | a block group or item group in `groups.yml`                    |
| building             | `replaces`, `conflicts`, `aliases`                  | other building ids                                             |
| age                  | `core_building`                                     | a building id                                                  |
| age                  | `requirements[].building`, `retire`                 | building ids                                                   |
| government           | `policies`                                          | policy ids of `kind: government`                               |
| economy              | `policies`                                          | policy ids of `kind: economy`                                  |
| government / economy | `min_age`                                           | an age id                                                      |
| settings             | `defaults.government`, `defaults.economy`           | government and economy ids                                     |
| contributions        | item names                                          | Minecraft material names                                       |

## Names, colours and text

`name` and `description` fields accept `&` colour codes (`&6Gold`, `&c`, and so on). Descriptions are lists of lines. The first line of an upgrade description is shown in the catalogue.

## Material and entity names

All materials are Bukkit `Material` names (`OAK_LOG`, `IRON_INGOT`). Ages has built-in aliases for historical renames (for example `GRASS` becomes `SHORT_GRASS`). To add your own, create `plugins/Ages/materials-aliases.yml` with one `OLD_NAME: NEW_NAME` pair per line. It is loaded at startup if present. See [Compatibility and Requirements](compatibility-and-requirements.md) for what happens when a name is unknown.

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
| `window`                                                                   | glass, glass panes, stained glass panes and tinted glass                                                  |
| `logs`                                                                     | log blocks                                                                                                |
| `bed`, `door`, `carpet`, `banner`                                          | vanilla tags (beds and doors count 2 blocks each)                                                         |
| `support_beam`                                                             | logs, fences, planks                                                                                      |
| `natural_stone`                                                            | `minecraft:base_stone_overworld` (mines)                                                                  |
| `chest_any`                                                                | chest, trapped chest, barrel                                                                              |
| `furnace_any`                                                              | furnace, blast furnace, smoker                                                                            |
| `rail`                                                                     | `minecraft:rails`                                                                                         |
| `ore_coal`, `ore_iron`, `ore_copper`, `ore_gold`, `ore_gem`, `ore_emerald` | ore blocks, including deepslate variants                                                                  |

The file also defines leaf, crop and furniture groups (`oak_leaves`, `farmland`, `wheat_crop`, `anvil`, `cauldron`, `bookshelf`, `loom`, `brewing`, `redstone_comp`, `iron_bars` and others) and an `item_groups:` section (for example `wool_items`, which expands to all wool colours) used in recipe `produce` and `consume` entries.

## Catalogue categories (`shop.yml`)

`shop.yml` only configures the **catalogue layout**, not prices. Each category has a `name`, `icon`, an optional main-menu `slot` (10 to 16 and 19 to 25) and a `description`. Shipped categories: `government`, `community`, `food`, `resources`, `industry`, `trade`, `military`, `infrastructure`. A building that names a category missing from the file gets one created automatically with a generic icon.

## Runtime data folders

| Folder           | Contents                                                                       | Edit it?                         |
| ---------------- | ------------------------------------------------------------------------------ | -------------------------------- |
| `Towns/`         | A readable snapshot of each civilization. The database is the source of truth. | No                               |
| `core-inv/`      | The contents of each building's built-in storage.                              | No                               |
| `pending-cores/` | Cores parked in each civilization's Core storage.                              | No                               |
| `backup/`        | Files saved before an upgrade or `regen` replaced them.                        | Read only, to recover your edits |

Back up `ages.db`, `core-inv/` and `pending-cores/` together.
