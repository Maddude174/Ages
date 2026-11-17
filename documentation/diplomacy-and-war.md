# Diplomacy and War

Diplomacy is on by default and can be disabled with `diplomacy.enabled: false`, which also switches cannons off. Role permissions: `MANAGE_DIPLOMACY` for relations, `DECLARE_WAR` for war.

## Relations

Exactly one state exists between any two civilizations.

| State              | Meaning                                                                              |
| ------------------ | ------------------------------------------------------------------------------------ |
| `NEUTRAL`          | Default.                                                                             |
| `HOSTILE`          | Hostility declared. TNT can damage land.                                             |
| `ALLIED`           | Alliance. Up to `diplomacy.alliance_max_size` (5) civilizations in one alliance.     |
| `ALLIED_DEFENSIVE` | Defensive alliance.                                                                  |
| `WAR`              | Active war, with a goal and a **war score** from 0 to 1000.                          |
| `TRUCE`            | Ceasefire lasting between `truce_min_hours` (24) and `truce_max_hours` (168).        |
| `TRIBUTARY`        | One civilization pays a skim to an overlord. Stores the overlord and a tribute rate. |
| `SETTLEMENT`       | A subordinate civilization of an overlord.                                           |

Alliance and truce are **proposals**: the other side confirms with `/ages accept_diplo <civ>`. The diplomacy menu (`/ages diplomacy`) covers ally, hostile, truce, war, revolt and revolution.

## Commands

| Command                                   | Purpose                                                                                                    |
| ----------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| `/ages diplomacy`                         | Relations list and menu                                                                                    |
| `/ages ally <civ> [defensive]`            | Propose an alliance, optionally defensive                                                                  |
| `/ages breakally <civ>`                   | End an alliance                                                                                            |
| `/ages hostile <civ>`                     | Declare hostility                                                                                          |
| `/ages truce <civ> [hours]`               | Propose a truce (default length `truce_min_hours`)                                                         |
| `/ages accept_diplo <civ>`                | Accept an alliance or truce proposal                                                                       |
| `/ages war <civ> <goal>`                  | Declare war. The goal is **required**: `none`, `tributary`, `conquest`, `total_war`, `political` or `raid` |
| `/ages tributary <civ>` / `release <civ>` | Tributary relationship / release                                                                           |
| `/ages revolt`                            | Tributary or settlement breakaway                                                                          |
| `/ages civilwar` (or `revolution`)        | Start an internal civil war                                                                                |
| `/ages rebel` (or `defect`)               | Join the active opposition                                                                                 |

Revolt and revolution have their own commands rather than being chosen as war goals on `/ages war`. Other plugins can listen to Ages events, for example to veto a war declaration (see [Integrations](integrations.md)).

## War goals

The goal is chosen when war is declared and decides what happens when the war score reaches **1000**.

| Goal         | Outcome for the loser                                                                                                                      |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `none`       | Nothing special.                                                                                                                           |
| `tributary`  | Becomes the winner's tributary (default tribute rate 0.15, a 15% skim).                                                                    |
| `conquest`   | Same as tributary.                                                                                                                         |
| `total_war`  | The loser is **destroyed**: its buildings are disabled and the civilization is disbanded.                                                  |
| `political`  | The loser's government and economy are replaced with the winner's, and the winner's leader leads it. A truce of `truce_max_hours` follows. |
| `raid`       | The winner takes 50% of the loser's RP and money. A truce of `truce_min_hours` follows.                                                    |
| `revolt`     | Used by `/ages revolt`: a tributary or settlement breaks free.                                                                             |
| `revolution` | Used by `/ages civilwar`: internal civil war outcome.                                                                                      |

{% hint style="danger" %}
`total_war` destroys a civilization. Consider restricting `DECLARE_WAR` in your governments if you run a persistent server.
{% endhint %}

## Revolt versus revolution

* **Revolt** (`/ages revolt`) is a tributary or settlement breakaway from its overlord.
* **Revolution** (`/ages civilwar`) is an **internal** civil war. A shadow **opposition town** is created, members can `/ages rebel` to defect to it, and the war is resolved like any other.

Revolt works only as a tributary or settlement. Revolution works only as an internal civil war. Anarchy can only be left through a revolution.

## War score

War score rises from kills, building disables and siege pressure. Every cannon shot adds `8 + 2 x level` points, and every shell that explodes adds a further 25. A war with the `tributary` goal can resolve once the score reaches 600. A truce resets the score.

## Cannons and sieges

The **Cannon** (Medieval, dispenser core, limit 4 per civilization and 2 per player, `military` tag) is a building with `function: {type: siege}`. Everything about it is in `buildings/military/cannon.yml`, so you can change ammunition, timing, range and damage.

### How a cannon fires

1. It runs on the building's interval, with the usual speed modifiers.
2. It only fires at civilizations you are **at war** with, in the same world and within range. If diplomacy is disabled it does nothing.
3. **Target priority**: a civilization's **core** (the Fireplace, Chief's Hut or any age core) first, then buildings tagged `aimable` (for example the Keep), then any other building, nearest first. Disabled buildings are slightly less attractive.
4. If a target exists, the cannon consumes one **shell recipe** from its storage and fires. A missing shell shows as `Need ammo (see shell recipes)`.
5. If **no war target is in range**, the cannon holds fire (`No war target in <range>m`) unless the owner or a manager switches on **Override** in the building menu. With Override on, it fires on a default arc in the direction it faces, using ammunition. This only wastes shells. It cannot target a civilization you are not at war with.

The description text inside the shipped cannon file mentions hostile relations, but the cannon only targets civilizations you are at **war** with.

### Shells (ammunition)

| Shell      | Needs                          | Consumes                                  |
| ---------- | ------------------------------ | ----------------------------------------- |
| Standard   | Level 1                        | 3 gunpowder, 1 cobblestone                |
| Heavy      | Level 2                        | 4 gunpowder, 2 cobblestone, 1 iron ingot  |
| Industrial | Level 3 and the Industrial Age | 5 gunpowder, 2 cobblestone, 2 iron ingots |

Put the ammunition into the cannon's storage (**Open storage** in its menu). The owner picks the shell in the **Production** panel, and the cannon uses the best tier it can supply. A cannon also earns 0.2 RP per cycle.

### Range, damage and cooldown

The `siege:` block sets the base values, and `function.interval` (or `siege.cooldown_seconds`) sets the reload time:

| Level                                             | Reload | `range` | `height` | `yield` |
| ------------------------------------------------- | ------ | ------- | -------- | ------- |
| I (base)                                          | 180 s  | 32      | 8        | 3.5     |
| II (Medieval upgrade, 3,350 RP + 5,000 money)     | 150 s  | 48      | 12       | 4.0     |
| III (Industrial upgrade, 6,450 RP + 15,000 money) | 120 s  | 64      | 16       | 5.0     |

On top of those values the plugin adds a level bonus: **+16 blocks of range and +4 of arc height for each level above I, and +0.5 yield per level**. The minimums are range 8, height 2 and yield 1.0. Shell damage is never below 2.5. Treat the numbers above as a starting point and test them on your own server.

### What shells destroy

* Shells **always damage structures** on the land they hit, regardless of `protection.tnt`.
* A civilization's **core blocks are never destroyed by a shell**. Buildings around them are damaged, which disables them.
* Shell damage respects `protection.tnt_requires_online`: with the default `true`, a civilization with no member online cannot be damaged.
* Damaged buildings are **disabled** until repaired (10% of the purchase price, times `repair_cost_mult`).

### Repairs

Policies such as Sappers' Corps (0.8), Castle Ward (0.85) and Fire Watch (0.85) lower `repair_cost_mult`, and Scorched Earth (0.75) lowers it further. Admins can repair with `/ages admin repair <civ>`.

## Logs and admin tools

War and diplomacy events are logged for `diplomacy.log_retention_days` (30). Admins have `relation`, `warscore`, `endwar`, `tribute` and `warlog` subcommands. See [Administration and Troubleshooting](administration-and-troubleshooting.md).
