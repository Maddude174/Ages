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

## Reloading

`/ages reload` re-reads every YAML file. Timestamps are kept, and every building with a function is re-queued. Errors and skipped definitions are printed to the console. If a reload fails, fix the YAML and reload again.

## Backups

Back up `plugins/Ages/ages.db` (and optionally the `Towns/` dumps) before upgrading. Ages writes to the database on a background thread and flushes changed timers and balances every `production.flush_interval_seconds` (default 30 seconds).

## Troubleshooting

| Symptom                                                                   | Likely cause and fix                                                                                                                      |
| ------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| `The plugin cannot start: fix the error(s) above`                         | A fatal config error. Read the severe lines above that message in the console.                                                            |
| `N definition(s) were skipped`                                            | One or more building, age, government, economy or policy files has an error. Each skipped file is named with the reason.                  |
| `unknown material 'X'`                                                    | The material name is not valid for your Minecraft version. Fix the YAML, or add an alias in `materials-aliases.yml`.                      |
| `scan radius exceeds buildings.max_scan_radius`                           | Lower the building's `scan` or raise the setting (max 20).                                                                                |
| `unknown block group 'x'`                                                 | The building references a group that is missing from `groups.yml`.                                                                        |
| `logistics CORE needs a storage block`                                    | A building uses `CORE` containers but its `core_block` is not a chest or barrel.                                                          |
| Building will not place                                                   | Check each requirement (remember beds and doors count 2), the limits and caps, `conflicts`, `placement`, and the age. `/ages info` helps. |
| Building status `Needs ...`                                               | A recipe input is missing from the input container.                                                                                       |
| Building status `Output container is full`                                | Empty the output container or enlarge it.                                                                                                 |
| Building status `No input container found` or `No output container found` | The configured container block is missing inside the building area.                                                                       |
| Building status `Disabled: restore structure to repair`                   | The structure no longer meets its requirements. Rebuild it.                                                                               |
| Spawner `No free housing`                                                 | Civilization housing is full. Build more housing or raise `housing_mult`.                                                                 |
| Civilization cannot be founded                                            | Too close to another civilization (`founding.min_distance_between_towns`) or the name does not match `founding.name_regex`.               |
| Creative player cannot buy cores                                          | `shop.deny_creative_purchase`. Use `ages.creative.bypass`.                                                                                |
| Production stopped while players are away                                 | Buildings only run in **loaded chunks**. Missed cycles are not caught up unless `production.max_catchup_cycles` is above 1.               |
| Nothing happens after editing a shipped file                              | A content version bump may have replaced it. Check `backup/`.                                                                             |
| `conflict_scope` or `tnt_requires_online` has no effect                   | Known issue in 0.0.3A: these two keys are not read. See [settings.yml Reference](/broken/pages/settings-yml-reference).                   |

## Reporting bugs

Include the Minecraft version, Paper build, Java version, the relevant config files, the full stack trace and the steps to reproduce. Systems and YAML formats may change while the plugin is in alpha.
