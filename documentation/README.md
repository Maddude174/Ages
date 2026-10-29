# Ages Documentation

**Ages** is a civilization-building and era-progression plugin for **Paper 1.21.x** (Java 21). Found a settlement, place specialized buildings, run production chains, adopt a government and an economy, and advance through five ages: **Neolithic, Tribal, Village, Medieval and Industrial**.

This documentation covers **version 0.0.3A**, an early alpha. Systems and YAML formats may change.

{% hint style="warning" %}
Pre-alpha software. Back up `plugins/Ages/ages.db` before upgrading, and report bugs with your Minecraft version, Paper build, Java version, config and stack trace.
{% endhint %}

## Start here

| I want to...                                                 | Read                                                |
| ------------------------------------------------------------ | --------------------------------------------------- |
| Install the plugin and found my first civilization           | [Getting Started](getting-started.md)               |
| Learn the commands, permissions and gameplay                 | [Player Guide](player-guide.md)                     |
| Understand the config folder and how upgrades treat my edits | [Configuration Overview](configuration-overview.md) |
| Tune global rules                                            | [settings.yml Reference](settings-yml-reference.md) |

## Systems reference

| System                                                                      | What it covers                                                                        |
| --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| [Ages](ages.md)                                                             | The five ages, requirements, claim growth, tag limits and the age file format         |
| [Buildings](buildings.md)                                                   | Scan boxes, requirements, limits, conflicts, upgrades, `provides` and the file format |
| [Building Functions and Recipes](building-functions-and-recipes.md)         | Processors, spawners, tax, sell, recipe modes, produce and consume                    |
| [Building Catalogue](building-catalogue.md)                                 | All 63 shipped buildings by category                                                  |
| [Governments](governments.md)                                               | Roles, permission bundles, elections, leader selection and the file format            |
| [Economies](economies.md)                                                   | Money, resource points, contributions, withdrawals and the file format                |
| [Policies and Effects](policies-and-effects.md)                             | Slots, exclusive groups, every effect key and all shipped policies                    |
| [Diplomacy and War](diplomacy-and-war.md)                                   | Relations, war goals, revolt, revolution and cannon sieges                            |
| [Logistics](logistics.md)                                                   | Rail networks and storage hubs                                                        |
| [Integrations](integrations.md)                                             | Vault, WorldGuard, dynmap and PlaceholderAPI                                          |
| [Administration and Troubleshooting](administration-and-troubleshooting.md) | Admin commands, error messages, backups, developer tools                              |

## The big picture

1. **Found** a civilization by placing a Fireplace core and naming it in chat.
2. **Build** structures around core blocks. Each building validates its surroundings.
3. **Produce.** Buildings run on timers: they consume inputs from a container, output goods, and earn resource points and money.
4. **Govern.** A government decides roles and permissions, an economy decides how money works, and policies add modifiers.
5. **Advance.** Meet an age's requirements, buy its core building, and your claims, limits and catalogue grow.
6. **Compete.** Form alliances, make truces, declare wars and lay siege with cannons.

## Data-driven by design

Ages, buildings, governments, economies and policies are YAML files in `plugins/Ages/`. Add a file and restart (or `/ages reload`): it appears in the game with no code change.

## Known issues in 0.0.3A

* `buildings.conflict_scope` and `protection.tnt_requires_online` exist in `settings.yml` but are not read by the loader, so their defaults always apply.
* BlueMap is listed as a soft dependency, but only a dynmap integration ships.
* The helper scripts in `tools/` target an older file layout.

See the source repository for releases and issues: [github.com/Maddude174/Ages](https://github.com/Maddude174/Ages).
