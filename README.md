# Scrim Overlay

A browser-source overlay for OBS that displays live Apex Legends scrimmage scores from the `!now` StreamElements command.

## Setup

### 1. Configure the overlay

Open `index.html` and edit the two lines at the top of the `<script>` block:

```js
const CHANNEL   = 'verhulst';
const YOUR_TEAM = '100T';
```

### 2. Add to OBS as a Browser Source

1. In OBS, click **+** in the Sources panel → **Browser**
2. Check **Local file** and browse to `index.html`
3. Set **Width** to `485` and **Height** to `60` (matches webcam width, fixed ticker height)
4. Enable **Refresh browser when scene becomes active**
5. Under **Custom CSS**, paste:
   ```css
   body { background-color: rgba(0,0,0,0); margin: 0; overflow: hidden; }
   ```
6. Click **OK**
7. Position the browser source directly above the webcam source in the canvas

The overlay will show "Waiting for !now..." until someone types `!now` in chat and the StreamElements bot responds.

## How it works

- Connects to Twitch chat anonymously (read-only, no login needed)
- Listens for messages from the `streamelements` bot
- Parses messages matching the score format: `LOBBY --- TEAM: PTS, ... | Bans: ...`
- Automatically updates each time `!now` is triggered in chat
- Your team's row is highlighted in red

## Positioning in OBS

The browser source is 485×46 (same width as the webcam). Drag it into your scene and position it directly above the webcam feed. The transparent background means the red ticker bar will float cleanly over the game footage.
