# Building Catalogue

All 64 buildings shipped in 0.0.47, with their **base prices**. `RP` is resource points (from the civilization) and `money` is money. Both are before government, economy and policy modifiers (`building_cost_mult`). A dash means the building is not bought directly: core buildings are bought as part of age advancement, and the Fireplace is given.

How the money part is paid depends on the economy: the buyer's Vault balance by default, the treasury in Commons, Planned and Communism, and not at all (RP doubled) under Barter. See [Economies](economies.md).

Function types: **processor** (recipes), **spawner** (villagers and similar), **tax** (income per resident), **none** (passive). "Selected" means the owner picks the recipe in the Production panel. Upgrade prices are shown as `upgrade: RP / money`.

{% hint style="info" %}
Prices are many times higher than in 0.0.3A (for example Forager 6 RP before, 400 RP now). See [Release Notes 0.0.47](release-notes-0.0.47.md#balance-and-pricing).
{% endhint %}

## Government

| Building     | Age        | Function | Price                    | Notes                                                                                                                                                                                                                                                   |
| ------------ | ---------- | -------- | ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Fireplace    | Neolithic  | none     | not sold                 | Founding core, campfire. Claim bonus +2.                                                                                                                                                                                                                |
| Chief's Hut  | Tribal     | none     | age cost                 | Lodestone core. Replaces the Fireplace. Housing +2, claim bonus +4.                                                                                                                                                                                     |
| Village Hall | Village    | tax      | age cost                 | Bell core. Replaces the Chief's Hut. Claim +12, housing +4.                                                                                                                                                                                             |
| Town Hall    | Medieval   | tax      | age cost                 | Gold block core. Claim +20, housing +6.                                                                                                                                                                                                                 |
| City Hall    | Industrial | tax      | age cost                 | Diamond block core. Claim +32, housing +10.                                                                                                                                                                                                             |
| Embassy      | Industrial | none     | 17,850 RP / 54,000 money | Lectern core. `placement: foreign`: must be built inside **another** civilization's borders, never your own, and does not claim land. Pays 8 RP every 5 minutes. Limit 3 per civilization, 1 per player. Upgrades: 11,950 / 47,250 and 18,150 / 54,000. |

## Homes and faith (`community`)

| Building     | Age        | Function             | Price (RP / money) | Notes                                              |
| ------------ | ---------- | -------------------- | ------------------ | -------------------------------------------------- |
| Tribal Hut   | Neolithic  | spawner              | 400                | Housing 2. Limit 6 per civilization, 3 per player. |
| Burial Mound | Neolithic  | none                 | 400                | Respawn point.                                     |
| Cottage      | Village    | spawner              | 2,100 / 2,250      | Housing 4. Upgrade: 5,550 / 7,900.                 |
| Chapel       | Village    | none                 | 8,650 / 2,250      | Respawn point, claim +4, housing +1, `faith`.      |
| Graveyard    | Village    | none                 | 4,200 / 2,250      | Respawn point.                                     |
| Shrine       | Village    | none                 | 2,850 / 2,250      | Respawn point, `faith`.                            |
| Tavern       | Village    | processor            | 6,900 / 2,250      | Housing 2. Upgrade: 13,200 / 10,150.               |
| Bathhouse    | Medieval   | processor            | 17,850 / 11,250    |                                                    |
| Library      | Medieval   | processor            | 35,800 / 14,650    |                                                    |
| Manor House  | Medieval   | spawner              | 37,750 / 14,650    | Housing 8. Upgrade: 49,900 / 37,150.               |
| Monastery    | Medieval   | processor (selected) | 42,400 / 14,650    | Respawn point, `faith`.                            |
| Townhouse    | Medieval   | spawner              | 15,900 / 10,150    | Housing 6. Replaces Cottages.                      |
| Villa        | Medieval   | spawner              | 26,400 / 12,400    | Housing 7.                                         |
| Apartments   | Industrial | spawner              | 69,850 / 37,150    | Housing 16.                                        |
| Grand Manor  | Industrial | spawner              | 89,800 / 37,150    | Housing 12.                                        |
| University   | Industrial | processor            | 119,800 / 37,150   |                                                    |

Housing buildings use the `housing` tag pool.

## Food and farming (`food`)

| Building     | Age       | Function             | Price (RP / money) | Notes                              |
| ------------ | --------- | -------------------- | ------------------ | ---------------------------------- |
| Forager      | Neolithic | processor            | 400                | `placement: anywhere`              |
| Fisher's Hut | Neolithic | processor            | 400                | `placement: anywhere`              |
| Hunter's Hut | Neolithic | processor (random)   | 400                | `placement: anywhere`              |
| Animal Farm  | Village   | processor (selected) | 3,850 / 2,250      | `farming`                          |
| Bakery       | Village   | processor (selected) | 4,200 / 2,250      | Upgrade: 8,400 / 7,900.            |
| Farm         | Village   | processor (selected) | 2,850 / 2,250      | `farming`. Upgrade: 7,450 / 7,900. |
| Smokehouse   | Village   | processor (random)   | 3,850 / 2,250      |                                    |
| Apiary       | Medieval  | processor (all)      | 14,950 / 9,000     | `farming`                          |
| Herbalist    | Medieval  | processor            | 13,200 / 9,000     | `placement: anywhere`              |
| Orchard      | Medieval  | processor            | 12,250 / 9,000     | `farming`                          |
| Plantation   | Medieval  | processor (selected) | 14,050 / 9,000     | `farming`                          |
| Stables      | Medieval  | processor            | 20,650 / 10,150    | `farming`                          |

Buildings placed `anywhere` do not extend your claims.

## Resources (`resources`)

| Building   | Age       | Function             | Price (RP / money) | Notes                                                                                                                                                                                                               |
| ---------- | --------- | -------------------- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Woodcutter | Neolithic | processor (selected) | 900                | Chest core. Choose a wood type, with better axes by building level. `placement: anywhere`, `tags: {forestry: 1}`, limits 4 per civilization and 2 per player. Upgrades: 1,450 (Tribal) and 4,200 / 2,250 (Village). |
| Clay Pit   | Village   | processor            | 2,500 / 2,250      | `placement: anywhere`                                                                                                                                                                                               |
| Sand Pit   | Village   | processor            | 2,100 / 2,250      | `placement: anywhere`                                                                                                                                                                                               |
| Mine       | Village   | processor (first)    | 3,450 / 2,250      | Progressive picks, `placement: anywhere`, `mining`. Upgrades: 16,900 / 9,000 and 39,900 / 30,400. Replaces the old per-ore mines through aliases.                                                                   |

## Crafts and industry (`industry`)

| Building      | Age        | Function             | Price (RP / money) | Notes                                           |
| ------------- | ---------- | -------------------- | ------------------ | ----------------------------------------------- |
| Workshop      | Tribal     | processor (selected) | 550                |                                                 |
| Charcoal Kiln | Village    | processor            | 2,850 / 3,400      |                                                 |
| Pottery       | Village    | processor (selected) | 5,200 / 3,400      |                                                 |
| Apothecary    | Medieval   | processor (selected) | 19,750 / 14,650    |                                                 |
| Armory        | Medieval   | processor (selected) | 30,100 / 16,900    | Upgrade: 69,850 / 47,250.                       |
| Blacksmith    | Medieval   | processor (selected) | 28,200 / 16,900    | Upgrades: 59,850 / 47,250 and 194,200 / 47,250. |
| Glassworks    | Medieval   | processor (selected) | 22,600 / 14,650    |                                                 |
| Smithy        | Medieval   | processor (selected) | 24,450 / 15,750    |                                                 |
| Weaver        | Medieval   | processor (selected) | 15,900 / 13,500    |                                                 |
| Factory       | Industrial | processor (selected) | 64,800 / 47,250    | Limit 2 per civilization.                       |
| Foundry       | Industrial | processor (selected) | 69,850 / 47,250    | Upgrade: 181,650 / 47,250.                      |

## Storage and trade (`trade`)

| Building       | Age        | Function           | Price (RP / money) | Notes                                                                  |
| -------------- | ---------- | ------------------ | ------------------ | ---------------------------------------------------------------------- |
| Storage Hut    | Neolithic  | none               | 400                | Storage 1.                                                             |
| Granary        | Village    | none               | 2,850 / 2,250      | Storage 2.                                                             |
| Storehouse     | Village    | none               | 3,450 / 2,250      | Storage 3.                                                             |
| Marketplace    | Village    | processor (random) | 5,950 / 3,400      | Sells goods for money. Upgrade: 14,050 / 11,250. Alias `trading_post`. |
| Bank           | Medieval   | processor (all)    | 42,400 / 18,000    | Withdraw limit +600.                                                   |
| Treasury       | Medieval   | none               | 28,200 / 15,750    | Earns 4 RP and 6 money per 15 minutes. Withdraw limit +400.            |
| Stock Exchange | Industrial | processor (all)    | 129,750 / 43,900   | Withdraw limit +1,500.                                                 |

Money earned by these buildings is split between the treasury and the building according to the economy's `income_to_civ`.

## Defence (`military`)

| Building   | Age      | Function | Price (RP / money) | Notes                                                                                   |
| ---------- | -------- | -------- | ------------------ | --------------------------------------------------------------------------------------- |
| Watchtower | Village  | none     | 5,200 / 3,400      | Claim bonus +8.                                                                         |
| Cannon     | Medieval | none     | 11,250 / 12,400    | Siege weapon, dispenser core, `military`. Upgrades: 7,450 / 11,250 and 14,500 / 33,750. |
| Guardhouse | Medieval | spawner  | 18,850 / 14,650    | Trains iron golems from iron blocks in its barrel.                                      |
| Keep       | Medieval | none     | 47,200 / 20,250    | Respawn point, claim bonus +16.                                                         |

## Infrastructure (`infrastructure`)

| Building         | Age        | Function | Price (RP / money) | Notes                                                                            |
| ---------------- | ---------- | -------- | ------------------ | -------------------------------------------------------------------------------- |
| Railway Station  | Industrial | none     | 69,850 / 33,750    | Storage 4, logistics capacity +256. Limit 3 per civilization.                    |
| Freight Depot    | Industrial | none     | 79,800 / 33,750    | Storage 8, logistics capacity +512.                                              |
| Telegraph Office | Industrial | none     | 59,850 / 33,750    | Claim bonus +16, production speed +5%.                                           |
| Automation Works | Industrial | none     | 119,800 / 33,750   | Production speed +15% civilization-wide, capped by `production.max_speed_bonus`. |

{% hint style="info" %}
The exact structure requirements, recipes and upgrade levels of each building are in its own file under `plugins/Ages/buildings/<category>/<id>.yml`. They are intentionally data, not code, so they are easy to tune.
{% endhint %}

See also [Buildings](buildings.md) and [Building Functions and Recipes](building-functions-and-recipes.md).
