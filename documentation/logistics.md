# Logistics

Rail **logistics** move goods between buildings automatically, with no minecarts. Goods travel virtually along connected rails from the buildings that make them to the buildings that need them.

## Turning it on

Rail logistics run only for civilizations in an age whose file contains **`rails: true`**, and only while `logistics.enabled` is `true`. **No shipped age sets `rails: true`**, so logistics are off until you enable them:

```yaml
# plugins/Ages/ages/industrial.yml
id: industrial
rails: true
```

Set it on the ages where you want logistics to run (for example only Industrial, or Village and later), then `/ages reload`. The `logistics.min_age` setting in `settings.yml` only changes the hint text shown by `/ages logistics`. It does not turn anything on.

## How networks form

* Every building has **ports**: the rails on the shell of its area (the scan box plus one block).
* Rails that touch each other, including up and down slopes, form one **network**. Every building with a port on it joins.
* One long connected railway can therefore link a whole city. You do not need a separate line between each pair.
* Placing or breaking a rail near a building updates its ports. Only loaded chunks take part.

## What happens on each run

Each run (every `logistics.interval_seconds`) handles one civilization's network in four steps:

1. **Storage hubs sort.** A hub moves items from its input barrel to its output chest.
2. **Producers deliver.** Buildings send items straight to the buildings that need them: recipe ingredients, tools and goods that can be sold.
3. **Surplus is stored.** Whatever nobody needs goes to a storage hub.
4. **Shortages are filled.** Buildings still short of an ingredient pull it from storage.

Throughput is capped per building (`logistics.items_per_building_per_run`) and per network (`logistics.items_per_network_per_run`).

## Settings

| Key                                    | Default | Meaning                            |
| -------------------------------------- | ------- | ---------------------------------- |
| `logistics.enabled`                    | `true`  | Master switch.                     |
| `logistics.interval_seconds`           | `10`    | Run interval (minimum 2).          |
| `logistics.max_rails_per_network`      | `4000`  | Rail cap per network (minimum 50). |
| `logistics.items_per_building_per_run` | `64`    | Per-building throughput.           |
| `logistics.items_per_network_per_run`  | `512`   | Per-network throughput.            |
| age `rails`                            | `false` | Per-age switch in `ages/*.yml`.    |

## Logistics buildings

| Building        | Age        | Storage | Extra capacity | Containers                        |
| --------------- | ---------- | ------- | -------------- | --------------------------------- |
| Storage Hut     | Neolithic  | 1       | none           | barrel in, chest out              |
| Granary         | Village    | 2       | none           | barrels                           |
| Storehouse      | Village    | 3       | none           | chests                            |
| Railway Station | Industrial | 4       | 256            | chests (limit 3 per civilization) |
| Freight Depot   | Industrial | 8       | 512            | barrels                           |

A building with `provides: {storage: N}` is a **hub**: surplus is parked in it and shortages are pulled from it. `logistics_capacity` raises the network's per-run cap. The `logistics.stores` key (`any`, `food` or `goods`) controls what a hub accepts.

## Using real containers

Rails move items between **real container blocks** (barrels and chests) that a building names as its `logistics` input or output. That is why the hubs above use barrels and chests. Most production buildings keep their goods in the core's **built-in storage** (see [Buildings](buildings.md)), which is a virtual inventory. For those buildings the rails currently look at the physical block at the core position rather than the built-in storage, so test any layout before relying on it, and prefer hubs for rail traffic.

## Commands

`/ages logistics` lists the rail networks that link two or more of your buildings, or tells you that none do yet.
