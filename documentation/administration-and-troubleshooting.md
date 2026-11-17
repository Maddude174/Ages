# Administration and Troubleshooting

## Admin commands

All need `ages.admin`. `<civ>` is a civilization name.

| Command                                                  | Purpose                                         |
| -------------------------------------------------------- | ----------------------------------------------- |
| `/ages admin list`                                       | List civilizations                              |
| `/ages admin info <civ>`                                 | Show one civilization                           |
| `/ages admin points <civ> <amount>`                      | Add resource points (aliases `rp`, `addpoints`) |
| `/ages admin money <civ> <amount>`                       | Add treasury money (alias `addmoney`)           |
| `/ages admin setpoints <civ> <amount>`                   | Set resource points exactly                     |
| `/ages admin setmoney <civ> <amount>`                    | Set treasury money exactly                      |
| `/ages admin gov <civ> <governmentId>`                   | Force a government                              |
| `/ages admin econ <civ> <economyId>`                     | Force an economy                                |
| `/ages admin age <civ> <ageId>`                          | Force the age                                   |
| `/ages admin leader <civ> <player>`                      | Set the leader                                  |
| `/ages admin owner <civ> <player>`                       | Set the owner                                   |
| `/ages admin repair <civ>`                               | Repair disabled buildings                       |
| `/ages admin tp <civ>`                                   | Teleport to a civilization                      |
| `/ages admin disband <civ> confirm`                      | Disband a civilization                          |
| `/ages admin election <civ> open\|close\|status`         | Control elections                               |
| `/ages admin relation <civA> <civB> <state> [goal]`      | Force a relation                                |
| `/ages admin warscore <civA> <civB> <0-1000>`            | Set the war score                               |
| `/ages admin endwar <civA> <civB>`                       | End a war                                       |
| `/ages admin tribute <overlord> <subject> <rate\|clear>` | Set or clear a tribute rate                     |
| `/ages admin warlog`                                     | Show the diplomacy and war log                  |
| `/ages admin regen <target>`                             | Restore shipped config files (see below)        |

Other admin commands:

| Command                          | Purpose                                  |
| -------------------------------- | ---------------------------------------- |
| `/ages reload`                   | Reload all configuration                 |
| `/ages core [player]`            | Give the founding core (the Fireplace)   |
| `/ages fireplace [player]`       | Give a Fireplace core                    |
| `/ages give <building> [player]` | Give any building core, even unsold ones |

## Restoring shipped files (`regen`)

`/ages admin regen` copies the jar's shipped files over your live ones, after saving yours to `plugins/Ages/backup/regen-<timestamp>/`. Run `/ages reload` afterwards.

| Target                                                                            | Restores                                                                                  |
| --------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| `ages`, `buildings`, `governments` (or `gov`), `economies` (or `eco`), `policies` | The whole folder                                                                          |
| `settings`, `contributions`, `groups`, `shop`                                     | That root file                                                                            |
| `all`                                                                             | Every folder and root file above                                                          |
| `<id>` such as `mine`                                                             | One definition file. If the id matches several files, you are asked to give the full path |
| `buildings/resources/mine.yml`                                                    | One file by path                                                                          |

## Reloading

`/ages reload` re-reads every YAML file. Timestamps are kept, and every building with a function is re-queued. Errors and skipped definitions are printed to the console. If a reload fails, fix the YAML and reload again, or restore the file with `regen`.

## Backups

Back up `plugins/Ages/ages.db`, `core-inv/` and `pending-cores/` together (the database, each building's storage, and parked cores). Ages writes to the database on a background thread and flushes changed timers and balances every `production.flush_interval_seconds` (default 30 seconds).

When the shipped defaults change version, Ages copies the files it is about to replace into `plugins/Ages/backup/defaults-v<old>-<timestamp>/`. That includes `settings.yml` and `contributions.yml`. Keep those folders until you have re-applied your edits.

## Troubleshooting

| Symptom                                                                   | Likely cause and fix                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `UnsupportedClassVersionError` at startup                                 | The server is running an older Java. Ages needs **Java 25**.                                                                                                                                                                                                               |
| `The plugin cannot start: fix the error(s) above`                         | A fatal config error. Read the severe lines above that message. The usual cause is an unknown material in `contributions.yml`, which happens on a Minecraft version older than the shipped items. See [Compatibility and Requirements](compatibility-and-requirements.md). |
| `N definition(s) were skipped`                                            | One or more building, age, government, economy or policy files has an error. Each skipped file is named with the reason. A skipped age takes everything that depends on it with it.                                                                                        |
| `unknown material 'X'`                                                    | The material name is not valid for your Minecraft version. Remove it from the YAML, or add an alias in `materials-aliases.yml`. In `groups.yml` this is only a warning marked `(ignored)`.                                                                                 |
| `scan radius exceeds buildings.max_scan_radius`                           | Lower the building's `scan` or raise the setting (max 20).                                                                                                                                                                                                                 |
| `unknown block group 'x'`                                                 | The building references a group that is missing from `groups.yml`.                                                                                                                                                                                                         |
| `logistics.input ... is not a container block`                            | `logistics.input` or `output` names a material that is not a container. Use `CORE`, `BARREL` or `CHEST`.                                                                                                                                                                   |
| My `settings.yml` edits are gone                                          | A defaults version bump replaced it. Your old copy is in `plugins/Ages/backup/`. Re-apply the edits.                                                                                                                                                                       |
| Undo a bad edit                                                           | `/ages admin regen <file>`, then `/ages reload`.                                                                                                                                                                                                                           |
| Building will not place                                                   | Check each requirement (remember beds and doors count 2), the limits and caps, `conflicts`, `placement`, and the age. `/ages particles` shows the scan box, and `/ages info` helps.                                                                                        |
| `An embassy must be built on foreign land`                                | The Embassy uses `placement: foreign`. Build it on another civilization's land.                                                                                                                                                                                            |
| `anywhere` building refused                                               | `placement: anywhere` buildings are refused on another civilization's land.                                                                                                                                                                                                |
| Cannot buy: money or Vault errors                                         | Money costs come from your Vault balance by default. Install Vault and an economy, or switch to Commons or Planned so the treasury pays.                                                                                                                                   |
| Money is not reaching the treasury                                        | Building money is split by `income_to_civ`. The rest stays on the building until collected from its core menu.                                                                                                                                                             |
| Cannot advance an age                                                     | Disabled buildings do not count. Repair them, then check `/ages info`.                                                                                                                                                                                                     |
| Cannot change government: `only through revolution`                       | The current government (Anarchy) has `exit_only_by_revolution`. Use a civil war, or an admin command.                                                                                                                                                                      |
| Cannot afford a government or economy change                              | Its money part is paid from the treasury. Barter has no treasury money, so switch to Commons first.                                                                                                                                                                        |
| Building status `Needs ...`                                               | A recipe input is missing from the input storage, and no other unlocked recipe could run.                                                                                                                                                                                  |
| Building status `Output container is full`                                | Empty the output storage or enlarge it.                                                                                                                                                                                                                                    |
| Building status `No input container found` or `No output container found` | The configured container block is missing inside the building area.                                                                                                                                                                                                        |
| Building status `Disabled: restore structure to repair`                   | The structure no longer meets its requirements. Rebuild it.                                                                                                                                                                                                                |
| Cannon status `No war target in <range>m`                                 | No civilization at war with you is in range. Turn on Override only if you want it to fire anyway.                                                                                                                                                                          |
| Cannon status `Need ammo (see shell recipes)`                             | Put the shell ingredients into the cannon's storage.                                                                                                                                                                                                                       |
| Rail logistics do nothing                                                 | No age has `rails: true`. See [Logistics](logistics.md).                                                                                                                                                                                                                   |
| Spawner `No free housing`                                                 | Civilization housing is full. Build more housing or raise `housing_mult`.                                                                                                                                                                                                  |
| Civilization cannot be founded                                            | Too close to another civilization (`founding.min_distance_between_towns`) or the name does not match `founding.name_regex`.                                                                                                                                                |
| Creative player cannot buy cores                                          | `shop.deny_creative_purchase`. Use `ages.creative.bypass`.                                                                                                                                                                                                                 |
| Production stopped while players are away                                 | Buildings only run in **loaded chunks**. Missed cycles are not caught up unless `production.max_catchup_cycles` is above 1.                                                                                                                                                |
| Core block stays after a building is removed                              | `protection.remove_core_block_on_destroy` is `false`.                                                                                                                                                                                                                      |
| `bStats failed to start`                                                  | The metrics library could not initialise. Only metrics are affected.                                                                                                                                                                                                       |

## Reporting bugs

Include the Minecraft version, Paper build, Java version, the relevant config files, the full stack trace and the steps to reproduce. Systems and YAML formats may change while the plugin is in alpha.
