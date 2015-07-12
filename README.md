# alti+server
Altitude game server wrapper to enable enhanced modes of game play.

## Files

  * README.md  -  This file
  * alti+server  -  The perl code which starts the altitude game engine and monitors it for events
  * custom_json_commands.txt  -  The custom commands which allow people to interact with the server
  * small_custom_json_commands.txt  -  A smaller version of the custom commands which don't help users but allow for more maps to be loaded.

## Additional Commands

### Extended Commands for Players

  * **/switch**
    This command is useful for Coop maps, it allows users who are assigned
    to the wrong team to spectate and choose the other side.  This command
    should usually not be needed, as the correct side should be set by the
    server or map maker with the `/set assignTeam [left|right]` command.

  * **/spawnZone [zone]**
    When a player has a non-standard spawn zone, they can reset their spawn
    zone to the original map default by running `/spawnZone start`.
    Otherwise, this command works only if the server is in zone spawning
    mode. It allows players to choose which `zone` they will respawn into
    when they die. Also, when players execute this command, they will
    automatically be removed from the map and a new spawn will be initiated
    in `zone`. All bars and powerups will be lost.

  * **/sz [zone]**
    This is an alias for `/spawnZone`

  * **/restartMap**
    This command is a front-end to `/balanceTeams`. If you are the only
    person on a map, it will balanceTeams without the need for a vote. If
    you are on a 1de, 1dm, etc. map and are one of the last two players
    still alive, you can run `/restartMap` and his will `/balanceTeams`
    without a vote.

  * **/mesg [status]**
    The server has a private messaging feature designed to aid players in
    meeting goals or to talk privately with the map designer. Howerver, to
    ensure this feature is not abused, this feature can be disabled via
    this command and it's argument.
    `status` can be one of `Yes`, `No`, or `?` to
    turn messaging on, off, or query your message setting respectively.
    This setting is persistent.

  * **/talk [player] [text ...]**
    Send a private message to `player`. You do not have to be friends
    with `player` to do so, but they can disable messaging with the
    `/mesg No` command. Abusers will be banned.

### Extended Commands for Admins and Map makers

  * **/sum [crc32|md5|sha1|sha256]**
    Generate a checksum (message digest) of the *.altx* map file for the
    current map.  The purpose of this command is to help map makers verify
    the current map is the right version.

  * **/logPickups**
    Display the *X* and *Y* coordinates to the screen for each `powerupPickup`
    or `powerupAutoUse` for your plane.  The purpose of this command is to
    help identify powerups for use with *portals*, *doors*, and different
    spawn types.  These are defined more below.

  * **/location**
    Display the *X* and *Y* coordinates for the plane where it is about to
    The purpose of this command is to help identify locations for spawn
    points for for use with *portals*, *zones*, etc.
    These are defined more below.

  * **/help [setting]**
    Display a help message for `setting`.  If a `setting` has a
    `/list`, `/set`, `/unset`, `/add`, `/del`, or `/upload`
    command associated with it, then it will also have a `/help` command
    to provide more detail on what is required.

  * **/list [setting]**
    Display the current value or values for `setting`.  For a list of
    settings, see the `/set` and `/add` commands below.

  * **/unset [setting]**
    Remove current value or values for `setting` and restore them to the
    default behaviour.  For a list of
    settings, see the `/set` commands below.

  * **/set ballScore [left] [right]**
    Set the initial ball score for *ball* mode games to `left` and `right`.
    These values must be integers.  If they are negative, then that value
    will be subtracted from the winning score value.

  * **/set assignTeam [left|right|none]**
    Specify that players must join either the `left` or `right` team,
    depending on this setting.  You can also use `&lt;` and `&gt;` as
    shorthand for `left` and `right` respectively.  This feature is most
    useful in Coop game types, where every player should be on one team.
    Setting this value to `none` is equivalent to ``/unset assignTeam``,
    the setting will be removed and the server returned to its default
    behaviour.

  * **/set planes [Loopy|Bomber|Explodet|Biplane|Miranda] [...]**
    Specify that only the list of planes provided here are allowed on this
    map.  When players attempt to spawn with the wrong plane, they will
    be benched and the server will display a message explaining the valid
    plane list.  Nicknames whale and randa are also allowed.

  * **/set gravity [0|1|2|3]**
    Set the default gravity configuration for this map. This setting will
    be kept between server restarts for this map. If this map is removed
    from the server, the setting will be lost. This command can only be
    used to update the current map in-use. The numeric arguments correspond
    to the argument for the `testGravityMode`.

  * **/del [setting] [name]**
    Remove the configuration for `name` from the list of `setting` values.
    For a list of settings, see the `/add` commands below.

### Extended Commands for Super Admins

### Console only commands

  * **/showPlayer [player]**
    Print information on `player`

  * **/shutdown**
    Shutdown the Altitude game engine, then alti+server itself


## More information
See the MAP QA website for more information on using the server:
http://mapqa.glaciated.org/
