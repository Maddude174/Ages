# Building Catalogue

All 63 buildings shipped in 0.0.3A. Costs are base **resource points**, before government, economy and policy modifiers. A dash means the building is not bought with points (core buildings are bought as part of age advancement, or given).

Function types: **processor** (recipes), **spawner** (villagers and similar), **tax** (treasury income per resident), **none** (passive).

## Government

| Building     | Age        | Function | Cost (RP) | Notes                                                                         |
| ------------ | ---------- | -------- | --------- | ----------------------------------------------------------------------------- |
| Fireplace    | Neolithic  | none     | not sold  | Founding core. Campfire block. Claim bonus +2.                                |
| Chief's Hut  | Tribal     | none     | age cost  | Lodestone core. Replaces the Fireplace. Housing +2, claim bonus +4.           |
| Village Hall | Village    | tax      | age cost  | Bell core. 0.4 per resident. Replaces the Chief's Hut. Claim +12, housing +4. |
| Town Hall    | Medieval   | tax      | age cost  | Replaces the Village Hall.                                                    |
| City Hall    | Industrial | tax      | age cost  | Replaces the Town Hall.                                                       |

## Homes and faith (`community`)

| Building     | Age        | Function  | Cost (RP) | Notes                                             |
| ------------ | ---------- | --------- | --------- | ------------------------------------------------- |
| Tribal Hut   | Neolithic  | spawner   | 6         |                                                   |
| Burial Mound | Neolithic  | none      | 6         |                                                   |
| Cottage      | Village    | spawner   | 25        |                                                   |
| Chapel       | Village    | none      | 100       | Respawn point, claim +4, housing +1, `faith` tag. |
| Graveyard    | Village    | none      | 50        | Respawn point.                                    |
| Shrine       | Village    | none      | 35        | Respawn point, `faith` tag.                       |
| Tavern       | Village    | processor | 80        |                                                   |
| Bathhouse    | Medieval   | processor | 190       |                                                   |
| Library      | Medieval   | processor | 380       |                                                   |
| Manor House  | Medieval   | spawner   | 400       |                                                   |
| Monastery    | Medieval   | processor | 450       |                                                   |
| Townhouse    | Medieval   | spawner   | 170       | Housing 6, replaces Cottages.                     |
| Villa        | Medieval   | spawner   | 280       |                                                   |
| Apartments   | Industrial | spawner   | 700       |                                                   |
| Grand Manor  | Industrial | spawner   | 900       |                                                   |
| University   | Industrial | processor | 1200      |                                                   |

Housing buildings use the `housing` tag pool.

## Food and farming (`food`)

| Building     | Age       | Function  | Cost (RP) |
| ------------ | --------- | --------- | --------- |
| Forager      | Neolithic | processor | 6         |
| Fisher's Hut | Neolithic | processor | 8         |
| Hunter's Hut | Neolithic | processor | 8         |
| Animal Farm  | Village   | processor | 45        |
| Bakery       | Village   | processor | 50        |
| Farm         | Village   | processor | 35        |
| Smokehouse   | Village   | processor | 45        |
| Apiary       | Medieval  | processor | 160       |
| Herbalist    | Medieval  | processor | 140       |
| Orchard      | Medieval  | processor | 130       |
| Plantation   | Medieval  | processor | 150       |
| Stables      | Medieval  | processor | 220       |

## Resources (`resources`)

| Building   | Age       | Function  | Cost (RP) | Notes                                                                                                                                                                                                      |
| ---------- | --------- | --------- | --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Woodcutter | Neolithic | processor | 8         |                                                                                                                                                                                                            |
| Clay Pit   | Village   | processor | 30        | `placement: anywhere`                                                                                                                                                                                      |
| Sand Pit   | Village   | processor | 25        | `placement: anywhere`                                                                                                                                                                                      |
| Mine       | Village   | processor | 40        | Progressive (wooden, stone, iron, diamond picks), `mode: first`, `placement: anywhere`, `tags: {mining: 1}`, needs natural stone, support beams and light. Replaces the old per-ore mines through aliases. |

## Crafts and industry (`industry`)

| Building      | Age        | Function  | Cost (RP) |
| ------------- | ---------- | --------- | --------- |
| Workshop      | Tribal     | processor | 10        |
| Charcoal Kiln | Village    | processor | 35        |
| Pottery       | Village    | processor | 60        |
| Apothecary    | Medieval   | processor | 210       |
| Armory        | Medieval   | processor | 320       |
| Blacksmith    | Medieval   | processor | 300       |
| Glassworks    | Medieval   | processor | 240       |
| Smithy        | Medieval   | processor | 260       |
| Weaver        | Medieval   | processor | 170       |
| Factory       | Industrial | processor | 650       |
| Foundry       | Industrial | processor | 700       |

## Storage and trade (`trade`)

| Building       | Age        | Function  | Cost (RP) | Notes                                                                   |
| -------------- | ---------- | --------- | --------- | ----------------------------------------------------------------------- |
| Storage Hut    | Neolithic  | none      | 8         | Storage 1 (barrel in, chest out).                                       |
| Granary        | Village    | none      | 35        | Storage 2, barrels.                                                     |
| Marketplace    | Village    | processor | 70        | Sells food and goods for treasury money. Alias `trading_post`.          |
| Storehouse     | Village    | none      | 40        | Storage 3, chests.                                                      |
| Bank           | Medieval   | processor | 450       | Buys gold ingots, emeralds and diamonds for money. Withdraw limit +600. |
| Treasury       | Medieval   | none      | 300       | Earns 4 RP and 6 money per 15 min. Withdraw limit +400.                 |
| Stock Exchange | Industrial | processor | 1300      |                                                                         |

## Defence (`military`)

| Building   | Age      | Function | Cost (RP) | Notes                                                                                  |
| ---------- | -------- | -------- | --------- | -------------------------------------------------------------------------------------- |
| Watchtower | Village  | none     | 60        | Claim bonus +8.                                                                        |
| Cannon     | Medieval | none     | 120       | `siege:` block, dispenser core, `tags: {military: 1}`.                                 |
| Guardhouse | Medieval | spawner  | 200       | Trains **iron golems** (cap 2) from iron blocks put in its barrel, one per 30 minutes. |
| Keep       | Medieval | none     | 500       | Respawn point, claim bonus +16.                                                        |

## Infrastructure (`infrastructure`)

| Building         | Age        | Function | Cost (RP) | Notes                                                                             |
| ---------------- | ---------- | -------- | --------- | --------------------------------------------------------------------------------- |
| Railway Station  | Industrial | none     | 700       | Storage 4, logistics capacity +256, chests.                                       |
| Freight Depot    | Industrial | none     | 800       | Storage 8, logistics capacity +512, barrels.                                      |
| Telegraph Office | Industrial | none     | 600       | Claim bonus +16, production speed +5%.                                            |
| Automation Works | Industrial | none     | 1200      | Production speed +15% civilization-wide (capped by `production.max_speed_bonus`). |

{% hint style="info" %}
The exact structure requirements, recipes and upgrade levels of each building are in its own file under `plugins/Ages/buildings/<category>/<id>.yml`. They are intentionally data, not code, so they are easy to tune. Run `python3 tools/balance.py` from the source zip for an offline balance audit (adapt the paths first, see [Administration and Troubleshooting](administration-and-troubleshooting.md)).
{% endhint %}

See also [Buildings](buildings.md) and [Building Functions and Recipes](building-functions-and-recipes.md).
