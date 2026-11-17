# Building Catalogue

All 64 buildings shipped in 0.0.59, with their **base prices**. `RP` is resource points (from the civilization) and `money` is money. Both are before government, economy and policy modifiers (`building_cost_mult`). A dash means the building is not bought directly: core buildings are bought as part of age advancement, and the Fireplace is given.

How the money part is paid depends on the economy: the buyer's Vault balance by default, the treasury in Commons, Planned, Communism and Monarchy (or when `economy.force_core_purchase_treasury` is on), and not at all (RP doubled) under Barter. See [Economies](economies.md).

Function types: **processor** (recipes), **spawner** (villagers and golems), **tax** (income per resident), **siege** (cannon), **none** (passive). _Selected_ means the owner picks the product in the Production panel. Upgrade prices are listed as `RP / money`. Every price here can be changed in the building's own file.

## Government

| Building     | Age        | Core          | Price                   | Notes                                                                                                                                                                                                        |
| ------------ | ---------- | ------------- | ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Fireplace    | Neolithic  | Campfire      | not sold                | Founding core. Claim bonus +2.                                                                                                                                                                               |
| Chief's Hut  | Tribal     | Lodestone     | age cost                | Replaces the Fireplace. Housing +2, claim bonus +4.                                                                                                                                                          |
| Village Hall | Village    | Bell          | age cost                | Tax building. Replaces the Chief's Hut. Claim +12, housing +4.                                                                                                                                               |
| Town Hall    | Medieval   | Gold block    | age cost                | Tax building. Claim +20, housing +6.                                                                                                                                                                         |
| City Hall    | Industrial | Diamond block | age cost                | Tax building. Claim +32, housing +10.                                                                                                                                                                        |
| Embassy      | Industrial | Lectern       | 7,950 RP / 24,000 money | `placement: foreign`: must be built inside **another** civilization's borders, never your own, and does not claim land. Limit 3 per civilization, 1 per player. Upgrades: 5,350 / 21,000 and 8,100 / 24,000. |

## Homes and faith (`community`)

| Building     | Age        | Function             | Price (RP / money) | Notes                                              |
| ------------ | ---------- | -------------------- | ------------------ | -------------------------------------------------- |
| Tribal Hut   | Neolithic  | spawner              | 200                | Housing 2. Limit 6 per civilization, 3 per player. |
| Burial Mound | Neolithic  | none                 | 200                | Respawn point.                                     |
| Cottage      | Village    | spawner              | 950 / 1,000        | Housing 4. Limit 8 / 3. Upgrade: 2,500 / 3,550.    |
| Chapel       | Village    | none                 | 3,850 / 1,000      | Respawn point, claim +4, housing +1, `faith`.      |
| Graveyard    | Village    | none                 | 1,900 / 1,000      | Respawn point.                                     |
| Shrine       | Village    | none                 | 1,300 / 1,000      | Respawn point, `faith`.                            |
| Tavern       | Village    | processor (random)   | 3,100 / 1,000      | Housing 2. Upgrade: 5,900 / 4,550.                 |
| Bathhouse    | Medieval   | processor            | 7,950 / 5,000      |                                                    |
| Library      | Medieval   | processor            | 15,950 / 6,550     |                                                    |
| Manor House  | Medieval   | spawner              | 16,800 / 6,550     | Housing 8. Limit 3 / 1. Upgrade: 22,200 / 16,550.  |
| Monastery    | Medieval   | processor (selected) | 18,850 / 6,550     | Respawn point, `faith`.                            |
| Townhouse    | Medieval   | spawner              | 7,100 / 4,550      | Housing 6. Replaces Cottages. Limit 8 / 3.         |
| Villa        | Medieval   | spawner              | 11,750 / 5,550     | Housing 7. Limit 4 / 2.                            |
| Apartments   | Industrial | spawner              | 31,050 / 16,550    | Housing 16. Limit 4 / 2.                           |
| Grand Manor  | Industrial | spawner              | 39,950 / 16,550    | Housing 12. Limit 2 / 1.                           |
| University   | Industrial | processor            | 53,250 / 16,550    |                                                    |

Housing buildings use the `housing` tag pool.

## Food and farming (`food`)

| Building     | Age       | Function             | Price (RP / money) | Notes                                                         |
| ------------ | --------- | -------------------- | ------------------ | ------------------------------------------------------------- |
| Forager      | Neolithic | processor (first)    | 200                | `placement: anywhere`. Upgrade: 350.                          |
| Fisher's Hut | Neolithic | processor (selected) | 200                | `placement: anywhere`. Upgrades: 350, then 800 / 350.         |
| Hunter's Hut | Neolithic | processor (selected) | 200                | `placement: anywhere`. Upgrades: 400, then 900 / 400.         |
| Animal Farm  | Village   | processor (selected) | 1,750 / 1,000      | `farming`. Upgrades: 1,400 / 900 and 2,800 / 2,000.           |
| Bakery       | Village   | processor (selected) | 1,600 / 1,200      | Upgrades: 1,200 / 800 and 2,500 / 1,800.                      |
| Farm         | Village   | processor (selected) | 1,300 / 1,000      | `farming`. Upgrades: 1,500 / 1,000 and 3,350 / 3,550.         |
| Smokehouse   | Village   | processor (selected) | 1,400 / 1,000      | Upgrades: 1,100 / 700 and 2,200 / 1,500.                      |
| Apiary       | Medieval  | processor (all)      | 6,650 / 4,000      | `farming`                                                     |
| Herbalist    | Medieval  | processor            | 5,900 / 4,000      | `placement: anywhere`. Upgrades: 800 / 400 and 1,600 / 1,000. |
| Orchard      | Medieval  | processor (selected) | 5,450 / 4,000      | `farming`. Upgrade: 3,500 / 2,200.                            |
| Plantation   | Medieval  | processor (selected) | 6,250 / 4,000      | `farming`. Upgrade: 4,000 / 2,500.                            |
| Stables      | Medieval  | processor            | 9,200 / 4,550      | `farming`                                                     |

Buildings placed `anywhere` do not extend your claims.

## Resources (`resources`)

| Building   | Age       | Function             | Price (RP / money) | Notes                                                                                                                                                                              |
| ---------- | --------- | -------------------- | ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Woodcutter | Neolithic | processor (selected) | 400                | Chest core. One product per wood type, with better axes unlocked by upgrades (600, then 1,200). `placement: anywhere`, `forestry` tag, limits 4 per civilization and 2 per player. |
| Clay Pit   | Village   | processor (first)    | 1,150 / 1,000      | `placement: anywhere`. Upgrades: 900 / 500 and 1,800 / 1,200.                                                                                                                      |
| Sand Pit   | Village   | processor (first)    | 950 / 1,000        | `placement: anywhere`. Upgrades: 800 / 500 and 1,600 / 1,100.                                                                                                                      |
| Mine       | Village   | processor (first)    | 1,550 / 1,000      | Progressive picks, `placement: anywhere`, `mining`. Upgrades: 7,550 / 4,000 and 17,750 / 13,550. Replaces the old per-ore mines through aliases.                                   |

## Crafts and industry (`industry`)

| Building      | Age        | Function             | Price (RP / money) | Notes                                          |
| ------------- | ---------- | -------------------- | ------------------ | ---------------------------------------------- |
| Workshop      | Tribal     | processor (selected) | 250                |                                                |
| Charcoal Kiln | Village    | processor (first)    | 1,300 / 1,550      | Upgrades: 1,000 / 600 and 2,000 / 1,400.       |
| Pottery       | Village    | processor (selected) | 2,350 / 1,550      | Upgrades: 1,500 / 900 and 3,000 / 2,000.       |
| Apothecary    | Medieval   | processor (selected) | 8,800 / 6,550      |                                                |
| Armory        | Medieval   | processor (selected) | 13,400 / 7,550     | Upgrade: 31,050 / 21,000.                      |
| Blacksmith    | Medieval   | processor (selected) | 12,550 / 7,550     | Upgrades: 26,600 / 21,000 and 86,350 / 21,000. |
| Glassworks    | Medieval   | processor (selected) | 10,050 / 6,550     | Upgrade: 5,000 / 3,500.                        |
| Smithy        | Medieval   | processor (selected) | 10,900 / 7,000     |                                                |
| Weaver        | Medieval   | processor (selected) | 7,100 / 6,000      | Upgrade: 4,000 / 3,000.                        |
| Factory       | Industrial | processor (selected) | 28,800 / 21,000    | Limit 2 per civilization.                      |
| Foundry       | Industrial | processor (selected) | 31,050 / 21,000    | Upgrade: 80,750 / 21,000.                      |

## Storage and trade (`trade`)

| Building       | Age        | Function           | Price (RP / money) | Notes                                                                |
| -------------- | ---------- | ------------------ | ------------------ | -------------------------------------------------------------------- |
| Storage Hut    | Neolithic  | none               | 200                | Storage 1. Barrel in, chest out.                                     |
| Granary        | Village    | none               | 1,300 / 1,000      | Storage 2. Barrels.                                                  |
| Storehouse     | Village    | none               | 1,550 / 1,000      | Storage 3. Chests.                                                   |
| Marketplace    | Village    | processor (random) | 2,650 / 1,550      | Sells goods for money. Upgrade: 6,250 / 5,000. Alias `trading_post`. |
| Bank           | Medieval   | processor (all)    | 18,850 / 8,000     | Withdraw limit +600.                                                 |
| Treasury       | Medieval   | none               | 12,550 / 7,000     | Withdraw limit +400.                                                 |
| Stock Exchange | Industrial | processor (all)    | 57,700 / 19,550    | Withdraw limit +1,500.                                               |

Money earned by these buildings is split between the treasury and the building according to the economy's `income_to_civ`.

## Defence (`military`)

| Building   | Age      | Function         | Price (RP / money) | Notes                                                                                                      |
| ---------- | -------- | ---------------- | ------------------ | ---------------------------------------------------------------------------------------------------------- |
| Watchtower | Village  | none             | 2,350 / 1,550      | Claim bonus +8.                                                                                            |
| Cannon     | Medieval | siege (selected) | 5,000 / 5,550      | `military` tag. Upgrades: 3,350 / 5,000 and 6,450 / 15,000. See [Diplomacy and War](diplomacy-and-war.md). |
| Guardhouse | Medieval | spawner          | 8,400 / 6,550      | Trains iron golems from iron blocks in its barrel. `military` tag.                                         |
| Keep       | Medieval | none             | 21,000 / 9,000     | Respawn point, claim bonus +16. Targeted first by cannons.                                                 |

## Infrastructure (`infrastructure`)

| Building         | Age        | Function | Price (RP / money) | Notes                                                                            |
| ---------------- | ---------- | -------- | ------------------ | -------------------------------------------------------------------------------- |
| Railway Station  | Industrial | none     | 31,050 / 15,000    | Storage 4, logistics capacity +256. Limit 3 per civilization.                    |
| Freight Depot    | Industrial | none     | 35,500 / 15,000    | Storage 8, logistics capacity +512.                                              |
| Telegraph Office | Industrial | none     | 26,600 / 15,000    | Claim bonus +16, production speed +5%.                                           |
| Automation Works | Industrial | none     | 53,250 / 15,000    | Production speed +15% civilization-wide, capped by `production.max_speed_bonus`. |

{% hint style="info" %}
The exact structure requirements, recipes and upgrade levels of each building are in its own file under `plugins/Ages/buildings/<category>/<id>.yml`. They are intentionally data, not code, so they are easy to tune. Restore a shipped file with `/ages admin regen <id>`.
{% endhint %}

See also [Buildings](buildings.md) and [Building Functions and Recipes](building-functions-and-recipes.md).
