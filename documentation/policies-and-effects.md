# Policies and Effects

A **policy** is a modifier that a civilization can adopt, limited by slots. Files live in `plugins/Ages/policies/`. Each policy belongs to either the **government** or the **economy** pool.

```yaml
id: tithes
name: '&fTithes'
description: ['The faithful tithe: +10% resource points.']
icon: BELL
kind: government        # government | economy
min_age: village
exclusive_group: fiscal # only one active policy per group
effects: {points_output_mult: 1.1}
cost: {points: 80}      # paid once when adopted
```

| Field             | Meaning                                                                                          |
| ----------------- | ------------------------------------------------------------------------------------------------ |
| `kind`            | `government` or `economy`. Decides which pool of slots it uses. Any other value is a load error. |
| `min_age`         | Earliest age it can be adopted.                                                                  |
| `exclusive_group` | Only one active policy per group.                                                                |
| `effects`         | Effect keys applied while the policy is active.                                                  |
| `cost`            | Charged once on adoption from the shared balances.                                               |

## Adopting policies

`/ages policy <id>` **toggles**: it revokes the policy if it is active, otherwise adopts it. Adoption fails with a clear message if:

1. Your role lacks `MANAGE_POLICIES`.
2. The government or economy does not **offer** the policy.
3. The civilization has not reached `min_age`.
4. All slots of that `kind` are in use (`policy_slots` on the government or economy).
5. An active policy shares the `exclusive_group`.
6. The civilization cannot pay the cost.

Revoking is always allowed with the permission, and it is not refunded.

## Effects

Effects come from the government (`modifiers`), the economy (`modifiers`), active policies (`effects`) and buildings. They are combined:

* Keys ending in `_mult` **multiply** (default 1.0).
* Every other key **adds** (default 0.0).
* Nested maps are flattened to dotted keys (`tag_limit_add: {housing: 2}` becomes `tag_limit_add.housing`).

| Effect key               | Combine  | Gameplay effect                                                                                               |
| ------------------------ | -------- | ------------------------------------------------------------------------------------------------------------- |
| `production_speed_mult`  | multiply | Scales how fast building timers run. `2.0` is twice as fast.                                                  |
| `production_speed_bonus` | add      | Additive speed, capped by `production.max_speed_bonus`.                                                       |
| `points_output_mult`     | multiply | Scales RP yields from production and `always`.                                                                |
| `money_output_mult`      | multiply | Scales treasury money yields, before `economy.income_multiplier`.                                             |
| `building_cost_mult`     | multiply | Scales point and money costs of buildings and age cores. Item costs are **never** scaled. Below 1 is cheaper. |
| `contribution_rate_mult` | multiply | Scales RP from material contributions.                                                                        |
| `claim_radius_add`       | add      | Extra blocks of claim radius.                                                                                 |
| `housing_mult`           | multiply | Scales total housing capacity.                                                                                |
| `withdraw_limit_add`     | add      | Added to the economy's daily withdrawal limit.                                                                |
| `tag_limit_add.<tag>`    | add      | Raises the cap of a limit pool such as `housing`, `faith`, `farming`, `military`.                             |
| `repair_cost_mult`       | multiply | Scales RP charged to repair disabled buildings (base is 10% of purchase cost).                                |

Unknown keys are stored and shown in the civilization menu but have no gameplay effect until the code reads them.

{% hint style="warning" %}
Stacking many multipliers compounds quickly. Prefer small numbers (1.05 to 1.2 for multipliers, a few points for adders).
{% endhint %}

## Shipped policies

### Government policies

| Policy             | Min age    | Group  | Effects                                                | Cost (RP) |
| ------------------ | ---------- | ------ | ------------------------------------------------------ | --------- |
| Neolithic Sharing  | Neolithic  | none   | `contribution_rate_mult 1.15`                          | 20        |
| Seasonal Hunt      | Neolithic  | none   | `points_output_mult 1.1`                               | 20        |
| Tribal Muster      | Tribal     | none   | `production_speed_mult 1.1`                            | 20        |
| Communal Labor     | none       | none   | `production_speed_mult 1.1`, `money_output_mult 0.95`  | 30        |
| Frugal Rule        | none       | fiscal | `building_cost_mult 0.92`, `points_output_mult 0.95`   | 50        |
| Tribute            | none       | fiscal | `points_output_mult 1.1`, `contribution_rate_mult 0.9` | 40        |
| Tithes             | Village    | fiscal | `points_output_mult 1.1`                               | 80        |
| Guild Oaths        | Village    | none   | `production_speed_mult 1.08`                           | 20        |
| Land Grants        | Village    | none   | `claim_radius_add 12`, `building_cost_mult 1.05`       | 100       |
| Royal Charters     | Village    | none   | `money_output_mult 1.05`, `claim_radius_add 4`         | 120       |
| Civic Projects     | Medieval   | none   | `housing_mult 1.1`, `production_speed_mult 1.05`       | 250       |
| Guild Charters     | Medieval   | none   | `money_output_mult 1.1`                                | 250       |
| Militia Levy       | Medieval   | none   | `production_speed_mult 0.95`                           | 20        |
| University Charter | Medieval   | none   | `points_output_mult 1.1`                               | 20        |
| Sappers Corps      | Medieval   | none   | `repair_cost_mult 0.7`                                 | 40        |
| Scorched Earth     | Medieval   | none   | `repair_cost_mult 1.75`                                | 40        |
| Rail Subsidy       | Industrial | none   | `production_speed_mult 1.05`                           | 20        |

### Economy policies

| Policy           | Min age    | Effects                                                 | Cost (RP) |
| ---------------- | ---------- | ------------------------------------------------------- | --------- |
| Communal Stores  | none       | `contribution_rate_mult 1.15`                           | 30        |
| Work Quotas      | none       | `production_speed_mult 1.1`, `money_output_mult 0.9`    | 80        |
| Hearth Tax       | Tribal     | `claim_radius_add -1`                                   | 20        |
| Open Fields      | Village    | `points_output_mult 1.12`                               | 20        |
| Open Market      | Village    | `money_output_mult 1.1`                                 | 120       |
| Trade Tariffs    | Village    | `money_output_mult 1.06`, `contribution_rate_mult 0.95` | 100       |
| Investment Bonds | Medieval   | `building_cost_mult 0.95`                               | 300       |
| Road Tolls       | Medieval   | `money_output_mult 1.15`                                | 20        |
| War Taxes        | Medieval   | `repair_cost_mult 0.9`, `money_output_mult 1.1`         | 40        |
| Factory Acts     | Industrial | `production_speed_mult 1.12`                            | 20        |
| War Bonds        | Industrial | `money_output_mult 1.2`                                 | 20        |

The **fiscal** exclusive group (Frugal Rule, Tribute, Tithes) allows only one active fiscal policy.

## Writing a policy

1. Copy `policies/_example.yml`.
2. Choose a unique `id` that matches the file name, and set `kind`.
3. Pick effect keys from the table above.
4. Add the id to the `policies:` list of every government or economy that should offer it.
5. `/ages reload`.
