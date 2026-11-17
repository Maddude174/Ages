# settings.yml Reference

`plugins/Ages/settings.yml` holds the **global** rules. Per-age rules live in `ages/*.yml`, per-building rules in `buildings/**/*.yml`, roles in `governments/*.yml`, and item values for contributions in `contributions.yml`.

Values are read when the plugin starts and on `/ages reload`. Where the loader clamps a value to a minimum, the table says so. Keys marked **new** were added in recent versions.

{% hint style="danger" %}
`settings.yml` is a **shipped default**. After upgrading from an older defaults version, the first start **replaces it with the jar's copy and does not merge your values**. Your old file is saved in `plugins/Ages/backup/`. Re-apply your changes afterwards, or restore the shipped file at any time with `/ages admin regen settings`. See [Configuration Overview](configuration-overview.md).
{% endhint %}

## `founding`

How civilizations are created.

| Key                                   | Default                             | Meaning                                                                                                                                                                      |
| ------------------------------------- | ----------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `founding.min_distance_between_towns` | `96`                                | Minimum blocks between civilization centres. Placing a founding core closer is refused.                                                                                      |
| `founding.name_regex`                 | `"[A-Za-z0-9 '_-]{3,24}"`           | Java regex the name typed in chat must fully match.                                                                                                                          |
| `founding.recipe.enabled`             | `true`                              | Enables the crafting recipe for the founding core (the first age's core building, the Fireplace).                                                                            |
| `founding.recipe.ingredients`         | `{CAMPFIRE: 1, FLINT: 1, STICK: 4}` | The **shapeless** recipe. Each key is a material, each value an amount (minimum 1). Unknown materials are reported as errors. An empty map falls back to the default recipe. |

## `members` and `defaults`

| Key                   | Default    | Meaning                                                                                                           |
| --------------------- | ---------- | ----------------------------------------------------------------------------------------------------------------- |
| `members.max`         | `50`       | Maximum members per civilization.                                                                                 |
| `defaults.government` | `chiefdom` | Government a new civilization starts with. Must be a valid government id, otherwise the first loaded one is used. |
| `defaults.economy`    | `barter`   | Economy a new civilization starts with. Same fallback rule.                                                       |

## `protection`

Land protection for civilization territory.

| Key                                       | Default | Meaning                                                                                                                                                                                                       |
| ----------------------------------------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `protection.remove_core_block_on_destroy` | `true`  | **New.** When a building is destroyed or removed, its core block is cleared. Set `false` to leave the core block standing. Uses WorldEdit or FAWE when present, otherwise plain block removal.                |
| `protection.block_mob_spawning`           | `true`  | Hostile mobs cannot spawn inside civilization land.                                                                                                                                                           |
| `protection.block_mob_explosions`         | `true`  | Creepers, ghasts and similar cannot damage civilization blocks.                                                                                                                                               |
| `protection.tnt`                          | `never` | `allow` or `never` for **peacetime** TNT. Anything other than `allow` (including the legacy `war_only`) behaves like `never`. Wartime TNT (Hostile or War relations) and cannon shells can still damage land. |
| `protection.tnt_requires_online`          | `true`  | TNT and cannon shells only damage a civilization when at least one member is online.                                                                                                                          |

## `claims`

| Key                      | Default | Meaning                                                                                                                 |
| ------------------------ | ------- | ----------------------------------------------------------------------------------------------------------------------- |
| `claims.building_margin` | `12`    | Each building claims its own scan area plus this many blocks around it, on top of the civilization's main claim square. |

The main claim is a square around the civilization centre. Its radius is `min(max_radius, base_radius + per_building * (buildings placed inside) + bonuses)`, where the first three values come from the age file and the bonuses are `claim_bonus` from working buildings plus the `claim_radius_add` effect. Buildings with `placement: anywhere` or `placement: foreign` do not count toward the per-building growth and never claim land.

## `buildings`

| Key                                  | Default | Meaning                                                                                                                                    |
| ------------------------------------ | ------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `buildings.max_scan_radius`          | `20`    | Upper bound for any building's `scan` radius. `20` means a 41 x 41 x 41 check, the largest allowed. A building exceeding it fails to load. |
| `buildings.revalidate_delay_seconds` | `3`     | After blocks change near a building, wait this long before re-validating it (debounce).                                                    |
| `buildings.conflict_scope`           | `town`  | `town` or `player` for `conflicts:` checks. Any other value becomes `town`.                                                                |

## `respawn`

| Key                                  | Default | Meaning                                                                                                                                              |
| ------------------------------------ | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| `respawn.enabled`                    | `true`  | Buildings with `provides: {respawn_point: true}` (shrines, chapels, graveyards, burial mounds, monasteries, keeps) act as civilization spawn points. |
| `respawn.civilization_overrides_bed` | `false` | `false`: a bed or respawn anchor keeps priority. `true`: the civilization spawn wins.                                                                |

## `production`

| Key                                 | Default | Meaning                                                                                                                   |
| ----------------------------------- | ------- | ------------------------------------------------------------------------------------------------------------------------- |
| `production.max_catchup_cycles`     | `1`     | How many missed cycles a building may run after being unloaded or after downtime. `1` means none. Clamped to at least 1.  |
| `production.flush_interval_seconds` | `30`    | How often changed timers and balances are written to the database. Clamped to at least 5.                                 |
| `production.max_speed_bonus`        | `0.5`   | Cap on the additive civilization-wide speed bonus from buildings (`provides.production_speed`). `0.5` is +50%. Minimum 0. |

## `economy`

| Key                                     | Default | Meaning                                                                                                                                                                                                   |
| --------------------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `economy.force_core_purchase_treasury`  | `false` | **New.** When `true`, **every** building money cost is paid from the civilization treasury, whatever the economy or government says.                                                                      |
| `economy.income_multiplier`             | `1.0`   | Scales **all** building money income, whether it goes to the treasury or stays on the building. Buildings create money from nothing, so lower this on servers with a tightly balanced economy. Minimum 0. |
| `economy.no_money_purchase_points_mult` | `2.0`   | When the civilization's economy has `money_enabled: false` (Barter), building money costs are **dropped** and RP costs are multiplied by this value. Clamped to at least 1.0.                             |
| `economy.ledger.loaded_entries`         | `200`   | Ledger lines kept in memory per civilization. All lines are stored in the database. Clamped to at least 20.                                                                                               |
| `economy.ledger.coalesce_minutes`       | `60`    | Production and tax income within this window merges into one ledger line. Clamped to at least 1.                                                                                                          |

Contribution values are **no longer in `settings.yml`**. They live in `contributions.yml`, covered in [Economies](economies.md#contributions). An older `economy.contributions.items` block in `settings.yml` is still read, but values in `contributions.yml` win.

How money is split and who pays for buildings is set per economy and per government, not here. See [Economies](economies.md).

## `limits`

Shared limit pools. A building joins a pool with `tags:` in its own file.

```yaml
limits:
  tags:
    housing: {per_player: 12}
    farming: {per_player: 4}
    mining: {per_player: 3}
    faith: {per_player: 3}
```

Each tag accepts `per_player` and `per_town`. `0` or absent means unlimited at this level. Age files add their own `tag_limits`, and **the strictest non-zero cap wins**. Policies and governments can raise caps with the `tag_limit_add.<tag>` effect. Tags with no cap in this file and none in the age file are unlimited. See [Buildings](buildings.md).

## `villagers` and `shop`

| Key                           | Default | Meaning                                                                                        |
| ----------------------------- | ------- | ---------------------------------------------------------------------------------------------- |
| `villagers.untradeable`       | `true`  | Villagers spawned by buildings cannot be traded with.                                          |
| `shop.deny_creative_purchase` | `true`  | Creative-mode players cannot buy or place building cores (bypass with `ages.creative.bypass`). |
| `shop.remote_purchase`        | `false` | `true`: buy from `/ages` anywhere. `false`: only at your civilization core.                    |

## `logistics`

Rail networks that move goods between buildings. See [Logistics](logistics.md).

| Key                                    | Default      | Meaning                                                                                                                      |
| -------------------------------------- | ------------ | ---------------------------------------------------------------------------------------------------------------------------- |
| `logistics.enabled`                    | `true`       | Master switch.                                                                                                               |
| `logistics.min_age`                    | `industrial` | Only used in the hint shown by `/ages logistics`. Which ages run rail logistics is set by `rails: true` in the **age file**. |
| `logistics.interval_seconds`           | `10`         | Seconds between runs. Clamped to at least 2.                                                                                 |
| `logistics.max_rails_per_network`      | `4000`       | Maximum rail blocks per connected network. Clamped to at least 50.                                                           |
| `logistics.items_per_building_per_run` | `64`         | Items one building can move per run.                                                                                         |
| `logistics.items_per_network_per_run`  | `512`        | Items a whole network can move per run. Railway stations and depots raise it (`logistics_capacity`).                         |

## `diplomacy`

See [Diplomacy and War](diplomacy-and-war.md).

| Key                            | Default | Meaning                                                                               |
| ------------------------------ | ------- | ------------------------------------------------------------------------------------- |
| `diplomacy.enabled`            | `true`  | Master switch for all diplomacy and war features. Cannons do nothing while it is off. |
| `diplomacy.alliance_max_size`  | `5`     | Maximum civilizations in one alliance.                                                |
| `diplomacy.truce_min_hours`    | `24`    | Minimum truce length. Also the truce after a successful raid.                         |
| `diplomacy.truce_max_hours`    | `168`   | Maximum truce length (7 days). Also the truce after a political victory.              |
| `diplomacy.log_retention_days` | `30`    | How long the diplomacy and war log is kept.                                           |

## `governance`

| Key                               | Default | Meaning                                                                                      |
| --------------------------------- | ------- | -------------------------------------------------------------------------------------------- |
| `governance.election_cycle_days`  | `7`     | Full cycle between election starts for elected governments. Minimum 1.                       |
| `governance.election_voting_days` | `3`     | Days that voting stays open before the tally. Minimum 1, and **capped at the cycle length**. |

## `metrics`

| Key               | Default | Meaning                                                                                                                                                 |
| ----------------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `metrics.enabled` | `true`  | Sends anonymous usage statistics through bStats. Servers can also opt out globally in `plugins/bStats/config.yml`. See [Integrations](integrations.md). |

## Tuning recipes

* **Faster or slower progression:** edit the values in `contributions.yml`, the prices in the building and age files, or `economy.income_multiplier`.
* **Slower, harder server:** lower `production.max_speed_bonus`, and set `economy.income_multiplier` to `0.7`.
* **Everything from the treasury:** set `economy.force_core_purchase_treasury: true`.
* **Keep cores standing:** set `protection.remove_core_block_on_destroy: false`.
* **Peaceful server:** set `diplomacy.enabled: false` to disable all diplomacy and war features.
* **Creative or event server:** set `shop.deny_creative_purchase: false` and `shop.remote_purchase: true`.
* **No telemetry:** set `metrics.enabled: false`.
