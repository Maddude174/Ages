# Release Notes 0.0.47

This page lists what changed between **0.0.3A** and **0.0.47**. `CHANGELOG.md` in the zip still stops at 0.0.3A, so everything here comes from comparing the source trees (0.0.3A, 0.0.4, 0.0.41 and 0.0.47). Versions 0.0.4 and 0.0.41 were short-lived builds, and 0.0.47 supersedes them.

## Summary

| Area                 | Change                                                                                                                                                                              |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Money model          | Building money income is **split** between the treasury and the building, and money costs are paid from the **player's Vault balance** by default. See [Economies](economies.md).   |
| Prices               | Almost every cost was raised by one to two orders of magnitude, and most buildings now also cost **money**. See [Balance and pricing](release-notes-0.0.47.md#balance-and-pricing). |
| Governments          | Three new governments (Anarchy, Communism, Dictatorship), `leader_selection: none`, and new government-level purchase and income rules.                                             |
| Policies             | 33 new policies (65 in total), a new effect `income_to_civ`, and every cost rescaled.                                                                                               |
| Buildings            | New **Embassy** and `placement: foreign`. Early gatherers can be built in the wild. The Woodcutter is a progressive, selectable building.                                           |
| Recipes              | New `select_as` key groups tiered recipes under one menu choice. `require_level` and `require_age` are minimums.                                                                    |
| Age progress         | Disabled buildings, higher upgrade levels and aliases now count toward age requirements.                                                                                            |
| Upgrades of defaults | `DefaultsSync` is at version **15** and now **replaces every shipped file, including `settings.yml`**. See [Upgrading](release-notes-0.0.47.md#upgrading-from-an-older-version).    |
| Loader fixes         | `conflict_scope` and `tnt_requires_online` are read. The `leader` and `military` role bundles exist. Unknown tags or materials in `groups.yml` are warnings instead of errors.      |
| Interface            | New civilization menu layout, and a separate **Production** panel on the building menu.                                                                                             |
| Metrics              | Anonymous bStats statistics, on by default (`metrics.enabled`).                                                                                                                     |

## The money model

|                                  | 0.0.3A                      | 0.0.47                                                                                                                                                                                                         |
| -------------------------------- | --------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Building money income            | All to the treasury         | Treasury share = `economy.income_to_civ` plus the policy effect `income_to_civ`, clamped 0 to 1. **The rest stays on the building** until someone collects it from the core menu into their own Vault balance. |
| Building money costs             | Treasury (`SPEND_TREASURY`) | The buyer's **Vault** balance, unless the economy sets `charge_purchases_to_civ: true` or the government sets `force_treasury_purchases: true`.                                                                |
| Economies without money (Barter) | Money costs converted       | Money costs are **dropped** and RP costs are **multiplied** by `economy.no_money_purchase_points_mult` (default 2.0).                                                                                          |
| Resource points                  | Shared                      | Unchanged: always paid from the civilization.                                                                                                                                                                  |
| Items                            | Buyer's inventory           | Unchanged.                                                                                                                                                                                                     |

If an economy file has no `income_to_civ`, the default is **0**, so all building money stays on the buildings. Vault is effectively required for any economy that uses money.

## Balance and pricing

Shipped prices in 0.0.47 are far above 0.0.3A, while recipe yields and contribution values were not rescaled.

| Item                       | 0.0.3A              | 0.0.47                        |
| -------------------------- | ------------------- | ----------------------------- |
| Forager                    | 6 RP                | **400 RP**                    |
| Woodcutter                 | 8 RP                | **900 RP**                    |
| Hunter's Hut, Fisher's Hut | 8 RP                | **400 RP**                    |
| Advance to Tribal          | 25 RP + 16 oak logs | **2,250 RP**                  |
| Advance to Village         | 100 RP + items      | **9,000 RP + 7,900 money**    |
| Advance to Medieval        | 600 RP + items      | **48,750 RP + 27,000 money**  |
| Advance to Industrial      | 2,000 RP + items    | **165,000 RP + 78,750 money** |
| Embassy                    | n/a                 | **17,850 RP + 54,000 money**  |

For comparison, a Forager still earns `0.5` RP per 5-minute cycle and a log is still worth `0.16` RP when contributed (`settings.yml` is unchanged). The Forager's base income alone would take roughly 67 hours of uptime to repay its price. I have not playtested this, so it may be intended, but treat it as a balance point to review before opening a server.

## Age progress counting

Age requirements now count buildings more generously:

* A building at a **higher upgrade level** counts as the base type.
* **Aliases** resolve to the main building id.



## New and changed content

| Content     | Change                                                                                                                                        |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| Governments | Anarchy, Communism and Dictatorship added. Theocracy now uses the roles faithful, acolyte, elder, high\_priest, and a different modifier set. |
| Buildings   | Embassy added (64 buildings in total).                                                                                                        |
| Policies    | 65 in total. Costs rescaled, and most now cost money too.                                                                                     |
| Economies   | Rebalanced. Commons and Planned charge purchases to the treasury.                                                                             |
| Woodcutter  | Chest core, stone and iron and diamond axe tiers by building level, `select_as` wood types, `forestry` tag.                                   |

## Recipe selection (`select_as`)

Recipes in `selected` mode can share a `select_as` key, so one menu choice (for example `oak`) stands for several tiers. When a family is chosen, the highest unlocked tier runs first. If the chosen recipe cannot run this cycle, another unlocked recipe that can run is used. The status line only says `Needs ...` when no unlocked recipe could run.

## Upgrading from an older version

{% hint style="danger" %}
The defaults version is **15**. On the first start after upgrading from an older version, Ages **replaces every shipped default file with the jar's version**, including `settings.yml`. Your values are **not merged**. Your previous files are copied to `plugins/Ages/backup/defaults-v<old>-<timestamp>/`.
{% endhint %}

* Files you created yourself (new ids or new file names) are never touched.
* Edits you made to a shipped file are lost from the live copy and must be re-applied from the backup.
* A legacy `town.yml` is backed up and deleted. It is **not** migrated.
* Obsolete shipped files listed in `obsolete-defaults.txt` are removed. Buildings saved under old ids keep working through aliases.
* Back up `ages.db` and read the console lines that begin `Updating default configuration`.

## Fixed since 0.0.3A

* `buildings.conflict_scope` and `protection.tnt_requires_online` are read from `settings.yml`.
* The shipped Woodcutter and Embassy load (the `logs` and `window` block groups now exist).
* The shipped Communism and Dictatorship governments load (`leader` and `military` bundles added, `appointed` used).
* Unknown materials or tags in `groups.yml` produce `(ignored)` warnings rather than errors.
* Notices about disabled buildings now go to members who can manage buildings, not only the owner.

## Remaining content issues

* Some governments list policies of the wrong `kind` (for example Communism lists many economy policies, and Barter lists `neolithic_sharing`, a government policy). A policy is only offered through the list that matches its `kind`, so those entries do nothing.
* Five policies are offered by nothing: `blood_feud`, `frugal_rule`, `rail_subsidy`, `seasonal_camps` and `secret_police`.

