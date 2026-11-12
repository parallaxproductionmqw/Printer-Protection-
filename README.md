PrinterGuard 1.0
Description

PrinterGuard catches players who use a printer (automatic block placement from a schematic, as in Litematica and similar mods) and leaves EasyPlace users alone.

EasyPlace only places blocks where the player is looking, within normal reach, and no more than one per tick. A printer doesn’t follow those limits. The plugin doesn’t just count fast placements. It counts “suspicious” blocks, which have at least one of these signs:

several blocks in the same server tick;
a block placed farther than max-reach (6 blocks) from the player’s eyes;
the player is not looking at the block (the angle between their view and the block is larger than max-look-angle, 30°).

Fast EasyPlace doesn’t produce these signs, so the plugin ignores it.

What happens when it triggers
A player places min-flagged (5) suspicious blocks within window-ms (2 seconds). Admins with the printerguard.notify permission get a message: “Player X is using a printer!” along with the reasons. The player is not told anything. The event is written to the console, and the last block is cancelled.
If, within ban-window-seconds (3 seconds) afterwards, they place confirm-flagged (3) more suspicious blocks, the ban command from ban-command runs (by default tempban %player% 1d ..., so a one-day ban). This is also logged to the console and sent to admins.
If the player stops, the window closes and counting starts over.
Requirements
Paper 26.3 and Java 25.
A plugin that provides a ban command (for example tempban from Essentials or LiteBans). PrinterGuard can’t ban anyone by itself, it only runs the command from the config.
Installation
Put PrinterGuard-1.0.jar into the server’s plugins/ folder.
Fully restart the server (not /reload).
The settings appear in plugins/PrinterGuard/config.yml. After editing, run /printerguard reload.
Permissions and commands
Permission or command	Purpose	Default
printerguard.notify	receive printer alerts	op
printerguard.admin	the /printerguard reload command	op
printerguard.bypass	the plugin doesn’t check this player	nobody

To give a moderator the alert permission in LuckPerms, for example: /lp user NAME permission set printerguard.notify true.

Main settings (config.yml)
min-flagged, window-ms: how many suspicious blocks within how many milliseconds count as a printer.
max-reach, max-look-angle: the distance and view-angle thresholds.
ban-window-seconds, confirm-flagged: how many seconds and how many more blocks of continued use lead to a ban.
admin-message, ban-notify-message: the texts sent to admins (placeholders %player%, %count%, %reasons%, %ban_window%).
ban-command: the ban command, where %player% is replaced with the player’s name.
min-tps, max-ping: when the server is lagging or the player’s ping is high, the check is skipped to avoid false positives.
cancel-placement, ignore-creative: cancel the block when triggered, skip players in creative mode.
debug: log every suspicious block to the console (distance, angle, reason).
How to tune it before real use
Set debug: true and run /printerguard reload.
Place blocks with EasyPlace. The console should show no suspicious lines.
Test the printer (ideally on a test account). Flags and an admin alert should appear.
If EasyPlace shows up in the log, raise max-look-angle or max-reach. If the printer isn’t caught, lower min-flagged.
At first, put something harmless in ban-command, such as say, and watch who the plugin flags. Then restore the real ban command.
Turn debug off.
Limitations
This detection works from behavioral signs and is not a guarantee. If a printer turns the player’s view toward each block before placing it and places one block per tick, it will be hard to catch.
The plugin only cancels the block that triggered the detection. Blocks already placed are not rolled back.
Trigger state is kept in memory and resets when the server restarts.
I haven’t tested the plugin with a real printer or real EasyPlace, so tuning with debug is essential.
