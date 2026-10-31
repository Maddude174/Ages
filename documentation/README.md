# Ages Documentation

**Ages** is a civilization-building and era-progression plugin for **Paper 1.21.x** (Java 21). Found a settlement, place specialized buildings, run production chains, adopt a government and an economy, and advance through five ages: **Neolithic, Tribal, Village, Medieval and Industrial**.

This documentation covers **version 0.0.47**. The differences from 0.0.3A are listed in [Release Notes 0.0.47](release-notes-0.0.47.md). It is an early alpha, so systems and YAML formats may change.

{% hint style="warning" %}
Pre-alpha software. Back up `plugins/Ages/` before upgrading. Version 15 of the defaults **replaces every shipped file, including `settings.yml`**, on the first start after an upgrade. Report bugs with your Minecraft version, Paper build, Java version, config and stack trace.
{% endhint %}

## Start here

| I want to...                                                 | Read                                                |
| ------------------------------------------------------------ | --------------------------------------------------- |
| See what changed from 0.0.3A                                 | [Release Notes 0.0.47](release-notes-0.0.47.md)     |
| Install the plugin and found my first civilization           | [Getting Started](getting-started.md)               |
| Learn the commands, permissions and gameplay                 | [Player Guide](player-guide.md)                     |
| Understand the config folder and how upgrades treat my edits | [Configuration Overview](configuration-overview.md) |
| Tune global rules                                            | [settings.yml Reference](settings-yml-reference.md) |

## Systems reference

| System                                                                      | What it covers                                                                              |
| --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| [Ages](ages.md)                                                             | The five ages, requirements, costs, claim growth, tag limits and the age file format        |
| [Buildings](buildings.md)                                                   | Scan boxes, requirements, limits, placement modes, upgrades, `provides` and the file format |
| [Building Functions and Recipes](building-functions-and-recipes.md)         | Processors, spawners, tax, sell, recipe modes, `select_as`, money routing                   |
| [Building Catalogue](building-catalogue.md)                                 | All 64 shipped buildings by category, with prices                                           |
| [Governments](governments.md)                                               | Roles, permission bundles, elections, leaderless rule, purchase and income rules            |
| [Economies](economies.md)                                                   | Money, income split, who pays for buildings, contributions and the file format              |
| [Policies and Effects](policies-and-effects.md)                             | Slots, exclusive groups, every effect key and all 65 shipped policies                       |
| [Diplomacy and War](diplomacy-and-war.md)                                   | Relations, war goals, revolt, revolution and cannon sieges                                  |
| [Logistics](logistics.md)                                                   | Rail networks and storage hubs                                                              |
| [Integrations](integrations.md)                                             | Vault, WorldGuard, dynmap, PlaceholderAPI and bStats                                        |
| [Administration and Troubleshooting](administration-and-troubleshooting.md) | Admin commands, error messages, backups, developer tools                                    |

## The big picture

1. **Found** a civilization by placing a Fireplace core and naming it in chat.
2. **Build** structures around core blocks. Each building validates its surroundings.
3. **Produce.** Buildings run on timers: they consume inputs from a container, output goods, and earn resource points and money.
4. **Govern.** A government decides roles and permissions, an economy decides how money flows and who pays, and policies add modifiers.
5. **Advance.** Meet an age's requirements, buy its core building, and your claims, limits and catalogue grow.
6. **Compete.** Form alliances, make truces, declare wars and lay siege with cannons.

## Data-driven by design

Ages, buildings, governments, economies and policies are YAML files in `plugins/Ages/`. Add a file and restart (or `/ages reload`): it appears in the game with no code change.

See the source repository for releases and issues: [github.com/Maddude174/Ages](https://github.com/Maddude174/Ages).
