# Build and release

How to make a signed APK and publish an update that existing installs offer to download.

**Contents**
1. [How updates work](#1-how-updates-work)
2. [Before you start (one-time setup)](#2-before-you-start-one-time-setup)
3. [Release checklist](#3-release-checklist)
4. [Step 1: Set the new version](#4-step-1-set-the-new-version)
5. [Step 2: Build the signed APK](#5-step-2-build-the-signed-apk)
6. [Step 3: Publish the release on GitHub](#6-step-3-publish-the-release-on-github)
7. [Step 4: Update version.json](#7-step-4-update-versionjson)
8. [Step 5: Test the update](#8-step-5-test-the-update)
9. [Rules to remember](#9-rules-to-remember)
10. [Problems](#10-problems)

---

## 1. How updates work

```
 Your phone / TV                                GitHub (repository pappytest/youoke)
 ───────────────                                ─────────────────────────────────────
 YouOke starts
   │  reads  ─────────────────────────────────►  version.json   (the "latest version" note)
   │  compares versionCode with its own
   │  newer? shows "Update available"
   │  user taps Update
   │  downloads ──────────────────────────────►  Releases → YouOke-x.y.apk
   ▼
 Android installs the new version over the old one
```

Two things on GitHub must always agree:
1. the **release** that holds the APK file, and
2. the **`version.json`** file that points to it.

Upload the APK first, edit `version.json` last. As soon as `version.json` says a newer version exists, every running copy of the app will offer it.

---

## 2. Before you start (one-time setup)

- A GitHub account and a **public** repository named `youoke`.
- A `version.json` file in the repository root (branch `main`).
- A **signing key** (`.jks` file). Created the first time you build, see [Step 2](#5-step-2-build-the-signed-apk).
- In `Updater.kt`, `UPDATE_URL` must be:

```
https://raw.githubusercontent.com/pappytest/youoke/main/version.json
```

- The version of the app that is installed on a device **must already contain the update checker**. Older versions without it cannot see updates. They need one manual install first.

---

## 3. Release checklist

Copy this list for each release.

- [ ] Code finished and tested on a real phone and a TV
- [ ] `versionCode` raised by 1, `versionName` updated
- [ ] Signed release APK built with the **same key** as before
- [ ] APK renamed to `YouOke-<versionName>.apk`
- [ ] GitHub release created with the tag `<versionName>` and the APK attached, and **published** (not draft)
- [ ] The download link works in a browser
- [ ] `version.json` updated and committed
- [ ] `CHANGELOG.md` updated
- [ ] Update tested from the older version
- [ ] Keystore backup still safe

---

## 4. Step 1: Set the new version

Open `app/build.gradle.kts` (the **Module :app** one) and edit `defaultConfig`:

```kotlin
defaultConfig {
    ...
    versionCode = 3        // whole number, raise by 1 every release
    versionName = "1.2"    // what people see
}
```

| Field | Rule |
|---|---|
| `versionCode` | A whole number. **Must be higher** than the previous release. The update check compares only this |
| `versionName` | Text for people, such as `1.2`. Used in the file name, tag and notes |

Then press **Sync Now**.

Also check that code shrinking is off for release builds:

```kotlin
buildTypes {
    release {
        optimization {
            enable = false
        }
    }
}
```

(Older style: `isMinifyEnabled = false`.)

---

## 5. Step 2: Build the signed APK

1. **Build → Generate Signed App Bundle / APK...**
2. Choose **APK** → **Next**.
3. **Key store path:**
   - **First time:** click **Create new...** and choose a safe location, for example `C:\Users\<you>\keystores\youoke.jks`. Set a password, set **Alias** to `youoke`, keep validity at 25 years, and fill in your name. Click **OK**.
   - **Later releases:** click **Choose existing...**, pick the same `.jks` file, and enter the same passwords and alias.
4. **Next** → choose the **release** build variant → tick both signature versions (**V1** and **V2**) → **Create**.
5. When it finishes, use the **locate** link in the notification. The file is `app-release.apk` (usually in `app/release/`).
6. **Rename it** to `YouOke-<versionName>.apk`, for example `YouOke-1.2.apk`.

> ### Protect your signing key
> - **Back up the `.jks` file and its passwords** in at least two safe places (not only on one computer).
> - **Never upload it to GitHub** or share it.
> - **Android only updates an app if the new APK is signed with the same key.** If the key is lost, nobody can update: everyone must uninstall the app and install the new one fresh.

---

## 6. Step 3: Publish the release on GitHub

1. Open the repository: `github.com/pappytest/youoke`.
2. Click **Releases** on the right → **Draft a new release**.
3. **Choose a tag** → type the version (for example `1.2`) → **Create new tag: 1.2 on publish**.
   Use the same tag style every time. This project uses the plain number **without a `v`**.
4. **Release title:** `YouOke 1.2`. Add a short description of what changed.
5. Drag `YouOke-1.2.apk` into the box **Attach binaries by dropping them here**. Wait for the upload bar to finish.
6. Click **Publish release** (not "Save draft").

The download address will be:

```
https://github.com/pappytest/youoke/releases/download/1.2/YouOke-1.2.apk
```

Paste it in a browser. The APK should start downloading. If you see "404", the tag or file name does not match what you typed, or the release is still a draft.

---

## 7. Step 4: Update version.json

1. In the repository, click `version.json` → **pencil icon** (Edit).
2. Replace the content:

```json
{
  "versionCode": 3,
  "versionName": "1.2",
  "apkUrl": "https://github.com/pappytest/youoke/releases/download/1.2/YouOke-1.2.apk",
  "notes": "Fixed the scrolling title that stayed on screen."
}
```

| Field | Meaning |
|---|---|
| `versionCode` | Same number you put in `build.gradle.kts` |
| `versionName` | Shown in the update window |
| `apkUrl` | Exact download address from step 3 (copy it from the release's **Assets** list) |
| `notes` | Text shown in the update window |

3. Use plain straight quotes `"`, no trailing comma after the last line, and no extra spaces inside the address.
4. **Commit changes**.

GitHub's "raw" files can be cached for about 5 minutes, so the update may appear a few minutes later.

Check it by opening:
`https://raw.githubusercontent.com/pappytest/youoke/main/version.json`

---

## 8. Step 5: Test the update

1. Keep a device with the **older** version installed.
2. Fully close the app, wait a few minutes after committing `version.json`, then open it.
3. **Update available** should appear. Tap **Update**.
4. If Android asks, allow YouOke to install apps, then tap **Update** again.
5. Confirm the install. After the restart, open **Settings → Apps → YouOke** and check the version name.

---

## 9. Rules to remember

1. **Same key always.** Every APK must be signed with the same `.jks` file.
2. **`versionCode` only goes up.** A lower or equal number is never offered.
3. **Upload the APK first, edit `version.json` last.**
4. **Tag, file name and `apkUrl` must match exactly**, including capital letters.
5. **Public repository.** The app cannot download from a private repository.
6. **Do not commit** `.jks`, passwords or `local.properties`. (See the `.gitignore` file.)
7. **Debug and release builds are signed differently.** Uninstall a debug copy before installing a release APK.
8. Updating the app never removes the update checker, but a version built without `UpdatePrompt()` will not look for updates any more.

---

## 10. Problems

| Problem | Likely cause and fix |
|---|---|
| "Update failed: 404" inside the app | `apkUrl` does not match the release. Open it in a browser. Check the tag (`1.2` vs `v1.2`), the file name, that the release is **published**, and that the repository is **public**. Copy the link from the release's **Assets** list |
| No update is offered | `versionCode` in `version.json` is not higher than the installed one; GitHub has not refreshed yet (wait 5 minutes); the app has no internet; or the installed version has no update checker |
| Android refuses the update | The new APK is signed with a different key. Uninstall the old app and install fresh |
| "App not installed" | Same reason, or not enough storage space |
| Update button only opens settings | Android needs permission to install apps from YouOke. Allow it, return, tap **Update** again |
| Build error about opt-in / unstable API | See the [Developer guide](DEVELOPER_GUIDE.md#12-gotchas-and-lessons-learned) |

More: [Troubleshooting](TROUBLESHOOTING.md).
