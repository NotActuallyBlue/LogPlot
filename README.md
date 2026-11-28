# LogPlot

A fast desktop viewer for car data logs, made for tuned VW / Audi cars on Simos ECUs, with an optional connector that lets an AI assistant
(Claude Code, Gemini CLI and others that speak MCP) read your logs and point at what it found on your screen.

Free to use. Made by Ghostwerks.

## What it does

* **Opens CSV logs** from Simos.app, PhantomLog and Simos Tools, and older CSV logs with a clock-time column. Big logs open in a second or
  two. Logs written in other units (psi, °F, mph, lb-ft, kPa fuel pressure, or the unit in the column name) are read into one set of units,
  so pulls, flags, the dyno and Analyze work the same on all of them; the log's line at the top says what was converted. A log with AFR but
  no lambda gets Lambda (AFR ÷ 14.7).
* **Metric or imperial:** Display ▸ Units shows bar or psi, °C or °F, km/h or mph, Nm or lb-ft, with the unit next to every value.
* **Single and Split views:** every channel on one graph (each on its own scale, or one shared scale), or one graph per channel group.
* **A readout on the graph:** the plotted channels in their colours with their values where your mouse is. **Click to mark a spot:** the
  readout then shows the values at the mark next to the cursor's, and the time between them. Right-click or Esc clears the mark.
* **Plotted channels stay at the top** of the channel list.
* **Pulls and flags:** full-throttle pulls, and spans worth a look: knock, lean, boost under target, fuel pressure under setpoint, misfires,
  wheel slip, rich targets. Click one to zoom there.
* **Heatmap:** a value binned by two channels, like a map table (for example ignition by rpm and load). Click a cell to see when the car was there.
* **Scatter:** one channel against another, coloured by a third.
* **Dyno (road dyno):** wheel horsepower, estimated crank horsepower and torque against rpm from a full-throttle pull, worked out from the car's
  speed over time. It corrects for the road's slope when the log has a usable longitudinal acceleration channel. Open a second log with
  **Compare** and its best pull is drawn dashed on the same chart, to compare two tunes.
* **Compare** any two logs on the time graphs: the second log is drawn faint behind the first, with an offset and "Align pulls" to line
  up the strongest full-throttle pull of each, matched on engine speed so the two climb together. The channel list shows both logs' values at the cursor.
* **Analyze:** every pull side by side, plus the whole log. For each pull: peak power, torque, boost and turbo speed (the peak of a
  half-second average, so one spiky sample doesn't set it, with the raw peak under it), highest rpm, intake air temperature and LPFP duty,
  lowest timing, the worst knock and its cylinder, lambda furthest from target, the lowest direct-injection pressure as a percentage
  of its setpoint, the pressure ratio (PUT over ambient pressure: how hard the turbo works, comparable across weather), how far the
  intake air rose above the outside air (intercooler health), and acceleration times (0–60, 40–80 and 60–100 mph, or 0–100, 60–120 and 100–200 km/h). Timing, lambda and fuel pressure leave out the
  moment of a gear change (the gearbox cuts spark and fuel for it) and the sensor's recovery after a fuel cut, so a pull through
  2nd, 3rd and 4th doesn't show a false spark drop or a false lean reading; the pull's header shows the gears it went through. The strongest pull is
  starred. With a compare log open, its strongest pull is shown beside yours. Click a cell to zoom to that pull. **Copy** (to paste
  into Discord or a forum), **Save CSV** and **Save image** share the table.
* **Acceleration times** are timed from the car's speed with the pedal at 75 % or more for most of the run; "0" starts when the car
  first rolls. The speed comes from the wheels, so wheelspin makes a time look quicker than it was.
* **Notes:** "✎ Add a note" next to the log's name (fuel, parts, weather, passengers). Notes show in the logs list, Compare and Analyze,
  and are kept in LogPlot's settings folder; the log files are never changed.
* **Math channels:** ƒ Math… in the Channels tab adds your own values from a formula, such as `AFR = [Lambda] * 14.7` or
  `Boost error = [PUT SP] - [PUT]`. Channel names go in square brackets; + − * / ** %, brackets and abs, sqrt, log, exp, min, max
  are allowed. A formula is added to every log that has its channels. **Add presets** adds Pressure Ratio (`[PUT] / [Ambient Press]`)
  and IAT Rise (`[IAT] - [Ambient Temp]`).
* **Raw:** the samples as a table.

## What the pulls and flags look for

Each one is a simple rule on what the log recorded, checked sample by sample. Spans less than 0.3 s apart are joined into one. Most flags
also have to last at least 0.3 s, so a single noisy sample doesn't count. A flag means "worth a look", not "something is wrong". The
channel names are the ones Simos.app and PhantomLog use; if a log doesn't have a channel, the flag that needs it is skipped.

| In the list | What it catches | The rule |
|---|---|---|
| **Pull** | A full-throttle run, the part worth judging a tune on | Pedal at 90 % or more for at least 1.5 s, or pedal at 75 % or more for at least 1 s while the engine climbs 1000 rpm or more (a tuned car makes full boost well before the floor) |
| **Launch** | A standing start | Under 3 km/h with the pedal at 90 % or more and the engine above 2000 rpm, then past 20 km/h within 5 s (a rev while parked doesn't count) |
| **Knock** | The ECU pulling timing on a cylinder because it heard knock | Any knock retard channel away from zero, per cylinder when the log has them. Small single events are common; repeated or large ones are the ones to look at |
| **Lean** | The engine running leaner than the ECU asked for | Measured lambda more than 0.05 above target, above 2000 rpm, pedal at 50 % or more. Readings above 1.3 are fuel cut on a lift or shift and are ignored |
| **Boost under** | The turbo not making the pressure the ECU asked for | Actual pressure more than 15 kPa under target, pedal at 80 % or more, above 3000 rpm (after spool). Uses PUT and PUT SP when the log has them, else MAP and MAP SP |
| **Fuel pressure** | The high- or low-pressure fuel pump falling behind | Pressure more than 10 % under its setpoint under load (pedal at 50 % or more), direct and port injection checked separately |
| **Misfire** | The ECU counting a misfire | The misfire counter going up |
| **Wheel slip** | The front tyres spinning | Front wheels more than 8 % faster than the rears, above 15 km/h, with the pedal at 50 % or more (a slow turn is not slip) |
| **Rich target** | The ECU asking for a very rich mixture, often to protect the engine from heat | Lambda target under 0.78 under load. Many tunes ask for about 0.75 at the top end on purpose, so check it against your tune |

Gear shifts are found too (for the AI connector): every change in the gear channel, with the rpm before and after.

## Install

**Linux (x86-64):** download `LogPlot-x86_64.AppImage` from the [Releases](../../releases) page, make it executable and run it:

```
chmod +x LogPlot-x86_64.AppImage
./LogPlot-x86_64.AppImage            # or: ./LogPlot-x86_64.AppImage mylog.csv
```

LogPlot runs on Linux (x86-64). macOS and Windows aren't supported yet.

The **Logs** tab lists the CSVs in your logs folder: choose it there (**Change**). The AI connector reads the same folder; set `LOGPLOT_DIR` to
override it.

## The AI connector (optional)

`LogPlot --mcp` runs an MCP server (stdio) with tools on your logs: list and summarise logs, channel statistics, rows of samples, pulls, flags,
heatmaps, the road dyno, and `show_in_viewer`, which opens the log in LogPlot at the moment it is talking about, with the channels that prove it,
so you can check every claim on the plot.

Claude Code:

```
claude mcp add logplot -- /path/to/LogPlot-x86_64.AppImage --mcp
```

Gemini CLI (`~/.gemini/settings.json`):

```json
{ "mcpServers": { "logplot": { "command": "/path/to/LogPlot-x86_64.AppImage", "args": ["--mcp"] } } }
```

## About the road dyno

It is an estimate, not a chassis dyno. The defaults are a Mk7 GTI with DSG (1540 kg with driver and fuel, 13 % drivetrain loss): set your car's
mass and loss in the Dyno view. Road slope is the biggest error (each 1 % of grade is about 7 whp at highway speed): use a flat road, pull in
3rd gear from about 3000 rpm to near the limiter, and compare pulls on the same stretch of road. Commercial dynos, Dynojets in particular, often
read higher than road-load physics. The flags are simple threshold rules meant to point you at moments worth a look, not a diagnosis.

## Disclaimers

* Volkswagen, Audi, Golf, GTI and other names used here belong to their owners. LogPlot is not affiliated with or endorsed by them.
* Simos.app and Simos Tools are separate products by their own developers. LogPlot reads their log files; it is not made, sponsored, endorsed or
  supported by their developers.
* Modifying a vehicle's emissions equipment or its emissions-related software can be illegal for road use where you live. LogPlot only shows
  what is in a log.
* Never use LogPlot (or anything else) while driving.

## Licence

LogPlot is free to use; all rights reserved (`LICENSE`). Third-party libraries and their licences: `THIRD_PARTY_LICENSES.md` and `LICENSES/`,
also inside the AppImage.
