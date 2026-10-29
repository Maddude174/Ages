# Governments

A **government** decides who may do what inside a civilization, how the leader is chosen, which policies are available, and gives small modifiers. Governments are independent of economies. Files live in `plugins/Ages/governments/`.

## Shipped governments

| Government         | Min age | Leader      | Selection   | Roles (low to high)                                     | Policy slots | Change cost / cooldown |
| ------------------ | ------- | ----------- | ----------- | ------------------------------------------------------- | ------------ | ---------------------- |
| Chiefdom (default) | none    | Chief       | appointed   | recruit, citizen, elder, chief                          | 2            | none / 24 h            |
| Elder Council      | none    | Speaker     | **elected** | recruit, citizen, elder, speaker                        | 2            | 40 RP / 24 h           |
| Monarchy           | Village | Monarch     | appointed   | subject, burgher, steward, noble, monarch               | 2            | 100 RP / 48 h          |
| Republic           | Village | Consul      | **elected** | resident, citizen, magistrate, quaestor, legate, consul | 3            | 120 RP / 48 h          |
| Theocracy          | Village | High Priest | appointed   | novice, faithful, priest, high\_priest                  | 2            | 150 RP / 48 h          |

### Modifiers

| Government    | Modifiers                                                                     |
| ------------- | ----------------------------------------------------------------------------- |
| Chiefdom      | none                                                                          |
| Elder Council | `contribution_rate_mult 1.15`, `building_cost_mult 0.95`                      |
| Monarchy      | `building_cost_mult 0.9`, `money_output_mult 1.1`, `claim_radius_add 2`       |
| Republic      | `contribution_rate_mult 1.05`, `production_speed_mult 1.05`                   |
| Theocracy     | `points_output_mult 1.08`, `building_cost_mult 1.05`, `tag_limit_add.faith 2` |

### Policies each government offers

| Government    | Policies                                                                                   |
| ------------- | ------------------------------------------------------------------------------------------ |
| Chiefdom      | communal\_labor, tribute, frugal\_rule, neolithic\_sharing, seasonal\_hunt, tribal\_muster |
| Elder Council | communal\_labor, frugal\_rule, tribute                                                     |
| Monarchy      | royal\_charters, road\_tolls, militia\_levy, scorched\_earth, sappers\_corps               |
| Republic      | open\_market, civic\_projects, guild\_oaths                                                |
| Theocracy     | tithes, communal\_labor, university\_charter                                               |

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

Bundles keep role YAML short. A role lists `bundles: [..]` and the permissions are added together.

| Bundle           | Permissions                                                                                               |
| ---------------- | --------------------------------------------------------------------------------------------------------- |
| `basic_resident` | BUILD, CONTAINERS, INTERACT, CONTRIBUTE                                                                   |
| `worker`         | basic\_resident + USE\_SHOP, PURCHASE\_BUILDINGS, PLACE\_BUILDINGS, UPGRADE\_BUILDINGS, REMOVE\_BUILDINGS |
| `citizen`        | worker + VIEW\_FINANCES, VOTE, SPEND\_POINTS                                                              |
| `officer`        | citizen + MANAGE\_BUILDINGS, ADVANCE\_AGE, INVITE, KICK, APPOINT\_OFFICIALS                               |
| `treasurer`      | VIEW\_FINANCES, SPEND\_POINTS, SPEND\_TREASURY, WITHDRAW\_TREASURY, CONTRIBUTE, COLLECT\_TAXES            |
| `diplomat`       | MANAGE\_DIPLOMACY, DECLARE\_WAR, VIEW\_FINANCES, VOTE                                                     |
| `steward`        | MANAGE\_POLICIES, CHANGE\_SETTINGS, MANAGE\_RANKS, VIEW\_FINANCES, SPEND\_POINTS, SPEND\_TREASURY         |
| `all`            | every permission except the owner-only ones                                                               |

### Role resolution

A role first inherits its parent's permissions, then adds its `bundles`, `permissions`, `grant` and finally applies `deny`. Inheritance cycles and unknown parents are load errors, and so are unknown bundles and unknown permission names.

## Government file reference

```yaml
id: example_gov
name: "&6Example Government"
description: ["Shown in the government screen."]
icon: WHITE_BANNER
min_age: village
leader_title: Governor
leader_role: governor          # must be a defined role
default_role: citizen          # role for new members, must be defined
leader_selection: elected      # owner | appointed | elected
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
change_cost: {points: 300}
change_cooldown_hours: 48
```

| Field                         | Required | Description                                                                   |
| ----------------------------- | -------- | ----------------------------------------------------------------------------- |
| `id`, `name`                  | yes / no | Identification. A missing `id` skips the file.                                |
| `min_age`                     | no       | Earliest age at which it can be adopted.                                      |
| `leader_title`, `leader_role` | yes      | Leader display title and the role the leader holds.                           |
| `default_role`                | yes      | Role given to new members.                                                    |
| `leader_selection`            | no       | `owner`, `appointed` (default) or `elected`. Any other value is a load error. |
| `allow_custom_roles`          | no       | Whether players can create extra roles with `/ages rank create`.              |
| `roles`                       | yes      | At least one. See below.                                                      |
| `modifiers`                   | no       | Effect keys, see [Policies and Effects](policies-and-effects.md).             |
| `policies`                    | no       | Policy ids this government may adopt.                                         |
| `policy_slots`                | no       | How many can be active. Default 2.                                            |
| `change_cost`                 | no       | `points` and/or `items` to switch to this government.                         |
| `change_cooldown_hours`       | no       | Delay before the civilization can change again. Default 24.                   |

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

| Mode        | How the leader is chosen                                                            |
| ----------- | ----------------------------------------------------------------------------------- |
| `owner`     | The technical owner is the leader.                                                  |
| `appointed` | A member with `APPOINT_OFFICIALS` appoints the leader with `/ages leader <player>`. |
| `elected`   | Members with `VOTE` cast `/ages vote <player>` during the open window.              |

Elected governments open a vote every `governance.election_cycle_days` (default 7). Voting stays open for `election_voting_days` (default 3), then the votes are tallied on close. Votes can only be cast for current members, and a later vote replaces an earlier one. The **first election opens one hour after** an elected government is adopted. Admins can force it with `/ages admin election <civ> open|close|status`.

## Changing government

`/ages government set <id>` needs `CHANGE_GOVERNMENT` and checks the target's `min_age`, the `change_cost` and the cooldown. When the government changes:

* Every member keeps their role if the new government has a role with the same id, otherwise they fall back to the new `default_role`.
* The leader is kept (the owner for `owner` governments) and given the new `leader_role`. Any other member holding the leader role is reset to the default role.
* Any open election and its votes are cleared. If the new government is elected, its first election opens in one hour.

## Writing a custom government

1. Copy `governments/_example.yml` to `governments/my_gov.yml`.
2. Give it a unique `id`, define every role you reference as `leader_role` and `default_role`.
3. Use `inherits` and `bundles` instead of long permission lists.
4. List only policy ids that exist.
5. `/ages reload` and read the console. Mistakes name the file and the role.
