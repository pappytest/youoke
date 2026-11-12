<p align="center">
  <img src="docs/images/logo.png" width="160" alt="YouOke logo">
</p>

# YouOke

**YouTube karaoke for your TV, tablet and phone.** Search a song, line it up in the queue, and sing. Everything can be controlled from your phone with a built-in remote that works over your home Wi-Fi.

> **Unofficial app.** YouOke is not made by, or affiliated with, YouTube or Google. Read [Legal, license and privacy](docs/LEGAL.md) before sharing or publishing it.

---

## Features

**Finding songs**
- Type a song title and the app searches YouTube for `<your title> karaoke`.
- Shows up to **10 results** with large thumbnails (like YouTube on TV).
- When nothing has been searched, it shows **10 suggested karaoke videos**. After you play a few songs, the suggestions come from YouTube's own related videos.
- A **×** button in the search box clears the text and brings the suggestions back.

**Playing and queueing**
- Tap a result to choose **Play now**, **Reserve** (end of the queue) or **1st Reserve** (next in line).
- **Next** and **Pause / Resume** buttons.
- The next queued song is prepared in the background, so there is little or no gap between songs.
- **Adaptive quality:** starts at about 360p and moves up to 720p, or back down, depending on your internet speed.

**Full-screen mode**
- After **10 seconds** with no touch or remote button press, the video goes full screen.
- A **QR code** for the phone remote stays in the top-right corner.
- A **scrolling "up next" ticker** along the top shows the next 5 queued songs (first 20 characters of each title).
- An **"Up next" thumbnail card** appears in the lower-right corner during the last 20 seconds of a song.
- Any touch or button press brings the normal screen back.

**Phone remote (no app to install)**
- The TV app runs a small web page on your Wi-Fi. Scan the QR code, or type the address shown on screen.
- Search, clear, play now, reserve, 1st reserve, pause, next, and remove songs from the queue.
- The search box, Pause, Next and the now-playing bar stay pinned while you scroll.

**Other**
- Works on **Android phones, tablets and Google TV** (built-in launcher banner for TV).
- Shows your logo as the default "video" when nothing is playing.
- **Checks for updates** when the app opens and installs the new version if you agree.

> Tested on an Android phone and a Google TV. The tablet layout is built in but has had less testing.

---

## Quick start

1. Go to the [**Releases**](https://github.com/pappytest/youoke/releases) page and download the newest `YouOke-x.y.apk`.
2. Install it on your phone or Google TV. Step-by-step instructions: [docs/INSTALL.md](docs/INSTALL.md).
3. Open **YouOke**, search for a song, and tap it.
4. To use your phone as a remote, scan the QR code on the TV screen.

Full instructions for using the app: [docs/USER_GUIDE.md](docs/USER_GUIDE.md).

---

## Documentation

| Document | What it covers |
|---|---|
| [User guide](docs/USER_GUIDE.md) | Using the app, the full-screen mode and the phone remote |
| [Installation](docs/INSTALL.md) | Installing on a phone or Google TV, and updating |
| [Troubleshooting](docs/TROUBLESHOOTING.md) | Fixes for the problems you are most likely to meet |
| [Developer guide](docs/DEVELOPER_GUIDE.md) | How the app is built, file by file, and which settings to change |
| [Remote API](docs/REMOTE_API.md) | The web addresses the phone remote uses |
| [Build and release](docs/BUILD_AND_RELEASE.md) | Making a signed APK and publishing an update on GitHub |
| [Legal, license and privacy](docs/LEGAL.md) | YouTube terms, open-source licenses, what data the app touches |
| [Changelog](CHANGELOG.md) | What changed in each version |

---

## How it works (in short)

- **Kotlin + Jetpack Compose** for the screens.
- **[NewPipeExtractor](https://github.com/TeamNewPipe/NewPipeExtractor)** finds videos and their stream addresses (no YouTube account or API key needed).
- **Media3 / ExoPlayer** plays the video, using an adaptive (DASH) playlist built by the app.
- **NanoHTTPD** runs the phone-remote web page inside the TV app.
- A small `version.json` file on GitHub tells the app when a newer version exists.

More detail: [docs/DEVELOPER_GUIDE.md](docs/DEVELOPER_GUIDE.md).

---

## Known limitations

- **Unofficial YouTube access.** The app gets videos in a way that YouTube's terms do not allow, and it can stop working when YouTube changes something. Fixing that usually means updating the NewPipeExtractor library. See [LEGAL](docs/LEGAL.md) and [TROUBLESHOOTING](docs/TROUBLESHOOTING.md).
- **Not suitable for the Google Play Store** in its current form.
- **The phone remote has no password.** Anyone on the same Wi-Fi who knows the address can use it.
- **Pitch / key change is not included** (it was discussed but never built).
- **Some songs may not play** if YouTube returns no usable stream for that video.
- **The Android emulator cannot play the video** on many computers. Use a real phone or TV.

---

## Project status

Personal project, actively changed. Version history is in the [changelog](CHANGELOG.md).

## Credits

- [NewPipeExtractor](https://github.com/TeamNewPipe/NewPipeExtractor) by Team NewPipe
- [AndroidX Media3 / ExoPlayer](https://github.com/androidx/media), [OkHttp](https://square.github.io/okhttp/), [Coil](https://coil-kt.github.io/coil/), [ZXing](https://github.com/zxing/zxing) and [NanoHTTPD](https://github.com/NanoHttpd/nanohttpd)
- Karaoke videos belong to their uploaders and are played from YouTube.

## License

See [docs/LEGAL.md](docs/LEGAL.md). Because the app includes NewPipeExtractor (GPL-3.0), the recommended license for this project is **GPL-3.0-or-later**.
