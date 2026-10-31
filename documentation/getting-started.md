# Getting Started

This page takes you from an empty server to a founded civilization.

## Requirements

| Requirement               | Details                                                                                                                                                                                                             |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Server software           | **Paper** 1.21.x. The plugin declares `api-version: 1.21` and is built against `1.21.8-R0.1-SNAPSHOT`.                                                                                                              |
| Java                      | **Java 21**                                                                                                                                                                                                         |
| Economy                   | **Vault** plus an economy plugin. It is needed to collect building income, pay money costs, and use `/ages contribute money`, `/ages withdraw` and `/ages convert`. Almost every shipped building has a money cost. |
| Permissions (recommended) | A permissions plugin such as LuckPerms, to hand out `ages.*` nodes.                                                                                                                                                 |
| Optional integrations     | WorldGuard, dynmap, PlaceholderAPI. See [Integrations](integrations.md).                                                                                                                                            |

{% hint style="warning" %}
Ages 0.0.47 is an **early alpha**. Back up `plugins/Ages/` (especially `ages.db`) before upgrading. Read [Release Notes 0.0.47](release-notes-0.0.47.md), because upgrading replaces your shipped config files and prices are much higher than in 0.0.3A.
{% endhint %}

## Install

{% stepper %}
{% step %}
### Drop the jar in `plugins/`

Place the **main** Ages jar (`Ages-0.0.47.jar`) in your server's `plugins/` folder. Do not use the `-thin` jar, which has no bStats library.
{% endstep %}

{% step %}
### Start the server once

On first start Ages installs every shipped config file into `plugins/Ages/` and creates the SQLite database `ages.db`. The console prints a summary such as `Loaded 5 ages, 64 buildings, 8 governments, 5 economies, 65 policies, 0 civilizations.` Fewer than that means something was skipped, so read the `skipped` lines above it.
{% endstep %}

{% step %}
### Install dependencies

Install Vault, an economy plugin and a permissions plugin, then restart.
{% endstep %}

{% step %}
### Review the configuration

Skim [settings.yml](settings-yml-reference.md) and adjust the founding distance, member cap and income multiplier for your server. Decide whether you want bStats metrics (`metrics.enabled`). Apply changes with `/ages reload`.
{% endstep %}
{% endstepper %}

## What gets created

```
plugins/Ages/
├── settings.yml            global rules
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
├── backup/                 shipped files saved before an upgrade replaced them
├── defaults.version        which default-content version is installed
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
2. Open the **Catalogue**. The first buildings (Tribal Hut, Storage Hut, Forager, Fisher's Hut, Hunter's Hut) cost **400 RP** each. Under Barter there is no money, so costs are paid in resource points only, and money costs are dropped and RP costs **doubled** by default (`economy.no_money_purchase_points_mult`).
3. Build the structure the building asks for around its core block, then place the core. See [Buildings](buildings.md) for how validation works.
4. Put required inputs (a bow and arrows for the Hunter's Hut, for instance) into the building's **barrel core**. Production runs on a timer and drops output into the container. Recipes are in the building's **Production** panel.
5. Contribute materials with `/ages contribute hand` or `/ages contribute all` to earn **resource points** (RP). Contribution values are small (`0.16` RP per log), so expect to need many items.
6. When the Tribal requirements are met (a Fireplace plus one of Forager, Hunter's Hut or Fisher's Hut), buy and place a **Chief's Hut** for **2,250 RP** to advance. See [Ages](ages.md).
