# Making skins

A skin is a folder with three files:

```text
my-skin/
  skin.json   name and window size (required)
  skin.html   markup (required)
  skin.css    styles
```

Skins are HTML and CSS only. They run in a sandboxed frame with no network
access and no scripts, and the only things they can do are the actions
below. A `skin.js` file is ignored.

Put it in your skins folder (tray → **Skins → Open skins folder**), then
choose **Skins → Reload skins**. The folder name is the skin's id: lowercase
letters, digits, `-` and `_`. A skin with the same id as a built-in one
replaces it.

Locations: `%APPDATA%\com.allcapsjoe.nowplaying\skins` on Windows,
`~/Library/Application Support/com.allcapsjoe.nowplaying/skins` on macOS.

## skin.json

```json
{
  "name": "My skin",
  "author": "You",
  "width": 420,
  "height": 140,
  "effect": "glass",
  "cornerRadius": 8
}
```

- `width` / `height`: window size in points.
- `effect`: `"glass"` asks the OS to blur what's behind the window. Leave it
  out for skins that paint their own background. Either way the window itself
  is transparent, so only what you draw shows, and rounded or odd-shaped skins
  work.

## Hooks

The app fills these in and keeps them current:

| Attribute | Effect |
|---|---|
| `data-np="title"` | Text: `title`, `artist`, `album`, `source` (e.g. Spotify), `status`, `position`, `duration`, `remaining`, and the time of day: `clock` ("19:00"), `hour`, `minute`, in the 12 or 24 hour format of the user's language and region (not the system's own 24-hour switch) |
| `data-np-art` | An `<img>` gets the album art as `src`; any other element gets it as `background-image` |
| `data-np-progress` | `width` is set to the playback percentage |
| `data-np-action="toggle"` | Click runs `previous`, `toggle`, `play`, `pause`, `next`, `hide`, `pin`, `open-skins` or `shortcuts` (shows the keyboard shortcuts). Buttons the player can't use are `disabled`. |
| `data-np-if="playing"` | Shown only when true: `playing`, `paused`, `session`, `empty`, `art`, `album`, `duration`, `pinned`, `mac`, `windows`. Prefix `!` to negate. |
| `data-np-nodrag` | Clicking here won't start a window drag (buttons already don't). |

Classes on `<html>`: `np-playing`, `np-paused`, `np-session`, `np-empty`,
`np-has-art`, `np-pinned`, `np-focused` (the widget has keyboard focus),
`np-mac`, `np-windows`. The CSS variable
`--np-progress` goes from 0 to 1. Pausing animations is usually one line:

```css
.reel { animation: spin 2s linear infinite; animation-play-state: paused; }
.np-playing .reel { animation-play-state: running; }
```

The keyboard shortcuts are the app's and are the same in every skin: `b`
previous, `p` or Space play/pause, `n` next, `k` keep visible, `q` or Esc
hide, `o` skins folder, arrows move the widget to a screen edge, `?` lists
them. Skins can't add or change keys, but can show them, as Glass does.

Fill the window with your root element (`position: fixed; inset: 0;`). The
page background is transparent.

## Fonts and images

Skins can't load anything from the network, so `@import` and remote
`url(...)` don't work. The one exception is Spotify's artwork server
(`https://i.scdn.co`), so `data-np-art` can show Spotify album art. Draw with inline SVG and CSS gradients, and embed any
image or font as a `data:` URL:

```css
@font-face { font-family: "My Font"; src: url(data:font/woff2;base64,...) format("woff2"); }
```

The fonts the built-in skins use are bundled with the app: Archivo Narrow,
JetBrains Mono, Orbitron, Permanent Marker, Share Tech Mono and Space Mono. Name one in
`font-family` and it's there. Album art works as usual through `data-np-art`.

Anything that would navigate the widget away is removed: `<meta>`
refreshes, and links other than in-page `#targets` (including `data:` links).
Image sources must be `data:image/...` URLs.
