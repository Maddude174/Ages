# Ages Documentation

**Ages** is a civilization-building and era-progression plugin for **Paper 1.21.x to 26.3** (Java 25). Found a settlement, place specialized buildings, run production chains, adopt a government and an economy, and advance through five ages: **Neolithic, Tribal, Village, Medieval and Industrial**.

This documentation covers **version 0.0.59**. It is an early alpha, so systems and YAML formats may still change.

{% hint style="warning" %}
Pre-alpha software. Back up `plugins/Ages/` before upgrading. A new defaults version **replaces every shipped file, including `settings.yml`**, on the first start. See [Release Notes 0.0.59](release-notes-0.0.59.md).
{% endhint %}

## Start here

| I want to...                                                 | Read                                                                |
| ------------------------------------------------------------ | ------------------------------------------------------------------- |
| Check Java and Minecraft support                             | [Compatibility and Requirements](compatibility-and-requirements.md) |
| Install the plugin and found my first civilization           | [Getting Started](getting-started.md)                               |
| Learn the commands, permissions and gameplay                 | [Player Guide](player-guide.md)                                     |
| Understand the config folder and how upgrades treat my edits | [Configuration Overview](configuration-overview.md)                 |
| Tune global rules                                            | [settings.yml Reference](settings-yml-reference.md)                 |
| See what changed                                             | [Release Notes 0.0.59](release-notes-0.0.59.md)                     |

## Systems reference

| System                                                                      | What it covers                                                                                     |
| --------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| [Ages](ages.md)                                                             | The five ages, requirements, costs, claim growth, tag limits and the age file format               |
| [Buildings](buildings.md)                                                   | Scan boxes, requirements, limits, placement, core storage, upgrades and the file format            |
| [Building Functions and Recipes](building-functions-and-recipes.md)         | Processors, spawners, tax, sell, siege, recipe modes, `select_as`, money routing                   |
| [Building Catalogue](building-catalogue.md)                                 | All 64 shipped buildings by category, with prices                                                  |
| [Governments](governments.md)                                               | Roles, permission bundles, elections, purchase and income rules, and all eight shipped governments |
| [Economies](economies.md)                                                   | Money, income split, who pays for buildings, contributions and the file format                     |
| [Policies and Effects](policies-and-effects.md)                             | Slots, exclusive groups, every effect key and all 65 shipped policies                              |
| [Diplomacy and War](diplomacy-and-war.md)                                   | Relations, war goals, revolt, revolution and cannon sieges                                         |
| [Logistics](logistics.md)                                                   | Rail networks and storage hubs                                                                     |
| [Integrations](integrations.md)                                             | Vault, WorldEdit, dynmap, PlaceholderAPI, bStats and the API                                       |
| [Administration and Troubleshooting](administration-and-troubleshooting.md) | Admin commands, regen, error messages and backups                                                  |

## The big picture

1. **Found** a civilization by placing a Fireplace core and naming it in chat.
2. **Build** structures around core blocks. Each building validates its surroundings.
3. **Produce.** Buildings run on timers: they consume inputs from their storage, output goods, and earn resource points and money.
4. **Govern.** A government decides roles and permissions, an economy decides how money flows and who pays, and policies add modifiers.
5. **Advance.** Meet an age's requirements, buy its core building, and your claims, limits and catalogue grow.
6. **Compete.** Form alliances, make truces, declare wars and lay siege with cannons.

## Customizable by design

Ages, buildings, governments, economies, policies and contribution values are all YAML files in `plugins/Ages/`. Add a file and run `/ages reload`: it appears in the game with no code change. Anything you break is skipped with a clear console message, and `/ages admin regen` restores the shipped copy.

See the source repository for releases and issues: [github.com/Maddude174/Ages](https://github.com/Maddude174/Ages).
