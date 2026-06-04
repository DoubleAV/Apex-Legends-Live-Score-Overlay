# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

A static HTML/JS OBS browser-source overlay that displays live Apex Legends scrimmage standings. No build system, no npm, no bundler — just two HTML files opened directly in OBS or a browser.

- [index.html](index.html) — the live overlay (ticker bar)
- [config.html](config.html) — settings UI that writes to `localStorage`

## Development

Open files directly in a browser. Append `?demo` to `index.html` to render sample data without connecting to Twitch (e.g. `file:///path/to/index.html?demo`).

There are no build steps, tests, or linters.

## Architecture

**Config flow:** `config.html` writes a JSON object to `localStorage` under key `scrim-config`. `index.html` reads it on load. After changing config, the OBS browser source must be refreshed manually to pick up new values.

**Data flow:** `index.html` connects anonymously to Twitch IRC via WebSocket (`wss://irc-ws.chat.twitch.tv:443`) using `justinfan<random>` credentials. It listens only for `PRIVMSG` from the `streamelements` bot. Valid score messages contain ` --- ` and match `\w+: \d+`.

**Score message format:**
```
LOBBY NAME --- TEAM: 57, TEAM: 47, ... | Bans: Legend, Legend, ...
```
Teams are already sorted by score descending; rank is derived from array index.

**Ticker scroll loop:** The content HTML is duplicated inside `#ticker-track`. The CSS animation translates `-50%`, so when the first copy scrolls out, the second copy (identical) is already in place — creating a seamless loop. Scroll duration is computed from `track.scrollWidth / 2 / SCROLL_PPS`.

**Change detection between updates:**
- `prevScores` (Map) tracks last known score per team — drives the animated counter (`animateCounter`)
- `prevRanks` (Map) tracks last known rank — drives the ▲/▼ delta badges
- If ≥50% of teams changed score and it's not the first render, `triggerStandingsFlash()` fires

**OBS sizing:** The overlay is designed for 485px wide × 80px tall. The `body` in OBS must have `background-color: rgba(0,0,0,0); margin: 0; overflow: hidden;` (paste into OBS Custom CSS).

**Config fields** (all read from `localStorage['scrim-config']`):
| Field | Default | Purpose |
|-------|---------|---------|
| `channel` | `verhulst` | Twitch channel to join |
| `yourTeam` | `100T` | Primary team name to highlight |
| `aliases` | `['CRT']` | Additional names for the same team |
| `scrollPps` | `85` | Ticker scroll speed in pixels/second |
