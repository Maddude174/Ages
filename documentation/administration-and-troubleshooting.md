# Administration and Troubleshooting

## Admin commands

All need `ages.admin`. Run `/ages admin` for the exact arguments of each.

| Subcommand                                            | Purpose                                                                       |
| ----------------------------------------------------- | ----------------------------------------------------------------------------- |
| `/ages admin list` / `info`                           | List civilizations, or show one                                               |
| `points`, `money`                                     | Add resource points or treasury money (aliases `rp`, `addpoints`, `addmoney`) |
| `setpoints`, `setmoney`                               | Set balances exactly                                                          |
| `gov`, `econ`                                         | Force a government or economy                                                 |
| `age`                                                 | Force the age                                                                 |
| `leader`, `owner`                                     | Set the leader or owner                                                       |
| `disband`                                             | Disband a civilization (needs `confirm`)                                      |
| `repair`                                              | Repair disabled buildings                                                     |
| `tp`                                                  | Teleport to a civilization                                                    |
| `election <civ> open\|close\|status`                  | Control elections                                                             |
| `relation`, `warscore`, `endwar`, `tribute`, `warlog` | Diplomacy and war tools                                                       |

Other admin commands:

| Command                          | Purpose                                  |
| -------------------------------- | ---------------------------------------- |
| `/ages reload`                   | Reload all configuration                 |
| `/ages core [player]`            | Give the founding core (the Fireplace)   |
| `/ages fireplace [player]`       | Give a Fireplace core                    |
| `/ages give <building> [player]` | Give any building core, even unsold ones |

Because shipped prices are very high, `points` and `money` are the quickest way to test progression on a development server.

## Reloading

`/ages reload` re-reads every YAML file. Timestamps are kept, and every building with a function is re-queued. Errors and skipped definitions are printed to the console. If a reload fails, fix the YAML and reload again.

## Backups

Back up `plugins/Ages/ages.db` (and optionally the `Towns/` dumps) before upgrading. Ages writes to the database on a background thread and flushes changed timers and balances every `production.flush_interval_seconds` (default 30 seconds).

When the shipped defaults change version, Ages copies the files it is about to replace into `plugins/Ages/backup/defaults-v<old>-<timestamp>/`. That includes `settings.yml`. Keep those folders until you have re-applied your edits.

## Troubleshooting

| Symptom                                                                   | Likely cause and fix                                                                                                                                                                |
| ------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `The plugin cannot start: fix the error(s) above`                         | A fatal config error. Read the severe lines above that message in the console.                                                                                                      |
| `N definition(s) were skipped`                                            | One or more building, age, government, economy or policy files has an error. Each skipped file is named with the reason. A skipped age takes everything that depends on it with it. |
| `unknown material 'X'`                                                    | The material name is not valid for your Minecraft version. Fix the YAML, or add an alias in `materials-aliases.yml`. In `groups.yml` this is only a warning marked `(ignored)`.     |
| `scan radius exceeds buildings.max_scan_radius`                           | Lower the building's `scan` or raise the setting (max 20).                                                                                                                          |
| `unknown block group 'x'`                                                 | The building references a group that is missing from `groups.yml`.                                                                                                                  |
| `logistics CORE needs a storage block`                                    | A building uses `CORE` containers but its `core_block` is not a chest or barrel.                                                                                                    |
| My `settings.yml` edits are gone                                          | A defaults version bump replaced it. Your old copy is in `plugins/Ages/backup/`. Re-apply the edits.                                                                                |
| Building will not place                                                   | Check each requirement (remember beds and doors count 2), the limits and caps, `conflicts`, `placement`, and the age. `/ages info` helps.                                           |
| `An embassy must be placed inside another civilization's borders`         | The Embassy uses `placement: foreign`. Build it on another civilization's land.                                                                                                     |
| `You cannot build this on another civilization's land`                    | `placement: anywhere` buildings are refused on foreign land.                                                                                                                        |
| Cannot buy: money or Vault errors                                         | Money costs come from your Vault balance by default. Install Vault and an economy, or switch to Commons or Planned so the treasury pays.                                            |
| Money is not reaching the treasury                                        | Building money is split by `income_to_civ`. The rest stays on the building until collected from its core menu.                                                                      |
| Costs are far too high                                                    | Shipped 0.0.47 prices are very high compared with income. Lower the costs in the building, age and policy files, or raise `economy.income_multiplier` and the contribution values.  |
| Cannot change government: `only through revolution`                       | The current government (Anarchy) has `exit_only_by_revolution`. Use a civil war, or an admin command.                                                                               |
| Cannot afford a government or economy change                              | Its money part is paid from the treasury. Barter has no treasury money, so switch to Commons first.                                                                                 |
| Building status `Needs ...`                                               | A recipe input is missing from the input container, and no other unlocked recipe could run.                                                                                         |
| Building status `Output container is full`                                | Empty the output container or enlarge it.                                                                                                                                           |
| Building status `No input container found` or `No output container found` | The configured container block is missing inside the building area.                                                                                                                 |
| Building status `Disabled: restore structure to repair`                   | The structure no longer meets its requirements. Rebuild it.                                                                                                                         |
| Spawner `No free housing`                                                 | Civilization housing is full. Build more housing or raise `housing_mult`.                                                                                                           |
| Civilization cannot be founded                                            | Too close to another civilization (`founding.min_distance_between_towns`) or the name does not match `founding.name_regex`.                                                         |
| Creative player cannot buy cores                                          | `shop.deny_creative_purchase`. Use `ages.creative.bypass`.                                                                                                                          |
| Production stopped while players are away                                 | Buildings only run in **loaded chunks**. Missed cycles are not caught up unless `production.max_catchup_cycles` is above 1.                                                         |
| `bStats failed to start`                                                  | The bStats library could not initialise (for example with the `-thin` jar). Metrics are the only thing affected.                                                                    |

## Reporting bugs

Include the Minecraft version, Paper build, Java version, the relevant config files, the full stack trace and the steps to reproduce. Systems and YAML formats may change while the plugin is in alpha.
