# Developer guide

How YouOke is built, where things live, and which settings you can change. Written for someone who is new to Android.

**Contents**
1. [Overview](#1-overview)
2. [Setting up the project](#2-setting-up-the-project)
3. [Dependencies](#3-dependencies)
4. [Project layout](#4-project-layout)
5. [How a song gets from search to sound](#5-how-a-song-gets-from-search-to-sound)
6. [Playback in detail](#6-playback-in-detail)
7. [Suggestions](#7-suggestions)
8. [Full-screen mode, ticker and Up next card](#8-full-screen-mode-ticker-and-up-next-card)
9. [The phone remote](#9-the-phone-remote)
10. [The update checker](#10-the-update-checker)
11. [Settings you can change](#11-settings-you-can-change)
12. [Gotchas and lessons learned](#12-gotchas-and-lessons-learned)
13. [Ideas for later](#13-ideas-for-later)

---

## 1. Overview

| Item | Value |
|---|---|
| App name | YouOke |
| Package / namespace | `com.example.karaokeapp` |
| Project folder name | `KaraokeApp` |
| Language | Kotlin |
| UI | Jetpack Compose with Material 3 |
| Minimum Android | 7.0 (API 24) |
| Targets | Phone, tablet, Google TV / Android TV |
| Architecture | One `Activity`, one `ViewModel` that holds all state, Compose screens that read it |

The app name shown to users is **YouOke** (`app_name` in `strings.xml`). The code still uses the original package name `com.example.karaokeapp`. This is fine and avoids risky renames. Changing the package name later would also break updates for installed copies.

---

## 2. Setting up the project

1. Install **Android Studio** (the current stable version).
2. Open the project folder (`KaraokeApp`) with **File → Open**.
3. Let the first **Gradle sync** finish. It can take 5 to 10 minutes and downloads a lot.
4. If Gradle's own download times out, see [Troubleshooting](TROUBLESHOOTING.md#gradle-sync-fails-with-a-timeout).
5. Connect a **real Android phone** with USB debugging on, or a Google TV through adb, and press **Run**.

> The Android emulator often cannot decode video (`c2.goldfish.h264.decoder` errors). Test playback on a real device.

---

## 3. Dependencies

Declared in `app/build.gradle.kts` (Module :app):

| Library | Used for |
|---|---|
| `com.github.TeamNewPipe:NewPipeExtractor` (JitPack, e.g. `v0.26.5`) | Searching YouTube and reading video stream data |
| `androidx.media3:media3-exoplayer`, `media3-ui`, `media3-exoplayer-dash` (`1.4.1`) | Video playback, player view, adaptive (DASH) playback |
| `com.squareup.okhttp3:okhttp` (`4.12.0`) | Web requests for NewPipe and the update checker |
| `io.coil-kt:coil-compose` (`2.7.0`) | Loading thumbnails |
| `androidx.lifecycle:lifecycle-viewmodel-compose` | `ViewModel` in Compose |
| `org.nanohttpd:nanohttpd` (`2.3.1`) | The phone-remote web server |
| `com.google.zxing:core` (`3.5.3`) | Drawing the QR code |
| `com.android.tools:desugar_jdk_libs` | Java time APIs on older Android (`coreLibraryDesugaring`) |

Also required in `build.gradle.kts`:
- `isCoreLibraryDesugaringEnabled = true` inside `compileOptions`.
- JitPack in `settings.gradle.kts`: `maven("https://jitpack.io")`.
- Release builds should keep code shrinking **off** (see [gotchas](#12-gotchas-and-lessons-learned)).

---

## 4. Project layout

All Kotlin files are in `app/src/main/java/com/example/karaokeapp/`.

| File | What it does |
|---|---|
| `KaraokeApp.kt` | `Application` class. Starts NewPipe once with `NewPipe.init(DownloaderImpl())` |
| `DownloaderImpl.kt` | Lets NewPipe make web requests through OkHttp |
| `MainActivity.kt` | Starts the remote web server, keeps the screen on, tells the app about touches and key presses (for the 10-second full-screen timer), shows `UpdatePrompt()` |
| `KaraokeViewModel.kt` | **All app state and logic:** search, results, suggestions, queue, stream lookup and caching, prefetching, pause/next, full-screen flag, ticker trigger |
| `KaraokeScreen.kt` | **All screens and the player:** layouts for phone and TV, search panel, result cards, queue, `VideoSurface` (logo, QR, spinner, ticker, up-next card), and the ExoPlayer setup |
| `DashManifest.kt` | Builds the adaptive-playback playlist (DASH manifest) from YouTube streams |
| `RemoteServer.kt` | The web server (NanoHTTPD) and the phone-remote web page, plus `localIpAddress()` |
| `Updater.kt` | Checks `version.json`, downloads the APK, starts the install; contains `UpdatePrompt()` |

Resources (`app/src/main/res/`):

| Path | What it is |
|---|---|
| `drawable-nodpi/youoke_logo.jpg` | Logo shown when nothing is playing (the `nodpi` folder stops Android from enlarging it) |
| `raw/youoke_emblem.png` | Round logo, served to the phone remote page |
| `drawable-xhdpi/tv_banner.png` | 320x180 banner for the Google TV launcher |
| `mipmap-*/ic_launcher*` | App icons (made with Android Studio's Image Asset tool) |
| `xml/file_paths.xml` | Tells `FileProvider` where downloaded updates are stored |
| `values/strings.xml` | `app_name` = `YouOke` |

`AndroidManifest.xml` highlights:
- Permissions: `INTERNET`, `REQUEST_INSTALL_PACKAGES`.
- `uses-feature` for touchscreen and leanback, both **not required**, so the app installs on TVs.
- The activity has both `LAUNCHER` and `LEANBACK_LAUNCHER` intent categories.
- `android:banner="@drawable/tv_banner"`.
- `android:configChanges` on the activity, so rotating does not restart the app.
- A `FileProvider` (`${applicationId}.fileprovider`) for installing downloaded updates.

---

## 5. How a song gets from search to sound

```
 user types "unwell"
        │
        ▼
 vm.search("unwell")  ──►  NewPipe searches YouTube for "unwell karaoke"
        │                    (top 10 videos → results list with thumbnails)
        ▼
 user taps a result  ──►  Play now / Reserve / 1st Reserve
        │
        ▼
 vm.startSong(song)
   ├─ cached stream info?  yes ─► use it right away (no wait)
   └─ no ─► resolveStreams(): NewPipe reads the video's streams
              builds a list of "candidates" (ways to play it)
        │
        ▼
 vm.nowPlayingStream = first candidate
        │
        ▼
 KaraokeScreen (rememberKaraokePlayer) sees the change
   ─► builds an ExoPlayer media source ─► prepare() ─► plays
        │
        ▼
 song ends ─► vm.onSongEnded() ─► next song in the queue (already prepared)
```

State flows one way: the ViewModel holds `mutableStateOf` values, Compose reads them, and button taps call ViewModel functions.

---

## 6. Playback in detail

### Candidates (ways to play a song)

`resolveStreams()` in `KaraokeViewModel.kt` builds a list, in this order:

| # | Candidate | Label | How it plays |
|---|---|---|---|
| 1 | **Adaptive** | `Auto` (shown as `Auto 720p` etc.) | A DASH manifest with several H.264 qualities (144p up to 720p) and one AAC audio track. ExoPlayer picks the quality by internet speed |
| 2 | **HD merged** | `720p HD` | One 720p video-only stream + the audio stream, merged with `MergingMediaSource` |
| 3 | **Plain streams** | `360p` etc. | Streams that already contain picture and sound |
| 4 | **HLS** | `auto` | Only if YouTube provides an HLS address |

If one candidate fails (`onPlayerError`), `onPlaybackFailed()` moves to the next one. If all fail, an error message shows under the video.

### Adaptive playback

- `DashManifest.kt` writes a manifest in memory. It uses each stream's byte ranges (`indexRange` / `initRange`) taken from NewPipe's data.
- `KaraokeScreen.kt` parses it with `DashManifestParser` and gives it to `DashMediaSource`. No file or server is needed.
- The player is configured in `rememberKaraokePlayer()`:
  - `DefaultBandwidthMeter` with an initial estimate of **1.2 Mbps**, so playback starts around 360p.
  - `AdaptiveTrackSelection` that is willing to go up after about 4 seconds of buffer.
  - `setMaxVideoSize(1280, 720)` so it never goes above 720p.
  - `DefaultLoadControl`: starts playing after 1 s of buffered video, keeps 15 to 50 s ahead.

### Caching and prefetching

- Stream info is kept in memory for **2 hours** (`CACHE_MS`), because the addresses expire.
- When a song starts, or the queue changes, `prefetchNext()` looks up the first queued song in the background. When it is that song's turn, it starts instantly.
- Prefetching a song also collects its **related videos** (see suggestions).

### Quality label and spinner

- `videoHeight` is updated by the player when the picture size changes. `qualityLabel` turns it into text like `Auto 720p`.
- `buffering` is true while the player waits for data. The spinner shows while `nowPlayingStream == null` (still preparing) or `buffering`.

---

## 7. Suggestions

Shown when there are no search results (`vm.shown`).

- `SUGGESTED_SONGS` (top of `KaraokeViewModel.kt`) is the starting list of song names.
- `loadSuggestions()`:
  1. If the **related pool** has at least 10 videos, pick 10 random ones from it.
  2. Otherwise look up random names from `SUGGESTED_SONGS` (top video for `"<name> karaoke"`) and fill up to 10.
- The related pool (`relatedPool`) holds up to 80 videos from YouTube's related list for songs that were played. It keeps only titles that contain "karaoke" and are **1 to 15 minutes** long. It is not saved, so it resets when the app closes.
- `clearResults()` (the **×** button) calls `loadSuggestions()` again for a fresh set.

---

## 8. Full-screen mode, ticker and Up next card

All in `KaraokeScreen.kt` and `KaraokeViewModel.kt`.

**Idle timer**
- `MainActivity` calls `vm.userActivity()` on every touch (down/up) and key press. That stores the time and leaves full screen.
- In `KaraokeScreen`, while a song is playing, a loop checks every 0.5 s. If the last activity was more than `IDLE_MS` (10 s) ago, it calls `vm.enterImmersive()`.
- In full screen the system bars are hidden with `WindowInsetsControllerCompat`.
- On TV, the first key press in full screen is swallowed (`dispatchKeyEvent`), so it only wakes the screen. Volume keys pass through.

**Ticker (scrolling list on top)**
- The player loop (every 0.5 s) calls `vm.checkTicker(position, duration, TICKER_RUNS_PER_SONG)`.
- With 3 runs, the ticker is triggered at 1/4, 2/4 and 3/4 of the song. `tickerRun` counts how many were triggered.
- `UpNextTicker` reacts to `tickerRun`: it builds the text from the queued songs (up to `TICKER_ITEMS`, 20 by default) (first 20 characters of each title, `…` added if cut), measures it, and animates it from the right edge to beyond the left edge, repeating `TICKER_SCROLLS_PER_RUN` times.
- When a new song starts (`tickerRun` resets to 0), the ticker hides immediately.
- It only exists in full screen and only shows if the queue is not empty.

**Up next card**
- `nearEnd` becomes true in the last `UP_NEXT_MS` (20 s) of a song. In full screen, the first queued song's thumbnail and title show in the lower-right corner.

**QR code**
- `QrCode()` draws a QR code (ZXing `QRCodeWriter`) of the remote's address on a `Canvas`. It stays visible in full screen and sits below the ticker.

**TV remote (D-pad) support**
- `Modifier.dpadFocus(shape, color)` (in `KaraokeScreen.kt`) draws an outline when an item or one of its children has focus. Cards use the orange default. `Modifier.buttonFocus()` is the same with a **white** ring and a round shape, for the orange buttons (an orange ring would be invisible on an orange button). Use one of them on every button or card that should be reachable with the remote.
- `rememberIsTv()` detects a TV. Initial focus is only forced on a TV, so phones do not pop up a keyboard.
- `FocusRequester.requestFocusLater()` waits 150 ms and then asks for focus, because the target must be on screen first. It is used for the first suggestion / result card and for the first button of each dialog.
- The search box opens the keyboard on OK with `onPreviewKeyEvent` and `LocalSoftwareKeyboardController`.
- `BackHandler` in `SearchArea` clears the search before Back leaves the app.
- The video view (`PlayerView`) has its controller and focus switched off, so it cannot take the D-pad selection from the buttons.
- `MainActivity.dispatchKeyEvent` handles media keys first (`handleMediaKey`), then the full-screen wake logic. Seeking goes through `vm.seekBy()`, which the player reacts to in `rememberKaraokePlayer`.
- Queue items are focusable cards. Selecting one expands it in place (`QueueItemActions`) with Play now / To front / Remove (`vm.playFromQueue`, `vm.moveToFront`, `vm.removeFromQueue`).
- Open card choices are tracked **by position** (`selectedIndex`), not by song address, so the same video appearing twice in the queue can never open two rows at once. When the choices close, the row asks for focus again, so the TV selection stays on the card instead of jumping to the search box.
- Song cards expand in place too: `SongRow` shows `SongActionsOverlay` (Play now / Reserve / 1st Reserve) on top of the card, so the video stays visible. The card's own `clickable` is disabled while the overlay is open.
- **Full-screen countdown while typing:** `SearchArea` reports `markSearchBusy` while the search box has focus or a card menu is open, and `QueueArea` reports `markQueueBusy`. While either is true, the countdown is `IDLE_HOLD_MS` (30 s) instead of `IDLE_MS` (10 s), and every typed character calls `vm.userActivity()`. This is needed because the TV's on-screen keyboard does not send key events to `MainActivity`.

---

## 9. The phone remote

- `MainActivity` starts `RemoteServer(vm, applicationContext)` on port **8080**.
- `localIpAddress()` picks the device's Wi-Fi (or Ethernet) IPv4 address.
- The server serves one HTML page (`REMOTE_PAGE`, a string inside `RemoteServer.kt`) and a small JSON API. The page asks `/api/state` every 1.5 s and redraws.
- Buttons call the API (`/api/play?i=3` and so on). The server forwards them to the ViewModel using a `Handler` on the main thread.
- Everything is plain HTTP on the local network, with no password.

The list of addresses is in [REMOTE_API.md](REMOTE_API.md).

**Editing the remote page:** it is one big Kotlin raw string (`"""..."""`). Do **not** use a dollar sign (`$`) in the HTML or JavaScript, because Kotlin would treat it as a variable.

---

## 10. The update checker

`Updater.kt`:

1. `UpdatePrompt()` runs once when the app starts.
2. `Updater.check()` downloads `UPDATE_URL` (the `version.json` on GitHub) and compares its `versionCode` with the installed `versionCode`.
3. If it is newer, a dialog shows the version name and notes.
4. **Update** → `Updater.download()` saves the APK to `cacheDir/updates/` with a progress bar → `Updater.install()` opens Android's installer through `FileProvider`.
5. If the app is not yet allowed to install apps, it opens that setting (`ACTION_MANAGE_UNKNOWN_APP_SOURCES`) and asks the user to come back.

`version.json` format:

```json
{
  "versionCode": 2,
  "versionName": "1.1",
  "apkUrl": "https://github.com/pappytest/youoke/releases/download/1.1/YouOke-1.1.apk",
  "notes": "What's new."
}
```

Details: [BUILD_AND_RELEASE.md](BUILD_AND_RELEASE.md).

---

## 11. Settings you can change

**`KaraokeScreen.kt` (top of file)**

| Constant | Default | Meaning |
|---|---|---|
| `PLAYER_WEIGHT` / `SEARCH_WEIGHT` | `3f` / `1f` | Width split on TV: player 3 parts, search panel 1 part |
| `IDLE_MS` | `10_000` | Milliseconds of no activity before full screen |
| `UP_NEXT_MS` | `20_000` | How long before the end the Up next card appears |
| `TICKER_RUNS_PER_SONG` | `3` | How many times the ticker appears per song |
| `TICKER_SCROLLS_PER_RUN` | `2` | How many times it scrolls across each time |
| `TICKER_ITEMS` | `20` | Songs listed in the ticker (all queued songs, up to this many) |
| `TICKER_MAX_PASS_SECONDS` | `40f` | A long ticker speeds up so one pass is never longer than this |
| `IDLE_HOLD_MS` | `30_000` | Full-screen countdown while the user is typing or choosing |
| `TICKER_CHARS` | `20` | Characters of each title kept |
| `TICKER_SPEED_DP_PER_SEC` | `110f` | Ticker speed (smaller is slower) |
| `MAX_VIDEO_WIDTH` / `MAX_VIDEO_HEIGHT` | `1280` / `720` | Highest picture size for adaptive playback |
| `START_BITRATE_ESTIMATE` | `1_200_000` | Assumed starting speed (lower starts at lower quality) |
| `QrSizeWide` / `QrSizePhone` | `90.dp` / `64.dp` | QR code size |
| `UpNextWidthWide` / `UpNextWidthPhone` | `150.dp` / `100.dp` | Up next card width |

**`KaraokeViewModel.kt` (top of file)**

| Constant | Default | Meaning |
|---|---|---|
| `SUGGESTED_SONGS` | 32 names | Starting song names for suggestions. Add more for variety |
| `SEARCH_RESULTS` | `10` | Results shown for a search |
| `SUGGESTION_COUNT` | `10` | Suggested videos shown |
| `CACHE_MS` | 2 hours | How long prepared stream info is reused |
| `ADAPTIVE` | `true` | Try adaptive playback first |
| `MAX_HEIGHT` | `720` | Highest quality in the adaptive playlist |
| `PREFER_HD` | `true` | Try the fixed 720p merged stream as the second choice |
| `TARGET_HEIGHT` | `720` | Target height for that merged stream |

**`Updater.kt`**

| Constant | Meaning |
|---|---|
| `UPDATE_URL` | Address of `version.json` |

---

## 12. Gotchas and lessons learned

- **Property and function names can clash.** A Kotlin `var volume` creates a hidden `setVolume()`. Writing your own `setVolume()` gives "Platform declaration clash". This is why functions are named `changeX` or `updateX` when a property of that name exists.
- **NewPipeExtractor needs updating from time to time.** YouTube changes often. Old versions give errors such as "The page needs to be reloaded". Fix by updating the version number in `build.gradle.kts` to the newest release (see [Troubleshooting](TROUBLESHOOTING.md#search-fails-the-page-needs-to-be-reloaded)).
- **NewPipe field names.** The code uses `info.relatedItems`, `it.codec`, `deliveryMethod`, `initStart/initEnd/indexStart/indexEnd`. If a NewPipe update renames them, the build shows red lines in `KaraokeViewModel.kt` and `DashManifest.kt`.
- **Media3 "unstable API" warnings.** Many Media3 classes require an opt-in. `KaraokeScreen.kt` has `@file:OptIn(UnstableApi::class)` at the top for that reason.
- **Keep code shrinking off for release builds.** The optimizer can break the YouTube library. Use `optimization { enable = false }` (or `isMinifyEnabled = false`).
- **Do not put `$` in the remote page HTML/JS.** See [section 9](#9-the-phone-remote).
- **Emulators cannot decode the video.** Test on a real device.
- **Large images belong in `drawable-nodpi`.** Otherwise Android upsizes them and can run out of memory.
- **Same signing key for every release.** Otherwise installed copies cannot be updated.
- **Keep the same tag style in GitHub releases** (this project uses `1.1`, no `v`), and match it in `apkUrl`.

---

## 13. Ideas for later

- **Key change (pitch shift):** possible with ExoPlayer's playback parameters in the current setup; not possible with YouTube's official embedded player.
- **Singer names on queued songs**, shown in the queue and ticker.
- **PIN for the phone remote** so only people who know it can control the app.
- **Remember the suggestion pool** and the queue between app launches.
- **Favorites and history.**
- **Auto-skip a song if it fails to play.**
- **1080p option** (`TARGET_HEIGHT` and `MAX_HEIGHT`), with extra care about buffering.
