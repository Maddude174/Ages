# settings.yml Reference

`plugins/Ages/settings.yml` holds the **global** rules. Per-age rules live in `ages/*.yml`, per-building rules in `buildings/**/*.yml`, and roles in `governments/*.yml`. An older `town.yml` is migrated to this file automatically.

Values are read when the plugin starts and on `/ages reload`. Where the loader clamps a value to a minimum, the table says so.

{% hint style="warning" %}
**Two keys are not read in 0.0.3A.** `buildings.conflict_scope` and `protection.tnt_requires_online` appear in the file, but the loader does not parse them. The built-in defaults (`town` and `true`) always apply, whatever you write. Treat them as documentation of intended behaviour until a later release wires them up.
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

| Key                   | Default    | Meaning                                                                   |
| --------------------- | ---------- | ------------------------------------------------------------------------- |
| `members.max`         | `50`       | Maximum members per civilization.                                         |
| `defaults.government` | `chiefdom` | Government a new civilization starts with. Must be a valid government id. |
| `defaults.economy`    | `barter`   | Economy a new civilization starts with. Must be a valid economy id.       |

## `protection`

Land protection for civilization territory.

| Key                               | Default | Meaning                                                                                                                                                                                     |
| --------------------------------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `protection.block_mob_spawning`   | `true`  | Hostile mobs cannot spawn inside civilization land.                                                                                                                                         |
| `protection.block_mob_explosions` | `true`  | Creepers, ghasts and similar cannot damage civilization blocks.                                                                                                                             |
| `protection.tnt`                  | `never` | `allow` or `never` for **peacetime** TNT. Anything other than `allow` (including the legacy `war_only`) behaves like `never`. Wartime TNT (Hostile or War relations) can still damage land. |
| `protection.tnt_requires_online`  | `true`  | Intended: TNT only damages a civilization when at least one member is online. **Not read in 0.0.3A**, the default applies.                                                                  |

## `claims`

| Key                      | Default | Meaning                                                                                                                 |
| ------------------------ | ------- | ----------------------------------------------------------------------------------------------------------------------- |
| `claims.building_margin` | `12`    | Each building claims its own scan area plus this many blocks around it, on top of the civilization's main claim square. |

The main claim is a square around the civilization centre. Its radius is `min(max_radius, base_radius + per_building * (buildings placed inside) + bonuses)`, where the first three values come from the age file and the bonuses are `claim_bonus` from working buildings plus the `claim_radius_add` effect from the government, economy and policies. Buildings with `placement: anywhere` (mines, quarries) do not count toward the per-building growth.

## `buildings`

| Key                                  | Default | Meaning                                                                                                                                    |
| ------------------------------------ | ------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `buildings.max_scan_radius`          | `20`    | Upper bound for any building's `scan` radius. `20` means a 41 x 41 x 41 check, the largest allowed. A building exceeding it fails to load. |
| `buildings.revalidate_delay_seconds` | `3`     | After blocks change near a building, wait this long before re-validating it (debounce).                                                    |
| `buildings.conflict_scope`           | `town`  | Intended: `town` or `player` for `conflicts:` checks. **Not read in 0.0.3A**, `town` applies.                                              |

## `respawn`

| Key                                  | Default | Meaning                                                                                                                            |
| ------------------------------------ | ------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `respawn.enabled`                    | `true`  | Buildings with `provides: {respawn_point: true}` (shrines, chapels, graveyards, keeps and so on) act as civilization spawn points. |
| `respawn.civilization_overrides_bed` | `false` | `false`: a bed or respawn anchor keeps priority. `true`: the civilization spawn wins.                                              |

## `production`

| Key                                 | Default | Meaning                                                                                                                   |
| ----------------------------------- | ------- | ------------------------------------------------------------------------------------------------------------------------- |
| `production.max_catchup_cycles`     | `1`     | How many missed cycles a building may run after being unloaded or after downtime. `1` means none. Clamped to at least 1.  |
| `production.flush_interval_seconds` | `30`    | How often changed timers and balances are written to the database. Clamped to at least 5.                                 |
| `production.max_speed_bonus`        | `0.5`   | Cap on the additive civilization-wide speed bonus from buildings (`provides.production_speed`). `0.5` is +50%. Minimum 0. |

## `economy`

| Key                               | Default | Meaning                                                                                                                                                     |
| --------------------------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `economy.income_multiplier`       | `1.0`   | Scales **all** treasury money income. Buildings create money from nothing, so lower this on servers with a tightly balanced economy. Minimum 0.             |
| `economy.ledger.loaded_entries`   | `200`   | Ledger lines kept in memory per civilization. All lines are stored in the database. Clamped to at least 20.                                                 |
| `economy.ledger.coalesce_minutes` | `60`    | Production and tax income within this window merges into one ledger line. Clamped to at least 1.                                                            |
| `economy.contributions.items`     | map     | RP awarded **per item** when a member contributes materials, before economy modifiers (`contribution_rate_mult`). Unknown materials are reported as errors. |

Shipped contribution values include logs `0.16`, cobblestone `0.06`, stone `0.08`, bread `0.6`, leather `0.4`, coal `0.3`, raw iron `0.6`, iron ingot `0.7`, gold ingot `1.2`, book `0.88`, emerald `8.0` and diamond `12.0`. Items not listed cannot be contributed.

## `limits`

Shared limit pools. A building joins a pool with `tags:` in its own file.

```yaml
limits:
  tags:
    housing: {per_player: 12}
    farming: {per_player: 4}
    mining:  {per_player: 3}
    faith:   {per_player: 3}
```

Each tag accepts `per_player` and `per_town`. `0` or absent means unlimited at this level. Age files add their own `tag_limits`, and **the strictest non-zero cap wins**. Policies and governments can raise caps with the `tag_limit_add.<tag>` effect. See [Buildings](buildings.md) for the full limit rules.

## `villagers` and `shop`

| Key                           | Default | Meaning                                                                                        |
| ----------------------------- | ------- | ---------------------------------------------------------------------------------------------- |
| `villagers.untradeable`       | `true`  | Villagers spawned by buildings cannot be traded with.                                          |
| `shop.deny_creative_purchase` | `true`  | Creative-mode players cannot buy or place building cores (bypass with `ages.creative.bypass`). |
| `shop.remote_purchase`        | `false` | `true`: buy from `/ages` anywhere. `false`: only at your civilization core.                    |

## `logistics`

Rail networks that move goods between buildings. See [Logistics](/broken/pages/logistics).

| Key                                    | Default      | Meaning                                                                                              |
| -------------------------------------- | ------------ | ---------------------------------------------------------------------------------------------------- |
| `logistics.enabled`                    | `true`       | Master switch.                                                                                       |
| `logistics.min_age`                    | `industrial` | Age that unlocks rail logistics. Set it to `tribal` (or the first age) to have them from the start.  |
| `logistics.interval_seconds`           | `10`         | Seconds between runs. Clamped to at least 2.                                                         |
| `logistics.max_rails_per_network`      | `4000`       | Maximum rail blocks per connected network. Clamped to at least 50.                                   |
| `logistics.items_per_building_per_run` | `64`         | Items one building can move per run.                                                                 |
| `logistics.items_per_network_per_run`  | `512`        | Items a whole network can move per run. Railway stations and depots raise it (`logistics_capacity`). |

## `migration`

| Key                                     | Default | Meaning                                                                |
| --------------------------------------- | ------- | ---------------------------------------------------------------------- |
| `migration.legacy_money_cost_to_points` | `0.1`   | Old building files using `cost: {money: N}` are read as `N * this` RP. |

## `diplomacy`

See [Diplomacy and War](/broken/pages/diplomacy-and-war).

| Key                            | Default | Meaning                                                                  |
| ------------------------------ | ------- | ------------------------------------------------------------------------ |
| `diplomacy.enabled`            | `true`  | Master switch for all diplomacy and war features.                        |
| `diplomacy.alliance_max_size`  | `5`     | Maximum civilizations in one alliance.                                   |
| `diplomacy.truce_min_hours`    | `24`    | Minimum truce length. Also the truce after a successful raid.            |
| `diplomacy.truce_max_hours`    | `168`   | Maximum truce length (7 days). Also the truce after a political victory. |
| `diplomacy.log_retention_days` | `30`    | How long the diplomacy and war log is kept.                              |

## `governance`

| Key                               | Default | Meaning                                                                                      |
| --------------------------------- | ------- | -------------------------------------------------------------------------------------------- |
| `governance.election_cycle_days`  | `7`     | Full cycle between election starts for elected governments. Minimum 1.                       |
| `governance.election_voting_days` | `3`     | Days that voting stays open before the tally. Minimum 1, and **capped at the cycle length**. |

## Tuning recipes

* **Slower, harder server:** lower `production.max_speed_bonus`, raise `claims` margins carefully, set `economy.income_multiplier` to `0.7`.
* **Peaceful server:** set `diplomacy.enabled: false` to disable all diplomacy and war features.
* **Creative or event server:** set `shop.deny_creative_purchase: false` and `shop.remote_purchase: true`.
* **Early rail:** set `logistics.min_age: tribal` for early logistics experiments.
