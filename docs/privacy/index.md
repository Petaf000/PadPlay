---
title: PadPlay Privacy Policy
permalink: /privacy/
lang: en
---

[日本語](ja/)

# PadPlay Privacy Policy

- Published: 2026-10-07
- Last updated: 2026-10-09 (revised for the switch to PadPlay Link)
- Provided by: Petaf (ぺタフ)
- Contact: petaf570@gmail.com

This privacy policy explains what information the smartphone and tablet app "PadPlay" (the "App") handles and how.
The App connects to PC games that support PadPlay and turns your phone into a controller with a second screen.

## 1. Summary

- The App has **no accounts**. It does not collect your name, email address, phone number, location, contacts or photos.
- The App communicates **only with the game PC you choose by scanning its QR code (or pasting its URL)**, usually a PC on the same Wi-Fi / local network, directly and without going through any server of the provider (section 3).
- **All of that communication is encrypted.** The key is in the QR code and changes every time the game starts (section 3).
- The provider (Petaf) runs no servers and **does not receive your information**. The App sends nothing to the provider or to any other company, including Google and Apple.
- **No ads, no in-app purchases, no usage analytics, no tracking, and no analytics, crash-reporting or advertising SDKs in the App.**
- QR codes are read **entirely on your device**; no images are stored or sent (section 5).

## 2. How the App uses device features

| Feature / permission | What for | Stored or sent |
|---|---|---|
| Camera | Only to read the QR code shown by the game. | Frames are analyzed on the device. **No photos or videos are stored or sent** (section 5). You can paste the URL instead if you deny the camera. |
| Gyroscope / accelerometer | To use the tilt and rotation of your phone as game input. | Sent to the connected game (section 3). Not stored. |
| Touch and on-screen buttons | Game input. | Same as above. |
| Network (local network) | To connect directly to the game PC on the same Wi-Fi / local network (PadPlay Link, a dedicated UDP protocol). | Section 3 |
| Wi-Fi low-latency mode (Android) | Only while you play (while the pad screen is open), to reduce delay. Released when you leave. | — |
| Vibration | Signals from the game and feedback when you press a button. | — |
| Microphone | **Not used.** The App does not request microphone access and records nothing. | — |
| Bluetooth | **Not used.** The App does not request Bluetooth permissions. | — |
| Location, contacts, photos, files | **Not used.** | — |

## 3. Information sent to the connected game

When you connect, the App sends the following directly to the game PC written in the QR code. Nothing goes through a server on the internet run by the provider.

- **Device ID**: a random value (22 characters) created when the App is installed. It is not an advertising ID, IMEI, phone number or any other identifier
  tied to your device or to you. It lets you come back as the same player after the App is closed or Wi-Fi drops.
- **Device information**: model name (e.g. "Google Pixel 8"; on iPhone a model identifier such as "Apple iPhone15,2"), OS name and version, screen size and scale, and the App version.
  Used to map screen coordinates and to show which device is connected.
- **Capabilities**: which features this device can use (video, touch, tilt, buttons, vibration).
- **Input**: touch positions, on-screen buttons and sticks, tilt and rotation, and timestamps used to measure latency.
- **Connection status**: how the video is arriving (delay, jitter, loss rate, received bitrate) and how fast this device can display it.
  Used to adapt the video quality and format (H.264 or JPEG) to your network and device automatically.

The App receives the game's video, the layout of the on-screen buttons and vibration signals from the game. The video is only displayed, never stored.

**On the game side**: the PadPlay component built into games (PadPlay SDK) keeps this information in memory only while you are connected and for a short time
after a disconnect (60 seconds by default, to give you the same player back). It does not write it to files or send it to the internet. However, each game is made
by its own developer, who decides how the game handles what it receives. See the privacy policy of the game you play.

**Encryption**: everything the App exchanges with the game PC (the connection handshake, the device ID and device information, input, video, vibration and so on)
goes over PadPlay Link, PadPlay's own UDP protocol, and is **all encrypted** (ChaCha20-Poly1305). The key is in the QR code on the game's screen, changes every time
the game starts, and is never sent over the network. Someone on the same Wi-Fi who has not scanned the QR code cannot read the contents, impersonate your input or alter it.
The only unencrypted messages are short replies from the game PC saying "this QR code is out of date" or "the app version does not match"; they contain no
information about your device or you. The size and timing of the traffic cannot be hidden, and anyone who sees the QR code on the game's screen can connect the same way.

## 4. Information stored on your device

The App stores the following only in its own storage on your device. It is not sent to the provider or to anyone else.

- The device ID (section 3), in the App's settings storage (Flutter's shared_preferences; UserDefaults on iOS).
- A "reconnect key" (resumeKey) per game: a random value the game gives at connection, paired with a random value from that game's QR code (which changes every time the game starts).
  Stored in the same place; up to the 8 most recent are kept and older ones are deleted.

Nothing else is stored (the on-screen button layout is received from the game each time you connect). QR images and game video are not stored.

**How to delete**: uninstall the App, or on Android go to Settings → Apps → PadPlay → Storage → Clear data.
After that, your next connection is treated as a new player.

## 5. Reading QR codes

On both Android and iOS, the App reads QR codes with an open-source library built into the App (zxing-cpp, the C++ port of ZXing).
All camera frames are analyzed on the device. **Camera frames, scan results and device information are never sent to the provider or to any third party such as Google or Apple.**
The contents of the QR code (the address of the game PC, a one-time token and the encryption key) are used only to connect to that game.

## 6. Sharing with third parties

The provider does not receive your information and therefore does not share it with anyone. The only information that leaves the App is described in
section 3 (the game PC you choose).

## 7. Children

The App is not directed at children under 13.

## 8. Changes to this policy

This policy may change, for example when the App's behavior changes. Changes will be posted on this page and the "Last updated" date above will be revised.

## 9. Contact

For questions about this policy or how the App handles information, contact petaf570@gmail.com.
