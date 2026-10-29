# Diplomacy and War

Diplomacy is on by default and can be disabled with `diplomacy.enabled: false`. Role permissions: `MANAGE_DIPLOMACY` for relations, `DECLARE_WAR` for war.

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

Alliance and truce are **proposals**: the other side confirms with `/ages accept_diplo <civ>`. The diplomacy GUI covers ally, hostile, truce, war, revolt and revolution.

## Commands

| Command                                   | Purpose                                                                                                    |
| ----------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| `/ages diplomacy`                         | Relations list and GUI                                                                                     |
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

Revolt and revolution have their own commands rather than being chosen as war goals on `/ages war`.

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

Revolt works only as a tributary or settlement. Revolution works only as an internal civil war.

## War score and siege

War score rises from kills, building disables and siege pressure. A war with the `tributary` goal can resolve once the score reaches 600. A truce resets the score.

### Cannons

The **Cannon** (Medieval, dispenser core, 120 RP, limit 4 per civilization and 2 per player, `tags: {military: 1}`) fires propelled TNT during Hostile or War relations.

| Level                    | Range | Arc height | Cooldown | Shell yield |
| ------------------------ | ----- | ---------- | -------- | ----------- |
| Base                     | 32    | 8          | 180 s    | 3.5         |
| II (Medieval, 80 RP)     | 48    | 12         | 150 s    | 4.0         |
| III (Industrial, 160 RP) | 64    | 16         | 120 s    | 5.0         |

* Each shot consumes **3 gunpowder and 1 cobblestone** from the cannon's core inventory.
* Cannons prefer operational targets but may hit disabled ones, with a slight miss.
* A long cooldown and siege pressure drain the defender's RP and raise war score.
* Vanilla TNT: peacetime follows `protection.tnt`, Hostile or War can take damage.

### Repairs

Buildings damaged in a war become **disabled** and lose RP until repaired. Repair cost is 10% of the purchase cost times `repair_cost_mult`, which the Sappers Corps (0.7), War Taxes (0.9) and Scorched Earth (1.75) policies change. Admins can repair with `/ages admin repair`.

## Logs and admin tools

War and diplomacy events are logged for `diplomacy.log_retention_days` (30). Admins have `relation`, `warscore`, `endwar`, `tribute` and `warlog` subcommands. See [Administration and Troubleshooting](administration-and-troubleshooting.md).
