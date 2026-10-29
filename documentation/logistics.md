# Logistics

Rail **logistics** move goods between buildings automatically. They are enabled by default and unlock in the Industrial Age.

## How it works

* Rails that **touch a building's area** link that building to a network.
* **Storage hubs** (buildings with `provides: {storage: N}`) park surplus items, and pull shortages from them, as the network runs.
* Each run moves at most `items_per_building_per_run` items per building and `items_per_network_per_run` per network. Hubs raise the network limit with `provides.logistics_capacity`.
* A network may contain at most `logistics.max_rails_per_network` rail blocks.
* Runs happen every `logistics.interval_seconds`.

`/ages logistics` shows your networks.

## Settings

| Key                                    | Default      | Meaning                            |
| -------------------------------------- | ------------ | ---------------------------------- |
| `logistics.enabled`                    | `true`       | Master switch.                     |
| `logistics.min_age`                    | `industrial` | Age that unlocks logistics.        |
| `logistics.interval_seconds`           | `10`         | Run interval (minimum 2).          |
| `logistics.max_rails_per_network`      | `4000`       | Rail cap per network (minimum 50). |
| `logistics.items_per_building_per_run` | `64`         | Per-building throughput.           |
| `logistics.items_per_network_per_run`  | `512`        | Per-network throughput.            |

## Logistics buildings

| Building        | Age        | Storage | Extra capacity | Container            |
| --------------- | ---------- | ------- | -------------- | -------------------- |
| Storage Hut     | Neolithic  | 1       | none           | barrel in, chest out |
| Granary         | Village    | 2       | none           | barrels              |
| Storehouse      | Village    | 3       | none           | chests               |
| Railway Station | Industrial | 4       | 256            | chests               |
| Freight Depot   | Industrial | 8       | 512            | barrels              |

A building's `logistics.input` and `logistics.output` name the container it reads and writes, either `CORE` (a barrel or chest core) or the nearest block of that type inside the building area. `logistics.stores` can be `any`, `food` or `goods` to say what a hub parks.

## Tips

* Place a hub near your producers and connect with rails. Production buildings then move surplus out and pull inputs in.
* Keep outputs and inputs in containers big enough not to block recipes.
* To use logistics early, set `logistics.min_age: tribal`.
