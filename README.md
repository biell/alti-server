# alti+server
Altitude game server wrapper to enable enhanced modes of game play.

## Files

  * **README.md**  The file you are reading.

  * **FAQ.md**  Frequently Asked Questions, and helpful tips for server
    administrators and map makers.

  * **INSTALL.txt**   A list of the quick steps to install this software.
 
  * **UNLICENSE.txt**  This software is in the Public Domain, this file
    states so.

  * **alti+server**  The perl code which starts the altitude game engine
    and monitors it for events.

  * **altx-tool**  Manipulate Altitude map archive files.

## Alti+ game types

### Normal Games
All normal game types will run just as normal on `alti+server` systems.
They can be run with all default values, or spiced up by changing
camera or plane scale, gravity, etc.  It is up to the server admins.

### Capture the Flag
There are number of options for game types which are different from what
you would normally see. Capture the flag games have a powerup which is
located in an area heavlily protected by one team. The power should only
be accessible to the other team. The server game mode must be "Ball",
as each capture increases the goal count. One variety of Flag capture
is a 1-life version, where teams go head-to-head and is very similar to
1dm. Another allows re-spawns and is timed. This mode type should favor
the defensive team, and a single capture (goal) should be sufficient for
winning. Capture the flag games can also be configured to allow both teams
to score. Hear, the game would be similar to tbd, but withou a bomb to
pickup. This would hopefully get teams to work together better, instead
of people grabbing the bomb and flying off by themselves without support.

### Races
Races use specially assigned powerups to act as checkpoints.  Each time
the first player on a team crosses a checkpoint, a goal is scored.  For
this reason, the map must be configured for game type *ball*.  Most servers
are setup to have 6 goal win.  Races are best accomplished with 2 laps and
3 checkpoints, with players starting so that they cannot easilly access
the winning (6th) checkpoint until the end of their first lap (starting in
a pit row might work).  Checkpoints must be crossed in alphabetical order,
all powerups are sorted, and the sort order is used to ensure players
cross checkpoints in the correct order.  Keep this in mind when creating
checkpoint names.

A to B races would also be easy, with checkpoints along the way, and the
6th checkpoint at the end.  If a final (winning) checkpoint is all that
is desired in an A to B, then setting `/set ballScore -1 -1` would also
help accomplish this game type.

Records will be kept for races.  For races where each team has a single
spawn point, the nickname of the player who crosses the finish line first
will be stored with the record.  If teams have multiple spawn points, then
a record will only be saved if the number of players is at least equal
to the number of spawn points, and then the name of the team will be stored
with the record.

### Large Map Coops
Many new spawn options are available with the `alti+server`. Players
can be configured to spawn where they last died, to make continuation
easier. If that is too immidiate, players can spawn at the place of their
last pickup, or health. Coops can also be made more exciting by the use
of devices like portals, to teleport players from one area to another.

### Most Dangerous Game
Asymmetrical game mode mdg plays like Counter Strike.  The map is configured
to assign players to either the left or right team, just like in coop.
However, one player (the first one to join a team) is placed by themself
to fight an entire team of enemy planes.  In this mode, you can specify
alternate zones to spawn into.  The server will randomly spawn the single
player in one of these.  This allows the standard spawn zone for that
team to be boxed up like a coop game.  Here, any Bots can be seperated
from the rest of the game.  In 1-life games, this box should have a turret
to clean up the Bots.

If everyone agrees that a specific player should be the prey, a vote can
be called against the `/prey` command.  This can be used to override the
default behavior of first to join a team is the prey.

## Map Integration
Altitude maps end with a `.altx` extension.  These are simple archive files
which contain all the necessary information for game play.  A tool is
included with `alti+server` called `altx-tool`.  This tool allows you
to list, extract, update, and create `.altx` archive files (i.e. map files).
It has a special `-p` option which allows users to edit a special file
in the root of the archive called `plus.txt`.  This file may contain
directives to any `alti+server` to configure the map per the authors defaults.

This tool is increadibly useful.  If the author designs a coop map where
users should be defaulted to a specific side, gravity should be disabled,
initial goal scores should be set, map introductions are necessary, or any
other settable Alti+ attribute; those can be put here. Only the commands
*set*, *unset*, *add*, and *del* are allowed in this file.  Any line containing
anything else will be ignored.  Use this file to set new attributes, or unset
old ones which are no longer valid.

See [Extended Commands for Admins and Map Makers](#extended-commands-for-admins-and-map-makers)
for more information.

## Additional Commands
The below list of commands are available to players to interface with and
manage Alti+ game maps.

### Extended Commands for Players

  * **/switch**<br>
    This command is useful for Coop maps, it allows users who are assigned
    to the wrong team to spectate and choose the other side.  This command
    should usually not be needed, as the correct side should be set by the
    server or map maker with the `/set assignTeam [left|right]` command.

  * **/vote prey [player]**<br>
    If there is an mdg (Most Dangerous Game assymetric game play) map in
    the rotation, this command will be present.  Normally, the single player
    is choosen as the first to join the game (first to press `f`).  If
    greater than 60% of people believe another person should be the prey,
    then this command can be used to change the player listed as the prey.

    It is proper ettiquite for a person to nominate themselves, and not
    to for players to be chosen against their will.

  * **/spawnZone [zone]**<br>
    When a player has a non-standard spawn zone, they can reset their spawn
    zone to the original map default by running `/spawnZone start`.
    Otherwise, this command works only if the server is in zone spawning
    mode. It allows players to choose which `zone` they will respawn into
    when they die. Also, when players execute this command, they will
    automatically be removed from the map and a new spawn will be initiated
    in `zone`. All bars and powerups will be lost.

  * **/sz [zone]**<br>
    This is an alias for `/spawnZone`

  * **/changePlane**<br>
    Change plane without having to die.  If you run `/changePlane`, then your
    plane will be removed from the map and you will have the opportunity to
    choose a different plane and respawn when you left off.  This feature
    can be turned off on a map-by-map basis, so it may not always be enabled.
    The purpose of this map is mainly for coop maps which require multiple
    planes for specific parts, but where there are not enough players to
    fill each part.

  * **/cp**<br>
    This is an alias for `/changePlane`

  * **/restartMap**<br>
    This command is a front-end to `/balanceTeams`. If you are the only
    person on a map, it will balanceTeams without the need for a vote. If
    you are on a 1de, 1dm, etc. map and are one of the last two players
    still alive, you can run `/restartMap` and his will `/balanceTeams`
    without a vote.

  * **/vote randomMap**<br>
    Change to a random map from the mapRotation.  This command requires
    a vote threshold of 66% or greater.

  * **/mesg [status]**<br>
    The server has a private messaging feature designed to aid players in
    meeting goals or to talk privately with the map designer. Howerver, to
    ensure this feature is not abused, this feature can be disabled via
    this command and it's argument.
    `status` can be one of `Yes`, `No`, or `?` to
    turn messaging on, off, or query your message setting respectively.
    This setting is persistent.

  * **/talk [player] [text ...]**<br>
    Send a private message to `player`. You do not have to be friends
    with `player` to do so, but they can disable messaging with the
    `/mesg No` command. Abusers will be banned.

### Extended Commands for Admins and Map makers

  * **/sum [crc32|md5|sha1|sha256]**<br>
    Generate a checksum (message digest) of the *.altx* map file for the
    current map.  The purpose of this command is to help map makers verify
    the current map is the right version.

  * **/logPickups**<br>
    Display the *X* and *Y* coordinates to the screen for each `powerupPickup`
    or `powerupAutoUse` for your plane.  The purpose of this command is to
    help identify powerups for use with *portals*, *doors*, and different
    spawn types.  These are defined more below.

  * **/help [setting]**<br>
    Display a help message for `setting`.  If a `setting` has a
    `/list`, `/set`, `/unset`, `/add`, `/del`, or `/upload`
    command associated with it, then it will also have a `/help` command
    to provide more detail on what is required.

  * **/list [setting]**<br>
    Display the current value or values for `setting`.  For a list of
    settings, see the `/set` and `/add` commands below.  You should specify
    `setting` exactly as named for `/set` commands.  For `/add` commands
    you should pluralize the the `setting` name.

  * **/unset [setting]**<br>
    Remove current value or values for `setting` and restore them to the
    default behaviour.  For a list of
    settings, see the `/set` commands below.

  * **/set ballScore [left] [right]**<br>
    Set the initial ball score for *ball* mode games to `left` and `right`.
    These values must be integers.  If they are negative, then that value
    will be subtracted from the winning score value.

  * **/set assignTeam [left|right|none]**<br>
    Specify that players must join either the `left` or `right` team,
    depending on this setting.  You can also use `<` and `>` as
    shorthand for `left` and `right` respectively.  This feature is most
    useful in Coop game types, where every player should be on one team.
    Setting this value to `none` is equivalent to ``/unset assignTeam``,
    the setting will be removed and the server returned to its default
    behaviour.

  * **/set nextMap [map]**<br>
    This command is usefull for map makers.  When you want the next map
    to be a specific map, regardless of what players vote; then use this
    command.  It ensures that the next map loaded is `[map]`.  It even
    works across server restarts.  So, if you upload a new map and need
    to `/restartServer`, use this command first to ensure the server comes
    up on the new map.  Before the map is loaded it can be undone with the
    `/unset` command.  Once the map is loaded, the value of `nextMap` is
    erased automatically.

  * **/set planes [Loopy|Bomber|Explodet|Biplane|Miranda] [...]**<br>
    Specify that only the list of planes provided here are allowed on this
    map.  When players attempt to spawn with the wrong plane, they will
    be benched and the server will display a message explaining the valid
    plane list.  Nicknames whale and randa are also allowed.

  * **/set gravity [0|1|2|3]**<br>
    Set the default gravity configuration for this map. This setting will
    be kept between server restarts for this map. If this map is removed
    from the server, the setting will be lost. This command can only be
    used to update the current map in-use. The numeric arguments correspond
    to the argument for the `/testGravityMode`. Values are:
    `0` - default gravity,
    `1` - disable gravity for planes/weapons/powerups,
    `2` - disable ball gravity only, or
    `3` - disable all gravity.

  * **/set weapons [0|1|2|3]**<br>
    Set the weapons mode for this map.  This will ensure each time this
    map is loaded that the `/testDisableWeaponMode` command is run.
    Values are:
    `0` - default weapons mode,
    `1` - disable primary weapons only,
    `2` - disable secondary weapons only, or
    `3` - disable all weapons.

  * **/set spawnHealth [1-999]**<br>
    Set the full health for a plane to be a percentage of the default.
    100 is the default, lower is less than default.  And the maximum
    value allowed is 999% of normal.

  * **/set cameraScale [40-300]**<br>
    Set the camera scale (zoom) level from anywhere between `40` and `300`.
    The default is `100`%, scale up or down from there.
    This setting will automatically call `/testCameraViewScale` to set the
    current scale, and it will update the server to make this setting each
    time the current map is loaded in the future. This setting will persist
    across server restarts. To set it back to the default of `100`,
    you can `/unset cameraScale` or `/set cameraScale 100`.


  * **/set planeScale [40-300]**<br>
    Set the plane scale level from anywhere between `40` and `300`.
    The default is `100`%, scale up or down from there.
    This setting will automatically call `/testPlaneScale` to set the
    current scale, and it will update the server to make this setting
    each time the current map is loaded in the future. This setting will
    persist across server restarts. To set it back, you can
    `/unset planeScale` or `/set planeScale 100`.

  * **/set spawnMode [died|crash|powerup|health|zone|near|portal|normal]**<br>
    This specifies the type of respawning when a user dies, or requests
    to spawn. The type can be any of the following:
    *died* (the location of last death),
    *crash* (same as death, but don't turn players around after crashing),
    *powerup* (the location of their last powerup of any kind),
    *health* (the location of their last health powerup),
    *zone* (zone based spawning where uses can cange their spawn zone on demand),
    *near* (nearest zone to a player when they die),
    *portal* (players continue to spawn at the end of a portal after a death --implied with `died`, `crash`, `powerup`, and `health`),
    or *normal* (at the normal, map defined start).
    If users get into a bind, they can use `/sz start` to spawn in the
    normal location.

  * **/set changePlane [0|1]**<br>
    Enable (1, the default) or disable (0) the ability for players to use the
    `/changePlane` and `/cp` commands to respawn in their current location with
    a different plane.  This feature is mainly for coop maps, but can be
    used (and abused) on all game types.  Players will not record a death for
    their action, and they will be able to respawn at the same place they
    were when they ran the `/changePlane` command.

  * **/set maxPlayers [#]**<br>
    Set the maximum number of players who can spawn simultaneously on the
    map.  This allows a game to have a lower number of maximum players
    than the server does.  This setting is not intelligent like a ladder
    server.  It is a first come, first serve setting.  If overused, it is
    likely to cause players to leave the game.  However, it is useful for
    MDG games, where 13 against 1 seems a little too asymmetric.

  * **/set gameType [coop|flag|race|zone|mdg|std|normal]**<br>
    Normally the type of a game is ascertained by te file name.
    If that cannot be done properly, the game type can be set via this
    mechanism.

  * **/set intro [text ...]**<br>
    Store text to be displayed at the loading of a map, and to new players
    when they join.  This is intended to help players figure out what to
    do on the map, and what it is all about.

  * **/add portal [name] [px],[py] [sx],[sy],[sa]**<br>
    When a player auto-uses the health powerup located at `px`,`py`, then
    they are automatically transported to `sx`,`sy` and spawn at the
    angle `sa`.  See [Angles](#angles) for more information.

  * **/del portal [name]**<br>
    Delete the *portal* named `name` for this map.

  * **/add door [name] [x],[y]**<br>
    Create a door named `name` at coordinates `x`,`y`.  Any number of
    keys can be created with the same name, and all of these must be
    found before unlocking the door.  When a key is found, the finder
    will receive a message from the server.   When all the keys for
    a door are found, all users on the team which has unlocked the
    door will receive a message from the server.

  * **/del door [name]**<br>
    Delete the door named `name`.

  * **/set keys [team|personal]**<br>
    Set if keys apply to an entire team, or to just one player.

  * **/unset keys**<br>
    Unset all keys flags and return to default behavior.

  * **/add key [name] [x],[y]**<br>
    Create a key for the door named `name` at coordinates `x`,`y`.

  * **/del key [name]**<br>
    Delete all keys for the door named `name`.

  * **/add diverter [name] [x1],[y1] [x2],[y2]**<br>
    Create a diverter pair called `name` with one powerup diverter
    at coordinates `x1`,`y1` and the other at coordinates `x2`,`y2`.
    A diverter alternates access between it's two powerups.  The purpose
    is to forcably break up a team and ensure a fairly even split between
    two sections.

  * **/del diverter [name]**<br>
    Remove the divirter pair named `name`.

  * **/add oneway [x],[y] [direction]**<br>
    Specify that a powerup can only be used in one direction.  It isn't
    exactly one direction, it is actually 180 degrees of a circle with
    the middle being the specified direction.  So, players do not have
    to perfectly pass through a `oneway`.  This device isn't perfect.  The
    server periodically stores plane positions, and this is the data used
    to attain direction.  To ensure that the `logPlanePositions` command
    is issuing data from before the pickup, a delay is introduced.  It
    is not recommended to use a oneway in an area where the map would cause
    a player to cross back across the 180 degree plane of the `oneway` within
    2 seconds.

  * **/del oneway [x],[y]**<br>
    Delete the oneway powerup located at coordinates `x`,`y`.

  * **/add zone [name] [team] [sx],[sy],[sa]**<br>
    Create (or overwrite) a spawn zone named `name`. When invoked, spawn
    users at coordinates `sx`,`sy` and at angle `sa`.
    Players must use `/spawnZone name` to change their spawn zone to this
    location when *spawnMode* is set to `zone`.  Players will automatically
    spawn at the closest zone to their death when *spawnMode* is set to
    `near`.  See [Angles](#angles) for more information on angle specifications.

    If a zone is added with the special name `start`, then this will override
    the map-defined start locations for planes.  This feature is mostly only
    useful to ensure human players spawn on playable areas while bots spawn
    sequestered.

  * **/del zone [name] [team]**<br>
    Delete the *zone* named `name` for this map for `team`.  You can specify
    `left`, `right`, `<`, `>`, or `*` for the team.  `*` means both
    teams.

  * **/add powerup [name] [team] [x],[y]**<br>
    Specify that a special powerup for `team` should be at the coordinates
    `x`,`y`. If this is picked up by the correct `team`, then that
    `team` the behaviour is defined based on *gameType*. `team` can be one of
    `left`, `right`, `<`, `>`, or `*` (for both teams).  For exampe, if
    *gameType* is set to `flag`, then the team which picks up the powerup
    earns a goal, and the players respawn.

  * **/del powerup [name] [team]**<br>
    Delete the special powerup `name` for `team`.

  * **/add note [x],[y] [message text]**<br>
    Create a message associated with the powerup at coordinates `x`,`y`.
    Whenever a player trips this powerup, the message will be displayed to
    them.  This can be used to offer help to players, make fun of them, or
    congratulate them on meeting a certain objective.

  * **/del note [x],[y]**<br>
    Remove the message associated with the powerup at coordinates `x`,`y`.

  * **/! [command] [arg1] [arg2] [...]**<br>
    This special admin command is only availble when the server option 
    `configSize` is set to `tiny`.  All administrative commands are removed
    from the generated custom commands file.  Access to these funcions
    go through this command.  Enter `/!`, then a space, then the admin command
    as it would otherwise be (e.g. `/! set gravity 3`).  This command can also
    be used to access super admin funcitonality.  No help is given on this
    command, or any of the administrative sub-commands, save an error message
    when an invalid command is entered.  Only use this feature if you need
    to run more than 100 maps on a server and you set the `configSize` option
    to be `tiny`. See [Server Options](#server-options) below for more
    information on the `configSize` option.

### Extended Commands for Super Admins
Super admin functionality is set in the `alti+server` code itself.  The
`superAdmin` setting can be set to either
`admin` (all admins can perform super admin functionality),
`console` (super admin functionality is limited to the console), and
`list` (where an option array `superAdmins` contains a list of vaporIds which
are allowed to execute super admin functionality.  The purpose of the
distinction between normal Altitude admin functionality and Super Admin
functionality is that super admins can change the maps on a server and restart
it.  There may be users who are admins to set ban and kick, and can also
`set assignTeam` to keep the game going, but should have access to restart
the server.  If this option is set to `console`, then the `upload` and
`removeMap` commands will not be included in the *custom_json_commands.txt*
file to save space for more maps.

  * **/restartServer**<br>
    Restart the server. This will shut the currently running server down,
    and start a new one. This should be used to load new maps or to re-load
    the configuration after a map has been deleted. If an update to an
    existing map is made, the server does not need to be restarted.

  * **/removeMap [map]**<br>
    Remove the map named `map`. The map file will be removed from the server,
    and it will also be removed from the maplist and rotation.
    This will not be fully implemented until the server is restarted,
    which should always be done after removing a `map`.

  * **/upload official [map]**<br>
    Upload the map `map` FROM http://maps.altitudegame.com/ and save it
    on the server in the maps directory.  The name `map` must be exactly
    the name of the map file without the *.altx* extension.  If the
    uploaded map is not currently listed in the server configuration
    file, it will be added.  However, the server must be restarted for
    the map to be added to the maplist and rotation.

  * **/upload alti+ [map]**<br>
    Upload the map `map` FROM
    https://github.com/biell/alti-maps/blob/master/maps
    and save it on the server in the maps directory.  The name `map` must be
    exactly the name of the map file without the *.altx* extension.  If the
    uploaded map is not currently listed in the server configuration
    file, it will be added.  However, the server must be restarted for
    the map to be added to the maplist and rotation.

  * **/upload altitudegame [map]**<br>
    Upload the map `map` FROM http://altitudegame.com/map/ and save it on
    the server in the maps directory. The name `map` must be exacly the
    same as the linkable text of the altitude game map site.
    The most recent version of the map will be uploaded to the server
    from altitudegame.com. If the uploaded map is not currently listed in
    the server configuration file, it will be added. However, the server
    must be restarted for the map to be added to the maplist and rotation.

  * **/upload ladder [map]**<br>
    Upload the map `map` FROM http://maps.planeball.com/ and save it to
    the server in the maps directory. The name `map` must be exacly the
    same as the name of the file but without the *.altx* extension.
    If the uploaded map is not currently listed in the server configuration
    file, it will be added. However, the server must be restarted for the
    map to be added to the maplist and rotation.

  * **/upload tinyupload [key]**<br>
    Upload the map stored on http://s000.tinyupload.com/ with the *file_id*
    of `key` and save it to the server in the maps directory.  The key
    should be a 20 digit decimal number, you do not need to provide the
    rest of the URL.  Get the map name from the tinyupload website, and
    name the map in the maps directory from that scraped web content.
    If the uploaded map is not currently listed in the server configuration
    file, it will be added. However, the server must be restarted for the
    map to be added to the maplist and rotation.

  * **/upload url [map] [url]**<br>
    Upload a map to the server FROM `url` and save it as `map`.
    You do not need to specify an extension for the map, nor do you need
    to specify a location. The map will be stored in the correct location
    under the maps directory on the server. The `url` given may be an
    HTTP redirect, tinyurl, etc. The `url` given cannot require user
    interaction, or contain a javascript based redirect. If you attempt
    to upload a file from a location with a javascript URL, the HTML
    containing the javascript will instead be uploaded. This will be detected,
    and the upload will be aborted. If the uploaded map is not currently
    listed in the server configuration file, it will be added. However,
    the server must be restarted for the map to be added to the
    maplist and rotation.

### Console only commands

  * **/server [list|set|unset|add|del] [option] [args...]**<br>
    List, set, etc. server options.  These commands are available only
    from the console because they are sensitive.  For example, you can
    specify who is a super admin using these commands, change the
    message of the day (motd), or change the maximum size allowed for
    upload files.  These settings are stored in your stash, and read in
    front of the default options listed in the server code.  Which means
    they persist across software updates.  See
    [Server Options](#server-options) below for a full list of options
    with descriptions.

  * **/importDefaults**<br>
    If the `.altx` map file for the currently loaded map contains a file
    called `plus.txt`, then execute the commands in that file to set the
    authors defaults.  This function is automatically initiated if a map
    is uploaded using one of the `/upload` commands, or if the map is
    being loaded for the first time and has never been initialized.  If
    you need to load the defaults at any other time, use this console
    command to initiate that task.

  * **/stashDisplay [map]**<br>
    Print the stash object in memory for `map`.

  * **/stashClearSettings [map]**<br>
    Clear all settings for `map`.  Afterwards, only records (if any) will
    be left in the settings cache for this map.

  * **/stashClearRecords [map]**<br>
    Clear all records for `map`.  Afterwards, records will be started fresh
    if the map is played again and `recordTypes` is set to include `map`.

  * **/showPlayer [player]**<br>
    Print information on `player`

  * **/showTeams**<br>
    Print a list of teams and the players who are on them.

  * **/showSeen**<br>
    If user information is being kept per the "altBouncer" option, then
    display the users currently queued for the next altbouncer update.

  * **/showPowerups**<br>
    List all the powerups defined for the current map.  This is useful for
    finding powerup coordinates to specify Alti+ powerup overloading.

  * **/chatLeft [message]**<br>
    Send `message` to all members of the left team.

  * **/chatRight [message]**<br>
    Send `message` to all members of the right team.

  * **/chatSpec [message]**<br>
    Send `message` to all spectators.

  * **/shutdown**<br>
    Shutdown the Altitude game engine, then `alti+server` itself


## Server Options
Use the console-only `/server list`, `/server set`, `/server unset`,
`/server add`, and `/server del` commands to modify the below values.

  * **lobby [map]**<br>
    Set `map` to be the lobby map for this server.  When the server
    becomes idle, alti+server will change the map to lobby.  When players
    join the server, the server will change the map for them to a random
    map in rotation.  Lobbies serve two purposes:
    (1) maps should be started with players ready to play so they can be
    assured to start fresh, and
    (2) some maps use a lot of CPU (especially some coop maps where bots
    spawn on top of a health), and the lobby map can be set to be low
    CPU.
    If set to a null string, no lobby map is in effect; this is the default.
    N.B. The `nextMap` setting is not consulted on an idle server when
    `lobby` is set.

  * **updateMapList [0|1]**<br>
    Specify if alti+server can change your launcher_config.xml
    file with an updated map list and map rotation list.  These
    lists will be automatically randomized each time the server
    restarts.  Furthermore, if your map list is too large for
    the Altitude game engine to support, it will trim the list
    focusing on keeping more recent and popular maps in the
    list, and discarding less popular/older maps.  This feature
    is required if you wish to have the map upload feature
    automatically add new maps to the map list.  Without this
    feature, you will need to edit your launcher_config.xml
    file by hand (or with the server_configurator) to manually
    add new maps.
    Set to 1 to enable this feature, set to 0 to disable.
    
  * **enableTalk [0|1]**<br>
    Server aided direct chats with other players are supported when
    `enableTalk` is set to `1`.  They are disabled when it is set to `0`.
    Server aided direct chats allow any player to communicate only with
    one other player, regardless of if they are friends.  This feature
    is increadibly useful when developing new maps.  Map makers can
    discuss issues and provide tips to individual players without spamming
    all players.  Most servers do not require this kind of interaction, and
    server administrators may be concerned the commands will result in
    abuse.  As such, `enableTalk` is off by default.  Additionally, these
    commands take up space in the Altitude initialization packet, thus
    limiting the number of maps the server can load.  Not enabling this feature
    results in enough space for an additional couple maps to be run by
    the server.

  * **changePlane [0|1]**<br>
    Set the server wide default value for `changePlane`.  This can be
    overridden on a per map basis, but if not set, this will be the default.
    The code default is `1` (allowing use of the `/changePlane` and `/cp`
    commands.)  Setting this to `0` will reverse that behaviour.

  * **allowUploads [0|1]**<br>
    Allow uploads to the server by admins (assuming the user fits the criteria
    of a *superAdmin* as defined below).  If this option is disabled, then
    the `upload` and `removeMap` commands will not be included in the
    *custom_json_commands.txt* file.  This option is useful if you don't plan
    on using this feature, and wish to reserver more space for maps.  Disabling
    this ability will allow your server to run between 4 to 12 extra maps,
    depending on their names.  While on by default, it is recommended that
    this feature be disabled if you don't plan on using it.  Disabling this
    feature removes it from both the game and from the console.

  * **ulSizeLimit [#]**<br>
    Specify the maximum size of an uploaded map.  This number
    is an integer which represents Bytes.  If the integer is
    followed by the letters `g`, `m`, or `k` then the number
    is assumed to be in gigabytes, megabytes, and kilobytes (respectively).
    Since you may allow other people to upload files to your server,
    there needs to be some sort of maximum value.  Please note that the
    file may need to be fully downloaded before its size can
    be checked.  If the size is above ulSizeLimit, then the
    map file will be promptly deleted.

  * **altBouncer [0|1]**<br>
    Add support for sending data on users who join the server to the
    AltBouncer (http://tec27.com/altbouncer/) site.  Setting this to
    `0` (the default) will ensure data does not get forwarded to the
    AltBouncer site.  Setting this to `1` will periodically send data
    to the site.  Data will normally be sent when the server is otherwise
    idle, so the upload process does not affect game play.  Also, when
    shutting down, user info will be flushed to the AltBouner server before
    exiting.

  * **restartMap [#]**<br>
    Specify to `alti+server` that if users download the `altx` map file
    in longer than game delay, but less than `#` seconds, then restart
    the map over.  This allows the game to be more properly played and
    balanced.  Also, it is critical for race maps that players start at
    the same time.

  * **boxColor [black|blue|cyan|green|magenta|red|white|yellow]**<br>
    Two sections of the console curses interface are encapsulated
    in boxes.  This value specifes the color of these boxes.
    The color must fall into the limited Curses color name values.

  * **chatFromColor [black|blue|cyan|green|magenta|red|white|yellow]**<br>
    In the chat window, the name of the person sending a chat
    message will be displayed.  This value is the color of the
    nickname text of that person.  You must use the limited
    The color must fall into the limited Curses color name values.

  * **chatToColor [black|blue|cyan|green|magenta|red|white|yellow]**<br>
    In the chat window, a specifer as to the recipients of a
    message is displayed.  This value is the color of that specification.
    The color must fall into the limited Curses color name values.

  * **uiPrettyBoxes [0|1]**<br>
    Curses can draw attractive boxes on the screeen using either
    a terminal's Alternate Character Set (ACS) or Unicode
    drawing characters.  This almost always works, but when it
    doesn't, the experience is quite appalling.
    Setting this to `0` will disable these specialty characters
    and just the standard ASCII '-' and '|' characters.  Setting
    this to `1` will tell Curses to draw attractive boxes on the
    terminal screen.

  * **noviceLevel [#]**<br>
    Require the novice users (Ace 0) attain at least this level
    before being allowed to play on the server.  Players below
    this level will be forced to spectate, and sent a server
    message outlining the policy.  Continued attempts to join
    will result in a kick.  This value differes from the server
    enforced level requirements, as it only affects players of
    Ace Rank 0.

  * **aceLevel [#]**<br>
    Require users who have recently Aced (anywhere from 1 to 10)
    to have at least obtained this level before being allowed to
    play.  Players below this level will be forced to spectate,
    and sent a server message outlining the policy.  Continued
    attempts to join will result in a kick.  This value differs
    from the server enforced level requirements, as it only
    affects players of Ace Ranks `1` through `10`.

  * **snarkChance [#]**<br>
    Specify the likelihood that a snarky comment will be made
    about a player.  The server is configured with a number of
    funny comments to make about plane choices, perks, skins,
    etc.  Events which cause these to be displayed are things
    like spawning.  So, this number is the likelihood per user
    that this happens.  If you set this to `0`, then snarky
    comments are disabled.

  * **bullyKick [0|1]**<br>
    Online games with chat functionality will, from time to time,
    attract players who play a sort of meta-game, one where
    (for them) the real game is to abuse other players and create
    havoc.  These sorts of individuals often result to vulgar
    language to acheive their goals.  The server looks for these
    types of behaviour and warns players who engage in it.  After
    many repeated attempts, the server will kick the bullies.
    Setting this to 1 enables this player protection and the
    support of a positive gamming experience.  Setting this to
    0 disables the servers ability to protect players from this
    sort of harrasment.

  * **infractions #**<br>
    The number of infractions (defaults to `10`) before a player is kicked.
    An infraction could be language related if the `bullyKick` option is
    enabled, but there are also other infractions.  For example, continually
    trying to join a game that you cannot join is an infraction b/c it is
    seen as a way to try to break the server.  Spamming chat even after
    a player is chat blocked is seen as overly aggressive behavior when
    `bullyKick` is enabled, and so is also an infractions.  Normally,
    infraction count is incremented by 1.  However, some infractions will
    increase the count by more or less depending on their likelihood to
    be the cause of normal behavior vs a willfull attempt to break the
    system.  Each time the map changes 1 infraction is forgiven.

  * **debug [0|1]**<br>
    Enable debugging.  Statements in the code which call the debug
    library will now send messages to console, and to all admins
    who are playing the game.
 
  * **awards [0|1]**<br>
    At the end of a game, custom awards are provided.  This feature
    can be disabled by setting `awards` to `0`.
    See [Custom Awards](#custom-awards) for more information.

  * **recordsTypes [type1,type2,...]**<br>
    Keep records of game types `type1`, `type2`, and so on.  This is
    a commonly desired feature on coop and race map types, and not
    so much on other map types.  If this string is set to non-empty,
    then for these maps the top 5 times will be displayed at the end
    of each round.  Additionally, a team will be congratulated if they
    score in the top 5.

    For game types of `race`, `alti+server` will keep the name of the
    winner.  If the team the winner is on has exactly 1 available spawn
    point for members of that team, then the individual name is stored.
    Otherwise, the color of the team is stored with the record.

  * **rotationRE [RE]**<br>
    Not all maps need to be in the map rotation, and should only
    be available via an admin */changeMap* or a */vote changeMap*.
    This value is a regular expression (so you can seperate
    values with a pipe "|" character) which specifies which maps
    should be in rotation.  For example, "1de_coop" maps often
    kill servers, causing players to quit.  This can be used
    to keep this from happening.  Conversely, it could also
    be used to make only 1-life games occur.  It is the
    perogative of the server administrator.

  * **configSize [normal|small|tiny]**<br>
    Set the configuration size to be either
    `normal` (preferred), 
    `small` (if you need to serve more than 80 maps), or to
    `tiny` (if yo uneed to serve more than 100 maps).
    This configuration parameter changes the way the *custom_json_commands.txt*
    file is generated, and how users inside altitude interact with the
    alti+server code.  If the preferred setting of `normal` is used, users
    on the console and in the game have the same experience.  They both get
    help on arguments and command expansion when typing.  If set to `small`,
    then users inside the game no longer get help on arguments and expansions.
    Where lists of sub-commands used to be, free-form strings must be entered
    which either match commands, or produce errors.  If set to `tiny`, then
    administrative commands are pushed under a super command named `!`.  No
    help is given on this commands, and users inside the game must remember
    exact syntax.  For any setting here, use of the `/help` command is
    especially helpful.

  * **secretCode [password]**<br>
    Altitude servers can be configured using the `server_configurator` program
    to require a passcode to join the server.  This setting allows you to
    perform the same function on the `alti+server` console, saving you
    from having to run a gui or edit an XML file directly.

  * **superAdmin [admin|console|list]**<br>
    Some servers have two levels of admins.  Admins whose job
    it is to keep the peace, and admins who support the server.
    This setting allows for a distinction between these two types
    of admins with respect to alti+server's custom commands.
    Commands which add or remove maps are super admin commands.
    Also are commands which allow the restart of a server. Set
    this to `admin` to make admins and super admins the same
    group.  Set this to `console`, and super admin commands
    will only be available for users at the alti+server console.
    Finally, setting this to `list` will allow a list of
    vaporIds to be set with actual super admin users.
   
  * **admins [vaporID|nickname]**<br>
    An array of server admins.  If this list is unset, then the
    *adminsByVaporID* section of the `launcher_config.xml` file
    is left in-tact.  If this array is set, then the vapor IDs in
    this array are set to be the server administrators and any vapor IDs
    listed in the `launcher_config.xml` file are replaced with this
    list.

  * **superAdmins [vaporID|nickname]**<br>
    This is an array (so use "add" and "del" when at the console)
    of vapor IDs which have super admin privledges.  It is used
    only if the value of the above "superAdmin" value is set to "list".
    If you specify a `vaporID`, then it will be added to the list of
    super admins.  If you specify a `nickname`, then that player must be
    on the server right now, so their vaporID can be looked up and stored
    in the list of super admins.  You cannot actually store a nickname
    in the list of super admins and have it work, that would be dangerous.

  * **motd [message text...]**<br>
    This is the Message of the Day (motd) used when players
    join the server.  It is an array (each element is a new
    line) so use the "add" and "del" sub functions of
    `/server` when entering from the console.

## Angles
Various interfaces in `alti+server` allow you to specify angles for
spawning.  When specifying angles, the following table lists the
allowed values:

  * **0 to 80**<br>
    The angle in degrees starting from 0(facing right) to 180(facing left)

  * **-1 to -179**<br>
    The downward facing angles in degrees starting from -1 (very nearly
    facing right) to -179 (very nearly facing left). The angle of -90 is
    straight down.

  * **181 to 359**<br>
    The downward facing angles in degrees. These numbers are converted
    automatically to the correct -1 to -179 values where 181 is equivalent
    to -179, 270 is equivalent to -90, and 359 is equivalent to -1.

  * **right|up|left|down**<br>
    These directions are converted to 0, 90, 180, and -90 respectively.

  * **e|ne|n|nw|w|sw|s|se**<br>
    Compass directions which are converted to
    0, 45, 90, 135, 180, -135, -90, and -45 respectively.
    The long names of `east`, `northeast`, ..., `southeast` are also allowed.

  * **c|h|v**<br>
    Specify relative angles directed towards the
    center of the map,
    facing horizontally left or right towards the middle vertical axis, or
    facing vertically up or down towards the middle horizontal axis.
    The long names of `center`, `horizontal`, and `vertical` are also allowed.

## Custom Awards
At the end of a game, give out additional awards.  Awards are only
given if there are enough human players to make them not pointless,
and only if a human player is deserving of the award.
Providing custom awards can be disabled with the `awards` server option.  They
are on by default, and are defined as such:

  * **Training Wheels Award**<br>
    This award goes to the player who crashes the most. 

  * **Kenny McCormick Award**<br>
    This award goes to the player who dies the most, named after
    South Park character who can't make it to the end of an episode.

  * **Ball Hog Award**<br>
    This award goes to the player who holds onto the ball the most.
    Though slightly derogative souding, this can be a compliement.

  * **Sledge Hammer Award**<br>
    This award is named after the 1980's TV character who shot first and
    didn't bother to ask questions. To paraphrase:
    guns don't kill people, bullets kill people.
    This is for most damage dealt.

  * **Tank Award**<br>
    This award goes to the player who manages to sustain the most damage.
    Every team needs a good tank.

  * **Best Ratio**<br>
    This award is given to the player who has the best final ratio
    of Kills to Deaths. Players late to the game may scoop this award.

  * **Front Runner**<br>
    This award is given to the player who has is the first person on
    their team to get to the most checkpoints.  This award is designed for
    a game type of `race`.

## Snarky Server
The server can be configured to emit snarky comments from time to time by
setting the code OPTION `snarkChance` to a positive value.  Setting this
value to 20 (for example) should give each player a 1 in 20 change for
receiving a snarky comment for things for which a comment like this may
occur.  The most commong snarky comments are for plane configuraion choices
and occur when a plan spawns.  So, if a `snarkChance` is set to 20 and a
player dies 20 times in one game, it is likely that they will receive a
snarky comment (if their plane choices have a preconfigured comment).

Setting `snarkChance` to 0 disables this feature, and no snarky comments will
be emitted by the server.

## Minimum Level Requirements
If the `requireLevel` OPTION is set, then the server will not allow players
to spawn who don't meet the minimum requirements.

New players (Ace 0) must unlock all their planes (level 21) to join a game.

Recently aced players (Ace 1 - Ace 10) must unlock all their first loopy
blue perk (level 8).

## Managing Aggressive Behaviour
The server can be configured with the `bullyKick` OPTION to warn, then
eventually kick players who behave in overly aggressive ways towards other
players.  Language which is often used in offensive statements is looked
for, and after 10 utterances, the aggressive player is kicked.

## More information
See the MAP QA website for more information on using the server:
http://mapqa.glaciated.org/
