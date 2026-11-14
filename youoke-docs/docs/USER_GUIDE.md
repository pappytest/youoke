# User guide

How to use YouOke on a TV, tablet or phone, and how to use your phone as a remote.

**Contents**
1. [The main screen](#1-the-main-screen)
2. [Finding and playing a song](#2-finding-and-playing-a-song)
3. [The queue](#3-the-queue)
4. [Player controls](#4-player-controls)
5. [Full-screen mode](#5-full-screen-mode)
6. [Suggested songs](#6-suggested-songs)
7. [Using your phone as a remote](#7-using-your-phone-as-a-remote)
8. [Video quality](#8-video-quality)
9. [Updating the app](#9-updating-the-app)
10. [Tips](#10-tips)
11. [Using a TV remote (D-pad)](#11-using-a-tv-remote-d-pad)

---

## 1. The main screen

When you open YouOke with nothing playing, you see the **YouOke logo** where the video will appear. Along the bottom of the logo is a line like:

```
Remote: http://192.168.1.20:8080
```

That is the address of the phone remote (see [section 7](#7-using-your-phone-as-a-remote)). A **QR code** for the same address, labelled **Remote**, sits in the top-right corner of the video area.

**On a TV or tablet (wide screen)**
- Left: the video, the **Now playing** line with **Pause** and **Next** buttons, and the **Up next** queue underneath.
- Right: the search panel, with the search box, the **Search** button and the list of results.

**On a phone (tall screen)**
- Top: the video and its buttons.
- Middle: search and results.
- Bottom: the queue.

---

## 2. Finding and playing a song

1. Type the song title in the search box. You do **not** need to type the word "karaoke". The app adds it for you (searching for `unwell` searches YouTube for `unwell karaoke`).
2. Press **Search** (or the search key on the keyboard).
3. Up to 10 results appear, each with a thumbnail, title and channel name.
4. Tap a result (or select it with the TV remote and press OK). Three choices appear **on top of that card**, so the video stays visible:

| Choice | What it does |
|---|---|
| **Play now** | Starts this song immediately and replaces whatever is playing |
| **Reserve** | Adds the song to the **end** of the queue |
| **1st Reserve** | Puts the song at the **front** of the queue, so it plays next |

If nothing is playing, **Reserve** and **1st Reserve** start the song straight away.

**Clearing the search box:** tap the **×** at the right end of the box. The text is cleared and the suggested songs come back.

---

## 3. The queue

The **Up next** list shows the songs waiting to play, in order.

- When a song ends, the next one starts automatically.
- While a song is playing, the app prepares the next one in the background, so the change between songs is quick.
- You can search and add more songs while a song is playing. The song that is playing is not interrupted.
- To change a queued song, tap it (or select it and press OK). Three buttons appear inside its row: **Play now**, **To front** (plays next) and **Remove**. You can also remove songs with the **phone remote** (see below).

---

## 4. Player controls

| Button | What it does |
|---|---|
| **Pause / Resume** | Pauses the song, or continues it |
| **Next** | Stops the current song and starts the next one in the queue. If the queue is empty, playback stops and the logo returns |
| **Tap the video** (touchscreen) | Pauses or resumes. A "Paused" label shows while it is paused |

The text next to the title, such as `[Auto 720p]`, shows the current video quality (see [section 8](#8-video-quality)).

While the video is preparing or loading, an orange **spinner** shows over it.

---

## 5. Full-screen mode

While a song plays and you do not touch anything, the app goes **full screen** automatically after **10 seconds**. The search panel, queue and buttons disappear so the video fills the screen.

**What you see in full screen**

| Item | Where | What it shows |
|---|---|---|
| **QR code** | Top-right corner | The phone remote's address (labelled **Remote**), always visible |
| **Scrolling ticker** | Along the top | `UP NEXT  1. ...  •  2. ...` for your queued songs (up to 20). Only the first 20 characters of each title are shown, and a long list scrolls faster so a pass never takes too long. It appears **3 times per song**, spread evenly through the song, and scrolls across **2 times** each time. Not shown when the queue is empty |
| **"Up next" card** | Lower-right corner | The thumbnail and title of the next song, during the **last 20 seconds** of a song |

**Leaving full screen:** touch the screen, or press any button on the TV remote. On a TV, the **first** button press only brings the normal screen back and does nothing else, so you cannot skip a song by accident. Volume keys are not affected.

The screen also stays awake while the app is open.

---

## 6. Suggested songs

When the search box is empty (or a search finds nothing), the list shows **10 suggested karaoke videos** with thumbnails. They work like normal results: tap one and choose Play now, Reserve or 1st Reserve.

- At first, the suggestions come from a built-in list of well-known songs.
- After you play songs, the app collects the karaoke videos that YouTube recommends next to them, and the suggestions come from those.
- Tap **×** in the search box to get a fresh random set.
- The collected recommendations are forgotten when you close the app.

---

## 7. Using your phone as a remote

You do not need to install anything on the phone.

**Connect**
1. Make sure the phone and the TV are on the **same Wi-Fi network**.
2. Open YouOke on the TV.
3. On the phone, scan the **QR code** with the camera, or open the browser and type the address shown on the TV, for example `http://192.168.1.20:8080`. Type `http://`, not `https://`.

**What the remote page shows (top to bottom)**
- The YouOke logo and a status line (such as "10 results").
- A pinned area that stays at the top while you scroll, with:
  - the **search box** (with **×** to clear),
  - **Pause / Resume**, **Next** and **Queue** buttons,
  - the **now-playing** bar.
- The results (or suggestions), each with **Play now**, **Reserve** and **1st Reserve** buttons.
- The **Up next** queue, with a **Remove** button on each song.

**Good to know**
- The remote only works while YouOke is open on the TV.
- The page refreshes itself about every second and a half.
- Use the **Queue** button to jump down to the queue.
- You can add the remote page to your phone's home screen from the browser menu.
- Anyone on your Wi-Fi who knows the address can use the remote. Do not share the address outside your home.

If the remote will not connect, see [Troubleshooting](TROUBLESHOOTING.md#the-phone-remote-will-not-connect).

---

## 8. Video quality

YouOke uses **adaptive quality**:

- It starts at a lower quality (about **360p**) so the video starts quickly.
- It measures your internet speed and moves up to **720p** when the connection allows.
- If your connection slows down, it moves back down instead of freezing.

The label next to the song title shows what is happening:

| Label | Meaning |
|---|---|
| `[Auto 360p]` then `[Auto 720p]` | Adaptive quality is working |
| `[720p HD]` | A fixed 720p stream is being used (backup method) |
| `[360p]` or similar | A basic stream is being used (second backup) |

---

## 9. Updating the app

When YouOke opens, it checks whether a newer version is available (this needs an internet connection).

1. If there is one, an **Update available** window appears, with notes about what is new.
2. Tap **Update** to download and install it, or **Later** to skip it until the next time you open the app.
3. The first time, Android asks you to allow YouOke to install apps. Turn it on, go back, and tap **Update** again. On Google TV this setting is under **Settings → Apps → Security & restrictions → Unknown sources**.
4. When the download finishes, Android's installer opens. Confirm the install. The app restarts as the new version.

---

## 10. Tips

- **Best experience:** use a real TV or phone on good Wi-Fi. Use a wired TV connection if you can.
- **Fewer long waits:** queue the next song while the current one plays, so it is ready in advance.
- **Search tip:** include the artist for common titles (for example `Perfect Ed Sheeran`).
- **Wrong video?** Tap another result, or search again with more words.
- **Nothing happens when a song is chosen?** Wait a few seconds. The first song needs a moment to be prepared.

---

## 11. Using a TV remote (D-pad)

YouOke works with the arrow keys, **OK** and **Back** buttons of a Google TV / Android TV remote. An outline shows what is selected: **orange** around cards, and **white** around the (orange) buttons so it stays easy to see.

**Moving around**
- The arrow keys move the outline between the search box, buttons, songs and the queue. **OK** presses the selected item.
- When the app opens, the outline starts on the first suggested song. After you search, it jumps to the first result.

**Searching**
1. Move the outline to the search box and press **OK**. The on-screen keyboard opens.
2. Type the song and confirm with the keyboard's search key (or select the **Search** button).
3. The **×** button inside the search box clears the text and brings the suggestions back.
4. **Typing takes your time:** while you are typing or choosing, the full-screen countdown is 30 seconds instead of 10, and every key you type restarts it.

**The song choices**
- Choosing a song shows **Play now / Reserve / 1st Reserve** on that card. The outline starts on **Play now**. After you choose (or press **Back**), the outline returns to the same card.

**The queue**
- Select a song in the **Up next** list and press **OK**. Its row shows **Play now** (plays it immediately), **To front** (plays next) and **Remove**. **Back** closes them.

**The Back button**
- While there are search results (or text in the box), **Back** clears the search first. Press it again to leave the app.

**Media keys** (if your remote has them)

| Key | What it does |
|---|---|
| Play/Pause | Pauses or resumes |
| Play, Pause | Resumes, or pauses |
| Next | Goes to the next song in the queue |
| Fast-forward, Rewind | Jumps 10 seconds forward or back (hold for repeated jumps) |

Media keys work in full screen too, and they do not leave full screen.

**Full screen**
- Any other button press brings the normal screen back. That first press does nothing else, so you cannot skip a song by accident.
- Volume keys always control the TV volume.

**Updates**
- If an update is offered, the outline starts on **Update**.
