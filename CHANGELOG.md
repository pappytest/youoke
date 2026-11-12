# Changelog

All notable changes to YouOke are listed here, newest first. The format follows [Keep a Changelog](https://keepachangelog.com/), and version numbers go up with each release (`versionCode` in `build.gradle.kts` goes up by 1, `versionName` is the text such as `1.2`).

> The contents of 1.0 and 1.1 below were reconstructed from the development history. **Check them against what you actually shipped** and correct the entries and dates.

## [1.2] - Unreleased

### Added
- **Better TV remote (D-pad) support:**
  - An orange outline shows the selected button, card or queue item.
  - On a TV, the outline starts on the first suggested song, and moves to the first result after a search.
  - Pressing OK on the search box opens the on-screen keyboard.
  - Windows (song choices, update) start with the first button selected.
  - The **Back** button clears the search before it leaves the app.
  - **Queue menu:** select a queued song to play it now, move it to the front, or remove it.
  - **Media keys:** Play/Pause, Play, Pause, Next, and Fast-forward / Rewind (10 seconds). They also work in full screen without leaving it.
  - The video view no longer takes the remote's selection away from the buttons.

### Fixed
- The scrolling "up next" ticker no longer stays frozen at the top of the screen when you press **Next** or play another video while it is scrolling. It now hides as soon as a new song starts, and also when the queue runs out.

## [1.1]

### Added
- **Adaptive video quality:** playback starts at about 360p and moves between 144p and 720p automatically, depending on internet speed. Backup methods (fixed 720p, then 360p) are used if the adaptive one fails.
- **Quality label** next to the song title (for example `[Auto 720p]`) and a **loading spinner** over the video.
- **Scrolling "up next" ticker** along the top in full screen: the next 5 queued songs, first 20 characters of each title, 3 appearances per song, scrolling 2 times each.
- **10 search results** and **10 suggested videos** (with thumbnails), scrollable.
- **Suggestions from YouTube's related videos**, collected from songs you played, mixed with a built-in starting list of 32 songs.
- **Gap-free songs:** the next queued song is prepared while the current one plays. Reserve starts a song immediately if nothing is playing.
- **Phone remote:** the search box, Pause, Next and the now-playing bar stay pinned while scrolling; larger result cards with thumbnails.
- **Remote address fix:** the app now picks the Wi-Fi / Ethernet address for the QR code and on-screen address.

### Changed
- The QR code stays visible in full screen and is smaller.
- The "Up next" thumbnail card is smaller.

## [1.0]

### Added
- Search YouTube for `<song> karaoke` and play the result.
- **Play now / Reserve / 1st Reserve** choices, a queue, and **Next** and **Pause / Resume** buttons.
- Layouts for phone, tablet and Google TV (TV launcher banner included).
- **Auto full screen** after 10 seconds without activity.
- **Phone remote** over the local network (web page, QR code).
- Thumbnails on search results, a **×** button in the search box, and suggested songs when there are no results.
- YouOke name, logo as the default video background, and app icon.
- **Update check** at start-up using a `version.json` file on GitHub, with in-app download and install.

