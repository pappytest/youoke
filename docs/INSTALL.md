# Installation

YouOke is not on the Google Play Store. You install it from an **APK file** that you download from the [Releases page](https://github.com/pappytest/youoke/releases). This is called "sideloading".

**Requirements**
- Android 7.0 or newer (API 24+)
- Phone, tablet or Google TV / Android TV
- Internet connection (for searching and playing videos)

> Only install APK files from a source you trust. The official files for this project are on the project's Releases page.

---

## Install on an Android phone or tablet

1. On the phone, open the browser and go to the Releases page:
   `https://github.com/pappytest/youoke/releases`
2. Under the newest release, open **Assets** and tap **YouOke-x.y.apk** to download it.
3. When the download finishes, open the file (from the download notification or the **Files / Downloads** app).
4. If Android says the app is blocked, tap **Settings** and turn on **Allow from this source** for your browser or file manager, then go back and tap **Install**.
5. If Google Play Protect shows a warning about an unknown app, choose **More details → Install anyway**. This message appears for any app that is not from the Play Store.
6. Tap **Open**.

---

## Install on Google TV / Android TV

Google TV has no easy way to download files in a browser, so the usual method is **adb** from a computer on the same network.

### Method A: adb over Wi-Fi (recommended)

**1. Turn on developer options on the TV**
1. Go to **Settings → System → About**.
2. Select **Android TV OS build** (or **Build**) about 7 times until it says you are a developer.
3. Go back to **Settings → System → Developer options**.
4. Turn on **USB debugging**, and **Network debugging** (or **Wireless debugging**) if it is listed.

**2. Find the TV's IP address**
Go to **Settings → Network & Internet** and open the connected Wi-Fi network, or look in **Settings → System → About → Status**.

**3. Install adb on your computer**
Download Google's **Platform-Tools** (they contain `adb`). If you have Android Studio, adb is already installed at:
`C:\Users\<your name>\AppData\Local\Android\Sdk\platform-tools`

**4. Connect and install**
Download `YouOke-x.y.apk` to your computer, then open a terminal in the `platform-tools` folder and run:

```
adb connect TV_IP_ADDRESS:5555
adb install -r path\to\YouOke-x.y.apk
```

Accept the **Allow debugging?** message on the TV if it appears. The TV prints `Success` when the app is installed.

> With PowerShell, put `.\` in front of the command: `.\adb connect ...`.

### Method B: USB drive

1. Copy the APK to a USB stick and plug it into the TV.
2. Install a file manager app on the TV from the Play Store.
3. Open the APK from the USB drive with the file manager.
4. When asked, allow the file manager to **install unknown apps**.

### Where to find the app

YouOke appears in the **Apps** row of the Google TV home screen, with the YouOke banner.

---

## First launch

1. Open **YouOke**.
2. Search for a song to test it.
3. Scan the QR code on screen with your phone to open the remote.

---

## Updating

**From inside the app (easiest):** when YouOke starts, it offers an update if a newer version exists. See the [user guide](USER_GUIDE.md#9-updating-the-app).

**By hand:** download the newest APK and install it over the old one. Your settings are kept. This only works if both versions were signed with the same key (all official releases are).

If Android says **"App not installed"** or `INSTALL_FAILED_UPDATE_INCOMPATIBLE`, the new APK was signed with a different key. Uninstall the old app first, then install the new one.

---

## Uninstalling

- **Phone:** long-press the app icon → **App info → Uninstall**.
- **Google TV:** **Settings → Apps → See all apps → YouOke → Uninstall**.

---

## Common install problems

| Problem | What to do |
|---|---|
| "For your security, your phone is not allowed to install unknown apps" | Allow your browser or file manager to install unknown apps in **Settings → Apps → Special access → Install unknown apps** |
| Play Protect warning | **More details → Install anyway** |
| "App not installed" | Uninstall any older copy first (especially a test or debug copy) and try again |
| adb says `unauthorized` | Accept the **Allow debugging?** prompt on the device |
| adb cannot connect to the TV | Check the IP address, that both are on the same Wi-Fi, and that network debugging is on |
| The app still shows the old name or icon | Uninstall and reinstall, then restart the TV or phone launcher |

More fixes: [Troubleshooting](TROUBLESHOOTING.md).
