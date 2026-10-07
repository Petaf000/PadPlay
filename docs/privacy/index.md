---
title: PadPlay Privacy Policy
permalink: /privacy/
lang: en
---

[日本語](ja/)

# PadPlay Privacy Policy

- Published: 2026-10-07
- Last updated: 2026-10-07
- Provided by: Petaf (ぺタフ)
- Contact: petaf570@gmail.com

This privacy policy explains what information the smartphone and tablet app "PadPlay" (the "App") handles and how.
The App connects to PC games that support PadPlay and turns your phone into a controller with a second screen.

## 1. Summary

- The App has **no accounts**. It does not collect your name, email address, phone number, location, contacts or photos.
- The App communicates **only with the game PC you choose by scanning its QR code (or pasting its URL)**, usually a PC on the same Wi-Fi / local network.
- The provider (Petaf) runs no servers and **does not receive your information**.
- **No ads, no in-app purchases, no usage analytics, no tracking.**
- QR codes are read **entirely on your device**; nothing about them is sent anywhere (section 5).

## 2. How the App uses device features

| Feature / permission | What for | Stored or sent |
|---|---|---|
| Camera | Only to read the QR code shown by the game. | Frames are analyzed on the device. **No photos or videos are stored or sent** (section 5). You can paste the URL instead if you deny the camera. |
| Gyroscope / accelerometer | To use the tilt and rotation of your phone as game input. | Sent to the connected game (section 3). Not stored. |
| Touch and on-screen buttons | Game input. | Same as above. |
| Network (local network) | To connect to the game PC. | — |
| Vibration | Signals from the game and feedback when you press a button. | — |
| Microphone | **Not used.** On iOS the usage description is declared only because the communication library (WebRTC) requires it; nothing is recorded. On Android the App does not request microphone access. | — |
| Bluetooth | **Not used.** The communication library declares the permission on Android 11 and older, but the App does not use Bluetooth. | — |
| Location, contacts, photos, files | **Not used.** | — |

## 3. Information sent to the connected game

When you connect, the App sends the following directly to the game PC written in the QR code. Nothing goes through a server on the internet run by the provider.

- **Device ID**: a random value (22 characters) created when the App is installed. It is not an advertising ID, IMEI, phone number or any other identifier
  tied to your device or to you. It lets you come back as the same player after the App is closed or Wi-Fi drops.
- **Device information**: model name (Android only, e.g. "Google Pixel 8"), OS name and version, screen size and scale, and the App version.
  Used to map screen coordinates and to show which device is connected.
- **Input**: touch positions, on-screen buttons and sticks, tilt and rotation, and timestamps used to measure latency.

The App receives the game's video, the layout of the on-screen buttons and vibration signals from the game. The video is only displayed, never stored.

**On the game side**: the PadPlay component built into games (PadPlay SDK) keeps this information in memory only while you are connected and for a short time
after a disconnect (60 seconds by default, to give you the same player back). It does not write it to files or send it to the internet. However, each game is made
by its own developer, who decides how the game handles what it receives. See the privacy policy of the game you play.

**Encryption**: video and input (WebRTC) are encrypted. The initial connection handshake (which includes the device ID and device information) uses an unencrypted
WebSocket inside your local network. Do not use the App on untrusted networks such as public Wi-Fi.

## 4. Information stored on your device

The App stores the following only on your device (SharedPreferences on Android, UserDefaults on iOS). It is not sent to the provider.

- The device ID (section 3).
- A "reconnect key" per game: a random value the game gives at connection, paired with a random value from that game's QR code (which changes every time the game starts).
  Up to the 8 most recent are kept; older ones are deleted.

**How to delete**: uninstall the App, or on Android go to Settings → Apps → PadPlay → Storage → Clear data.
After that, your next connection is treated as a new player.

## 5. Reading QR codes

On both Android and iOS, the App reads QR codes with an open-source library built into the App (zxing-cpp, the C++ port of ZXing).
All camera frames are analyzed on the device. **Camera frames, scan results and device information are never sent to the provider or to any third party such as Google or Apple.**
The contents of the QR code (the address of the game PC and a one-time token) are used only to connect to that game.

## 6. Sharing with third parties

The provider does not receive your information and therefore does not share it with anyone. The only information that leaves the App is described in
section 3 (the game PC you choose).

## 7. Children

The App is not directed at children under 13.

## 8. Changes to this policy

This policy may change, for example when the App's behavior changes. Changes will be posted on this page and the "Last updated" date above will be revised.

## 9. Contact

For questions about this policy or how the App handles information, contact petaf570@gmail.com.
