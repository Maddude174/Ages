# Policies and Effects

A **policy** is a modifier that a civilization can adopt, limited by slots. Files live in `plugins/Ages/policies/`. Each policy belongs to either the **government** or the **economy** pool. There are **65** shipped policies: 31 government and 34 economy. Costs are RP, with money added on many (paid by the civilization, with the money from the treasury).

```yaml
id: tithes
name: '&fTithes'
description: ['The faithful tithe: +10% resource points.']
icon: BELL
kind: government        # government | economy
min_age: village
exclusive_group: fiscal # only one active policy per group
effects: {points_output_mult: 1.1}
cost: {points: 150}     # paid once when adopted
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

## Shipped government policies (31)

| Policy             | Min age    | Group  | Effects                                                                                                    | Cost               |
| ------------------ | ---------- | ------ | ---------------------------------------------------------------------------------------------------------- | ------------------ |
| Neolithic Sharing  | Neolithic  | none   | `income_to_civ 0.2`, `points_output_mult 1.05`, `building_cost_mult 0.95`                                  | 80 RP              |
| Oral Law           | Neolithic  | none   | `repair_cost_mult 0.95`, `points_output_mult 1.02`                                                         | 80 RP              |
| Seasonal Hunt      | Neolithic  | none   | `points_output_mult 1.1`                                                                                   | 80 RP              |
| Blood Feud         | Tribal     | none   | `production_speed_mult 0.92`, `claim_radius_add 1`, `repair_cost_mult 0.9`                                 | 100 RP             |
| Communal Labor     | Tribal     | none   | `building_cost_mult 0.9`, `points_output_mult 1.05`                                                        | 100 RP             |
| Council Fires      | Tribal     | none   | `claim_radius_add 1`, `production_speed_mult 0.98`                                                         | 100 RP             |
| Land Grants        | Tribal     | none   | `building_cost_mult 0.9`, `income_to_civ -0.05`, `claim_radius_add 1`                                      | 100 RP             |
| Path Markers       | Tribal     | none   | `claim_radius_add 1`                                                                                       | 100 RP             |
| Seasonal Camps     | Tribal     | none   | `production_speed_mult 1.08`, `claim_radius_add -1`                                                        | 100 RP             |
| Tribal Muster      | Tribal     | none   | `production_speed_mult 1.1`                                                                                | 100 RP             |
| Charter of Rights  | Village    | none   | `income_to_civ -0.1`, `points_output_mult 1.06`, `money_output_mult 1.05`                                  | 150 RP             |
| Church Lands       | Village    | none   | `points_output_mult 1.07`, `claim_radius_add 1`, `income_to_civ 0.05`                                      | 150 RP             |
| Civic Projects     | Village    | none   | `production_speed_mult 1.1`, `building_cost_mult 1.08`, `points_output_mult 1.05`                          | 150 RP             |
| Corvée             | Village    | none   | `building_cost_mult 0.88`, `production_speed_mult 0.95`, `points_output_mult 0.97`                         | 150 RP             |
| Feudal Oaths       | Village    | none   | `claim_radius_add 2`, `income_to_civ -0.1`, `building_cost_mult 0.95`                                      | 150 RP             |
| Fire Watch         | Village    | none   | `repair_cost_mult 0.85`                                                                                    | 150 RP             |
| Frugal Rule        | Village    | fiscal | `building_cost_mult 0.9`, `money_output_mult 0.92`, `points_output_mult 0.95`                              | 150 RP             |
| Granary Law        | Village    | none   | `points_output_mult 1.1`, `production_speed_mult 1.03`                                                     | 150 RP             |
| Militia Levy       | Village    | none   | `claim_radius_add 2`, `production_speed_mult 0.95`, `repair_cost_mult 0.9`                                 | 150 RP             |
| Royal Charters     | Village    | none   | `building_cost_mult 0.92`, `claim_radius_add 1`, `points_output_mult 0.97`                                 | 150 RP             |
| Tithes             | Village    | fiscal | `points_output_mult 1.1`                                                                                   | 150 RP             |
| Tribute            | Village    | fiscal | `income_to_civ 0.15`, `money_output_mult 1.05`                                                             | 150 RP             |
| Village Militia    | Village    | none   | `claim_radius_add 1`, `repair_cost_mult 0.92`                                                              | 150 RP             |
| Castle Ward        | Medieval   | none   | `claim_radius_add 2`, `repair_cost_mult 0.85`, `building_cost_mult 1.05`                                   | 250 RP + 50 money  |
| Guild Charters     | Medieval   | none   | `production_speed_mult 1.08`, `building_cost_mult 1.06`, `points_output_mult 1.04`                         | 250 RP + 50 money  |
| Guild Oaths        | Medieval   | none   | `production_speed_mult 1.06`, `points_output_mult 1.04`                                                    | 250 RP + 50 money  |
| Sappers' Corps     | Medieval   | none   | `repair_cost_mult 0.8`, `building_cost_mult 1.08`                                                          | 250 RP + 50 money  |
| Scorched Earth     | Medieval   | none   | `repair_cost_mult 0.75`, `production_speed_mult 0.9`, `money_output_mult 0.95`                             | 250 RP + 50 money  |
| Secret Police      | Medieval   | none   | `production_speed_mult 1.05`, `points_output_mult 0.97`, `repair_cost_mult 0.95`                           | 250 RP + 50 money  |
| University Charter | Medieval   | none   | `production_speed_mult 1.1`, `points_output_mult 1.06`                                                     | 250 RP + 50 money  |
| Labour Unions      | Industrial | none   | `points_output_mult 1.1`, `money_output_mult 1.08`, `building_cost_mult 1.1`, `production_speed_mult 0.97` | 400 RP + 100 money |

## Shipped economy policies (34)

| Policy              | Min age    | Group  | Effects                                                                           | Cost               |
| ------------------- | ---------- | ------ | --------------------------------------------------------------------------------- | ------------------ |
| Forage Rights       | Neolithic  | none   | `points_output_mult 1.08`, `building_cost_mult 0.97`                              | 60 RP              |
| Gift Economy        | Neolithic  | none   | `points_output_mult 1.1`, `money_output_mult 0.85`, `building_cost_mult 0.95`     | 60 RP              |
| Tool Sharing        | Neolithic  | none   | `building_cost_mult 0.93`, `production_speed_mult 1.02`                           | 60 RP              |
| Barter Posts        | Tribal     | none   | `points_output_mult 1.05`, `production_speed_mult 1.03`                           | 80 RP              |
| Communal Stores     | Tribal     | none   | `points_output_mult 1.08`, `income_to_civ 0.15`, `money_output_mult 0.9`          | 80 RP              |
| Kin Stores          | Tribal     | none   | `points_output_mult 1.06`, `money_output_mult 0.92`                               | 80 RP              |
| Potlatch            | Tribal     | none   | `points_output_mult 1.12`, `money_output_mult 0.8`                                | 80 RP              |
| Civic Tithe         | Village    | fiscal | `income_to_civ 0.18`, `money_output_mult 0.95`                                    | 50 RP + 200 money  |
| Common Pasture      | Village    | none   | `points_output_mult 1.05`, `claim_radius_add 1`                                   | 50 RP + 200 money  |
| Craft Guilds        | Village    | none   | `points_output_mult 1.08`, `production_speed_mult 1.04`                           | 50 RP + 200 money  |
| Hearth Tax          | Village    | fiscal | `income_to_civ 0.1`, `building_cost_mult 1.05`                                    | 50 RP + 200 money  |
| Market Day          | Village    | none   | `money_output_mult 1.06`                                                          | 50 RP + 200 money  |
| Open Fields         | Village    | none   | `points_output_mult 1.08`, `claim_radius_add 1`                                   | 50 RP + 200 money  |
| Open Market         | Village    | none   | `money_output_mult 1.12`                                                          | 50 RP + 200 money  |
| Private Enterprise  | Village    | none   | `income_to_civ -0.2`, `money_output_mult 1.1`, `points_output_mult 1.05`          | 50 RP + 200 money  |
| Road Tolls          | Village    | fiscal | `income_to_civ 0.1`, `money_output_mult 1.03`                                     | 50 RP + 200 money  |
| Sales Levy          | Village    | fiscal | `income_to_civ 0.12`, `money_output_mult 0.96`                                    | 50 RP + 200 money  |
| Trade Tariffs       | Village    | fiscal | `income_to_civ 0.12`, `money_output_mult 0.97`                                    | 50 RP + 200 money  |
| Central Bank        | Medieval   | none   | `money_output_mult 1.15`, `building_cost_mult 1.05`, `income_to_civ 0.08`         | 75 RP + 400 money  |
| Free Ports          | Medieval   | none   | `money_output_mult 1.15`, `income_to_civ -0.05`                                   | 75 RP + 400 money  |
| Investment Bonds    | Medieval   | none   | `money_output_mult 1.12`, `building_cost_mult 1.05`                               | 75 RP + 400 money  |
| Merchant Courts     | Medieval   | none   | `money_output_mult 1.08`, `income_to_civ 0.05`                                    | 75 RP + 400 money  |
| Merchant Privileges | Medieval   | none   | `money_output_mult 1.1`, `income_to_civ -0.12`, `points_output_mult 1.03`         | 75 RP + 400 money  |
| Public Works        | Medieval   | none   | `building_cost_mult 0.9`, `income_to_civ 0.08`, `points_output_mult 1.04`         | 75 RP + 400 money  |
| State Requisition   | Medieval   | fiscal | `income_to_civ 0.25`, `money_output_mult 0.9`, `points_output_mult 0.95`          | 75 RP + 400 money  |
| Toll Roads          | Medieval   | none   | `money_output_mult 1.07`, `claim_radius_add 1`, `income_to_civ 0.06`              | 75 RP + 400 money  |
| War Bonds           | Medieval   | none   | `money_output_mult 1.05`, `income_to_civ 0.1`, `building_cost_mult 1.05`          | 75 RP + 400 money  |
| War Taxes           | Medieval   | fiscal | `income_to_civ 0.2`, `building_cost_mult 1.1`, `production_speed_mult 0.95`       | 75 RP + 400 money  |
| Work Quotas         | Medieval   | none   | `points_output_mult 1.1`, `production_speed_mult 0.97`, `money_output_mult 0.95`  | 75 RP + 400 money  |
| Factory Acts        | Industrial | none   | `production_speed_mult 0.95`, `money_output_mult 1.1`, `points_output_mult 1.05`  | 100 RP + 800 money |
| Factory Boards      | Industrial | none   | `production_speed_mult 1.15`, `building_cost_mult 1.08`, `income_to_civ 0.1`      | 100 RP + 800 money |
| Patent Office       | Industrial | none   | `production_speed_mult 1.08`, `building_cost_mult 1.05`, `money_output_mult 1.06` | 100 RP + 800 money |
| Rail Subsidy        | Industrial | none   | `production_speed_mult 1.12`, `building_cost_mult 0.95`, `money_output_mult 0.97` | 100 RP + 800 money |
| Stock Exchange      | Industrial | none   | `money_output_mult 1.2`, `income_to_civ -0.05`                                    | 100 RP + 800 money |

The **fiscal** exclusive group holds Frugal Rule, Tithes and Tribute (government) and Civic Tithe, Hearth Tax, Road Tolls, Sales Levy, State Requisition, Trade Tariffs and War Taxes (economy). Only one fiscal policy can be active at a time. Policies with a positive `income_to_civ` push building money into the treasury, and negative ones leave it on the buildings for owners to collect. Under Barter (no money) the income-split effects do nothing, because all money is converted to RP.

Eight policies are offered by no shipped government or economy: Seasonal Hunt, Tithes, Tribal Muster, Civic Tithe, Patent Office, Stock Exchange, War Bonds and War Taxes. Add their ids to a government's or economy's `policies:` list to use them.

## Writing a policy

1. Copy `policies/_example.yml`.
2. Choose a unique `id` that matches the file name, and set `kind`.
3. Pick effect keys from the table above.
4. Add the id to the `policies:` list of every government (for `kind: government`) or economy (for `kind: economy`) that should offer it.
5. `/ages reload`.
