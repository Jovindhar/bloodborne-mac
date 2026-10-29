# Profiles and saves

[← Documentation](../README.md)

Each **profile** has its own saves (which can hold several characters), backups and trophies.
Stop the game before changing profiles or managing saves.

## Your first profile

The first time you open the launcher (or after updating from a version without profiles), it
asks for a profile name. Your existing saves stay where they are and belong to this profile.
Play is available once the profile is created.

## Profiles

**Profiles** (in the menu) lists them; the active one is marked.

- **New profile** starts with no saves.
- **Use profile**, or the profile above Play (click its name), chooses which one Play uses.
- **Rename** changes only its name.
- **Delete** removes an inactive profile with its saves and backups, after a confirmation. The
  active profile and the last one cannot be deleted.

## Saves and backups

**Saves** (in the menu) shows the active profile's characters first, then its current save and
its backups.

- Each readable character shows its name, level, attributes, Blood Echoes, Insight, playtime and
  journey, with **Edit character** (see below).
- A backup is made automatically **before every launch**. **Create backup** makes another.
  Backups of a save edited by the launcher are marked **Edited**, the others **Original**.
- **Restore** (then confirm) replaces the save with a backup; the current save is backed up first.
- The folder button opens the save or backup folder. Backups are kept until you delete them.

## Editing a character

**Edit character** opens the save editor, with a tab for each part. The game must be stopped,
and the editor reads names, icons and values from your game files: select the game folder on
Home first. Nothing changes until **Save changes**; **Discard changes** drops them.

- **Character:** Vitality (7–99), Endurance (8–99) and the other four attributes (1–99), Blood
  Echoes (up to 999,999,999) and Insight (up to 99). **Max all** sets them to their maxima,
  reaching level 544. Level follows the attributes; changing Vitality or Endurance also sets the
  saved health or stamina to its full base value. Runes and equipment may change the values
  shown in game.
- **Items** and **Storage:** amounts, up to what the game allows, and **Add item**.
- **Weapons:** a weapon's level and imprint (Uncanny, Lost), with its stats before and after and
  how the imprints compare, and its gem slots. Slots follow the level as in the game (most
  weapons open them at +1, +3 and +6, their shapes depending on the imprint); a gem that no
  longer fits is taken out. **Add weapon** adds one at +0.
- **Armor:** **Add armor**.
- **Gems:** each gem's weapon slot (put in, moved or taken out) and the strength of its effects.
  **Add gem** adds one: choose its effect and its shape (radial, triangle, waning, circle or
  droplet).

The Add buttons open a list of what the game has, with a search; what you add waits in the list
until you save. Adding weapons, armor and gems isn't available yet for the few saves that keep
two copies of the character: saving says so and changes nothing.

Saving first backs up the complete current save set, then marks the save as edited. **An edited
save can't earn new trophies and plays offline only.** To continue from a clean save, restore a
backup from before the first edit.

## Trophies

Trophies you earn are recorded on this device for the active profile (nothing is sent to PSN).
**Trophies** (in the menu) lists all 40, Bloodborne and The Old Hunters separately, with grades,
short requirements and the date you earned each. Show all, unlocked or locked ones, and sort them
by game order, recently earned, locked first, or grade. Platinum comes after the other 33
base-game trophies. Trophies earned before this tracking existed are not imported from a save.

While the active save is edited, the trophy next to the profile's name is slashed (its tooltip
says why), the Trophies page says new trophies are disabled, and none are recorded; trophies earned before stay. Restoring a backup from before the edit
restores them.

## Where it is stored

In `~/Library/Application Support/bloodborne_mac/`:

| Path | Contents |
|---|---|
| `launcher.db` | Profiles, settings, backup records and trophies |
| `user/` | Your first profile's saves, and the shader cache all profiles share |
| `save-profiles/<id>/user/` | Other profiles' saves |
| `save-backups/<id>/` | Backups |
| `database-backups/` | Copies of `launcher.db` made before launcher updates changed it |

## If profiles cannot load

The launcher shows the error with **Retry** and **Show the folder**. Free disk space, fix
permissions or close another launcher, then retry. If it says the database is from a newer
launcher, install the latest version.

**Do not delete `launcher.db`** to get past an error: your profiles, which saves are whose, and
your trophies are kept there. Quit the launcher, copy the whole data folder (including
`launcher.db-wal` and `launcher.db-shm`), and
[report the message](https://github.com/bloodborne-native/bloodborne-mac/issues).
