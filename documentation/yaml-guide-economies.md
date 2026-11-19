# YAML Guide: Economies

An economy file decides **how money behaves** for a civilization: whether money exists at all, how much of each building's income reaches the shared treasury, who pays for buildings, and what players can do with treasury money. Put it in `plugins/Ages/economies/<id>.yml` and run `/ages reload`. The shipped `economies/_example.yml` is the quick reference. This page explains every key.

## Minimum viable economy

Only the `id` is required:

```yaml
id: myserver_plain
name: "&7Plain Economy"
```

That gives money enabled, no income reaching the treasury (everything stays on the buildings), buyers paying from their own balance, no conversion, no withdrawals, 2 policy slots, no cost and a 24 hour cooldown.

## The complete annotated template

```yaml
# ---------------------------------------------------------------- identity
id: myserver_example             # REQUIRED. Unique. Lowercase. Match the file name.
name: "&bExample Economy"        # Shown in menus. Defaults to the id.
description:
  - "&7One line on how it plays."
icon: EMERALD                    # Menu icon.
min_age: village                 # Earliest age it can be adopted. Omit for any age.

# ---------------------------------------------------------------- does money exist?
money_enabled: true              # false = Barter-style. Default true.
money_to_points: 0.5             # Only when money_enabled is false: RP gained per unit of
                                 #   building money. Default 0.5. Must be 0 or more.
contribution_money: true         # Members may pay money into the treasury. Defaults to money_enabled.

# ---------------------------------------------------------------- where does income go?
income_to_civ: 0.5               # 0 to 1. Share of building income sent to the treasury.
                                 #   The rest stays on the building. Default 0. Values are clamped.

# ---------------------------------------------------------------- who pays for buildings?
charge_purchases_to_civ: false   # true = money costs come from the treasury. Default false
                                 #   (the buyer's Vault balance pays).

# ---------------------------------------------------------------- treasury tools
exchange_rate: 0.3               # /ages convert: RP gained per unit of treasury money.
                                 #   0 = conversion not allowed. Default 0. Must be 0 or more.
withdraw_limit: 600              # Money a role with WITHDRAW_TREASURY may take per rolling
                                 #   24 hours. 0 = withdrawals not allowed. Default 0.

# ---------------------------------------------------------------- effects
modifiers:                       # Same keys as policy effects.
  money_output_mult: 1.1
  points_output_mult: 0.95

# ---------------------------------------------------------------- policies
policies: [open_market, craft_guilds]   # Ids of kind: economy policies. Others are ignored.
policy_slots: 2                  # Default 2.

# ---------------------------------------------------------------- switching
change_cost: {points: 500, money: 900}   # Paid by the civilization. Money comes from the treasury.
change_cooldown_hours: 24                 # Default 24.
```

## Key reference

| Key                           | Required | Default                 | Notes                                                       |
| ----------------------------- | -------- | ----------------------- | ----------------------------------------------------------- |
| `id`                          | yes      | none                    | A blank id skips the file. Duplicates are skipped.          |
| `name`, `description`, `icon` | no       | id, none, default       | Display only.                                               |
| `min_age`                     | no       | none                    | An unknown age is ignored with a console note.              |
| `money_enabled`               | no       | `true`                  | `false` turns all building money into RP.                   |
| `money_to_points`             | no       | `0.5`                   | Only used when money is disabled. Cannot be negative.       |
| `contribution_money`          | no       | same as `money_enabled` | Controls `/ages contribute money`.                          |
| `income_to_civ`               | no       | `0`                     | Clamped to 0 to 1.                                          |
| `charge_purchases_to_civ`     | no       | `false`                 | Treasury pays building money costs.                         |
| `exchange_rate`               | no       | `0`                     | `/ages convert` rate. `0` disables it. Cannot be negative.  |
| `withdraw_limit`              | no       | `0`                     | Per 24 hours. `0` disables withdrawals. Cannot be negative. |
| `modifiers`                   | no       | none                    | Numeric effects.                                            |
| `policies`                    | no       | none                    | Only `kind: economy` policies work here.                    |
| `policy_slots`                | no       | `2`                     |                                                             |
| `change_cost`                 | no       | free                    | `points` and `money`. Items are not charged.                |
| `change_cooldown_hours`       | no       | `24`                    |                                                             |

## How money flows

### Income

When a building earns money (a `money:` product, an `always` entry, tax or sales):

1. The amount is multiplied by `money_output_mult` (government x economy x policies) and by `economy.income_multiplier` from `settings.yml`.
2. If `money_enabled` is `false`, **all** of it becomes RP at `money_to_points`.
3. Otherwise the **treasury share** is `income_to_civ` plus the `income_to_civ` effect from policies and modifiers, clamped to 0 to 1. A government with `force_income_to_civ` makes it 1.
4. The treasury share is paid into the **treasury**. The rest is **stored on the building** until a member collects it into their own Vault balance.

**Worked example.** A building earns `{money: 100}`. The economy has `money_output_mult 1.1` and `income_to_civ 0.5`, and an active policy adds `income_to_civ +0.1`:

* Amount: 100 x 1.1 = 110.
* Treasury share: 0.5 + 0.1 = 0.6, so 66 goes to the treasury.
* The remaining 44 waits on the building for its owner (or a role with `VIEW_FINANCES` or `SPEND_TREASURY`) to collect.

### Purchases

Resource points always come from the civilization. Items always come from the buyer's inventory. The **money** part is paid by:

| Condition                                                                                                                          | Who pays                                                                                                                |
| ---------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `money_enabled: false`                                                                                                             | Nobody. The money part is dropped and RP costs are multiplied by `economy.no_money_purchase_points_mult` (default 2.0). |
| `charge_purchases_to_civ: true`, or the government has `force_treasury_purchases`, or `economy.force_core_purchase_treasury` is on | The treasury. The buyer needs `SPEND_TREASURY`.                                                                         |
| Otherwise                                                                                                                          | The buyer's own Vault balance.                                                                                          |

### Treasury tools

* `/ages contribute money <n>` pays money into the treasury if `contribution_money` is true.
* `/ages convert <n>` turns treasury money into RP at `exchange_rate`. `0` turns it off.
* `/ages withdraw <n>` pays treasury money to the player, up to `withdraw_limit` per rolling 24 hours (plus building and policy bonuses).

## Designing an economy

| You want                                                       | Set                                                                                                                  |
| -------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| **No money, points only** (Barter-like)                        | `money_enabled: false`, tune `money_to_points`, leave `exchange_rate` and `withdraw_limit` at 0.                     |
| **Private enterprise**, players keep what their buildings earn | `income_to_civ` low (0 to 0.25), `charge_purchases_to_civ: false`, a `withdraw_limit` of 0.                          |
| **A shared treasury** that builds things                       | `income_to_civ` high (0.75 to 1.0), `charge_purchases_to_civ: true`. Give the right roles `SPEND_TREASURY`.          |
| **Mixed**                                                      | `income_to_civ` around 0.5, purchases from Vault, a moderate `withdraw_limit` and an `exchange_rate` of 0.25 to 0.5. |
| **Prevent cashing out**                                        | `withdraw_limit: 0`.                                                                                                 |
| **Prevent money turning into points**                          | `exchange_rate: 0`.                                                                                                  |
| **Make the economy a big prize**                               | A high `money_output_mult` modifier, with a high `change_cost` and a long cooldown.                                  |

## Worked example: Guild Economy

```yaml
id: guild_economy
name: "&bGuild Economy"
description: ["&7Half of every building's income reaches the treasury."]
icon: EMERALD
min_age: village
money_enabled: true
money_to_points: 0.5               # unused while money is enabled
exchange_rate: 0.3                 # 100 treasury money converts to 30 RP
contribution_money: true
withdraw_limit: 600                # 600 money per member role per 24 hours
charge_purchases_to_civ: false     # buyers pay from their own Vault balance
income_to_civ: 0.5                 # half of building income reaches the treasury
modifiers:
  money_output_mult: 1.1           # +10% building money
  points_output_mult: 0.95         # -5% points: the trade-off
policies: [open_market, craft_guilds, market_day]
policy_slots: 2
change_cost: {points: 500, money: 900}
change_cooldown_hours: 24
```

## Pitfalls

* **The Barter trap.** Barter earns no treasury money, so a government or economy with a money `change_cost` cannot be afforded from it. Keep a cheap, RP-only economy (the shipped Commons costs 300 RP) as the way out.
* **`income_to_civ` defaults to 0.** Leave it out and every building keeps its income, so nothing reaches the treasury and treasury-paid purchases cannot be afforded.
* **Treasury purchases need permissions.** With `charge_purchases_to_civ`, only roles with `SPEND_TREASURY` can buy. Check your government's roles.
* **Vault is needed.** Purchases from player balances, collecting income, contributing and withdrawing money all use Vault. Without it they are refused.
* **Do not set `exchange_rate` too high.** Money from buildings is created from nothing, so an easy conversion to RP bypasses your price tuning. The shipped economies use 0.25 to 0.5.
* **Combine with `economy.income_multiplier`** in `settings.yml` to scale all income at once rather than editing every building.

## Messages you may see

| Console message                        | Fix                                                                        |
| -------------------------------------- | -------------------------------------------------------------------------- |
| `missing 'id'`                         | Add an `id`.                                                               |
| `rates and limits cannot be negative`  | `money_to_points`, `exchange_rate` and `withdraw_limit` must be 0 or more. |
| `unknown policy 'x' ignored`           | The policy id does not exist.                                              |
| `unknown min_age 'x', treated as none` | The age does not exist.                                                    |
