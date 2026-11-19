# YAML Guide: Policies

A policy is a **modifier** a civilization can adopt, limited by slots. Put it in `plugins/Ages/policies/<id>.yml`, **offer it** from a government or economy, and run `/ages reload`. The shipped `policies/_example.yml` and `policies/_effects.yml` are the quick references. This page explains every key.

## Minimum viable policy

Only the `id` is strictly required, but a policy does nothing without `effects`:

```yaml
id: myserver_bonus
name: "&aBonus"
kind: government
effects: {points_output_mult: 1.05}
```

## The complete annotated template

```yaml
id: myserver_example             # REQUIRED. Unique. Lowercase. Match the file name.
name: "&aExample Policy"         # Shown in menus. Defaults to the id.
description:                     # Say the numbers. Players should not have to guess.
  - "&7+10% resource points."
  - "&cProduction runs 5% slower."
icon: WHEAT                      # Menu icon.
kind: economy                    # government | economy. Default government. Decides which
                                 #   slot pool it uses AND which list offers it.
min_age: tribal                  # Earliest age it can be adopted. Omit for any age.
exclusive_group: harvest_rules   # Only one active policy per group name. Omit for none.
effects:                         # What it does. Multipliers multiply, others add.
  points_output_mult: 1.10
  production_speed_mult: 0.95
cost: {points: 150, money: 50}   # Paid once on adoption by the civilization (money from the
                                 #   treasury). Items are not charged.
```

## Key reference

| Key                           | Required | Default           | Notes                                                        |
| ----------------------------- | -------- | ----------------- | ------------------------------------------------------------ |
| `id`                          | yes      | none              | A blank id skips the file. Duplicates are skipped.           |
| `name`, `description`, `icon` | no       | id, none, default | Display only.                                                |
| `kind`                        | no       | `government`      | Must be `government` or `economy`, otherwise an error.       |
| `min_age`                     | no       | none              | Checked when adopting.                                       |
| `exclusive_group`             | no       | none              | A free-text name shared by policies that exclude each other. |
| `effects`                     | no       | none              | Numeric values only. Nested maps flatten to dotted keys.     |
| `cost`                        | no       | free              | `points` and `money`.                                        |

## Making it available

A policy file alone does nothing. It must appear in the `policies:` list of a **government** (for `kind: government`) or an **economy** (for `kind: economy`):

```yaml
# governments/clan_council.yml
policies: [neolithic_sharing, myserver_bonus]
```

* A policy in the **wrong** list (an economy policy in a government) is ignored.
* An unknown id is ignored with a console note.
* The same policy can be offered by several governments or economies.
* `kind` also decides the slot pool: government policies use the government's `policy_slots`, economy policies use the economy's.

## Effects

Effects come from the government (`modifiers`), the economy (`modifiers`), every active policy (`effects`) and some buildings. They combine like this:

* A key ending in **`_mult`** **multiplies**. The default is 1.0.
* Every other key **adds**. The default is 0.
* A nested map becomes a dotted key: `tag_limit_add: {housing: 2}` is `tag_limit_add.housing: 2`.

| Effect key               | Combine  | What it changes                                                                                                                        | Shipped policies stay roughly within |
| ------------------------ | -------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------ |
| `production_speed_mult`  | multiply | How fast building timers run. `1.1` is 10% faster.                                                                                     | 0.90 to 1.15                         |
| `production_speed_bonus` | add      | Extra additive speed, capped by `production.max_speed_bonus`.                                                                          | not used                             |
| `points_output_mult`     | multiply | RP from production and `always`.                                                                                                       | 0.95 to 1.12                         |
| `money_output_mult`      | multiply | Building money income, before `economy.income_multiplier`.                                                                             | 0.80 to 1.20                         |
| `income_to_civ`          | add      | Added to the economy's treasury share, then clamped to 0 to 1. Positive moves income to the treasury, negative leaves it on buildings. | -0.20 to +0.25                       |
| `building_cost_mult`     | multiply | RP and money cost of buildings and age cores. Items are never scaled. Below 1 is cheaper.                                              | 0.88 to 1.10                         |
| `contribution_rate_mult` | multiply | RP from contributing items.                                                                                                            | 0.95 to 1.25                         |
| `claim_radius_add`       | add      | Blocks added to the claim radius.                                                                                                      | -2 to +3                             |
| `housing_mult`           | multiply | Scales total housing.                                                                                                                  | about 1.1                            |
| `withdraw_limit_add`     | add      | Added to the daily withdrawal limit.                                                                                                   | about 100                            |
| `tag_limit_add.<tag>`    | add      | Raises the cap of a limit pool such as `housing`, `farming`, `faith` or `military`.                                                    | 1 to 2                               |
| `repair_cost_mult`       | multiply | RP to repair a disabled building.                                                                                                      | 0.75 to 1.00                         |

Keys that are not in this table are **stored and shown** in the civilization menu, but they change nothing until the plugin reads them. Typos therefore fail silently, so copy key names exactly.

### How stacking works

Two sources of `points_output_mult`, 1.10 and 1.05, give 1.10 x 1.05 = 1.155. Two `claim_radius_add` sources, +2 and -1, give +1. Because every active policy, the government and the economy all stack, a few small numbers go a long way.

## Exclusive groups

Policies that share an `exclusive_group` cannot be active together, across both pools. Use them for **either-or** choices (a tax regime, a military doctrine). The shipped `fiscal` group holds all the tax policies, so a civilization can only run one tax policy.

## Costs and slots

* `cost` is paid **once** when the policy is adopted, by the civilization. Revoking does not refund it.
* Adoption needs `MANAGE_POLICIES`.
* Slots are per pool, from `policy_slots` on the government and the economy.
* Players adopt or revoke with `/ages policy <id>`, which toggles.

## Worked examples

### An economy policy with a trade-off: Shared Harvest

```yaml
id: shared_harvest
name: "&aShared Harvest"
description:
  - "&7The whole civilization shares the harvest: +10% resource points."
  - "&cProduction runs 5% slower."
icon: WHEAT
kind: economy                      # offered from an economy's policies: list
min_age: tribal
exclusive_group: harvest_rules     # cannot run with another harvest_rules policy
effects:
  points_output_mult: 1.10         # the benefit
  production_speed_mult: 0.95      # the cost
cost: {points: 150}
```

### A government policy with a price: War Levy

```yaml
id: war_levy
name: "&cWar Levy"
description: ["&7Wider claims and cheaper repairs, but costlier buildings."]
icon: IRON_SWORD
kind: government                   # offered from a government's policies: list
min_age: medieval
effects:
  claim_radius_add: 2              # +2 blocks of claim
  repair_cost_mult: 0.85           # repairs 15% cheaper
  building_cost_mult: 1.05         # buildings 5% dearer: the trade-off
cost: {points: 300, money: 100}    # money comes from the treasury
```

## Design notes

* **Give every benefit a price.** A policy that is only good is just a free upgrade. Pair a gain with a loss on a different axis.
* **Stay small.** Most shipped multipliers are between 0.9 and 1.15. Stacking five policies at 1.15 is a 2x multiplier.
* **Describe the numbers.** The description is the only place a player sees what a policy does before adopting it.
* **Use the income split deliberately.** `income_to_civ` is the lever between a shared-treasury and a private-income civilization, and negative values are useful trade-offs.
* **Scale the cost with the age.** The shipped policies cost more in later ages. Price a policy against what one cycle of the civilization earns.
* **Gate with `min_age`** rather than a high price.
* **Offer it somewhere.** Check the policy appears in `/ages policy list` for a civilization under the government or economy you added it to.

## Messages you may see

| Console message                                           | Fix                                                                                                    |
| --------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| `missing 'id'`                                            | Add an `id`.                                                                                           |
| `kind must be government or economy`                      | Fix the value.                                                                                         |
| `unknown policy 'x' ignored` (in a government or economy) | The id in that list does not exist.                                                                    |
| Policy never appears in `/ages policy list`               | It is not in the right list, its `kind` does not match, or the civilization has not reached `min_age`. |
