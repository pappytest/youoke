# Legal, license and privacy

> This page explains the situation in plain language. It is not legal advice, and the author is not a lawyer. If you plan to share or publish the app widely, ask a qualified person.

**Contents**
1. [Not affiliated with YouTube](#1-not-affiliated-with-youtube)
2. [YouTube's terms](#2-youtubes-terms)
3. [Video content](#3-video-content)
4. [Open-source licenses](#4-open-source-licenses)
5. [Choosing a license for YouOke](#5-choosing-a-license-for-youoke)
6. [Google Play Store](#6-google-play-store)
7. [Privacy](#7-privacy)
8. [Trademarks and branding](#8-trademarks-and-branding)
9. [No warranty](#9-no-warranty)

---

## 1. Not affiliated with YouTube

YouOke is an independent, unofficial project. It is **not** made, approved or supported by YouTube or Google. "YouTube" is a trademark of Google LLC.

## 2. YouTube's terms

YouOke finds videos and plays them using the **NewPipeExtractor** library. This is **not** YouTube's official player or official API. Accessing YouTube this way goes around the official player and is **not allowed by YouTube's Terms of Service**. Consequences to be aware of:

- It can **stop working at any time** when YouTube changes something. The usual fix is to update NewPipeExtractor.
- Using the app is at your own risk.
- It is meant for **personal, private use** (for example a karaoke night at home). Do not use it to make money, and do not use it to download or re-upload videos. The app streams videos and does not save them.

An approved alternative exists: YouTube's official embedded player plus the YouTube Data API. It would need a rewrite of the playback part, has daily search limits, and some videos block embedding. Key change (pitch shifting) would not be possible with it.

## 3. Video content

The karaoke videos belong to the people who uploaded them and to the owners of the songs. YouOke does not host, copy or store any video. It plays videos from YouTube. The quality and the legality of each upload is the uploader's responsibility.

## 4. Open-source licenses

YouOke uses these libraries. Check each project's own page for the exact and current license terms.

| Library | License |
|---|---|
| [NewPipeExtractor](https://github.com/TeamNewPipe/NewPipeExtractor) | **GNU GPL v3** (or later) |
| AndroidX Media3 / ExoPlayer | Apache License 2.0 |
| Jetpack Compose and other AndroidX libraries | Apache License 2.0 |
| OkHttp | Apache License 2.0 |
| Coil | Apache License 2.0 |
| ZXing ("Zebra Crossing") | Apache License 2.0 |
| NanoHTTPD | BSD 3-Clause |
| Kotlin and kotlinx libraries | Apache License 2.0 |

### The important one: NewPipeExtractor is GPL-3.0

NewPipeExtractor is licensed under the **GNU General Public License, version 3 or later**. The project describes this as free software that you may redistribute and modify under the terms of that license.

What this means in practice (general explanation, not legal advice):

- **Using the app privately** (installing it on your own devices): nothing special is required.
- **Sharing the app with other people**, for example by publishing the APK on GitHub Releases: the app as a whole should be offered under the **GPL-3.0 (or a compatible license)**, and you should make the **source code available** to the people who receive the APK.

## 5. Choosing a license for YouOke

Because of the GPL requirement above, the recommended license for YouOke is **GPL-3.0-or-later**.

**How to add it on GitHub**
1. In your repository, click **Add file → Create new file**.
2. Name the file `LICENSE`.
3. A button **Choose a license template** appears on the right. Click it, choose **GNU General Public License v3.0**, click **Review and submit**, and commit.

**Also publish the source code**
If you share the APK, upload the project source to the same repository (without secrets, see below), so people who get the APK can also get the code. The included `.gitignore` already excludes build output and signing keys.

**Never upload**
- your signing key (`.jks` / `.keystore` files) or its passwords,
- `local.properties`,
- any personal data.

## 6. Google Play Store

In its current form, YouOke is **not suitable for the Google Play Store**, mainly because it plays YouTube videos without YouTube's official player or API. Distributing the APK yourself (GitHub, your own site, or direct installs) avoids Play Store review, but the YouTube terms in [section 2](#2-youtubes-terms) still apply.

## 7. Privacy

YouOke has **no accounts, no analytics, no ads, and no tracking** built in. It does not collect or send personal information to the developer.

What the app does connect to:

| Connection | Why | What is sent |
|---|---|---|
| **YouTube** (through NewPipeExtractor) | Search and play videos | Your search words and video requests, like any program that looks up YouTube. Your device's IP address is visible to YouTube |
| **GitHub** (`raw.githubusercontent.com` and `github.com`) | Check for and download updates | A normal web request for `version.json`, and the APK if you choose to update. Your IP address is visible to GitHub |
| **Your home network** | The phone remote | Search words and commands between your devices, over plain HTTP, **without encryption or a password** |

Stored on the device: the downloaded update APK (in the app's private cache) and temporary thumbnails cache. Nothing is stored in an online account.

Phone-remote note: anyone on the same Wi-Fi who knows the address can control the app and see what is playing. Use it on a network you trust.

## 8. Trademarks and branding

- "YouTube" belongs to Google LLC. The app's logo tagline uses the words "YouTube Karaoke". If you publish the app widely, consider changing the tagline and the name to avoid confusion with YouTube.
- Do not copy the look, name or logo of other karaoke apps.

## 9. No warranty

The software is provided **"as is"**, without warranty of any kind. The author is not responsible for any damage, data loss, or other problems caused by using it. This follows the usual wording of the GPL-3.0 license, which applies if you adopt it.
