---
title: Debugging
lang: en
---

# Debugging

> This is the online copy of the documentation that ships inside the package (`Documentation~`). SDK 0.2.0. [All pages](./) · [Support](https://github.com/Petaf000/PadPlay)

Tools for answering three questions without leaving the Unity Editor:
**Is the phone connected? What is arriving from it? What does its screen show?**

| Tool | Where | Use it for |
|---|---|---|
| [Display preview](#1-display-preview-each-phones-screen-on-display-2-3) | Game view → *Display 2*, *Display 3*, … | Seeing each player's phone screen (video, fingers, buttons) next to the TV picture. |
| [PadPlay Debugger](#2-padplay-debugger) | *Window → PadPlay → Debugger* | Connection state, round trip, input rates, button bits, gyro, touches, the event log; recenter, test haptics, disconnect. |
| [Debug Overlay](#3-debug-overlay-in-built-games) | *Add Component → PadPlay → Debug Overlay* | A small status box in **built games** (F3 shows / hides it). |
| [`host.Diagnostics`](#4-reading-the-same-data-from-code) | C# | The same numbers from your own code or tools. |
| Connection Check | *Window → PadPlay → Connection Check* | Why a phone cannot connect or stays at `Connecting`: QR addresses and adapters, port, Windows Firewall, the last connection attempt. Read-only. See [Quick Start §11](QuickStart.md#11-troubleshooting). |
| [Virtual Phones](#5-trying-it-without-a-phone-virtual-phones) | *Window → PadPlay → Virtual Phones* | Simulated phones in the Editor: touch with the mouse, buttons with the mouse or keys, tilt with sliders. No phone needed. |
| Input Debugger (Unity) | *Window → Analysis → Input Debugger* | What the Input System does with the `PadPlayPad` devices (bindings, actions, `PlayerInput`). |

The first four only **read** the host and never change what the game receives; Virtual Phones send input like a real phone.
The *Debug* section of the Host Inspector (between *Modules* and *Connection*) holds the preview settings and buttons
to open the Debugger and add the overlay.

---

## 1. Display preview: each phone's screen on Display 2, 3, … {#1-display-preview-each-phones-screen-on-display-2-3}

While playing in the Editor, the host draws what each phone shows on its own display:
**P1 → Display 2, P2 → Display 3**, and so on. Display 1 (your game) is never touched.

1. Open a second (and third) **Game view**: right-click the Game tab → *Add Tab → Game*.
2. In each new Game view pick **Display 2** / **Display 3** in the top-left dropdown.
   Set its size to your tablet's resolution (for example 1920 × 1200) if you like; the picture keeps its aspect ratio.
3. Press **Play** and connect phones.

What you see on each display:

- **Black** — the part of the display outside the phone.
- **Dark grey** — the phone's screen (its aspect ratio from the phone's reported size; landscape unless the phone reports portrait).
- **The video** — the `PadPlayView` texture sent to that player, letterboxed exactly like the phone does
  (the video's aspect ratio, 16:9 before any video exists).
- **Overlay** — the player's fingers (dots in the player's color) and the on-screen buttons of the player's layout.
  Pressed buttons light up in the player's color while held (optionally for at least *Button Highlight Ms* after the press);
  stick knobs follow the stick values.
- **Caption** — player, display, state, phone model and round trip. Without a phone the display reads *waiting for a phone*.

Settings: *Host Inspector → Debug* (`host.Settings.Preview`), all changeable while playing.

| Field | Default | What it does |
|---|---|---|
| Mode | EditorOnly | `Off`, `EditorOnly` (built games show nothing), or `EditorAndBuilds` (built games use extra monitors that exist and activate them). |
| Player Displays | 2, 3, 4, 5 | Display number per player: element 0 is P1. `0` hides that player. `1` is not allowed (it is the game's screen). |
| Show Touches / Show Buttons | on | The overlay parts. |
| Overlay Opacity | 0.6 | How strong the overlay is. |
| Show Caption | on | The text line at the top. |
| Button Highlight Ms | 0 | `0` = lit only while held (the value the game reads). Above 0, a pressed button stays lit at least this long after the press, in the preview, the Debugger and the Debug Overlay. Also editable as *Min lit ms* in the Debugger. |

How it works: for each shown player the host creates (outside your scene hierarchy, not saved) a camera that only clears
the display to black (`cullingMask = 0`) and a *Screen Space – Overlay* Canvas on that display. The Canvas has no
`GraphicRaycaster`, so mouse clicks and phone touches never hit it. The video is the same `RenderTexture` that is being
encoded, so the preview costs no extra rendering.

---

## 2. PadPlay Debugger

*Window → PadPlay → Debugger* (or **Open Debugger** in the Host Inspector's *Debug* section).
It shows nothing until you press Play.

**Top bar** — the host, whether it runs and on which port, how many players, whether new players are accepted,
the **Join URI** with *Copy* (paste it into the phone app when the camera cannot read the code) and a **QR** toggle.

**Players** — one row per player slot that joined since the host started:

| Column | Meaning |
|---|---|
| Device | Phone model and OS. |
| State | `Connecting` (joined, channels not open), `Ready`, `Reconnecting` (dropped, slot held for *Resume Hold Seconds*), `Gone` (left; the reason in brackets). |
| RTT ms | Round trip of the control channel's clock pings. |
| Clock ms | Phone clock minus PC clock (`ClockSync.OffsetMs`). |
| Touch / Gyro / Btn Hz | Packets per second arriving from the phone. `(off)` = it arrives, but the module is unticked so the host drops it. |
| Last in | Seconds since any input arrived. |
| Video | Sent resolution, frame rate, measured bitrate and encode time per frame (from WebRTC stats, once a second). |
| Rot | The phone's display rotation in degrees. |

**Details of the selected player** (click *P1*, *P2*, … in the first column):

- **Buttons** — the 32 buttons as the game reads them (the player's `PadPlayPad`, or the `ButtonsReceived` values
  when devices are off): 0–13 are the Gamepad buttons (A/South … right stick press), 14–31 are `custom14`…`custom31`.
  A held button is drawn in the player's color while it is held, exactly like the Input Debugger. Optionally, *Min lit ms*
  under the grid (= `Settings.Preview.ButtonHighlightMs`, default 0) keeps a pressed button lit that long in a lighter color.
  The small number in each cell is **how many times it was pressed since Play started** (the title shows the total):
  tap a button 10 times and expect 10 — a smaller number means presses were lost before they reached the game,
  a correct number with a flicker you did not see means only the drawing was too slow.
  The window redraws every frame while playing and visible.
- **Sticks** — left and right stick values (−1…1, y up).
- **Attitude** — a phone-shaped box (screen face in the player's color) with Euler angles and angular velocity.
  It shows the calibrated `pad.attitude` when the player has a `PadPlayPad`, otherwise the raw phone attitude.
- **Touches** — the fingers on the phone's video area (0…1, y = 0 at the top) with their ids.
- **Recenter (yaw / full)** — `pad.Calibrate(...)`. **Test haptics** — vibrates for 0.3 s via `host.SetHaptics`.
  **Disconnect** — `host.Kick(slot)` (the slot is not held).

**Events** — the host's log: host started / stopped, joined, resumed, ready (again after a video change), left,
removed, layout sent, haptic strength changed (repeats are not logged), calibrate sent, screen rotation, refused join (*JoinRejected*: bad_token, full or version).
Filter by kind, by text, or to the selected player; *Pause* freezes the list (the host keeps recording); *Clear* empties it.
The host keeps the last 512 events.

---

## 3. Debug Overlay (in built games)

*Add Component → PadPlay → Debug Overlay* (or **Add Debug Overlay** in the Host Inspector) on any GameObject.
It draws a small box with the number of players and, for each player, state, phone model, round trip, a warning when
a ready player has sent nothing for over a second, and — once a button was pressed — the lit buttons and the press count
(`buttons: South (12 presses)`; lit for at least *Button Highlight Ms* like the Debugger). It is rebuilt every frame.
It uses IMGUI (no Canvas, no EventSystem).

| Field | Default | |
|---|---|---|
| Host | *(found in the scene)* | |
| Toggle Key | F3 | Input System key that shows / hides it (`None` = no key). |
| Visible | on | Shown at start. |
| Corner | TopLeft | |
| Font Size | 18 | At 1080 px screen height; scales with the screen. |

---

## 4. Reading the same data from code

```csharp
using System.Collections.Generic;
using PadPlay.Unity;
using UnityEngine;

PadPlayDiagnostics diagnostics = host.Diagnostics;
foreach (PadPlayPlayerDiagnostics player in diagnostics.Players)
{
    Debug.Log($"P{player.PlayerSlot + 1} {player.State} {player.Device.Model} " +
              $"gyro {player.AttitudeRateHz:F0} Hz, fingers {player.Touches.Count}, buttons {player.Buttons.Bits:X8}");
}

var events = new List<PadPlayDebugEvent>();
diagnostics.CopyEvents(events);      // oldest first; diagnostics.TotalEventCount tells you when new ones arrived
```

| Member | |
|---|---|
| `host.Diagnostics` | `PadPlayDiagnostics`: `Players`, `TryGetPlayer(slot, out player)`, `LastJoinAttempt` (`JoinAttempt`: `RemoteAddress`, `AtMs`, `Result` = Waiting / Accepted / BadToken / Full / Version / NoJoin / NotWebSocket, `PlayerSlot`; null = nothing connected yet), `JoinAttemptCount`, `EventCount`, `TotalEventCount`, `GetEvent(i)`, `CopyEvents(list)`, `ClearEvents()`, `EventCapacity` (512). |
| `PadPlayPlayerDiagnostics` | `PlayerSlot`, `Device`, `AppVersion`, `State`, `LeftReason`, `JoinedHostMs`, `ResumeCount`, `TouchRateHz`, `AttitudeRateHz`, `ButtonsRateHz`, `LastTouchHostMs`, `LastAttitudeHostMs`, `LastButtonsHostMs`, `LastInputHostMs`, `Touches`, `HasAttitude`, `Attitude`, `HasButtons`, `Buttons` (last packet received), `GameButtonBits` (held now, as the game reads them), `TotalButtonPresses`, `GetButtonPressCount(id)`, `GetButtonLastPressedHostMs(id)`, `GetButtonLastReleasedHostMs(id)`, `IsButtonLit(id, nowMs, minLitMs)`, `GetLitButtonBits(nowMs, minLitMs)`, `HasScreenRotation`, `ScreenRotationDegrees`, `HapticStrength`, `LayoutsSent`, `PacketsReceived`. Times use the `PadPlayHost.NowMs` clock (−1 = never). |
| `PadPlayDebugEvent` | `HostMs`, `PlayerSlot` (−1 = host), `Kind` (`PadPlayDebugEventKind`), `Detail`. |
| `host.Settings.Preview` | `PadPlayPreviewSettings`: `Mode` (`PadPlayPreviewMode`), `PlayerDisplays`, `ShowTouches`, `ShowButtons`, `OverlayOpacity`, `ShowCaption`, `ButtonHighlightMs` (0), `IsActive(isEditor)`, `GetDisplay(slot)`. |
| `PadPlayDebugOverlay` | `Host`, `Visible`, `ToggleKey`, `BuildText()`. |

Input counts include input of unticked modules (it arrived, the host then dropped it), so you can tell
"the phone sends nothing" from "the module is off".

---

## 5. Trying it without a phone: Virtual Phones

*Window → PadPlay → Virtual Phones* (or **Virtual Phones** in the Host Inspector's *Debug* section) connects simulated phones
to the host while playing in the Editor — like a simulator for a console controller. Each one joins **the same way the phone app does**
(the join URI's token, the WebSocket signaling, WebRTC `input` / `control` channels), so your game cannot tell it from a real phone:
it gets a player slot, `PlayerJoined` / `PlayerReady`, a `PadPlayPad`, uGUI pointer events, `PlayerLeft` / `PlayerRemoved`.
Real phones can join at the same time (for example a real phone is P1 and a virtual one P2).

1. Press **Play**, open the window and press **Add phone** (once per player, up to *Max Players*).
2. Each phone shows the player's screen — the `PadPlayView` texture sent to that player (grey with *no video* when the player has
   no view or the *Video* module is off) — with the on-screen buttons of the player's layout on top.
3. Use it:

| Input | How |
|---|---|
| Touch | Left mouse button on the screen = one finger (press, drag, release). Positions outside the video area are clamped, like on the phone. |
| Buttons and sticks | Left mouse button on a button or a stick of the layout. Buttons with the same *Group* can be slid across while held. |
| Keys | The selected phone (the one you clicked last, outlined in its player color) while the window has focus. Defaults: Space = A (South), Left Shift = B (East), Q = X (West), E = Y (North), Z / C = LB / RB, Return = Start, Tab = Select, arrows = D-pad, WASD = left stick, IJKL = right stick. Change them under **Keys** (saved per computer). |
| Tilt | **Yaw** (turn right = +), **Pitch** (look down = +), **Roll** (lean left = +) sliders. The values are the Euler angles your game reads from `pad.attitude` before any recenter. **Face front** sets them back to 0; **Recenter request** sends the same request as the phone's corner long-press. |
| Vibration | The phone's frame glows while a haptic command is running (brighter = stronger). |
| Disconnect / Reconnect | **Disconnect** drops the connection silently (like Wi-Fi going away): the slot is held for *Resume Hold Seconds*, and **Reconnect** returns to the same player (`PlayerJoined` with `Resumed`). |
| Remove | Leaves with `bye`: the slot is freed (`PlayerRemoved`) and the phone disappears from the window. |

Like the phone app, a virtual phone only sends buttons and sticks that exist in the player's layout, and only while a layout with
buttons was sent (no layout → the keys do nothing). It does not report a screen rotation, so the gyro components use the sliders as they are.
The joined device reads *PadPlay Virtual Phone* (1080 × 2400, landscape) in the Debugger.

Limits: one finger (no multi-touch), no real sensor noise or network loss, and no video is decoded (the window draws the
texture the host is about to send, so encoding problems only show on a real phone).
Virtual phones exist only in the Editor: nothing of them is included in a built game, and they are closed when you stop playing.

---

## 6. Which tool for which problem

| Symptom | Look at |
|---|---|
| The phone never connects | *Connection Check* (or the Debugger's **Check** button): its last section says whether anything reached the PC (nothing / old QR / full), the others point at the Wi-Fi address, the port or the firewall. |
| Stuck in `Connecting` | The data channels do not open: Windows Firewall blocks UDP for Unity. *Connection Check* flags it after 8 s and shows the firewall rule. |
| `Ready`, but the game reacts to nothing | Debugger *Hz* columns: numbers arrive → check the module checkboxes (`(off)`) and your code; zero → the phone app sends nothing (buttons are sent only when a layout with buttons was sent: look for *LayoutSent* in Events). |
| Buttons do nothing | Debugger *Buttons* grid lights up? Yes → your bindings (Input Debugger, `<PadPlayPad>/custom20`…). No → the layout / the phone. |
| Some taps seem missed | Debugger *Buttons* press count: tap N times and compare. N → the game received every press (check how your code reads it, e.g. `isPressed` polled in `FixedUpdate`). Fewer → presses were lost on the way (Wi-Fi; *Btn Hz*). |
| Touches land on the wrong thing | Display preview: is the dot where the finger is? Then check *Touch → Player Targets* and `PadPlayView.UseAsTouchTarget`. |
| The phone shows black or the wrong picture | Display preview: the same texture is sent. Black there too → the source camera / RenderTexture. Fine there → video encoding or the network (Debugger *Video* column: fps and kbps). |
| Gyro feels laggy or jumpy | Debugger *Gyro Hz* (about 60 expected) and *RTT*; tune *Attitude* settings. |
| A player keeps dropping | Events: repeated *Left* / *Resumed*, and the reason (`closed`, `timeout`). |
| Vibration does not happen | Events: *HapticSent* appears? No → *Buttons → Allow Haptics* / strength scale; yes → the phone (silent mode, vibration settings). |
| No phone at hand, or need a second player | Virtual Phones: add one per player. |
| Input System questions (actions, `PlayerInput`, pairing) | Unity's Input Debugger: each phone is a `PadPlayPad` device (a `Gamepad`). |
