# YAML Guide

Everything in Ages that players interact with is defined in YAML files in `plugins/Ages/`. This guide explains how to **write your own** buildings, governments, economies and policies, or change the shipped ones, and what every line does.

| Page                                                            | Covers                                                                    |
| --------------------------------------------------------------- | ------------------------------------------------------------------------- |
| [YAML Guide: Buildings](yaml-guide-buildings.md)                | Structure, costs, limits, containers, functions, recipes, upgrades, siege |
| [YAML Guide: Governments](/broken/pages/yaml-guide-governments) | Roles, permissions, bundles, leader selection, purchase and income rules  |
| [YAML Guide: Economies](/broken/pages/yaml-guide-economies)     | Money flow, who pays, income split, exchange and withdrawal               |
| [YAML Guide: Policies](/broken/pages/yaml-guide-policies)       | Effects, costs, slots, exclusive groups                                   |

For the values the shipped content uses, see the [Building Catalogue](building-catalogue.md), [Governments](governments.md), [Economies](economies.md) and [Policies and Effects](policies-and-effects.md). For ages, see [Ages](ages.md).

## The workflow

{% stepper %}
{% step %}
### Copy, do not edit

Copy a shipped file (or the `_example.yml` in each folder) to a **new file name**, and give it a **new `id`**. Shipped files are replaced when the defaults version changes, but files you create yourself are never touched.
{% endstep %}

{% step %}
### Edit

Change the id, name and values. Keep the structure of the file you copied.
{% endstep %}

{% step %}
### Reload and read the console

Run `/ages reload`. Every error names the file, the line's meaning and the reason. A file with an error is **skipped**, and everything else still loads.
{% endstep %}

{% step %}
### Test

Give yourself the building with `/ages give <id>` (admin), build it, and watch its status line. Check `/ages contribute list` and your prices together so the content is not trivially cheap or impossibly expensive.
{% endstep %}
{% endstepper %}

## Where files go

| Content     | Folder                  | Notes                                                                                                 |
| ----------- | ----------------------- | ----------------------------------------------------------------------------------------------------- |
| Buildings   | `buildings/<category>/` | Any sub-folder works. The folder is only for tidiness. The `category:` key decides the catalogue tab. |
| Governments | `governments/`          | One file per government.                                                                              |
| Economies   | `economies/`            | One file per economy.                                                                                 |
| Policies    | `policies/`             | One file per policy.                                                                                  |
| Ages        | `ages/`                 | See [Ages](ages.md).                                                                                  |

Rules that apply to all of them:

* Files must end in `.yml`. Files starting with `_` are **ignored**, so they are safe reference copies.
* Every definition needs a unique `id`. A duplicate id is skipped with `duplicate ... id`. Ids are lower-cased, so use lowercase letters, digits and underscores.
* Keep the file name equal to the id. `/ages admin regen <id>` finds shipped files by name.
* Use a **prefix** for your own content (for example `myserver_`) so it can never collide with a future shipped id.

## YAML basics

```yaml
# A comment starts with a hash and runs to the end of the line.
id: my_thing                 # text
weight: 40                   # whole number
income_to_civ: 0.25          # decimal
shop: true                   # true or false
description:                 # a list: one line per entry
  - "&7First line"
  - "&7Second line"
tags: {housing: 2, faith: 1} # a map on one line, same as the block form below
scan:                        # a map in block form
  x: 5
  z: 5
```

* **Indent with spaces, never tabs.** Two spaces per level is the convention.
* **Quote any text that starts with `&`** (colour codes), contains `:` or starts with a special character: `name: "&aMy Name"`.
* A key written twice in the same map is a mistake. YAML keeps one of them.
* Material and entity names are the standard Bukkit names in capitals: `OAK_LOG`, `IRON_INGOT`, `VILLAGER`.
* Times are in **seconds**, except `change_cooldown_hours` and `withdraw`-style limits, which say what they are.

## Text and colours

`name` and `description` accept `&` colour codes (`&6` gold, `&c` red, `&7` grey, `&l` bold). A description is a **list**, one entry per line in the menu.

## Numbers that mean the same everywhere

| Idea                           | Meaning                                                                                                                               |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------- |
| **RP** (`points`)              | Resource points, the shared currency of a civilization.                                                                               |
| **money**                      | Real money, paid from a player's Vault balance or the treasury depending on the economy. `treasury` is accepted as an alias in costs. |
| `_mult`                        | A multiplier. `1.0` changes nothing, `1.1` is +10%, `0.9` is -10%. Combined by multiplying.                                           |
| `_add` and other plain numbers | Added together across all sources.                                                                                                    |

## Common mistakes

| Symptom                             | Cause                                                                                                                         |
| ----------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| File silently missing from the game | The file name starts with `_`, or the id is blank. Check the console summary for a `skipped` line.                            |
| `unknown material 'X'`              | A misspelt name, or an item your Minecraft version does not have.                                                             |
| `unknown block group 'x'`           | The group is not in `groups.yml`.                                                                                             |
| Building skipped because of its age | `age:` names an age that does not exist, or one that was itself skipped.                                                      |
| A policy never appears              | The policy id is missing from the government's or economy's `policies:` list, or its `kind` does not match the list it is in. |
| Changes do not show                 | You forgot `/ages reload`, or an error skipped the file.                                                                      |
| Text looks wrong                    | The value is not quoted, so YAML read `&` or `:` as syntax.                                                                   |

## Best practices

1. **Start from a working file.** Change one thing at a time and reload.
2. **Price against income.** Divide a building's price by its per-cycle yield to see how long it takes to repay. See the pricing notes on the buildings page.
3. **Never reuse a shipped id** for something different. Old saves refer to ids.
4. **Keep copies of your custom files** outside the server. `/ages admin regen` and defaults upgrades only touch shipped files, but a bad `delete` is still yours.
5. **Use limits** (`limits`, `tags`) on anything strong, so one player cannot stack it.
6. **Add a comment** above anything non-obvious. Comments are free and survive reloads.

{% hint style="info" %}
If you break something, `/ages admin regen <id>` restores a shipped file, saving your copy to `plugins/Ages/backup/` first. See [Administration and Troubleshooting](administration-and-troubleshooting.md).
{% endhint %}
