# Integrations

All integrations are **soft dependencies**: Ages runs without them, apart from the money features that need Vault.

## Vault

Vault connects Ages to your economy plugin. It is used for:

* **Paying building money costs** from the buyer's own balance (unless the economy or government charges the treasury).
* **Collecting building income** into a player's balance.
* `/ages contribute money` and `/ages withdraw`.

The economy is resolved lazily, so it may register after Ages enables. Without Vault, money purchases are refused and uncollected building income cannot be paid out. Under Barter (no money) none of this applies.

## WorldEdit and FastAsyncWorldEdit

When a building is removed or destroyed, Ages clears its core block (see `protection.remove_core_block_on_destroy`). If **WorldEdit** or **FastAsyncWorldEdit** is installed, the core block is cleared through it, which is quieter and avoids block physics. Without it, Ages falls back to plain block removal, which works the same for players.

WorldGuard is **no longer used** by Ages. Land protection is handled by Ages itself (`protection.*`, roles, and the `ages.bypass` permission), so it works alongside WorldGuard regions without any link between them.

## dynmap

Civilization borders and markers are drawn on dynmap. The map refreshes shortly after startup and whenever civilizations change. If dynmap enables after Ages, the markers are started one tick later.

{% hint style="info" %}
`plugin.yml` also lists **BlueMap** as a soft dependency, but Ages ships only a dynmap hook.
{% endhint %}

## PlaceholderAPI

If PlaceholderAPI is installed, Ages registers an expansion with the identifier `ages` at startup. Use placeholders as `%ages_<key>%`.

| Placeholder                                        | Value                                              |
| -------------------------------------------------- | -------------------------------------------------- |
| `%ages_town%` (also `name`, `civ`, `civilization`) | The player's civilization name                     |
| `%ages_tag%`                                       | Civilization name, empty when none (chat-friendly) |
| `%ages_age%` / `%ages_age_id%`                     | Age display name / id                              |
| `%ages_rank%` / `%ages_rank_id%`                   | Role title / id                                    |
| `%ages_leader%` / `%ages_owner%`                   | Leader / owner name                                |
| `%ages_members%` (also `member_count`)             | Member count                                       |
| `%ages_buildings%` (also `building_count`)         | Building count                                     |
| `%ages_points%`                                    | Resource points                                    |
| `%ages_treasury%` (also `money`)                   | Treasury money                                     |
| `%ages_government%` (also `gov`)                   | Government name                                    |
| `%ages_economy%` (also `econ`)                     | Economy name                                       |
| `%ages_is_leader%` / `%ages_is_owner%`             | Yes or no                                          |
| `%ages_in_town%` (also `has_town`)                 | Yes or no                                          |
| `%ages_here_town%` (also `here_name`)              | Civilization owning the land the player stands on  |
| `%ages_here_age%` / `%ages_here_age_id%`           | Age of the civilization owning that land           |

Values are empty or `0` when the player has no civilization.

## Other plugins (API and events)

Ages exposes a small public API and a set of events that other plugins can listen to, including cancellable ones such as war declarations. Use them to add your own rules, for example to block a war during an event or to log civilization activity. The API reference (`API.md`) is in the plugin's source repository.

## bStats (anonymous metrics)

Ages reports anonymous usage statistics through [bStats](https://bstats.org). It is **on by default**.

* Turn it off for Ages with `metrics.enabled: false` in `settings.yml`.
* Turn it off for every plugin on the server with `enabled: false` in `plugins/bStats/config.yml`.
* **What is sent:** the plugin version, Minecraft version, server software and Java version; which optional plugins are installed; civilization, member and building counts; the mix of ages, governments and economies; the most-used building types and categories; active policies; relation and war counts; and a few settings flags such as whether diplomacy is enabled. No player names, addresses or locations are sent.
* If metrics cannot start, Ages logs a warning and carries on.

The console prints a line at startup saying whether bStats started.
