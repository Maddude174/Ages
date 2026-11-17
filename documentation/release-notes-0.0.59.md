# Release Notes 0.0.59

This page covers what server owners need to know when moving to **0.0.59**. It lists the changes since 0.0.47 first, then a short recap of the larger changes since 0.0.3A for anyone upgrading from an older version.

{% hint style="danger" %}
**Back up before you upgrade.** The shipped-defaults version is now **31**. The first start after upgrading from an older version **replaces every shipped file**, including `settings.yml` and `contributions.yml`. Your values are **not merged**. Previous files are copied to `plugins/Ages/backup/defaults-v<old>-<timestamp>/`. See [Configuration Overview](configuration-overview.md).
{% endhint %}

## Requirements

Ages 0.0.59 runs on **Paper 1.21.x through 26.3** and needs **Java 25**. See [Compatibility and Requirements](compatibility-and-requirements.md) for the version notes, including content that only exists on newer Minecraft versions.

## Changes since 0.0.47

| Area                | Change                                                                                                                                                                                   |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Java and Paper      | The jar targets Java 25 and the Paper 26.3 API. `api-version` stays at `1.21`.                                                                                                           |
| Prices              | Shipped building, age, policy and government prices were **lowered** again from 0.0.47. A Forager costs 200 RP and advancing to Tribal costs 1,000 RP.                                   |
| Contributions       | Item values moved to a new **`contributions.yml`** (154 items) and were raised roughly five times. They are no longer in `settings.yml`.                                                 |
| Core storage        | Every building has a built-in **27-slot storage** that works with any core block. Purchased cores can be parked in a **Core storage** menu until you are ready to place them.            |
| Cannons             | Siege is now a building **function** (`type: siege`) with editable shell recipes. Cannons only fire at civilizations you are **at war** with, unless the owner switches on **Override**. |
| Governments         | **Dictatorship was removed.** **Feudal** was added, and **Monarchy** was redesigned as the centralised government.                                                                       |
| Policies            | All policy costs and effects were rebalanced. There are still 65.                                                                                                                        |
| Production          | Gatherer, food and industry buildings use progressive tiers with a **Choose product** menu.                                                                                              |
| Age requirements    | **Disabled buildings no longer count** toward age requirements.                                                                                                                          |
| Settings            | New `protection.remove_core_block_on_destroy` and `economy.force_core_purchase_treasury`. `migration.legacy_money_cost_to_points` was removed.                                           |
| Commands            | New `/ages particles`, `/ages contribute list` and `search`, and `/ages admin regen`.                                                                                                    |
| Integrations        | WorldEdit and FastAsyncWorldEdit replace the WorldGuard hook. Other plugins can use a public API and events.                                                                             |
| Metrics             | bStats now reports a broader set of anonymous statistics. See [Integrations](integrations.md).                                                                                           |
| Recipes             | The `optional: true` recipe flag is **gone**. A recipe that uses an unknown item now fails the building.                                                                                 |
| Building containers | A building whose `logistics` input or output is `CORE` uses its built-in storage, so its core block no longer needs to be a chest or barrel.                                             |

### What this means in practice

* **Revisit your price and contribution tuning.** Contribution values rose about fivefold while prices fell, so progression is much faster than in 0.0.47.
* **Civilizations on the removed Dictatorship government** are treated as the default government by the registry lookup. Check their roles and use `/ages admin gov <civ> <governmentId>` to move them to a government that exists.
* **Anything you edited in a shipped file is replaced** on the first start. Re-apply it from the backup folder, or keep your changes in new files with new ids.
* **Admins can restore any shipped file** at any time with `/ages admin regen` (see [Administration and Troubleshooting](administration-and-troubleshooting.md)).
* **Rail logistics are off by default.** An age must contain `rails: true` for civilizations in it to run them. See [Logistics](logistics.md).

## Recap: larger changes since 0.0.3A

| Area        | Summary                                                                                                                                                                                                                                                                                                            |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Money       | Building money income is split between the treasury and the building (`income_to_civ`). Money costs are paid from the buyer's **Vault** balance by default, or from the treasury in treasury-purchase economies. Barter has no money: money costs are dropped and RP costs doubled. See [Economies](economies.md). |
| Governments | `leader_selection: none`, `exit_only_by_revolution`, `force_treasury_purchases` and `force_income_to_civ`. Ten permission bundles exist.                                                                                                                                                                           |
| Buildings   | `placement: foreign` and the Embassy, `select_as` recipe families, and `cost.money`.                                                                                                                                                                                                                               |
| Settings    | `conflict_scope`, `tnt_requires_online`, `no_money_purchase_points_mult` and `metrics.enabled` are read.                                                                                                                                                                                                           |
| Defaults    | Upgrades replace shipped files instead of merging.                                                                                                                                                                                                                                                                 |
