# Economies

An **economy** (economic system) controls how money, resource points and contributions behave, who pays for buildings, and how much building income reaches the treasury. It is independent of the government. Files live in `plugins/Ages/economies/`. Players change it with `/ages economy set <id>` (needs `CHANGE_ECONOMY`).

## Two currencies

| Currency                 | Where it comes from                                                | Used for                                                                            |
| ------------------------ | ------------------------------------------------------------------ | ----------------------------------------------------------------------------------- |
| **Resource points (RP)** | `always: {points}` and recipes, plus material contributions        | Buying buildings, advancing ages, adopting policies, changing government or economy |
| **Money**                | `money:` outputs, tax and sell buildings, `/ages contribute money` | Building money costs, treasury withdrawals and conversion                           |

Money lives in three places:

| Place                          | Holds                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------- |
| **Treasury**                   | The civilization's shared money.                                                                    |
| **A building's stored income** | Money a building earned that was not sent to the treasury. Collected from the building's core menu. |
| **Personal Vault balance**     | A player's own money. Pays money costs by default and receives collected income.                    |

## Income split (`income_to_civ`)

Every time a building earns money (production, `always`, tax, sell):

1. The amount is multiplied by `money_output_mult` and by `economy.income_multiplier` from `settings.yml`.
2. If the economy has `money_enabled: false`, **all** of it becomes RP at `money_to_points`.
3. Otherwise the treasury share is `income_to_civ` plus the `income_to_civ` effect from policies, clamped to 0 to 1. A government with `force_income_to_civ: true` forces it to 1.
4. That share goes to the **treasury**. The remainder is **stored on the building**, up to the function's `max_stored` (or 1,000,000 if unset).

Stored income is collected from the building's core menu. The collector is paid into their **own Vault balance**. Who can collect: the building's owner, or any role with `VIEW_FINANCES` or `SPEND_TREASURY`. Without Vault the menu shows `Uncollected income` and cannot pay out.

A building using the legacy `payout: owner` setting always stores 100% on the building, ignoring the split.

## Who pays for buildings (`charge_purchases_to_civ`)

| Case                                                                                                                                                                  | Money cost is paid by                                                                                                     |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| Economy has `money_enabled: false`                                                                                                                                    | Nobody. Money costs are **dropped** and RP costs are multiplied by `economy.no_money_purchase_points_mult` (default 2.0). |
| `economy.force_core_purchase_treasury: true` in `settings.yml`, `charge_purchases_to_civ: true` on the economy, or `force_treasury_purchases: true` on the government | The **treasury**. The buyer needs `SPEND_TREASURY`.                                                                       |
| Otherwise (default)                                                                                                                                                   | The buyer's **Vault** balance. If there is no Vault economy, the purchase is refused.                                     |

Resource points always come from the civilization (and need `SPEND_POINTS`). Item costs always come from the buyer's inventory. Ledger lines note whether money came from the treasury or the player.

## Shipped economies

| Economy          | Min age  | Money? | `income_to_civ` | Buyers pay with | Withdraw limit | Modifiers                                                                        | Slots | Change cost          | Cooldown |
| ---------------- | -------- | ------ | --------------- | --------------- | -------------- | -------------------------------------------------------------------------------- | ----- | -------------------- | -------- |
| Barter (default) | none     | **no** | 0               | RP only (x2)    | 0              | `contribution_rate_mult 1.2`                                                     | 1     | 200 RP               | 24 h     |
| Commons          | none     | yes    | **0.85**        | **treasury**    | 0              | `points_output_mult 1.1`, `money_output_mult 0.6`, `contribution_rate_mult 1.25` | 2     | 300 RP               | 24 h     |
| Market           | Village  | yes    | 0.25            | Vault           | 400            | `money_output_mult 1.2`, `points_output_mult 0.95`                               | 2     | 400 RP + 800 money   | 24 h     |
| Mercantile       | Medieval | yes    | 0.1             | Vault           | 1,000          | `money_output_mult 1.3`, `building_cost_mult 1.1`, `points_output_mult 0.9`      | 2     | 500 RP + 1,500 money | 48 h     |
| Planned          | Medieval | yes    | **0.55**        | **treasury**    | 0              | `points_output_mult 1.2`, `money_output_mult 0.7`, `building_cost_mult 0.96`     | 2     | 600 RP + 2,000 money | 48 h     |

`money_to_points` and `exchange_rate` per economy: Barter 0.5 and 0 (no convert), Commons 0.6 and 0.4, Market 0.5 and 0.3, Mercantile 0.5 and 0.25, Planned 0.8 and 0.5.

An economy's change cost is paid by the civilization, with money coming from the **treasury**. Barter earns no treasury money, so the first stop out of Barter is **Commons**, which costs RP only.

### Policies each economy offers

| Economy    | Policies                                                                                                                               |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| Barter     | gift\_economy, forage\_rights, tool\_sharing, kin\_stores, barter\_posts, potlatch, communal\_stores                                   |
| Commons    | communal\_stores, open\_fields, common\_pasture, gift\_economy, kin\_stores                                                            |
| Market     | open\_market, private\_enterprise, market\_day, craft\_guilds, trade\_tariffs, sales\_levy, road\_tolls, hearth\_tax                   |
| Mercantile | open\_market, merchant\_privileges, free\_ports, investment\_bonds, merchant\_courts, toll\_roads, private\_enterprise, trade\_tariffs |
| Planned    | central\_bank, public\_works, work\_quotas, state\_requisition, factory\_boards, factory\_acts, investment\_bonds, rail\_subsidy       |

Several economy policies (`civic_tithe`, `patent_office`, `stock_exchange`, `war_bonds`, `war_taxes`) are not offered by any shipped economy. Add their ids to an economy's `policies:` list to use them. See [Policies and Effects](policies-and-effects.md).

## Economy file reference

```yaml
id: example_economy
name: "&bExample Economy"
description: ["Shown in the economy screen."]
icon: EMERALD
min_age: village
money_enabled: true
money_to_points: 0.5
exchange_rate: 0.3
contribution_money: true
withdraw_limit: 400
charge_purchases_to_civ: false
income_to_civ: 0.25
modifiers: {money_output_mult: 1.2, points_output_mult: 0.95}
policies: [open_market]
policy_slots: 2
change_cost: {points: 400, money: 800}
change_cooldown_hours: 24
```

| Field                                  | Meaning                                                                                                                                                                                                        |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `money_enabled`                        | `false`: all money income becomes RP at `money_to_points`, money contributions are refused, and money building costs are dropped (RP costs are multiplied).                                                    |
| `money_to_points`                      | RP per unit of money when money is disabled. Must be 0 or more.                                                                                                                                                |
| `exchange_rate`                        | `/ages convert`: RP gained per unit of treasury money. `0` means conversion is not allowed.                                                                                                                    |
| `contribution_money`                   | Whether members may pay money into the treasury.                                                                                                                                                               |
| `withdraw_limit`                       | Money a role with `WITHDRAW_TREASURY` may take per rolling 24 hours. Treasury, Bank and Stock Exchange buildings add to it, and policies add with `withdraw_limit_add`. `0` means withdrawals are not allowed. |
| `charge_purchases_to_civ`              | `true`: building money costs come from the treasury. Default `false`.                                                                                                                                          |
| `income_to_civ`                        | Treasury share of building money income, 0 to 1. Default **0** (everything stays on the buildings). Values are clamped to that range.                                                                          |
| `modifiers`                            | Effect keys, see [Policies and Effects](policies-and-effects.md).                                                                                                                                              |
| `policies`, `policy_slots`             | Policy ids of `kind: economy` offered, and how many can be active.                                                                                                                                             |
| `change_cost`, `change_cooldown_hours` | Cost (points and money from the civilization) and cooldown to switch to this economy.                                                                                                                          |

Rates and limits cannot be negative.

## Contributions

Members turn materials into RP with `/ages contribute hand` or `all`. The value of each item lives in **`plugins/Ages/contributions.yml`**, which ships with **154 items**:

```yaml
items:
  COBBLESTONE: 0.325
  OAK_LOG: 0.845
  COAL: 1.3
  IRON_INGOT: 4.55
  DIAMOND: 15.6
```

* Values are RP **per item**, before `contribution_rate_mult` from your government, economy and policies.
* **Remove a line or set it to `0`** to stop accepting an item. Items that are not listed cannot be contributed.
* Every name must be a valid material on your server. An unknown name stops the plugin from starting, so check the console after editing. See [Compatibility and Requirements](compatibility-and-requirements.md).
* An older `economy.contributions.items` block in `settings.yml` is still read, but `contributions.yml` takes priority.
* Restore the shipped values with `/ages admin regen contributions`.

Players can see what is accepted with `/ages contribute list` and look up one item with `/ages contribute search <name>`. The file's own comments give rough benchmarks, for example a stack of 64 cobblestone is worth about 21 RP and a stack of logs about 54 RP, tuned for roughly 6 to 12 hours to reach the Village Age when mixed with early production.

{% hint style="info" %}
Buildings create money from nothing. If your Vault economy has real value, lower `economy.income_multiplier`, or use Barter, Commons or Planned, where most money stays in the shared treasury or is converted to RP.
{% endhint %}
