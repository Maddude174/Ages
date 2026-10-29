# Integrations

All integrations are **soft dependencies**: Ages works without them.

## Vault

Vault connects Ages to your economy plugin. It is used for **personal funds**: `/ages contribute money` and `/ages withdraw`. The economy is resolved lazily, so it may register after Ages enables. Without Vault, those money commands are unavailable and the treasury still works from building income.

## WorldGuard

When a core block is removed by the plugin, the WorldGuard hook clears the core block only in that case, so regions are not left behind. Ages enforces its own land protection (`protection.*`, roles and `ages.bypass`) independently of WorldGuard.

## dynmap

Civilization borders and markers are drawn on dynmap. The map refreshes shortly after startup and whenever civilizations change. If dynmap enables after Ages, the markers are started one tick later.

{% hint style="info" %}
`plugin.yml` also lists **BlueMap** as a soft dependency, but 0.0.3A ships only a dynmap hook.
{% endhint %}

## PlaceholderAPI

If PlaceholderAPI is installed, Ages registers an expansion with the identifier `ages` at startup (`PlaceholderAPI expansion registered (identifier: ages)` in the console). Use placeholders as `%ages_<key>%`.

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
