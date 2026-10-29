# Economies

An **economy** (economic system) controls how money, resource points and contributions behave. It is independent of the government. Files live in `plugins/Ages/economies/`. Players change it with `/ages economy set <id>` (needs `CHANGE_ECONOMY`).

## Two currencies

| Currency                 | Where it comes from                                         | Used for                                                                            |
| ------------------------ | ----------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| **Resource points (RP)** | `always: {points}` and recipes, plus material contributions | Buying buildings, advancing ages, adopting policies, changing government or economy |
| **Treasury money**       | `money:` outputs, tax buildings, `/ages contribute money`   | Buildings with a treasury cost, and withdrawals (`/ages withdraw`)                  |

Personal funds are the player's own **Vault** balance, and are only touched by `contribute money` and `withdraw`.

## Shipped economies

| Economy          | Min age  | Money? | `money_to_points` | `exchange_rate` | Withdraw limit | Modifiers                                                                        | Slots | Change cost |
| ---------------- | -------- | ------ | ----------------- | --------------- | -------------- | -------------------------------------------------------------------------------- | ----- | ----------- |
| Barter (default) | none     | **no** | 0.5               | 0 (no convert)  | 0              | `contribution_rate_mult 1.2`                                                     | 1     | free        |
| Commons          | none     | yes    | 0.5               | 0               | 0              | `points_output_mult 1.1`, `money_output_mult 0.6`, `contribution_rate_mult 1.25` | 2     | 40 RP       |
| Market           | Village  | yes    | 0.5               | 0.3             | 400            | `money_output_mult 1.2`, `points_output_mult 0.95`                               | 2     | 80 RP       |
| Mercantile       | Medieval | yes    | 0.5               | 0.25            | 1000           | `money_output_mult 1.3`, `building_cost_mult 1.1`, `points_output_mult 0.9`      | 2     | 300 RP      |
| Planned          | Medieval | yes    | 0.8               | 0.5             | 0              | `points_output_mult 1.2`, `money_output_mult 0.7`, `building_cost_mult 0.96`     | 2     | 300 RP      |

Change cooldown is 24 hours for Barter, Commons and Market, and 48 hours for Mercantile and Planned.

### Policies each economy offers

| Economy    | Policies                                                          |
| ---------- | ----------------------------------------------------------------- |
| Barter     | communal\_stores, hearth\_tax, open\_fields                       |
| Commons    | communal\_stores, work\_quotas                                    |
| Market     | open\_market, trade\_tariffs, road\_tolls, war\_bonds, war\_taxes |
| Mercantile | open\_market, investment\_bonds, trade\_tariffs                   |
| Planned    | work\_quotas, investment\_bonds                                   |

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
modifiers: {money_output_mult: 1.2, points_output_mult: 0.95}
policies: [open_market]
policy_slots: 2
change_cost: {points: 80}
change_cooldown_hours: 24
```

| Field                                  | Meaning                                                                                                                                                                                                                    |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `money_enabled`                        | `false`: all treasury **money income is converted to RP** at `money_to_points`, and money contributions are refused.                                                                                                       |
| `money_to_points`                      | RP per unit of money when money is disabled. Must be 0 or more.                                                                                                                                                            |
| `exchange_rate`                        | `/ages convert`: RP gained per unit of treasury money. `0` means conversion is not allowed. The file notes that a fair value is about 0.25.                                                                                |
| `contribution_money`                   | Whether members may pay money into the treasury.                                                                                                                                                                           |
| `withdraw_limit`                       | Money a role with `WITHDRAW_TREASURY` may take per rolling 24 hours. Treasury and Bank buildings add to it (`provides.withdraw_limit`), and policies add with `withdraw_limit_add`. `0` means withdrawals are not allowed. |
| `modifiers`                            | Effect keys, see [Policies and Effects](policies-and-effects.md).                                                                                                                                                          |
| `policies`, `policy_slots`             | Policy ids offered and how many can be active.                                                                                                                                                                             |
| `change_cost`, `change_cooldown_hours` | Cost and cooldown to switch to this economy.                                                                                                                                                                               |

Rates and limits cannot be negative.

## Contributions

Members turn materials into RP with `/ages contribute hand` or `all`. The per-item values are in `settings.yml` under `economy.contributions.items`, multiplied by `contribution_rate_mult`. Items not listed are not accepted. See [settings.yml Reference](/broken/pages/settings-yml-reference).

## Money rules

* Money income goes to the treasury, multiplied by `money_output_mult` and `economy.income_multiplier`.
* Withdrawals are checked per player over a rolling 24 hours.
* The ledger (`/ages treasury`) merges production and tax income within `economy.ledger.coalesce_minutes`.
* With Barter, buildings that produce `money:` still work: the money becomes RP instead.

Buildings create money from nothing. If you run a Vault economy with real value, lower \`economy.income\_multiplier\`, or use Barter, Commons or Planned.
