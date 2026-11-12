# 🛡️ PrinterGuard

**Catch printer users. Leave EasyPlace users alone.**

PrinterGuard is a Paper plugin that detects players using a **printer** (automatic block placement from a schematic, as in Litematica and similar mods) and quietly alerts your staff. If the player keeps going, it bans them automatically.

> **Version:** 1.0  |  **Server:** Paper 26.3  |  **Java:** 25

---

## ✨ Features

- 🎯 **Tells printer and EasyPlace apart.** Fast EasyPlace building is ignored.
- 🤫 **Silent to the player.** Only staff with the alert permission are notified.
- ⚡ **Automatic ban on repeat.** If the player keeps going within a few seconds, a ban command runs (one day by default).
- 📜 **Console logging** of every alert and every ban.
- 🧪 **False-positive protection.** Checks are skipped during server lag and for players with high ping.
- 🔧 **Fully configurable** thresholds, messages and ban command.

---

## 🔍 How detection works

EasyPlace only places blocks where the player is looking, within normal reach, and no more than one per tick. A printer doesn't follow those limits.

Instead of counting every fast placement, PrinterGuard counts **suspicious blocks**. A block is suspicious if at least one of these is true:

| Sign | Meaning | Default threshold |
|---|---|---|
| **Same tick** | More than one block placed in the same server tick | n/a |
| **Too far** | The block is placed against a block farther from the player's eyes than normal reach | `max-reach: 6.0` |
| **Not looking** | The angle between the player's view and the block is too large | `max-look-angle: 30.0` |

Normal building and fast EasyPlace trigger none of these, so they are never flagged.

---

## ⚙️ What happens when it triggers

1. **Alert.** A player places `min-flagged` (5) suspicious blocks within `window-ms` (2 seconds). Staff with `printerguard.notify` get a message: *"Player X is using a printer!"* with the reasons. The player is told nothing. The event is logged to the console and the last block is cancelled.
2. **Ban.** If, within `ban-window-seconds` (3 seconds) afterwards, the player places `confirm-flagged` (3) more suspicious blocks, the command from `ban-command` runs. This is also logged and sent to staff.
3. **Reset.** If the player stops, the window closes and counting starts over.

---

## 📦 Requirements

- Paper **26.3**
- Java **25**
- A plugin that provides a ban command (for example `tempban` from Essentials or LiteBans). PrinterGuard doesn't ban anyone itself, it runs the command from the config.

---

## 🚀 Installation

1. Put `PrinterGuard-1.0.jar` into your server's `plugins/` folder.
2. Fully restart the server (don't use `/reload`).
3. Edit `plugins/PrinterGuard/config.yml` to your liking.
4. Apply changes with `/printerguard reload`.

---

## 🔑 Permissions and commands

| Permission / Command | Description | Default |
|---|---|---|
| `printerguard.notify` | Receive printer alerts | op |
| `printerguard.admin` | Use `/printerguard reload` | op |
| `printerguard.bypass` | The player is never checked | nobody |
| `/printerguard reload` | Reload the config | n/a |

Give a moderator alerts in LuckPerms, for example:

```
/lp user NAME permission set printerguard.notify true
```

---

## 🛠️ Configuration

| Option | Description | Default |
|---|---|---|
| `min-flagged` | Suspicious blocks needed to raise an alert | `5` |
| `window-ms` | Time window for those blocks (ms) | `2000` |
| `max-reach` | Max distance to the block being placed against | `6.0` |
| `max-look-angle` | Max angle between view and block (degrees) | `30.0` |
| `ban-window-seconds` | Seconds after an alert in which continuing leads to a ban | `3` |
| `confirm-flagged` | Suspicious blocks in that window that trigger the ban | `3` |
| `admin-message` | Alert text for staff | see config |
| `ban-notify-message` | Ban text for staff | see config |
| `ban-command` | Command run on ban, `%player%` is the player's name | `tempban %player% 1d ...` |
| `min-tps` | Skip checks if server TPS is below this | `18.0` |
| `max-ping` | Skip checks for players with ping above this (ms) | `400` |
| `cancel-placement` | Cancel the block that triggered the detection | `true` |
| `ignore-creative` | Don't check players in creative mode | `false` |
| `debug` | Log every suspicious block to the console | `false` |

Message placeholders: `%player%`, `%count%`, `%reasons%`, `%ban_window%`.

---

## 🎚️ Tuning before real use

1. Set `debug: true` and run `/printerguard reload`.
2. Place blocks with **EasyPlace**. The console should show no suspicious lines.
3. Test a **printer** (ideally on a test account). You should see flags and a staff alert.
4. If EasyPlace shows up in the log, raise `max-look-angle` or `max-reach`. If the printer isn't caught, lower `min-flagged`.
5. At first, set `ban-command` to something harmless like `say` and watch who gets flagged. Then restore the real ban command.
6. Set `debug` back to `false`.

---

## ⚠️ Limitations

- Detection is based on behavior, not a guarantee. A printer that turns the player's view toward every block and places one block per tick is hard to catch.
- Only the block that triggered the detection is cancelled. Blocks already placed are not rolled back.
- Trigger state is kept in memory and resets on server restart.
- Not yet tested against a real printer or real EasyPlace, so tuning with `debug` is essential.
