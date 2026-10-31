# Ages

An **age** is a tier of civilization. Ages are config-driven: each is one file in `plugins/Ages/ages/`. A civilization advances by **buying and placing the next age's core building**.

## The shipped ages

| # | Age        | Core building | Claim base / per building / max radius | Advance cost              | Retires      |
| - | ---------- | ------------- | -------------------------------------- | ------------------------- | ------------ |
| 1 | Neolithic  | Fireplace     | 16 / 2 / 32                            | none (the founding age)   | none         |
| 2 | Tribal     | Chief's Hut   | 24 / 3 / 56                            | 2,250 RP                  | Fireplace    |
| 3 | Village    | Village Hall  | 40 / 3 / 80                            | 9,000 RP + 7,900 money    | Chief's Hut  |
| 4 | Medieval   | Town Hall     | 64 / 3 / 128                           | 48,750 RP + 27,000 money  | Village Hall |
| 5 | Industrial | City Hall     | 96 / 3 / 192                           | 165,000 RP + 78,750 money | Town Hall    |

The money part follows the normal purchase rules: it comes from the buyer's Vault balance, or from the treasury in treasury-purchase economies and governments, and it is dropped (with RP costs doubled) under Barter. See [Economies](economies.md). These costs are **far higher than in 0.0.3A**, which charged 25, 100, 600 and 2,000 RP plus items.

## Requirements to advance

| To reach   | Requirements                                                                                               |
| ---------- | ---------------------------------------------------------------------------------------------------------- |
| Tribal     | A Fireplace, plus **one of** Forager, Hunter's Hut or Fisher's Hut                                         |
| Village    | Chief's Hut, 2 Tribal Huts, Storage Hut, Woodcutter, plus **one of** Hunter's Hut, Forager or Fisher's Hut |
| Medieval   | Village Hall, Marketplace, Storehouse, Watchtower, plus **one of** Shrine or Chapel                        |
| Industrial | Town Hall, Bank, Treasury, Smithy, Glassworks, Library, 2 Townhouses, plus a Mine                          |

`/ages info` shows the live checklist for your civilization.

### How buildings are counted

Since 0.0.47 an age requirement counts a building if:

* it is the named type, **an alias of it**, or a **higher upgrade level** of it,
* and it is **not necessarily working**: Disabled buildings still count toward age unlocks.

Limits and most other checks still ignore disabled buildings.

## Per-age tag limits

Each age raises the caps on shared limit pools. Format is `per_player / per_town`.

| Tag       | Neolithic | Tribal | Village | Medieval | Industrial |
| --------- | --------- | ------ | ------- | -------- | ---------- |
| `housing` | 2 / 6     | 3 / 10 | 6 / 24  | 8 / 48   | 12 / 96    |
| `farming` | none      | none   | 2 / 5   | 3 / 10   | 4 / 14     |
| `mining`  | none      | none   | none    | 2 / 4    | 3 / 8      |
| `faith`   | 1 / 1     | 1 / 2  | 2 / 5   | 2 / 6    | 2 / 8      |

The global `limits.tags` in `settings.yml` also applies, and the strictest non-zero cap wins.

## How advancing works

1. The core building has an `age:` and is sold in the catalogue once the civilization qualifies.
2. **Buying** the core checks the age's `requirements` and takes the `advance_cost`.
3. **Placing** the core validates its structure like any building. The **new core is validated before the old one is retired**, in one database transaction, so a failed placement changes nothing.
4. Buildings listed in `retire` are unregistered when the age is entered. Their blocks stay in the world, only the registration goes.
5. The new core usually lists the old one under `replaces:`.

Role permission needed: `ADVANCE_AGE` (part of the `officer` bundle), plus the spending permissions.

{% hint style="warning" %}
If an age's file names a building that does not exist (for example after you delete or break a building file), the loader **skips the age**, and then every building and age that depends on it. Keep an eye on the `skipped` lines in the console.
{% endhint %}

## Age file reference

```yaml
id: example_age
name: "&dExample Age"
order: 5                       # distinct, ascending
core_building: example_core    # building whose placement moves a civ INTO this age
aliases: [old_age_id]          # old ids in saved data resolve to this age
claim: {base_radius: 96, per_building: 3, max_radius: 192}
tag_limits:
  housing: {per_player: 12, per_town: 96}
building_limits: {example_building: 3}   # optional per-building caps while in this age
requirements:                  # to BUY and place the core
  all_of:
    - {building: town_hall, count: 1}
advance_cost: {points: 165000, money: 78750}
retire: [town_hall]
```

| Field                | Required | Description                                                                                  |
| -------------------- | -------- | -------------------------------------------------------------------------------------------- |
| `id`                 | yes      | Unique id. A blank id skips the file.                                                        |
| `name`               | no       | Display name with `&` colour codes.                                                          |
| `order`              | yes      | Position in the sequence. A new age appears after the last one with a higher `order`.        |
| `core_building`      | yes      | Building id whose placement enters this age.                                                 |
| `aliases`            | no       | Old ids that resolve to this age (renames). The shipped `industrial` has `late_medieval`.    |
| `claim.base_radius`  | no       | Base claim radius. Default `24`.                                                             |
| `claim.per_building` | no       | Radius added per placed building. Default `3`.                                               |
| `claim.max_radius`   | no       | Hard cap on the claim radius. Default `64`.                                                  |
| `tag_limits`         | no       | Per-age caps on shared tag pools.                                                            |
| `building_limits`    | no       | Per-building caps while in this age. Combined with the building's own limit, strictest wins. |
| `requirements`       | no       | Condition tree, see below.                                                                   |
| `advance_cost`       | no       | `points`, `money`, `treasury` and `items`.                                                   |
| `retire`             | no       | Building ids unregistered on entering this age.                                              |

### Requirement conditions

A list is an implicit `all_of`. Nest `all_of` and `any_of` freely.

| Leaf                                                  | Meaning                                                                                                          |
| ----------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| `{building: id, count: N, level: L}`                  | At least `N` buildings of that type (or an alias, or a higher level), optionally at upgrade level `L` or higher. |
| `{tag: name, count: N}`                               | Weighted usage of a shared tag pool.                                                                             |
| `{members: N}`                                        | Number of members.                                                                                               |
| `{points: N}`                                         | Resource points the civilization holds.                                                                          |
| `{treasury: N}`                                       | Treasury money.                                                                                                  |
| `{population: {min: N, count: [players, villagers]}}` | Population. Supported by the loader, not used by the shipped ages.                                               |

Legacy `buildings: {id: count}` maps are still accepted. Unknown keys cause the file to be skipped with a console error.

### Adding an age

1. Create `ages/<id>.yml` with an `order` higher than the current last age.
2. Create its core building (`age: <id>`, usually with `replaces:`).
3. Add the age to any building's `age`, `obsolete_in` or `hide_after_age` and to `logistics.min_age` where relevant.
4. Run `/ages reload` and read the console.
