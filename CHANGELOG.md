# NFL_Scoreboard changelog

## v1.0.0 (2026-09-28)
First release. Cloned from NAHL_Scoreboard v2.0.0 (same CYD hardware, architecture and touch UX), rebuilt for the NFL.

* **Setup portal**: hotspot "NFL-Scoreboard-Setup" with a QR code. The phone page has Wi-Fi scan + password, a picker for
  all 32 teams grouped by division, time zone (defaults to the team's home zone), Invert colors, a Brightness slider
  with live preview, and "Install updates automatically".
  Brightness and invert are set on the phone only; the on-screen Settings page doesn't change them.
* **NVS**: `nflsb` for Wi-Fi/team/tz/auto-update, `nflhw` for invert + brightness (kept after a factory reset),
  `nfldiag` for OTA diagnostics. None of them collide with the NAHL namespaces.
* **Data**: ESPN public site API: team schedule, league scoreboard, standings, news, and a single-event scoreboard for
  live games. Uses no custom User-Agent (ESPN returns 403 with one), streamed parsing through ArduinoJson filters,
  chunked reads with generous timeouts, and retries on truncated downloads.
* **Live mode**: when your team's game is in progress (including halftime), the screen locks on a live scoreboard:
  logos, score, quarter + clock ("HALF" in yellow), down & distance / ball-on, a possession marker, red-zone
  highlight, timeouts, team records and the last play. It polls every 15 s. Taps can browse other pages, and the
  scoreboard comes back after 30 s. The final stays up for 5 min.
  Game-start detection polls every 60 s from 30 min before kickoff (30 s after the listed kickoff, 120 s once the
  game is an hour late, and it gives up after 6 h).
* **Scoring alerts** (your team only): "TOUCHDOWN!" banner with 12 LED blinks in the team's two colours; smaller
  "FIELD GOAL" / "SAFETY" banner with 4 blinks. Scoring type comes from the score change (+6/+7/+8 = TD, +3 = FG,
  +2 on a play ESPN marks as a safety = safety; a lone +1/+2 conversion doesn't alert).
* **Pages** (auto-rotate every 10 s when no game is live, 24 h data refresh): Game (live board or next game with a
  countdown), Recent results (W green / L red / T), Division standings (W-L-T, PCT, DIV, STRK, DIFF), Season schedule
  (with BYE week and the next game highlighted), League scoreboard (all games this week, 8 per screen, scrolling),
  NFL News (your team's headlines first), and Settings.
* **Touch**: left half = back, right half = forward. Hold 2 s = test touchdown celebration. Hold 5 s on Settings = open setup.
  Hold the screen at power-on = factory reset.
* **OTA**: same checker and installer as NAHL v2.0.0 (SHA-256 verified, rollback-safe), releases come from
  github.com/49thMedia/nfl-scoreboard-firmware (`latest.json` + GitHub Releases). The manifest must say
  `"product":"nfl-scoreboard"`. If `latest.json` is missing (HTTP 404), Settings shows "No updates published".
