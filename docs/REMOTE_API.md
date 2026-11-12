# Remote API

The TV app runs a small web server (NanoHTTPD) so a phone, tablet or computer on the same Wi-Fi can control it. The phone-remote page uses the addresses below, and you can call them yourself (for example from a browser, a script or another app).

- **Base address:** `http://<device-ip>:8080` (the exact address is shown on the app's start screen and in the QR code)
- **Protocol:** plain HTTP. All calls are `GET` requests. Responses are JSON unless noted.
- **Security:** none. There is no password. Only use it on a network you trust.
- **Works only while the app is open** on the device.

> The server code is in `RemoteServer.kt`.

---

## Pages and files

| Address | Returns |
|---|---|
| `/` | The phone-remote web page (HTML) |
| `/logo.png` | The round YouOke logo (PNG), used on the page and as its icon |

---

## Reading the state

### `GET /api/state`

Returns what the app is doing right now. The page asks for this every 1.5 seconds.

```json
{
  "searching": false,
  "searched": true,
  "suggested": false,
  "loading": false,
  "paused": false,
  "error": "",
  "now": "Unwell - Matchbox Twenty (Karaoke Version)",
  "results": [
    {
      "title": "Song title",
      "channel": "Channel name",
      "thumb": "https://i.ytimg.com/vi/.../hq720.jpg"
    }
  ],
  "queue": ["Queued song 1", "Queued song 2"]
}
```

| Field | Meaning |
|---|---|
| `searching` | A search is running |
| `searched` | A search has been made (and results were not cleared) |
| `suggested` | The `results` list is currently the **suggested videos**, not search results |
| `loading` | Suggestions are still being loaded |
| `paused` | Playback is paused |
| `error` | Error text to show, or an empty string |
| `now` | Title of the song that is playing, or empty |
| `results` | The list on screen: search results, or suggestions when `suggested` is true. Up to 10 items |
| `queue` | Titles of the queued songs, in play order |

The `i` number used by the commands below is the **position in `results`**, starting at 0.

---

## Commands

| Address | What it does |
|---|---|
| `/api/search?q=<text>` | Searches for `<text> karaoke`. URL-encode the text |
| `/api/clear` | Clears the results and loads fresh suggestions |
| `/api/play?i=<n>` | **Play now** for item `n` of `results` |
| `/api/reserve?i=<n>` | **Reserve** item `n` (end of the queue; starts it if nothing is playing) |
| `/api/first?i=<n>` | **1st Reserve** item `n` (front of the queue; starts it if nothing is playing) |
| `/api/pause` | Pauses, or resumes if already paused |
| `/api/next` | Stops the current song and plays the next one in the queue |
| `/api/remove?i=<n>` | Removes item `n` from the **queue** (position in `queue`, starting at 0) |

All commands return `{}`. An unknown address returns `404 Not found`.

Examples (replace the address with your own):

```
http://192.168.1.20:8080/api/search?q=perfect%20ed%20sheeran
http://192.168.1.20:8080/api/play?i=0
http://192.168.1.20:8080/api/next
```

---

## Notes

- Commands act on the **current** list. If the list changes between reading it and sending a command (for example a new search finishes), the index may point to a different song.
- Calls run on the app's main thread through a `Handler`, so they take effect within a moment.
- To add a new command: add a branch in the `when (session.uri)` block in `RemoteServer.kt`, add a function to `KaraokeViewModel.kt`, and (optionally) a button in the page's HTML.
