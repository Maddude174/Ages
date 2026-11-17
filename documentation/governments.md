# Governments

A **government** decides who may do what inside a civilization, how the leader is chosen, which policies are available, and gives small modifiers. It can also override how buildings are paid for and how building income is routed. Governments are independent of economies. Files live in `plugins/Ages/governments/`.

## Shipped governments

| Government         | Min age    | Leader          | Selection   | Policy slots | Change cost          | Cooldown |
| ------------------ | ---------- | --------------- | ----------- | ------------ | -------------------- | -------- |
| Chiefdom (default) | none       | Chief           | appointed   | 2            | 500 RP               | 24 h     |
| Elder Council      | none       | Speaker         | **elected** | 2            | 600 RP               | 24 h     |
| Anarchy            | Tribal     | none            | **none**    | 2            | 800 RP               | 12 h     |
| Feudal             | Village    | High Lord       | appointed   | 3            | 1,500 RP + 200 money | 48 h     |
| Monarchy           | Village    | Monarch         | appointed   | 3            | 1,800 RP + 300 money | 72 h     |
| Republic           | Village    | Consul          | **elected** | 3            | 1,600 RP + 400 money | 48 h     |
| Theocracy          | Village    | High Priest     | appointed   | 2            | 1,400 RP + 250 money | 48 h     |
| Communism          | Industrial | Party Secretary | appointed   | 3            | 2,500 RP + 500 money | 48 h     |

A government's change cost is paid by the **civilization**: RP from the shared points and money from the **treasury**. Barter earns no treasury money, so governments that charge money cannot be adopted until you move to an economy that earns it (Commons costs RP only). Change costs also pass through your cost modifiers. The old **Dictatorship** government was removed in 0.0.59.

### Roles (low to high)

Weights decide rank. A number in brackets is the most members that can hold the role.

| Government    | Roles                                                                                                              |
| ------------- | ------------------------------------------------------------------------------------------------------------------ |
| Chiefdom      | recruit (10), citizen (40), elder (70, max 6), chief (100, max 1)                                                  |
| Elder Council | recruit (10), citizen (40), elder (70, max 8), speaker (100, max 1)                                                |
| Anarchy       | free\_person (50). There is no leader role.                                                                        |
| Feudal        | serf (10), freeman (30), steward (50, max 8), noble (75, max 8), high\_lord (100, max 1)                           |
| Monarchy      | subject (15), soldier (40, max 20), minister (70, max 6), monarch (100, max 1). Custom roles are not allowed.      |
| Republic      | resident (10), citizen (40), magistrate (60, max 6), quaestor (65, max 3), legate (70, max 3), consul (100, max 1) |
| Theocracy     | faithful (40), acolyte (55, max 8), elder (70, max 4), high\_priest (100)                                          |
| Communism     | comrade (40), cadre (60, max 8), commissar (75, max 4), secretary (100)                                            |

### Modifiers and special rules

| Government    | Modifiers                                                                                                                          | Special rules                                                                                                             |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| Chiefdom      | none                                                                                                                               |                                                                                                                           |
| Elder Council | `contribution_rate_mult 1.15`, `building_cost_mult 0.95`                                                                           |                                                                                                                           |
| Anarchy       | `building_cost_mult 0.95`, `claim_radius_add -2`, `contribution_rate_mult 1.1`                                                     | No leader. **Cannot be changed by decree**, only by revolution.                                                           |
| Feudal        | `building_cost_mult 0.92`, `money_output_mult 0.95`, `claim_radius_add 3`                                                          | Delegated rule: purchases and income follow the economy.                                                                  |
| Monarchy      | `points_output_mult 1.08`, `money_output_mult 1.12`, `building_cost_mult 1.05`, `claim_radius_add 2`, `production_speed_mult 1.05` | Centralised: `force_treasury_purchases` and `force_income_to_civ`.                                                        |
| Republic      | `contribution_rate_mult 1.05`, `production_speed_mult 1.05`                                                                        |                                                                                                                           |
| Theocracy     | `points_output_mult 1.08`, `contribution_rate_mult 1.1`, `claim_radius_add 1`                                                      |                                                                                                                           |
| Communism     | `points_output_mult 1.15`, `money_output_mult 0.85`, `contribution_rate_mult 1.2`, `building_cost_mult 0.92`                       | Centralised: **all** building money costs come from the treasury, and **all** building money income goes to the treasury. |

### Policies each government offers

A policy is only offered through the list that matches its `kind`. These are the policies that **work** in each government.

| Government    | Working policies                                                                                                                                     |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| Chiefdom      | neolithic\_sharing, oral\_law, path\_markers, council\_fires, seasonal\_camps, blood\_feud, communal\_labor                                          |
| Elder Council | neolithic\_sharing, oral\_law, council\_fires, path\_markers, communal\_labor, land\_grants, granary\_law, seasonal\_camps                           |
| Anarchy       | neolithic\_sharing, communal\_labor, land\_grants, oral\_law, charter\_of\_rights, militia\_levy                                                     |
| Feudal        | feudal\_oaths, land\_grants, militia\_levy, castle\_ward, tribute, seasonal\_camps, corvee, fire\_watch                                              |
| Monarchy      | royal\_charters, militia\_levy, tribute, civic\_projects, frugal\_rule, secret\_police, scorched\_earth, sappers\_corps                              |
| Republic      | charter\_of\_rights, civic\_projects, guild\_charters, guild\_oaths, village\_militia, university\_charter, labour\_unions, granary\_law             |
| Theocracy     | church\_lands, granary\_law, oral\_law, fire\_watch, communal\_labor, university\_charter, civic\_projects                                           |
| Communism     | labour\_unions, communal\_labor, civic\_projects, militia\_levy, granary\_law, fire\_watch (`public_works` is an economy policy and is ignored here) |

The policies `seasonal_hunt`, `tithes` and `tribal_muster` are not offered by any shipped government. Add them to a `policies:` list to use them.

## Roles and permissions

A role is a set of **permissions** (`Perm` names). Roles have a `weight`: a higher weight outranks a lower one, and you can only manage ranks and members **below your own weight**.

### All permissions

| Group      | Permissions                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------- |
| World      | `BUILD`, `CONTAINERS`, `INTERACT`                                                                                               |
| Buildings  | `USE_SHOP`, `PURCHASE_BUILDINGS`, `PLACE_BUILDINGS`, `UPGRADE_BUILDINGS`, `REMOVE_BUILDINGS`, `MANAGE_BUILDINGS`, `ADVANCE_AGE` |
| Finance    | `CONTRIBUTE`, `SPEND_POINTS`, `SPEND_TREASURY`, `WITHDRAW_TREASURY`, `VIEW_FINANCES`, `COLLECT_TAXES`                           |
| People     | `INVITE`, `KICK`, `MANAGE_RANKS`, `APPOINT_OFFICIALS`, `VOTE`                                                                   |
| Governance | `CHANGE_SETTINGS`, `CHANGE_GOVERNMENT`, `CHANGE_ECONOMY`, `MANAGE_POLICIES`, `MANAGE_DIPLOMACY`, `DECLARE_WAR`                  |
| Owner-only | `DISBAND`, `TRANSFER_OWNERSHIP`                                                                                                 |

The two owner-only permissions are always held by the technical owner and are **never** included in a role's `ALL`. A government may still grant them explicitly.

### Permission bundles

Bundles keep role YAML short. A role lists `bundles: [..]` and the permissions are added together. Ten bundles exist.

| Bundle           | Permissions                                                                                               |
| ---------------- | --------------------------------------------------------------------------------------------------------- |
| `basic_resident` | BUILD, CONTAINERS, INTERACT, CONTRIBUTE                                                                   |
| `worker`         | basic\_resident + USE\_SHOP, PURCHASE\_BUILDINGS, PLACE\_BUILDINGS, UPGRADE\_BUILDINGS, REMOVE\_BUILDINGS |
| `citizen`        | worker + VIEW\_FINANCES, VOTE, SPEND\_POINTS                                                              |
| `officer`        | citizen + MANAGE\_BUILDINGS, ADVANCE\_AGE, INVITE, KICK, APPOINT\_OFFICIALS                               |
| `treasurer`      | VIEW\_FINANCES, SPEND\_POINTS, SPEND\_TREASURY, WITHDRAW\_TREASURY, CONTRIBUTE, COLLECT\_TAXES            |
| `diplomat`       | MANAGE\_DIPLOMACY, DECLARE\_WAR, VIEW\_FINANCES, VOTE                                                     |
| `steward`        | MANAGE\_POLICIES, CHANGE\_SETTINGS, MANAGE\_RANKS, VIEW\_FINANCES, SPEND\_POINTS, SPEND\_TREASURY         |
| `military`       | BUILD, CONTAINERS, INTERACT, DECLARE\_WAR, MANAGE\_DIPLOMACY, VIEW\_FINANCES, MANAGE\_BUILDINGS, VOTE     |
| `all`            | every permission except the owner-only ones                                                               |
| `leader`         | An alias of `all`                                                                                         |

### Role resolution

A role first inherits its parent's permissions, then adds its `bundles`, `permissions`, `grant` and finally applies `deny`. The word `ALL` is accepted only inside `permissions`, not `grant` or `deny`. Inheritance cycles and unknown parents are load errors, and so are unknown bundles and unknown permission names.

## Government file reference

```yaml
id: example_gov
name: "&6Example Government"
description: ["Shown in the government screen."]
icon: WHITE_BANNER
min_age: village
leader_title: Governor
leader_role: governor          # must be a defined role (omit for leader_selection: none)
default_role: citizen          # role for new members, must be defined
leader_selection: elected      # owner | appointed | elected | none
exit_only_by_revolution: false # default true when leader_selection is none
force_treasury_purchases: false
force_income_to_civ: false
allow_custom_roles: true
roles:
  resident:
    weight: 10
    title: Resident
    bundles: [basic_resident]
    authority: [civic]
  officer:
    weight: 70
    title: Officer
    inherits: citizen
    bundles: [officer, diplomat]
    grant: [MANAGE_POLICIES]
    deny: []
    can_appoint: true
    max_holders: 5
    authority: [civic, diplomatic]
  governor:
    weight: 100
    inherits: officer
    permissions: [ALL]
    can_appoint: true
    max_holders: 1
modifiers: {building_cost_mult: 0.95, points_output_mult: 1.05}
policies: [civic_projects]
policy_slots: 2
change_cost: {points: 1600, money: 400}
change_cooldown_hours: 48
```

| Field                         | Required           | Description                                                                                                                                                                          |
| ----------------------------- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `id`, `name`                  | yes / no           | Identification. A missing `id` skips the file.                                                                                                                                       |
| `min_age`                     | no                 | Earliest age at which it can be adopted.                                                                                                                                             |
| `leader_title`, `leader_role` | yes, except `none` | Leader display title and the role the leader holds. With `leader_selection: none` the leader role is ignored.                                                                        |
| `default_role`                | yes                | Role given to new members.                                                                                                                                                           |
| `leader_selection`            | no                 | `owner`, `appointed` (default), `elected` or `none`. Any other value is a load error.                                                                                                |
| `exit_only_by_revolution`     | no                 | `true`: `/ages government set` cannot leave this government. Only a revolution (civil war) or an admin can. Defaults to `true` when `leader_selection` is `none`, otherwise `false`. |
| `force_treasury_purchases`    | no                 | All building money costs come from the treasury, overriding the economy.                                                                                                             |
| `force_income_to_civ`         | no                 | All building money income goes to the treasury, overriding the economy's split.                                                                                                      |
| `allow_custom_roles`          | no                 | Whether players can create extra roles with `/ages rank create`.                                                                                                                     |
| `roles`                       | yes                | At least one. See below.                                                                                                                                                             |
| `modifiers`                   | no                 | Effect keys, see [Policies and Effects](policies-and-effects.md).                                                                                                                    |
| `policies`                    | no                 | Policy ids this government may adopt. Only policies of `kind: government` take effect.                                                                                               |
| `policy_slots`                | no                 | How many can be active. Default 2.                                                                                                                                                   |
| `change_cost`                 | no                 | `points` and `money`, both paid from the civilization (money from the treasury). Items are not charged.                                                                              |
| `change_cooldown_hours`       | no                 | Delay before the civilization can change again. Default 24.                                                                                                                          |

### Role fields

| Field           | Meaning                                                                           |
| --------------- | --------------------------------------------------------------------------------- |
| `weight`        | Rank. Higher outranks lower.                                                      |
| `title`         | Display title. Defaults to the role id.                                           |
| `inherits`      | Parent role id. Permissions are inherited.                                        |
| `bundles`       | Permission bundles to add.                                                        |
| `permissions`   | Explicit permission list. `ALL` expands to every non-owner permission.            |
| `grant`, `deny` | Add or remove permissions after inheritance.                                      |
| `can_appoint`   | May assign and demote roles strictly below own weight.                            |
| `max_holders`   | Maximum simultaneous holders. `-1` is unlimited.                                  |
| `authority`     | Soft tags for UI and future rules: `civic`, `diplomatic`, `military`, `economic`. |

## Leader selection

| Mode        | How the leader is chosen                                                                                                                        |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| `owner`     | The technical owner is the leader.                                                                                                              |
| `appointed` | A member with `APPOINT_OFFICIALS` appoints the leader with `/ages leader <player>`.                                                             |
| `elected`   | Members with `VOTE` cast `/ages vote <player>` during the open window. The civilization menu has a **Vote** button that opens the ballot.       |
| `none`      | There is no leader. `/ages leader` answers `This government has no leader.` Anyone holding the former leader role is moved to the default role. |

Elected governments open a vote every `governance.election_cycle_days` (default 7). Voting stays open for `election_voting_days` (default 3), then the votes are tallied on close. Votes can only be cast for current members, and a later vote replaces an earlier one. The **first election opens one hour after** an elected government is adopted. Admins can force it with `/ages admin election <civ> open|close|status`.

## Changing government

`/ages government set <id>` needs `CHANGE_GOVERNMENT` and checks the target's `min_age`, the `change_cost` and the cooldown. If the **current** government has `exit_only_by_revolution`, the command is refused with a message that it can only be changed through revolution (civil war). When the government changes:

* Every member keeps their role if the new government has a role with the same id, otherwise they fall back to the new `default_role`.
* For `none`, any holder of the old leader role is reset to the default role and the leader is cleared.
* Otherwise the leader is kept (the owner for `owner` governments) and given the new `leader_role`, and any other member holding the leader role is reset.
* Any open election and its votes are cleared. If the new government is elected, its first election opens in one hour.

## Writing a custom government

1. Copy `governments/_example.yml` to `governments/my_gov.yml`.
2. Give it a unique `id`, define every role you reference as `leader_role` and `default_role`.
3. Use `inherits` and `bundles` instead of long permission lists, and only use the ten bundles above.
4. List only policy ids that exist, and only `kind: government` policies.
5. `/ages reload` and read the console. Mistakes name the file and the role.
