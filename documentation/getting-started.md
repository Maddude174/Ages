# Getting Started

This page takes you from an empty server to a founded civilization.

## Requirements

| Requirement               | Details                                                                                                                                                                                                                                  |
| ------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Server software           | **Paper**, Minecraft **1.21.x through 26.3**. See [Compatibility and Requirements](compatibility-and-requirements.md).                                                                                                                   |
| Java                      | **Java 25**                                                                                                                                                                                                                              |
| Economy                   | **Vault** plus an economy plugin. It is needed to pay money costs, collect building income, and use `/ages contribute money`, `/ages withdraw` and `/ages convert`. Almost every shipped building has a money cost, except under Barter. |
| Permissions (recommended) | A permissions plugin such as LuckPerms, to hand out `ages.*` nodes.                                                                                                                                                                      |
| Optional integrations     | PlaceholderAPI, dynmap, WorldEdit or FastAsyncWorldEdit. See [Integrations](integrations.md).                                                                                                                                            |

{% hint style="warning" %}
Ages 0.0.59 is an **early alpha**. Back up `plugins/Ages/` (especially `ages.db`) before upgrading. A new defaults version replaces every shipped file, including `settings.yml`. See [Release Notes 0.0.59](release-notes-0.0.59.md).
{% endhint %}

## Install

{% stepper %}
{% step %}
### Drop the jar in `plugins/`

Place the Ages jar in your server's `plugins/` folder.
{% endstep %}

{% step %}
### Start the server once

On first start Ages installs every shipped config file into `plugins/Ages/` and creates the SQLite database `ages.db`. The console prints a summary such as `Loaded 5 ages, 64 buildings, 8 governments, 5 economies, 65 policies, 0 civilizations.` Fewer than that means something was skipped, so read the `skipped` lines above it. If you see `unknown material` errors, your Minecraft version is older than some shipped items. See [Compatibility and Requirements](compatibility-and-requirements.md).
{% endstep %}

{% step %}
### Install dependencies

Install Vault, an economy plugin and a permissions plugin, then restart.
{% endstep %}

{% step %}
### Review the configuration

Skim [settings.yml](settings-yml-reference.md) and [contributions.yml](economies.md#contributions), and adjust the founding distance, member cap, income multiplier and item values for your server. Decide whether you want bStats metrics (`metrics.enabled`). Apply changes with `/ages reload`.
{% endstep %}
{% endstepper %}

## What gets created

```
plugins/Ages/
├── settings.yml            global rules
├── contributions.yml       resource points per contributed item
├── shop.yml                catalogue categories (names, icons, slots)
├── groups.yml              block groups and item groups used by buildings
├── ages/                   one file per age
├── buildings/<category>/   one file per building
├── governments/            government types and their roles
├── economies/              economic systems
├── policies/               policies (plus _effects.yml, documentation only)
├── materials-aliases.yml   OPTIONAL. Your own material renames
├── Towns/                  one YAML dump per civilization (read-only snapshot)
├── ages.db                 SQLite database: civilizations, buildings, ledger, relations
├── core-inv/               each building's built-in storage contents
├── pending-cores/          cores parked in a civilization's Core storage
├── backup/                 shipped files saved before an upgrade or regen replaced them
├── defaults.version        which default-content version is installed (31)
└── defaults.manifest       which shipped files have already been installed
```

Files starting with `_` (for example `buildings/_example.yml`) are **references, not content**. The loader ignores them. Copy one to start a new definition.

## Found your first civilization

{% stepper %}
{% step %}
### Get a Fireplace core

Craft one: the default founding recipe is **shapeless**, `1 campfire + 1 flint + 4 sticks` (configurable under `founding.recipe`). Admins can hand one out with `/ages fireplace [player]` or `/ages core [player]`.
{% endstep %}

{% step %}
### Place it

The Fireplace is a campfire block. Placing it starts founding. The plugin checks that the spot is at least `founding.min_distance_between_towns` blocks (default **96**) from every other civilization.
{% endstep %}

{% step %}
### Name it in chat

Type the civilization's name in chat within **90 seconds**, or type `cancel`. The name must match `founding.name_regex` (default: 3 to 24 characters of letters, digits, spaces, `'`, `_`, `-`).
{% endstep %}

{% step %}
### You're founded

You become the owner and leader. A new civilization starts in the **Neolithic Age** with the government and economy from `defaults` (**Chiefdom** and **Barter**).
{% endstep %}
{% endstepper %}

## Your first hour

1. Run `/ages` to open the civilization menu, and `/ages info` for a summary and the checklist for the next age.
2. Open the **Catalogue**. The first buildings (Tribal Hut, Storage Hut, Forager, Fisher's Hut, Hunter's Hut and Woodcutter) cost **200 to 400 RP**. Barter has no money, so money costs are dropped and RP costs are **doubled** by default (`economy.no_money_purchase_points_mult`).
3. Build the structure the building asks for around its core block. Run `/ages particles` to draw the building's scan box so you can see the area it checks. When a placement fails, the box is flashed automatically.
4. Place the core. Right-click it and choose **Open storage** (or **Open input**) to put ingredients and tools in. Most buildings keep their goods in the core's **built-in storage**, so the core block can be anything.
5. Open the **Production** panel to choose a product. The building uses the best tier it can run with the tools you supplied.
6. Contribute materials with `/ages contribute hand` or `/ages contribute all` to earn **resource points** (RP). Use `/ages contribute list` and `/ages contribute search <item>` to see what is accepted and what it is worth.
7. When the Tribal requirements are met (a Fireplace plus one of Forager, Hunter's Hut or Fisher's Hut), buy and place a **Chief's Hut** for **1,000 RP** to advance (2,000 under Barter). See [Ages](ages.md).
