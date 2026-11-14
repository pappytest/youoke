# Troubleshooting

Find your problem in the list, then follow the steps. Problems are grouped by where they happen.

**Using the app**
- [A song takes a long time to start, or keeps buffering](#a-song-takes-a-long-time-to-start-or-keeps-buffering)
- [The video quality is low](#the-video-quality-is-low)
- [Search fails: "The page needs to be reloaded"](#search-fails-the-page-needs-to-be-reloaded)
- [A song will not play](#a-song-will-not-play)
- [The scrolling title is stuck on screen](#the-scrolling-title-is-stuck-on-screen)
- [The screen went full screen and I cannot find the buttons](#the-screen-went-full-screen-and-i-cannot-find-the-buttons)

**Phone remote**
- [The phone remote will not connect](#the-phone-remote-will-not-connect)
- [The remote shows the wrong address](#the-remote-shows-the-wrong-address)

**Installing and updating**
- [Update failed with 404](#update-failed-with-404)
- [No update is offered](#no-update-is-offered)
- [The app name or icon is old](#the-app-name-or-icon-is-old)
- [Installation is blocked](#installation-is-blocked)

**Building (for developers)**
- [Gradle sync fails with a timeout](#gradle-sync-fails-with-a-timeout)
- [Red errors after pasting code](#red-errors-after-pasting-code)
- [Emulator: video cannot be decoded](#emulator-video-cannot-be-decoded)
- [Release build crashes but debug works](#release-build-crashes-but-debug-works)

---

## Using the app

### A song takes a long time to start, or keeps buffering

Part of the wait is the app looking the song up on YouTube, which takes a few seconds the first time. Songs in the queue are prepared in advance and start much faster.

1. Check the label next to the song title (see [the user guide](USER_GUIDE.md#8-video-quality)):
   - `[Auto 360p]` then `[Auto 720p]`: adaptive quality is working. Buffering then comes from the connection.
   - `[720p HD]` or `[360p]`: the adaptive method failed and a backup is being used. Tell the developer which label you see.
2. Test your connection: the TV or phone should be on strong Wi-Fi. A cable connection to the TV is best.
3. Reserve the next song early, so it is ready when the current one ends.
4. Developers: in `KaraokeScreen.kt`, lower `START_BITRATE_ESTIMATE` to start at a lower quality, or lower `MAX_VIDEO_HEIGHT`.

### The video quality is low

- It starts at about 360p on purpose and moves up within a few seconds if your connection is fast.
- The highest quality is 720p. Raising it to 1080p is possible in the code (`MAX_HEIGHT`, `TARGET_HEIGHT`, `MAX_VIDEO_HEIGHT`) but needs a very steady connection.
- If the label stays `[360p]`, YouTube may not have offered an HD version for that video, or the HD method failed and the app used a backup.

### Search fails: "The page needs to be reloaded"

This message comes from YouTube and means the version of the **NewPipeExtractor** library inside the app is too old for YouTube's current format.

**Fix (developer):**
1. Find the newest release number at `github.com/TeamNewPipe/NewPipeExtractor/releases`.
2. In `app/build.gradle.kts`, change the NewPipeExtractor line to that version, for example:
   `implementation("com.github.TeamNewPipe:NewPipeExtractor:v0.26.5")`
3. **Sync Now**, rebuild, publish a new version (see [Build and release](BUILD_AND_RELEASE.md)).

If many searches or videos suddenly stop working for everyone, this update is the first thing to try.

### A song will not play

- Wait several seconds; the first song needs time to be prepared.
- Press **Next**, or try another result for the same song.
- If an error shows under the video, read it. Some videos (age-restricted, region-blocked, removed or live) have no usable stream.
- The app tries several playback methods automatically. If it shows `Playback error ... (tried N streams)`, none worked for that video.
- If every song fails, update NewPipeExtractor (see above).

### The scrolling title is stuck on screen

This was a bug in version 1.1 (the ticker stayed on screen if you pressed Next while it scrolled). It is fixed in version 1.2. Update the app.

### The screen went full screen and I cannot find the buttons

Touch the screen, or press any button on the TV remote. The normal screen returns. On a TV, the first button press only wakes the screen.

---

## Phone remote

### The phone remote will not connect

Check these in order:

1. **Same Wi-Fi.** The phone and the TV must be on the same network. Mobile data, VPNs, and "smart network switch" features can bypass it. Guest networks and routers with **AP isolation** block devices from reaching each other (turn that off in the router settings).
2. **Type the address exactly:** `http://` (not `https://`), the numbers, then `:8080`. The QR code avoids typing mistakes.
3. **The app must be open on the TV.** The remote stops when the app is closed.
4. **Use the address that is on screen now.** The TV's address can change after a restart.
5. **Test with another device.** Open the same address on a computer on that Wi-Fi. If it also fails, the problem is the network or router.
6. **The TV on a cable and the phone on Wi-Fi:** some routers keep them on separate networks. Put both on Wi-Fi to test.
7. **Check the line under the logo.** If it says `Remote unavailable: ...`, the server could not start (for example the port is busy). Close and reopen the app.

### The remote shows the wrong address

The app picks the device's Wi-Fi or Ethernet address. If you see an address that does not start with `192.168.`, `10.` or `172.16.` to `172.31.`, the device may be on an unusual network. Check the address in the device's network settings and type it with `:8080`.

---

## Installing and updating

### Update failed with 404

The app found `version.json`, but the APK address inside it does not exist on GitHub.

1. Paste the `apkUrl` from `version.json` into a browser. If the APK does not download, the address is wrong.
2. Common mistakes:
   - **Tag mismatch:** the release tag is `1.2` but the address says `v1.2` (or the other way around).
   - **File name mismatch:** capital letters and dots must match the file in the release's **Assets** list.
   - **Draft release:** open it and click **Publish release**.
   - **Private repository:** make it public.
3. Easiest fix: in the release page, right-click the APK under **Assets** → **Copy link address**, and paste that into `apkUrl`.
4. Wait about 5 minutes after committing `version.json`, close the app, and try again.

Full steps: [Build and release](BUILD_AND_RELEASE.md#10-problems).

### No update is offered

- `versionCode` in `version.json` must be **higher** than the installed version's `versionCode`.
- GitHub can take about 5 minutes to show an edited file.
- The device needs internet access.
- The installed version must already contain the update checker.
- Open `https://raw.githubusercontent.com/pappytest/youoke/main/version.json` in a browser to confirm it shows what you expect.

### The app name or icon is old

1. Check that `app_name` in `strings.xml` is `YouOke`, and that `android:label="@string/app_name"` is in the manifest (on both `<application>` and, if present, the activity).
2. Clean and rebuild the project, build a new APK, **uninstall the old app first**, then install.
3. On a TV, restart the TV, since the launcher remembers old names and icons.

### Installation is blocked

| Message | Fix |
|---|---|
| "Your phone is not allowed to install unknown apps" | Allow your browser or file manager under **Settings → Apps → Special access → Install unknown apps** |
| Google Play Protect warning | **More details → Install anyway** |
| "App not installed" | Uninstall any older or test copy first. If updating, the APK must be signed with the same key |
| `INSTALL_FAILED_UPDATE_INCOMPATIBLE` (adb) | Different signing key. Uninstall the old app, then install |
| In-app update only opens a settings page | Allow YouOke to install apps, come back, tap **Update** again. On Google TV: **Settings → Apps → Security & restrictions → Unknown sources** |

---

## Building (for developers)

### Gradle sync fails with a timeout

`Could not install Gradle distribution ... Read timed out` is a network problem while downloading Gradle.

1. **File → Sync Project with Gradle Files** and retry.
2. Add `networkTimeout=120000` to `gradle/wrapper/gradle-wrapper.properties`.
3. Download the Gradle zip from the address in the error, unzip it, and in **Settings → Build Tools → Gradle** choose **Specified location**.
4. Try another network or turn off VPN, proxy, or strict antivirus.

### Red errors after pasting code

| Message | Cause and fix |
|---|---|
| `Unresolved reference 'X'` | A missing import. Click the word, press **Alt+Enter**, choose **Import**. Or a missing library: check `build.gradle.kts` and **Sync Now** |
| `Unresolved reference 'dash'` | The `media3-exoplayer-dash` dependency is missing. Add it and sync |
| `Platform declaration clash` | A property and a function share a name (such as `volume` and `setVolume`). Rename the function |
| `Conflicting import ... ambiguous` | The same import appears twice. Delete one |
| `This annotation is not repeatable` | A line like `@Composable` appears twice. Delete one |
| `This declaration is opt-in ... UnstableApi` | Add `@file:OptIn(UnstableApi::class)` at the very top of the file and `import androidx.media3.common.util.UnstableApi` |
| Errors mentioning `relatedItems`, `codec`, `getItagItem` | NewPipeExtractor renamed something. Check its release notes for the new names |
| Syntax errors at the end of a file | A missing or extra `}`. Replace the whole file instead of pasting pieces |

Tips: pasting a **whole file** is safer than pasting fragments. After pasting, use **Build → Rebuild Project**, and look in the **Build** panel for lines starting with `e:` which give the file, line number and reason.

### Emulator: video cannot be decoded

`Decoder failed: c2.goldfish.h264.decoder` means the emulator cannot decode video. It is not a bug in the app. Use a real phone or Google TV. If you must use the emulator: Device Manager → edit → Graphics **Software** or **Hardware**, cold boot, or create a new device with a recent x86_64 image that includes Google Play.

### Release build crashes but debug works

Code shrinking (R8) may be breaking the YouTube library. In `build.gradle.kts`, set `optimization { enable = false }` (or `isMinifyEnabled = false`) for the release build type, and rebuild.
