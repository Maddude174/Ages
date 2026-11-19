# YAML Guide: Governments

A government file defines **who can do what** (roles and permissions), **how the leader is chosen**, which **policies** are available, a few **modifiers**, and optional rules about **how buildings are paid for and where income goes**. Put it in `plugins/Ages/governments/<id>.yml` and run `/ages reload`. The shipped `governments/_example.yml` is the quick reference. This page explains every key.

## Minimum viable government

You need an `id`, a `leader_role`, a `default_role` and the `roles` they point to:

```yaml
id: myserver_simple
leader_role: boss          # must be a role below
default_role: member       # given to every new member, must be a role below
roles:
  member:
    weight: 10
    bundles: [citizen]
  boss:
    weight: 100
    permissions: [ALL]
```

The leader is appointed (the default), the title is "Leader", and there are 2 policy slots, no policies, no cost and a 24 hour cooldown.

## The complete annotated template

```yaml
# ---------------------------------------------------------------- identity
id: myserver_example             # REQUIRED. Unique. Lowercase. Match the file name.
name: "&6Example Government"     # Shown in menus. Defaults to the id.
description:                     # Lines shown in the government screen.
  - "&7One line on how it plays."
icon: WHITE_BANNER               # Menu icon. A valid item.
min_age: village                 # Earliest age it can be adopted. Omit for any age.

# ---------------------------------------------------------------- leadership
leader_title: Governor           # Display title of the leader. Default "Leader".
leader_role: governor            # REQUIRED unless leader_selection is none. Must be a role.
default_role: citizen            # REQUIRED. Role for new members. Must be a role.
leader_selection: elected        # owner | appointed | elected | none. Default appointed.

# ---------------------------------------------------------------- special rules (all optional)
exit_only_by_revolution: false   # true = /ages government set cannot leave this government.
                                 #   Default true when leader_selection is none, else false.
force_treasury_purchases: false  # true = every building money cost comes from the treasury.
force_income_to_civ: false       # true = every building money income goes to the treasury.
allow_custom_roles: true         # false = players cannot create extra roles. Default true.

# ---------------------------------------------------------------- roles
roles:
  resident:                      # The role id. Lowercased.
    weight: 10                   # Rank. Higher outranks lower. Default 10.
    title: Resident              # Display title. Defaults to a tidy version of the id.
    bundles: [basic_resident]    # Permission bundles (list below).
  officer:
    weight: 70
    title: Officer
    inherits: resident           # Start with everything resident has.
    bundles: [officer, diplomat] # Add bundles...
    permissions: [MANAGE_POLICIES]  # ...and exact permissions.
    grant: [SPEND_TREASURY]      # Also adds. Applied after permissions.
    deny: [KICK]                 # Removes. Always applied last.
    can_appoint: true            # May assign roles below own weight. Default true when weight >= 70.
    max_holders: 5               # Most members who can hold the role. Default -1 (unlimited).
    authority: [civic]           # Free-text tags: civic, diplomatic, military, economic.
  governor:
    weight: 100
    title: Governor
    inherits: officer
    permissions: [ALL]           # ALL = every permission except the two owner-only ones.
    max_holders: 1

# ---------------------------------------------------------------- effects
modifiers:                       # Same keys as policy effects. Multipliers multiply, others add.
  building_cost_mult: 0.95
  points_output_mult: 1.05

# ---------------------------------------------------------------- policies
policies: [civic_projects]       # Ids of kind: government policies this government offers.
policy_slots: 2                  # How many can be active at once. Default 2.

# ---------------------------------------------------------------- switching
change_cost: {points: 1600, money: 400}   # Paid by the civilization. Money comes from the treasury.
change_cooldown_hours: 48                  # Default 24.
```

## Key reference

| Key                           | Required           | Default                              | Notes                                                                 |
| ----------------------------- | ------------------ | ------------------------------------ | --------------------------------------------------------------------- |
| `id`                          | yes                | none                                 | A blank id skips the file. Duplicates are skipped.                    |
| `name`, `description`, `icon` | no                 | id, none, default                    | Display only.                                                         |
| `min_age`                     | no                 | none                                 | An unknown age is ignored (treated as none) with a console note.      |
| `leader_title`                | no                 | `Leader`                             | Display only.                                                         |
| `leader_role`                 | yes, unless `none` | none                                 | Must be defined in `roles`. With `none` it is ignored.                |
| `default_role`                | yes                | none                                 | Must be defined in `roles`.                                           |
| `leader_selection`            | no                 | `appointed`                          | `owner`, `appointed`, `elected` or `none`. Anything else is an error. |
| `exit_only_by_revolution`     | no                 | `true` for `none`, otherwise `false` | Blocks leaving by decree.                                             |
| `force_treasury_purchases`    | no                 | `false`                              | Overrides the economy's purchase rule.                                |
| `force_income_to_civ`         | no                 | `false`                              | Overrides the economy's income split.                                 |
| `allow_custom_roles`          | no                 | `true`                               | Whether `/ages rank create` works.                                    |
| `roles`                       | yes                | none                                 | At least one.                                                         |
| `modifiers`                   | no                 | none                                 | Numeric effects. Nested maps flatten to dotted keys.                  |
| `policies`                    | no                 | none                                 | Unknown ids are ignored with a console note.                          |
| `policy_slots`                | no                 | `2`                                  |                                                                       |
| `change_cost`                 | no                 | free                                 | `points` and `money` (items are not charged).                         |
| `change_cooldown_hours`       | no                 | `24`                                 |                                                                       |

## Leader selection

| Value       | Behaviour                                                                                                                                                     |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `owner`     | The civilization's owner is the leader.                                                                                                                       |
| `appointed` | A member with `APPOINT_OFFICIALS` names the leader with `/ages leader <player>`.                                                                              |
| `elected`   | Members with `VOTE` vote during an election window (see `governance.*` in `settings.yml`). The first election opens one hour after the government is adopted. |
| `none`      | There is no leader. Roles still exist, the leader role is ignored, and `exit_only_by_revolution` defaults to `true`. This is the "Anarchy" model.             |

## Roles

A role is a named bundle of permissions with a rank.

| Key           | Default                        | Meaning                                                                                                                              |
| ------------- | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| `weight`      | `10`                           | Rank. A role can only manage members and roles **below** its own weight, so leave gaps (10, 40, 70, 100) to make room for new roles. |
| `title`       | tidy id                        | Display name.                                                                                                                        |
| `inherits`    | none                           | A parent role. Its permissions are copied first. Cycles and unknown parents are errors.                                              |
| `bundles`     | none                           | Named permission sets, below.                                                                                                        |
| `permissions` | none                           | Exact permission names. `ALL` is accepted here only.                                                                                 |
| `grant`       | none                           | More permission names. `ALL` is **not** accepted.                                                                                    |
| `deny`        | none                           | Names removed at the end. `ALL` is **not** accepted.                                                                                 |
| `can_appoint` | `true` if weight is 70 or more | May assign and demote roles strictly below its weight.                                                                               |
| `max_holders` | `-1`                           | `-1` is unlimited. Use `1` for the leader.                                                                                           |
| `authority`   | none                           | Free-text tags for the interface.                                                                                                    |

### How permissions are resolved

For each role, in this order:

1. Everything the `inherits` parent has.
2. Every permission in each of `bundles`.
3. Every permission in `permissions`.
4. Every permission in `grant`.
5. **Remove** every permission in `deny`.

So `deny` always wins over inheritance and bundles on the same role. A child role inherits the parent's **result**, including the parent's denials.

### All permissions

| Permission           | Allows                                                 |
| -------------------- | ------------------------------------------------------ |
| `BUILD`              | Place and break blocks in civilization land.           |
| `CONTAINERS`         | Open chests, barrels and furnaces.                     |
| `INTERACT`           | Use doors, buttons and levers.                         |
| `USE_SHOP`           | Browse the building catalogue.                         |
| `PURCHASE_BUILDINGS` | Buy building cores.                                    |
| `PLACE_BUILDINGS`    | Place building cores.                                  |
| `UPGRADE_BUILDINGS`  | Upgrade buildings.                                     |
| `REMOVE_BUILDINGS`   | Remove or relocate your own building cores.            |
| `MANAGE_BUILDINGS`   | Use, upgrade and remove other members' buildings.      |
| `ADVANCE_AGE`        | Buy and place the next age's core.                     |
| `CONTRIBUTE`         | Contribute materials or money.                         |
| `SPEND_POINTS`       | Spend shared resource points.                          |
| `SPEND_TREASURY`     | Spend shared treasury money.                           |
| `WITHDRAW_TREASURY`  | Withdraw money from the treasury.                      |
| `VIEW_FINANCES`      | View the treasury and its history.                     |
| `COLLECT_TAXES`      | Collect income from buildings paid out to their owner. |
| `INVITE`             | Invite players.                                        |
| `KICK`               | Remove members.                                        |
| `MANAGE_RANKS`       | Create and edit roles.                                 |
| `APPOINT_OFFICIALS`  | Assign roles and appoint the leader.                   |
| `VOTE`               | Vote in leadership elections.                          |
| `CHANGE_SETTINGS`    | Change civilization settings.                          |
| `CHANGE_GOVERNMENT`  | Change the government type.                            |
| `CHANGE_ECONOMY`     | Change the economic system.                            |
| `MANAGE_POLICIES`    | Adopt and revoke policies.                             |
| `MANAGE_DIPLOMACY`   | Manage alliances, truces and tributaries.              |
| `DECLARE_WAR`        | Declare war on another civilization.                   |
| `DISBAND`            | Owner-only. Disband the civilization.                  |
| `TRANSFER_OWNERSHIP` | Owner-only. Transfer ownership.                        |

`DISBAND` and `TRANSFER_OWNERSHIP` are always held by the technical owner and are **not** included in `ALL` or any bundle. A role can still be given them explicitly.

### Bundles

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
| `all`            | Every permission except the two owner-only ones                                                           |
| `leader`         | An alias of `all`                                                                                         |

An unknown bundle name is an error that skips the government.

## Special rules

| Rule                             | Effect                                                                                 | Use it for                                       |
| -------------------------------- | -------------------------------------------------------------------------------------- | ------------------------------------------------ |
| `force_treasury_purchases: true` | Every building money cost is paid from the treasury, and buyers need `SPEND_TREASURY`. | Collectivist or state-run governments.           |
| `force_income_to_civ: true`      | Every building's money income goes to the treasury, none stays on the building.        | Governments where private income does not exist. |
| `exit_only_by_revolution: true`  | `/ages government set` cannot leave. Only a civil war or an admin can.                 | Anarchy or a locked-in regime.                   |
| `allow_custom_roles: false`      | `/ages rank create` is refused.                                                        | A fixed hierarchy.                               |

The three money rules **override** the economy. A government with both forcing rules behaves the same under every economy for purchases and income.

## `modifiers` and `policies`

`modifiers` use the same keys as policy effects. See [YAML Guide: Policies](yaml-guide-policies.md) for the full list and how they combine. `policies` lists the ids of `kind: government` policies this government offers. A policy with `kind: economy` in this list is **ignored**. `policy_slots` limits how many are active at once.

## Worked examples

### An elected tribal council: Clan Council

```yaml
id: clan_council
name: "&2Clan Council"
description:
  - "&7Clans send speakers to a council. The Speaker is elected."
icon: GREEN_BANNER
min_age: tribal
leader_title: Speaker
leader_role: speaker
default_role: clan_member
leader_selection: elected          # members vote
allow_custom_roles: false          # a fixed ladder of four roles
roles:
  clan_member:
    weight: 10
    title: Clan Member
    bundles: [basic_resident]      # build, open containers, contribute
  hunter:
    weight: 30
    title: Hunter
    inherits: clan_member          # everything above...
    bundles: [worker]              # ...plus shop, buy, place, upgrade, remove
    grant: [SPEND_POINTS]          # and may spend shared RP
  elder:
    weight: 70
    title: Elder
    inherits: hunter
    bundles: [officer, diplomat]
    max_holders: 5                 # at most five elders
    can_appoint: true
  speaker:
    weight: 100
    title: Speaker
    inherits: elder
    permissions: [ALL]
    can_appoint: true
    max_holders: 1                 # exactly one leader
modifiers:
  contribution_rate_mult: 1.1      # +10% RP from contributions
  building_cost_mult: 0.95         # buildings 5% cheaper
policies: [neolithic_sharing, council_fires, communal_labor]
policy_slots: 2
change_cost: {points: 800}
change_cooldown_hours: 24
```

### A treasury-run league: Merchant League

```yaml
id: merchant_league
name: "&6Merchant League"
description: ["&7A league of traders. Everything is paid from the shared treasury."]
icon: GOLD_INGOT
min_age: village
leader_title: Guildmaster
leader_role: guildmaster
default_role: apprentice
leader_selection: elected
force_treasury_purchases: true     # buyers need SPEND_TREASURY; the treasury pays
force_income_to_civ: true          # all building money goes to the treasury
roles:
  apprentice:
    weight: 10
    title: Apprentice
    bundles: [citizen]             # note: no SPEND_TREASURY, so apprentices cannot buy
  factor:
    weight: 50
    title: Factor
    inherits: apprentice
    bundles: [treasurer, diplomat] # treasurer adds SPEND_TREASURY
    deny: [DECLARE_WAR]            # diplomat includes it, deny removes it
    max_holders: 6
  guildmaster:
    weight: 100
    title: Guildmaster
    inherits: factor               # inherits the result, so no DECLARE_WAR yet...
    permissions: [ALL]             # ...but ALL adds every permission back
    max_holders: 1
modifiers:
  money_output_mult: 1.1
  building_cost_mult: 1.05
policies: [guild_charters, guild_oaths, university_charter]
policy_slots: 3
change_cost: {points: 1800, money: 600}
change_cooldown_hours: 48
```

## Design notes

* **Think in ladders.** Give each role one job. Use `inherits` so a higher role is a strict superset of the one below.
* **Space the weights.** Rank and authority are decided by weight, so leave gaps. Weight 70 or more can appoint by default.
* **Mind the money permissions.** With `force_treasury_purchases`, a role without `SPEND_TREASURY` cannot buy buildings. With `COLLECT_TAXES` or `VIEW_FINANCES` a member can collect building income.
* **Elections need voters.** Give `VOTE` (the `citizen` bundle includes it) to the roles that should be able to vote.
* **Price the switch.** A high `change_cost` and a long `change_cooldown_hours` stop players hopping between governments. Remember Barter earns no treasury money, so a money cost is unpayable there until the civilization changes economy.
* **Keep modifiers small.** The shipped governments stay within about 0.85 to 1.15 for multipliers and a few blocks for `claim_radius_add`.

## Messages you may see

| Console message                                               | Fix                                   |
| ------------------------------------------------------------- | ------------------------------------- |
| `missing 'id'`                                                | Add an `id`.                          |
| `no roles defined`                                            | Add a `roles` section.                |
| `leader_role 'x' is not a defined role`                       | Add that role, or fix the name.       |
| `default_role 'x' is not a defined role`                      | Add that role, or fix the name.       |
| `leader_selection must be owner, appointed, elected, or none` | Fix the value.                        |
| `role 'x' unknown bundle 'y'`                                 | Use one of the ten bundles.           |
| `role 'x' has unknown permission 'y'`                         | Use a name from the permission table. |
| `role 'x' grant unknown 'ALL'`                                | `ALL` only works under `permissions`. |
| `role inheritance cycle at 'x'` / `inherits unknown 'y'`      | Fix the `inherits` chain.             |
| `unknown policy 'x' ignored`                                  | The policy id does not exist.         |
| `unknown min_age 'x', treated as none`                        | The age does not exist.               |
