# Player Guide

Everything a player needs after the civilization is founded.

## Core concepts

| Concept                  | Meaning                                                                                                                                                                        |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Civilization**         | The group of players that shares land, a treasury, buildings, a government and an economy. Internally called a _town_.                                                         |
| **Age**                  | The tech tier of the civilization: Neolithic, Tribal, Village, Medieval, Industrial. Each age unlocks buildings and enlarges claims.                                           |
| **Core block**           | The one block that defines a building. Placing it validates the surrounding structure and registers the building.                                                              |
| **Resource points (RP)** | The plugin's own shared currency. Most buildings cost RP and many generate it every cycle.                                                                                     |
| **Money**                | Real money. It lives in the shared **treasury**, on individual **buildings** (uncollected income) and in each player's **Vault balance**. How it flows depends on the economy. |
| **Role**                 | A set of permissions granted by the government.                                                                                                                                |
| **Policy**               | A slot-limited modifier a government or economy can adopt.                                                                                                                     |

## The civilization menu

`/ages` opens the civilization menu. It is laid out in rows:

| Item                           | What it does                                                          |
| ------------------------------ | --------------------------------------------------------------------- |
| **Civilization info** (paper)  | Summary and next-age status. Click for the advancement checklist.     |
| **Vote** (lectern)             | Opens the ballot. Active only under elected governments.              |
| **Catalogue**                  | Browse and buy buildings and see costs.                               |
| **Members and roles**          | Member list, roles, appointments.                                     |
| **Buildings**                  | Your buildings. Click one to open its core menu.                      |
| **Government** and **Economy** | Roles, leadership and policies, shown when your role may change them. |
| **Treasury**                   | Balances and the recent ledger.                                       |

A building's core menu has structure status, upgrades, storage, **Collect income** and relocation, plus a separate **Production** panel for recipes, timing and status.

## Commands

The base command is `/ages` (aliases `/age` and `/town`). Tab completion is available for all subcommands.

### Civilization and membership

| Command                                                        | Purpose                                                      |
| -------------------------------------------------------------- | ------------------------------------------------------------ |
| `/ages` or `/ages menu`                                        | Open the civilization GUI                                    |
| `/ages info [civilization]`                                    | Summary and advancement checklist                            |
| `/ages buildings`                                              | List your civilization's buildings                           |
| `/ages invite <player>` / `accept` / `leave` / `kick <player>` | Membership                                                   |
| `/ages rank list`                                              | Show roles                                                   |
| `/ages rank create <name> [weight]` / `delete <name>`          | Create or delete a custom role (if the government allows it) |
| `/ages rank perm <name> <PERM> <true\|false>`                  | Toggle one permission on a role                              |
| `/ages rank set <player> <role>`                               | Assign a role                                                |
| `/ages owner transfer <player>`                                | Transfer technical ownership (owner only)                    |
| `/ages disband`                                                | Disband the civilization (owner only)                        |

### Economy

| Command                          | Purpose                                                                                              |
| -------------------------------- | ---------------------------------------------------------------------------------------------------- |
| `/ages treasury`                 | View balances and recent ledger                                                                      |
| `/ages contribute hand` / `all`  | Turn held or all accepted items into RP                                                              |
| `/ages contribute money <n>`     | Pay your own Vault money into the treasury (if the economy allows it)                                |
| `/ages withdraw <n>`             | Withdraw treasury money into your Vault balance (needs `WITHDRAW_TREASURY`, subject to a 24 h limit) |
| `/ages convert <n>`              | Convert treasury money into RP at the economy's exchange rate                                        |
| `/ages economy [list\|set <id>]` | View or change the economic system                                                                   |

### Government and leadership

| Command                             | Purpose                                                                                      |
| ----------------------------------- | -------------------------------------------------------------------------------------------- |
| `/ages government [list\|set <id>]` | View or change the government                                                                |
| `/ages policy [list\|<id>]`         | List policies, or adopt/revoke one                                                           |
| `/ages vote <player>`               | Vote in an open election (elected governments)                                               |
| `/ages leader <player>`             | Appoint the leader (appointed governments). Not available when the government has no leader. |

### Diplomacy and war

| Command                                                          | Purpose                                 |
| ---------------------------------------------------------------- | --------------------------------------- |
| `/ages diplomacy`                                                | Relations list and GUI entry            |
| `/ages ally <civ> [defensive]` / `breakally <civ>`               | Alliance                                |
| `/ages hostile <civ>`                                            | Declare hostility                       |
| `/ages truce <civ> [hours]`                                      | Propose a truce                         |
| `/ages accept_diplo <civ>`                                       | Accept an alliance or truce proposal    |
| `/ages war <civ> <goal>`                                         | Declare war with a goal                 |
| `/ages tributary <civ>` / `release <civ>`                        | Tributary relationship                  |
| `/ages revolt`                                                   | Break free as a tributary or settlement |
| `/ages civilwar` (alias `revolution`) / `rebel` (alias `defect`) | Internal civil war                      |
| `/ages logistics`                                                | Show rail logistics networks            |

See [Diplomacy and War](diplomacy-and-war.md) for the mechanics.

## Permissions

| Node                   | Default | Meaning                                                                       |
| ---------------------- | ------- | ----------------------------------------------------------------------------- |
| `ages.admin`           | op      | Admin tools, `/ages reload`, give cores, points and money                     |
| `ages.bypass`          | op      | Ignore civilization land protection                                           |
| `ages.creative.bypass` | op      | Buy and place cores in creative mode when `shop.deny_creative_purchase` is on |

Everything else a player can do is governed by their in-civilization **role**, not by server permission nodes. See [Governments](governments.md).

## Buying and placing buildings

1. Open the **Catalogue** (you need the `USE_SHOP` role permission). Buildings are grouped by category (Government, Homes & Faith, Food & Farming, Resources, Crafts & Industry, Storage & Trade, Defence, Infrastructure).
2. Pay the cost shown. This needs `PURCHASE_BUILDINGS`, plus:
   * **RP**: from the civilization, needs `SPEND_POINTS`.
   * **Items**: from your inventory.
   * **Money**: from your **own Vault balance** by default. Under Commons, Planned or Communism it comes from the **treasury** instead (needs `SPEND_TREASURY`). Under Barter there is no money cost, but the RP cost is doubled.
3. Build the structure around where the core will go. Each building lists required blocks (for example 24 wall blocks, 2 door blocks, 1 light source).
4. Place the core block. Ages scans the area, validates the requirements, checks limits, conflicts, age and placement rules, and registers the building.
5. If you later break the structure below the requirements, the building becomes **disabled** until it is restored. Disabled buildings stop producing and bleed RP. Repairs cost 10% of the purchase price (modified by `repair_cost_mult`).

Beds and doors are two blocks each, so one bed or one door counts as `2` toward a requirement.

The catalogue is remote-purchase-restricted by default (`shop.remote_purchase: false`): you must be at your civilization core.

Prices are high in 0.0.47. A Forager costs 400 RP and the age upgrades run to tens of thousands of RP, so plan your contributions and production. See [Building Catalogue](building-catalogue.md).

## Collecting building income

When a building earns money, a share goes to the treasury and the rest stays on the building. Open the building's core menu and click **Collect income** to pay the stored money into your own Vault balance. The building's owner can collect, and so can any role with `VIEW_FINANCES` or `SPEND_TREASURY`. This needs a Vault economy. See [Economies](economies.md).

## Advancing through the ages

Open `/ages info` for a live checklist. To advance, meet the next age's requirements, then buy and place its **core building** (Chief's Hut, Village Hall, Town Hall, City Hall). Placing the new core retires the previous one in a single database transaction, so a failed placement changes nothing. Disabled buildings and higher upgrade levels still count toward the checklist. See [Ages](ages.md) for the costs.

## Governments, economies and policies

* Change the government with `/ages government set <id>`. It costs RP and money (`change_cost`, paid by the civilization, with money from the treasury) and has a cooldown (`change_cooldown_hours`). **Anarchy cannot be left this way**, only through revolution.
* Change the economy with `/ages economy set <id>`, with its own cost and cooldown. The economy decides whether money exists, who pays for buildings and how income is split.
* Adopt policies with `/ages policy <id>`. Slots are limited by the government or economy. Policies in the same `exclusive_group` cannot be active together.

## Land protection

Civilization land is protected from outsiders. By default hostile mobs cannot spawn there, mob explosions cannot damage blocks, and TNT does nothing in peacetime. TNT can damage land when civilizations are **Hostile** or at **War**, and only while a member is online (`protection.tnt_requires_online`). See [`protection` in settings.yml](settings-yml-reference.md).
