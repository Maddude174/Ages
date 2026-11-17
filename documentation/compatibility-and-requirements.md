# Compatibility and Requirements

## Supported versions

| Component             | Support                                                                                                                                                                |
| --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Minecraft / Paper** | **1.21.x through 26.3.** The plugin is built against the Paper 26.3 API and declares `api-version: 1.21` as its minimum.                                               |
| **Java**              | **Java 25.** The jar is compiled for Java 25, so older Java versions cannot load it.                                                                                   |
| **Server software**   | **Paper** (and forks that keep the Paper API). Ages uses only the public Bukkit and Paper API, with no server internals.                                               |
| **Economy**           | **Vault** and any economy plugin. Required for money purchases, collecting building income, `/ages contribute money` and `/ages withdraw`. Barter works without money. |
| **Permissions**       | Any permissions plugin, for example LuckPerms.                                                                                                                         |
| **Optional**          | PlaceholderAPI, dynmap, WorldEdit or FastAsyncWorldEdit. See [Integrations](integrations.md).                                                                          |

Minecraft now uses year-based version numbers (26.x). Ages does not check the version number. It resolves every material and entity from the names in your YAML when the plugin loads, so what matters is whether your server knows those names.

## Content that needs a newer Minecraft version

The shipped YAML refers to some items that only exist on newer versions. If your server is older than the version in this table, those names are unknown to it. The versions below are the ones I know of, so confirm them against your server's console output.

| Item                                                                                           | Needs about | Used in                                                                                                |
| ---------------------------------------------------------------------------------------------- | ----------- | ------------------------------------------------------------------------------------------------------ |
| Pale oak logs and planks                                                                       | 1.21.4      | `contributions.yml`, Charcoal Kiln                                                                     |
| Copper tools and armor (pickaxe, axe, shovel, hoe, sword, helmet, chestplate, leggings, boots) | 1.21.9      | Woodcutter, Mine, Clay Pit, Sand Pit, Farm, Plantation, Blacksmith, Armory, Hunter's Hut, Fisher's Hut |
| Copper nugget                                                                                  | 1.21.9      | `contributions.yml`, Mine                                                                              |
| Spears (wooden, stone, copper, iron)                                                           | 1.21.11     | Hunter's Hut, Fisher's Hut                                                                             |

### What happens when a name is unknown

| Where                                              | Result                                                                                                                                              |
| -------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `contributions.yml`                                | **Fatal.** The plugin refuses to start and prints `contributions.yml: unknown material`.                                                            |
| A building recipe (`consume`, `produce`, `any_of`) | That **building is skipped**. Skips cascade: if the Woodcutter is skipped, the Village Age (which requires it) is skipped, and everything after it. |
| `groups.yml`                                       | A warning marked `(ignored)`. The group loads with the entries that do exist.                                                                       |
| `materials-aliases.yml`                            | Ignored if the target name is unknown.                                                                                                              |

The `optional: true` recipe flag, which used to drop such recipes quietly, no longer exists.

### Running on an older 1.21.x server

If your server is older than the versions above:

1. Start the server once and read the console. Each unknown name is listed with its file.
2. In `contributions.yml`, delete the lines for the unknown items.
3. In each building file that is named, delete the unknown item from the `any_of` lists (for example `COPPER_AXE` from the Woodcutter), or delete the whole recipe tier that needs it.
4. Run `/ages reload` and check that the console reports no skipped definitions. The summary line should list 64 buildings and 8 governments.
5. Keep your edited files in a safe place. A defaults version bump replaces shipped files on upgrade.

## Moving to a newer Minecraft version

1. Update Paper and keep Java at 25.
2. Start once and read the console for `unknown material` or `unknown entity` messages.
3. If Mojang renamed an item that your YAML uses, fix the name in the YAML, or add a rename to `plugins/Ages/materials-aliases.yml` (one `OLD_NAME: NEW_NAME` per line).
4. Check founding, production, cannons, and your integrations.

Things that can still break after a Minecraft update: renamed materials or entities that Ages has no alias for, and optional plugins (Vault, PlaceholderAPI, dynmap, WorldEdit) that have not been updated.

## Not guaranteed

* Running the jar on Java older than 25.
* Data packs or custom item plugins that change how vanilla items behave.
* Servers that are not based on Paper.
