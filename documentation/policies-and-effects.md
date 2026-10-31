# Policies and Effects

A **policy** is a modifier that a civilization can adopt, limited by slots. Files live in `plugins/Ages/policies/`. Each policy belongs to either the **government** or the **economy** pool. There are **65** shipped policies, 32 government and 33 economy. Costs are RP, with money added on most (paid by the civilization, with money from the treasury).

```yaml
id: tithes
name: '&fTithes'
description: ['The faithful tithe: +10% resource points.']
icon: BELL
kind: government        # government | economy
min_age: village
exclusive_group: fiscal # only one active policy per group
effects: {points_output_mult: 1.1}
cost: {points: 4000, money: 1200}   # paid once when adopted
```

| Field             | Meaning                                                                                                                       |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `kind`            | `government` or `economy`. Decides which pool of slots it uses **and** which list offers it. Any other value is a load error. |
| `min_age`         | Earliest age it can be adopted.                                                                                               |
| `exclusive_group` | Only one active policy per group.                                                                                             |
| `effects`         | Effect keys applied while the policy is active.                                                                               |
| `cost`            | Charged once on adoption from the civilization's balances.                                                                    |

## Adopting policies

`/ages policy <id>` **toggles**: it revokes the policy if it is active, otherwise adopts it. Adoption fails with a clear message if:

1. Your role lacks `MANAGE_POLICIES`.
2. The government (for `kind: government`) or the economy (for `kind: economy`) does not **offer** the policy.
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

| Effect key               | Combine  | Gameplay effect                                                                                                                                         |
| ------------------------ | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `production_speed_mult`  | multiply | Scales how fast building timers run. `2.0` is twice as fast.                                                                                            |
| `production_speed_bonus` | add      | Additive speed, capped by `production.max_speed_bonus`.                                                                                                 |
| `points_output_mult`     | multiply | Scales RP yields from production and `always`.                                                                                                          |
| `money_output_mult`      | multiply | Scales building money yields, before `economy.income_multiplier`.                                                                                       |
| `income_to_civ`          | add      | Added to the economy's `income_to_civ`, then clamped 0 to 1. Positive sends more building money to the treasury, negative leaves more on the buildings. |
| `building_cost_mult`     | multiply | Scales point and money costs of buildings and age cores. Item costs are **never** scaled. Below 1 is cheaper.                                           |
| `contribution_rate_mult` | multiply | Scales RP from material contributions.                                                                                                                  |
| `claim_radius_add`       | add      | Extra blocks of claim radius.                                                                                                                           |
| `housing_mult`           | multiply | Scales total housing capacity.                                                                                                                          |
| `withdraw_limit_add`     | add      | Added to the economy's daily withdrawal limit.                                                                                                          |
| `tag_limit_add.<tag>`    | add      | Raises the cap of a limit pool such as `housing`, `faith`, `farming`, `military`.                                                                       |
| `repair_cost_mult`       | multiply | Scales RP charged to repair disabled buildings (base is 10% of purchase cost).                                                                          |

Unknown keys are stored and shown in the civilization menu but have no gameplay effect until the code reads them.

{% hint style="warning" %}
Stacking many multipliers compounds quickly. Prefer small numbers (1.05 to 1.2 for multipliers, a few points for adders).
{% endhint %}

## Shipped government policies (32)

Costs are `RP / money`.

| Policy             | Min age    | Group  | Effects                                                    | Cost            |
| ------------------ | ---------- | ------ | ---------------------------------------------------------- | --------------- |
| Seasonal Camps     | Neolithic  | none   | `claim_radius_add 2`                                       | 600             |
| Fire Watch         | Neolithic  | none   | `repair_cost_mult 0.95`                                    | 750             |
| Seasonal Hunt      | Neolithic  | none   | `points_output_mult 1.1`                                   | 1,000           |
| Oral Law           | Neolithic  | none   | `contribution_rate_mult 1.05`, `claim_radius_add 1`        | 1,150           |
| Neolithic Sharing  | Neolithic  | none   | `contribution_rate_mult 1.15`                              | 1,500           |
| Council Fires      | Tribal     | none   | `contribution_rate_mult 1.05`                              | 1,500           |
| Path Markers       | Tribal     | none   | `claim_radius_add 2`                                       | 1,150           |
| Tribal Muster      | Tribal     | none   | `production_speed_mult 1.1`                                | 1,000           |
| Blood Feud         | Tribal     | none   | `claim_radius_add 1`, `building_cost_mult 1.05`            | 1,900           |
| Tribute            | Tribal     | none   | `income_to_civ 0.1`, `contribution_rate_mult 1.05`         | 1,250           |
| Communal Labor     | none       | none   | `production_speed_mult 1.1`, `money_output_mult 0.95`      | 2,250           |
| Frugal Rule        | none       | fiscal | `building_cost_mult 0.92`, `points_output_mult 0.95`       | 3,750           |
| Guild Oaths        | Village    | none   | `production_speed_mult 1.08`                               | 1,500 / 1,350   |
| Village Militia    | Village    | none   | `claim_radius_add 1`, `repair_cost_mult 0.95`              | 1,500 / 500     |
| Granary Law        | Village    | none   | `points_output_mult 1.05`, `income_to_civ 0.08`            | 2,400 / 2,050   |
| Charter of Rights  | Village    | none   | `building_cost_mult 0.97`, `contribution_rate_mult 1.05`   | 3,000 / 2,700   |
| Tithes             | Village    | fiscal | `points_output_mult 1.1`                                   | 4,000 / 1,200   |
| Land Grants        | Village    | none   | `claim_radius_add 12`, `building_cost_mult 1.05`           | 7,500 / 6,750   |
| Royal Charters     | Village    | none   | `money_output_mult 1.05`, `claim_radius_add 4`             | 6,000 / 1,800   |
| Militia Levy       | Medieval   | none   | `production_speed_mult 0.95`                               | 1,500 / 1,350   |
| University Charter | Medieval   | none   | `production_speed_mult 1.08`, `points_output_mult 1.05`    | 4,000 / 1,250   |
| Sappers Corps      | Medieval   | none   | `repair_cost_mult 0.7`                                     | 2,000 / 600     |
| Scorched Earth     | Medieval   | none   | `repair_cost_mult 1.75`                                    | 2,000 / 600     |
| Church Lands       | Medieval   | none   | `tag_limit_add.faith 1`, `points_output_mult 1.04`         | 3,150 / 2,700   |
| Feudal Oaths       | Medieval   | none   | `claim_radius_add 2`, `contribution_rate_mult 1.08`        | 3,400 / 2,950   |
| Castle Ward        | Medieval   | none   | `repair_cost_mult 0.9`, `claim_radius_add 1`               | 3,750 / 3,400   |
| Civic Projects     | Medieval   | none   | `housing_mult 1.1`, `production_speed_mult 1.05`           | 18,750 / 16,900 |
| Guild Charters     | Medieval   | none   | `money_output_mult 1.1`                                    | 18,750 / 16,900 |
| Rail Subsidy       | Industrial | none   | `production_speed_mult 1.05`                               | 1,000 / 300     |
| Labour Unions      | Industrial | none   | `production_speed_mult 1.05`, `contribution_rate_mult 1.1` | 4,150 / 3,600   |
| Factory Boards     | Industrial | none   | `production_speed_mult 1.08`, `points_output_mult 1.06`    | 4,500 / 4,050   |
| Secret Police      | Industrial | none   | `claim_radius_add 2`, `building_cost_mult 1.08`            | 4,000 / 1,200   |

The **fiscal** exclusive group (Frugal Rule, Tithes) allows only one active fiscal policy. Blood Feud, Frugal Rule, Rail Subsidy, Seasonal Camps and Secret Police are not offered by any shipped government, so they cannot be adopted unless you add them to a list.

## Shipped economy policies (33)

| Policy              | Min age    | Effects                                                                       | Cost            |
| ------------------- | ---------- | ----------------------------------------------------------------------------- | --------------- |
| Forage Rights       | Neolithic  | `points_output_mult 1.08`                                                     | 750             |
| Tool Sharing        | Neolithic  | `production_speed_mult 1.08`, `points_output_mult 1.05`                       | 600             |
| Gift Economy        | Neolithic  | `contribution_rate_mult 1.15`, `money_output_mult 0.9`                        | 1,150           |
| Kin Stores          | Tribal     | `points_output_mult 1.06`, `income_to_civ 0.05`                               | 1,350           |
| Hearth Tax          | Tribal     | `claim_radius_add -1`                                                         | 1,500           |
| Potlatch            | Tribal     | `contribution_rate_mult 1.2`                                                  | 1,500           |
| Barter Posts        | Tribal     | `contribution_rate_mult 1.1`, `money_output_mult 1.05`                        | 1,650           |
| Communal Stores     | none       | `contribution_rate_mult 1.15`                                                 | 2,250           |
| Work Quotas         | none       | `production_speed_mult 1.1`, `money_output_mult 0.9`                          | 4,000           |
| Open Fields         | Village    | `points_output_mult 1.12`                                                     | 1,500 / 1,350   |
| Common Pasture      | Village    | `points_output_mult 1.07`, `income_to_civ 0.05`                               | 2,100 / 1,800   |
| Sales Levy          | Village    | `income_to_civ 0.1`, `money_output_mult 0.98`                                 | 1,500 / 450     |
| Market Day          | Village    | `money_output_mult 1.12`, `income_to_civ 0.05`                                | 2,650 / 2,250   |
| Civic Tithe         | Village    | `income_to_civ 0.25`                                                          | 2,650 / 2,250   |
| Craft Guilds        | Village    | `production_speed_mult 1.06`, `building_cost_mult 1.03`                       | 2,850 / 2,500   |
| Private Enterprise  | Village    | `income_to_civ -0.15`, `money_output_mult 1.05`                               | 2,000 / 600     |
| Public Works        | Village    | `income_to_civ 0.2`, `building_cost_mult 0.95`                                | 2,250 / 650     |
| Trade Tariffs       | Village    | `money_output_mult 1.06`, `contribution_rate_mult 0.95`, `income_to_civ 0.12` | 5,000 / 1,500   |
| Open Market         | Village    | `money_output_mult 1.1`, `income_to_civ -0.05`                                | 9,000 / 8,100   |
| Road Tolls          | Medieval   | `money_output_mult 1.15`, `income_to_civ 0.08`                                | 1,000 / 300     |
| War Taxes           | Medieval   | `repair_cost_mult 0.9`, `money_output_mult 1.1`, `income_to_civ 0.15`         | 2,000 / 600     |
| Corvee              | Medieval   | `production_speed_mult 1.1`, `building_cost_mult 0.94`                        | 3,000 / 2,700   |
| Toll Roads          | Medieval   | `income_to_civ 0.12`, `money_output_mult 1.05`                                | 2,200 / 650     |
| Merchant Courts     | Medieval   | `money_output_mult 1.1`, `income_to_civ -0.05`                                | 3,600 / 3,150   |
| Merchant Privileges | Medieval   | `income_to_civ -0.2`, `money_output_mult 1.08`                                | 3,750 / 3,400   |
| Free Ports          | Medieval   | `income_to_civ -0.1`, `money_output_mult 1.12`, `building_cost_mult 1.05`     | 4,150 / 3,600   |
| State Requisition   | Medieval   | `income_to_civ 0.35`, `money_output_mult 0.95`                                | 3,000 / 900     |
| Investment Bonds    | Medieval   | `building_cost_mult 0.95`                                                     | 22,500 / 20,250 |
| Factory Acts        | Industrial | `production_speed_mult 1.12`                                                  | 1,500 / 1,350   |
| War Bonds           | Industrial | `money_output_mult 1.2`                                                       | 1,000 / 300     |
| Central Bank        | Industrial | `income_to_civ 0.15`, `money_output_mult 1.08`, `withdraw_limit_add 100`      | 4,900 / 4,300   |
| Stock Exchange      | Industrial | `money_output_mult 1.18`, `income_to_civ -0.08`, `building_cost_mult 1.05`    | 3,500 / 1,050   |
| Patent Office       | Industrial | `production_speed_mult 1.12`, `points_output_mult 1.05`                       | 4,350 / 3,850   |

Policies with a positive `income_to_civ` push building money into the treasury, and negative ones leave it on the buildings for owners to collect. Under Barter (no money) the income-split effects do nothing, because all money is converted to RP.

## Writing a policy

1. Copy `policies/_example.yml`.
2. Choose a unique `id` that matches the file name, and set `kind`.
3. Pick effect keys from the table above.
4. Add the id to the `policies:` list of every government (for `kind: government`) or economy (for `kind: economy`) that should offer it.
5. `/ages reload`.
