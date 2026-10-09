---
title: PadPlay SDK documentation
lang: en
---

# PadPlay SDK documentation

PadPlay SDK turns a phone into a second-screen controller for a Unity game: touch screen, gyro,
on-screen buttons, rumble and a second screen. Start with the Quick Start; come back here to find a topic.

In the Editor, *Window → PadPlay → Welcome* (it opens once by itself after you install the package) shows the first steps,
imports a sample with one click and links to these pages.

**You need** the separate PadPlay phone app on every player's phone, and the PC and the phones on the **same Wi-Fi / LAN**
(no internet play). See [Quick Start §1](QuickStart.md#1-requirements) for the requirements and the tested setup.

## Pages

| Page | Read it when |
|---|---|
| [Quick Start](QuickStart.md) | You set the SDK up for the first time. |
| [Concepts](Concepts.md) | You want to know how it works: slots, tokens, encryption, state and reliable messages, touch injection, attitude, layouts. |
| [Debugging](Debugging.md) | Something does not arrive, or you want to see what each phone shows. |
| [Pose Camera](PoseCamera.md) | You want to look around by pointing the phone: the PC renders for the phone's orientation and the phone corrects the remaining turn on screen. |
| Changelog (`CHANGELOG.md` in the package) | You update the package or look for a renamed API. |
| [用語集 / Glossary (Japanese)](Glossary-ja.md) | You use the editor in Japanese (*Preferences → PadPlay*) and want the English term. |

## By task

| I want to… | Go to |
|---|---|
| Check what you need (phone app, same Wi-Fi, firewall) and the tested setup | [Quick Start §1](QuickStart.md#1-requirements) |
| Know what it cannot do yet (macOS / Linux hosts send JPEG, no browser join) and what PadPlay Lite leaves out | [Quick Start §1](QuickStart.md#what-it-cannot-do-yet), [PadPlay Lite](QuickStart.md#padplay-lite) |
| Understand the encryption, H.264 / JPEG and how video adapts to the Wi-Fi | [Concepts: Encryption](Concepts.md#encryption), [Second screen](Concepts.md#second-screen-video) |
| Install the package | [Quick Start §2](QuickStart.md#2-install-the-package) |
| Add the host in one click (*GameObject → PadPlay → PadPlay Setup*) and understand its Inspector (modules, players, connection) | [Quick Start §3](QuickStart.md#3-add-a-padplayhost) |
| Show the QR code, react to players joining and leaving | [Quick Start §4](QuickStart.md#4-show-the-qr-code), [§5](QuickStart.md#5-connect-the-phone) |
| Receive touches in uGUI, know which player touched | [Quick Start §6](QuickStart.md#6-touch-arrives-as-normal-ugui-events) |
| Read buttons, sticks and the attitude as a Gamepad | [Quick Start §7](QuickStart.md#7-read-the-phone-as-a-gamepad) |
| Turn the camera with the phone, or point with it | [Quick Start §8](QuickStart.md#8-gyro-camera-and-pointer) |
| Show a picture on the phone | [Quick Start §9](QuickStart.md#9-second-screen-video) |
| Put game-specific buttons on the phone, rumble it | [Quick Start §10](QuickStart.md#10-on-screen-buttons-and-haptics) |
| Fix "cannot connect", "stuck connecting" (*Window → PadPlay → Connection Check*), wrong touches, black video | [Quick Start §11](QuickStart.md#11-troubleshooting), [Debugging §6](Debugging.md#6-which-tool-for-which-problem) |
| See each phone's screen in the Editor | [Debugging §1](Debugging.md#1-display-preview-each-phones-screen-on-display-2-3-) |
| Inspect connection state, input rates, button bits, events | [Debugging §2](Debugging.md#2-padplay-debugger) |
| Show a status box in a built game | [Debugging §3](Debugging.md#3-debug-overlay-in-built-games) |
| Read the same diagnostics from code | [Debugging §4](Debugging.md#4-reading-the-same-data-from-code) |
| Try the game without a phone (simulated phones in the Editor) | [Debugging §5](Debugging.md#5-trying-it-without-a-phone-virtual-phones) |
| Try the samples (Treasure Map, Floating Island, Connect Demo, Gyro Window, Panel Dive) | [Quick Start §12](QuickStart.md#12-samples) |

## Main types

| Type | Add it / use it for |
|---|---|
| `PadPlayHost` | *Add Component → PadPlay → Host*. One per scene: connections, players, settings (`host.Settings`). |
| `PadPlayQrView`, `PadPlayStatusView` | *PadPlay → QR View* on a Raw Image, *Status View* on a Text. |
| `PadPlayPointerEventData` | The `PointerEventData` of phone touches (`PlayerSlot`, `FingerId`, `PhonePosition`). |
| `PadPlayPad` | The Input System device of each phone (`Gamepad` + `attitude`, `angularVelocity`, `custom14`…`custom31`). |
| `PadPlayGyroCamera`, `PadPlayGyroPointer` | *PadPlay → Gyro Camera / Gyro Pointer*. |
| `PadPlayView` | *PadPlay → View*: stream a camera or RenderTexture to a phone. |
| `PadPlayButtonLayout` | *Assets → Create → PadPlay → Button Layout*: the buttons and sticks on the phone. |
| `PadPlayRaycaster` | *PadPlay → Raycaster*: a `GraphicRaycaster` that ignores transparent pixels. |
| `PadPlayDebugOverlay`, `host.Diagnostics` | *PadPlay → Debug Overlay*; diagnostics from code. |

## Phone app

Players use the **PadPlay** app (Flutter, Android and iPhone). It is not in a store yet; build and install it from
`client/` in the SDK repository (see `client/README.md` there). The current iPhone build has not been tried on a device yet.

## Support

- Questions, bug reports and ideas: [github.com/Petaf000/PadPlay](https://github.com/Petaf000/PadPlay)
  (Issues and Discussions; FAQ and known issues). Please include the result of *Window → PadPlay → Connection Check* in bug reports.
- Privacy policy of the PadPlay app: [English](https://petaf000.github.io/PadPlay/privacy/) / [日本語](https://petaf000.github.io/PadPlay/privacy/ja/)
