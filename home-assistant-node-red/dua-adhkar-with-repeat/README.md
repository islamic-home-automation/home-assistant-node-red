# Dynamic Audio Playlist System (Node-RED + Home Assistant)

This Node-RED flow dynamically plays a customizable playlist of audio files via Home Assistant's media player. It's designed for scenarios like adhkar, du'as, Quran recitations, or bedtime routines.

Each entry in the playlist specifies:
- A folder path
- A filename pattern (e.g. `Mulk`, `Nas`)
- A repeat count

The flow:
1. Finds matching audio files.
2. Picks one randomly.
3. Plays it via Home Assistant.
4. Waits for the exact duration using `ffprobe`.
5. Repeats if needed, then continues to the next entry.

---

## Features

✅ Dynamic file discovery using filename patterns  
✅ Randomized selection  
✅ Accurate delay via `ffprobe`  
✅ Repeat control per entry  
✅ Manual and Home Assistant-triggered start  
✅ Stops automatically when all entries are played

---

## Prerequisites

### Home Assistant
- Home Assistant set up and accessible in Node-RED
- Media player entity (e.g. `media_player.bedroom_1`) configured and reachable
- Optional: `input_button.night_adhkar` (for dashboard-triggered playback)

### Node-RED
- Node-RED installed with Home Assistant WebSocket integration
- `exec` node enabled (used for `find` and `ffprobe` commands)
- `ffprobe` must be installed and available in the system path (part of [FFmpeg](https://ffmpeg.org/download.html))

---

## Folder Structure Example

/media/Audio/Islamic/
├── Quran/
│ ├── Sura_Mulk.mp3
│ └── Sura_Nas.mp3
├── Dua or Part Quran/
│ ├── Ayatul Kursi.mp3


---

## How to Use

### 1. Configure the Playlist
Edit the `List to Repeat` function node to define your playlist:

```js
msg.playlist = [
  { folder: "/media/Audio/Islamic/Quran", pattern: "Mulk", repeat: 1, currentRepeat: 0 },
  { folder: "/media/Audio/Islamic/Quran", pattern: "Nas", repeat: 3, currentRepeat: 0 },
  { folder: "/media/Audio/Islamic/Dua or Part Quran", pattern: "Ayatul Kursi", repeat: 1, currentRepeat: 0 }
];
msg.currentIndex = 0;
return msg;
```

---

Each item in the array:

    folder: Absolute path to look in

    pattern: Partial filename match (case-insensitive)

    repeat: Number of times to repeat this item

### 2. Trigger the Flow

    Manually: Use the Manual Trigger inject node

    Home Assistant UI: Press the input_button.night_adhkar

Known Limitations

    If no matching files are found in a folder, the flow logs a warning and skips to the next item.

    Requires your media to be stored under /media and exposed as media-source://media_source/local/...

    Audio duration detection adds a ~3s buffer to ensure natural transition

Optional Improvements

    Add pause/resume with input_boolean

    Use input_text helpers to configure the playlist dynamically

    Show progress on a dashboard using ui_template or text nodes

License

MIT
