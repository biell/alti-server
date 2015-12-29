# alti+server Frequently Asked Questions and Tips
Included in this document are tips, tricks, and answers to questions
you may have regarding Altitude+ games.  The `alti+server` allows
extended game types, but they must be coded to function properly.
See below for help on using the server.


#### Why does my server restart multiple times when I start alti+server?

This is normally due to having too many maps.  The Altitude java server program
will throw and error and quit if the size of the initialization packet is
over 1450 bytes of compressed data.  Since the data is compressed, an exact
calculation of how much space is available is not possible.  When the Altitude
server fails to start due to this condidtion, `alti+server` removes maps from
the map list (it tries to remove files which are not as popular) and tries to
restart the server.  `alti+server` will loop until it has removed enough maps
that the server starts successfully.

Since the number of maps is not the only part of this equation, you may wish
to tune `alti+server` to allow more maps.

If you need a lot of maps (>40) it will be hard to fine tune the server to
allow enough maps.  It is better to take a larger cut to the server, by
running `/server set configSize small`, you will remove the help provided
for custom commands and this takes up a lot of space.  If you need to load
more than 100 maps on a server, you may want to go as far as
`/server set configSize tiny`.  This moves all admin custom commands
(`add`, `del`, `set`, `unset`, and `upload`) under  a single `!` command
with no help.  This last method can work by itself, but is not the only
thing you can do to get to the coveted 100 maps on a server mark.  Combining
a `configSize` of `small` with other methods can also be used to achieve
this goal.

Look at the number of
custom commands supported by the `alti+server` and see if you can remove some
of them.  On the console, run `/command list all` and look for commands with
a value of `1`.  If any of these are not necessary (`/switch` on a non-coop
server for example), then disable them with `/command disable switch` (or
whatever command you wish to disable).

Look at the number of users which can join a server.  On a Coop server,
for example, 14 players is too many.  I lower that to 10.  Each player uses
around 20 bytes of space, and that adds up.  By removing
4 players, you buy yourself 80 bytes of map space.  Since
most maps on a coop server start with "tbd_coop_", that part compresses
really well, and what you are left with is most of that 80 bytes available
to what is left, and that often compresses better than nicknames too.


#### How do I setup checkpoints for race maps?

A race map type is a modification to the built-in Altitude ball mode.  Instead
of players being awarded a goal for getting the ball into the goal, the server
awards them for crossing checkpoints.  The first thing you need to think about
is the number of laps you desire.  Most servers are setup with a 6 goals wins
configuration.  If you are setting up your own server, and only require your
race maps to function on that server, then feel free to change everything below
as appropriate.

You need to pick a race type (A to A, or A to B).  If your track is not a loop,
then you will need to place 6 checkpoints throughout your course, with the
final checkpoint being the finish line.  Alternatively, you could set the
initial ball score to 5 each with `/set ballScore 5 5`, and have 1 single
powerup at the finish line.

If your track is a loop, divide 6 by the number of laps (note, 6 laps with
a single checkpoint will allow for aggressive cheating).  Generally 2 lap
and 3 lap races work the best.  If you choose a 3 lap race, try to ensure
the checkpoints are close to equidistant from each other.  It needs to be
close enough such that turning around isn't faster (unless your race map
is designed so you don't care if people turn around).

Now that you know where to place checkpoints, it is important to understand
a little bit about how they work.  Checkpoints are powerups (usually health)
which are registered with the `alti+server` via the `/add powerup ...`
command.  You need to place more than one of these, and make the respawn
time very low (usually 0.01 seconds).  Make sure that players
have little choice but to go through the health, avoiding it will nullify
all future checkpoint crosses.  It is recommended that 3 columns of powerups
be used to ensure that the checkpoint is longer than a single plane.  This
keeps foolish players from sitting on the checkpoint and disabling other
players from picking up checkpoints.  It is also recommended that a couple
team specific powerups are used to further deter this behaviour.  If you are
traveling so close to another player that they pickup a checkpoint and you
can't, then don't worry (you will receive the checkpoint via a proximity
algorithm).


#### Should I place oneways on my track when implementing race maps?

If you wish to enforce a direction to your map, then a `oneway` is a good
method to do this.  Remember that `oneway`s are not perfect.  You should
place them on only one checkpoint, and on only one column of that checkpoint.
Also, a `oneway` takes more than 1 second to process, so be on the safe side.
Make sure that players don't loop back behind the oneway within a couple
seconds after pickup.

#### I disabled weapons, but warping randas are ruining everything, help?

Yes, it is annoying that even with `testDisableWeaponsMode` set to 3 that
randas can still damage planes when they warp.  A nice thing about how
`testHealthModifier` is implemented is that it is affected by weapons and
not walls.  So, you can increase this to lower the effect of a warping
randa by running `/set spawnHealth 400` (or higher).  Alternatively, you can
`/set planes loopy bomber whale bip` to disallow randas from spawning.

The fastest plane configuration is Loopy with Flexi-wings and Rev, anyway.


#### /set this, /add that, is there an easier way to make these settings?

Yes.  The `alti+server` code comes with a tool called `altx-tool` which can
edit a map archive after it has been saved by the map_editor.  The easiest
way to do this is to put all your commands into a text file.  The, run
`altx-tool -p path/to/map.altx path/to/settings.txt`.  This will take the
contents of `settings.txt` and place it into the map archive (.altx file)
under the name `plus.txt`.  When an `alti+server` loads a map for the first
time, it looks for this file and executes all lines which start with
`/set`, `/unset`, `/add`, or `/del`.  You can use `/unset` and `/del` to
remove old values from previous versions of a map.  Any other line is
treated as a comment, but it is customary to start comment lines with a
pound (#) sign.


#### These maps are complicated, are there any examples?

Yes, you can take a look at the
[alti+maps](https://github.com/biell/alti-maps) github repository
for example maps and `plus.txt` configuration files.


#### I am running an alti+server, how can I get the standard maps installed?

Built-in to the `/upload` command is a way to upload maps from the standard
Altitude+ maps repository.  From console, simply run `/upload alti+ map_name`.
After uploading all the maps you like, run `/restartServer` for the maps to
be entered into the map list and rotation.


#### My race map has weapons, single player records aren't fair, what to do?

Race maps store records based on time.  Unlike Coop, which generally gets
harder with fewer players, a weapons enabled race is too easy with a single
player.  If each team has a single spawn point (in the map definition), then
only one player is needed to save a record, and records the persons nickname
when they achieved that record is saved.  If each team has 2 spawn points or
more, then there must be at least as many players as spawn points, and the
team color is stored with the record.  So, to answer the question, add more
spawn points (you can put them right next to each other, if you like).

#### Should I allow wall powerups

Generally speaking, no.  Alti+ games rely on specific powerups to implement
extended fatures.  A wall can move a powerup off it's original location.
When this happens, alti+server doesn't know the powerup was moved, and
can't register it.  Capture the flag maps should never use walls, and
race maps should only use them if checkpoints are sufficiently large such
that a wall couldn't move all the powerups.  Races would also be very
badly affected by walls.  Ruin type maps will not be effected.

#### I can't finish my ruin map, it says there is still a turret left

The server does not log when turrets kill turrets.  So, you can not place
turrets such that a turret from one team would kill a turret from another.
If this happens, the turret is destroyed, but not logged.  So, the Alti+
code things the turret is still there, and that you must destroy it to
finish.


