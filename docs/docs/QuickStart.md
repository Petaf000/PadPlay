---
title: Quick Start
lang: en
---

# Quick Start

> This is the online copy of the documentation that ships inside the package (`Documentation~`). SDK 0.2.0. [All pages](./) · [Support](https://github.com/Petaf000/PadPlay)

Get from an empty Unity project to "my phone controls the game" in about ten minutes.

You will: install the package → add a `PadPlayHost` → show a QR code → connect the phone app →
receive touch, gamepad and gyro input → (optionally) stream a second screen, place on-screen buttons and rumble the phone.

> PadPlay SDK is in early development (0.2.0). Names may still change before 1.0.
> For the ideas behind the design, see [Concepts](Concepts.md); for the Editor tools, [Debugging](Debugging.md).
> All pages: [Documentation index](index.md).
>
> *Window → PadPlay → Welcome* (it opens once by itself after you install the package) has the same first steps,
> imports and opens a sample with one click, and links to the phone app and these pages.

---

## 1. Requirements

| | |
|---|---|
| **Unity** | Unity 6 (`6000.0` or newer). Developed and tested on 6000.6 (Windows, Direct3D 12). |
| **Packages** | Installed automatically as dependencies: Input System (`com.unity.inputsystem` 1.11.2+), uGUI (`com.unity.ugui`). Nothing else: the connection is the SDK's own *PadPlay Link* over UDP. |
| **Active Input Handling** | *Project Settings → Player → Active Input Handling* = **Input System Package (New)** or **Both**. |
| **PC (the host)** | Windows 10 / 11, in the Editor or a Windows x64 standalone build. macOS and Linux hosts are not tested yet (and send video as JPEG only, see below). |
| **GPU** (second-screen video only) | H.264 is encoded by Windows Media Foundation (the GPU's hardware encoder when one works, otherwise Windows' software encoder) from Direct3D 11 / 12 without leaving the GPU; Vulkan reads the frame back first (slower). Where H.264 cannot be used (OpenGL, macOS / Linux, Windows without an H.264 encoder) the video is sent as JPEG. Touch, gamepad and gyro do not need a GPU encoder. |
| **Render pipeline** | **Built-in** and **URP** (Universal Render Pipeline). The second-screen video works the same in both, with no setup. HDRP has not been tested yet. The samples' 3D materials are made for the Built-in pipeline: in a URP project, convert them once (see [Samples](#12-samples)). |
| **Phone / tablet** | Android 7.0 or newer, or an iPhone with iOS 13 or newer, with a gyroscope, running the separate **PadPlay** app (see step 5). |
| **Network** | PC and phone on the **same Wi-Fi / LAN**. Everything uses one **UDP** port (7780 by default). Internet play is out of scope. |

### What you need besides Unity

| | |
|---|---|
| **The PadPlay phone app** | Every player needs the separate **PadPlay** app on their phone. It is not part of this package. One generic app works with every PadPlay game; there is no per-game app. It is not in the stores yet: build and install it yourself (step 5). |
| **The same Wi-Fi** | The PC and the phones must be on the **same Wi-Fi / LAN**. PadPlay never goes over the internet and needs no internet connection. Guest networks and routers with "AP isolation" (privacy separator) keep devices apart: use a normal home or office network. |
| **Windows Firewall** | On the first Play, Windows asks whether Unity may use the network: choose **Allow** on **Private** networks, and make your Wi-Fi a Private network. Your built game is a separate program and asks again. See [Troubleshooting](#11-troubleshooting). |

### Tested setup

| | Tested with |
|---|---|
| PC | Windows, NVIDIA GPU, Direct3D 12, Unity 6000.6 |
| Android | Android 10, Samsung Galaxy S9+ (one device) |
| iPhone | Not yet on a device with this version: the current build (PadPlay Link) builds and passes its encryption test vectors in CI, but has not been tried on an iPhone. An earlier build of the app (before PadPlay Link) ran on one iPhone |

Direct3D 11, Vulkan and OpenGL Core (JPEG) are covered by automated tests on the same PC, and so is the video in a URP project
(URP 17.6 on Unity 6000.6). Other GPUs' hardware encoders,
Windows' software encoder, macOS / Linux hosts and other phones may work but have not been tested yet.

### What it cannot do (yet)

| | |
|---|---|
| **macOS / Linux host** | No H.264 encoder there yet: the second screen is sent as JPEG (larger, slower). Not tested. |
| **Joining from a web browser** | Not in this version: every player needs the PadPlay app. Planned as a free 1.x update. |
| **Internet play** | Same LAN only (no relay server, no STUN / TURN). |
| **HDRP** | Not tested yet. |

### PadPlay Lite

The free **PadPlay Lite** edition works exactly like the full version, with these limits: **1 player** (*Max Players* is capped at 1),
**no second-screen video** (`PadPlayView` and the video encoder are not included; the *Video* module shows a note and does nothing),
only the *Connect Demo* sample (without video), and no phone app source code. Sections of this page about video or several players
apply to the full **PadPlay SDK** only.

---

## 2. Install the package

**From a git URL** — *Window → Package Manager → + → Install package from git URL…*

```
https://github.com/<owner>/<repo>.git?path=/packages/com.petaf.padplay
```

(The repository is not public yet. Replace `<owner>/<repo>` with the location you were given.)

**From a local folder** — *Install package from disk…* and pick `packages/com.petaf.padplay/package.json`,
or add it to `Packages/manifest.json`:

```json
"com.petaf.padplay": "file:../../path/to/packages/com.petaf.padplay"
```

Unity resolves the dependencies (Input System, uGUI) on the next refresh.

---

## 3. Add a PadPlayHost

**One click:** *GameObject → PadPlay → PadPlay Setup*. It adds, as one undoable step:

| Object | What it is |
|---|---|
| `PadPlay` | The `PadPlayHost` (selected afterwards). |
| `PadPlay UI` | A Screen Space – Overlay Canvas (sorting order 100, no raycaster, so it never catches touches) with a **QR code** (`PadPlayQrView`, bottom right), its caption (game name and join URL) and a **player list** (`PadPlayStatusView`, top left). Move, resize or delete them as you like. |
| `EventSystem` | Only if the scene has none: an EventSystem with the Input System UI module. An existing EventSystem is left as it is. |

If the scene already has a `PadPlayHost`, nothing is added; the existing host is selected.

**By hand** (same result):

1. Create an empty GameObject (for example `PadPlay`).
2. *Add Component → PadPlay → Host*.
3. If your scene has uGUI, keep (or add) the usual **EventSystem**. Phone touches are sent through it.

That is all the setup the Host needs. With **Auto Start** on (default) it starts listening when you press Play.

### The Host Inspector

The Inspector is laid out top to bottom as **Players → Modules → Debug → Connection** (plus a status line while playing).
Tick the modules your game uses; each checkbox opens the module's settings right below it.
An unticked module is folded and greyed out, and its values are kept.
Hover over any field for a short explanation.

Most values can be changed **while playing** and take effect at once, so you can tune by feel.
Fields read only when the host starts are greyed out during Play and marked *(locked in Play mode)*.

**Players** (`Settings.Players`)

| Field | Default | What it does | While playing |
|---|---|---|---|
| Max Players | 4 | How many phones may join. | No |
| Resume Hold Seconds | 60 | A phone that drops (Wi-Fi blip, app in background) gets the same player slot back within this time. After it, `PlayerRemoved` fires. | No |
| Accept New Players | on | Off = new phones are refused as "full"; dropped players can still come back. Same as `host.AcceptNewPlayers`. | Yes |
| Calibrate Yaw On Connect | on | On (re)connect, the direction the player faces becomes forward. | Next connection |
| Create Devices | on | Create a `PadPlayPad` (Gamepad) per player in the Input System. | Next connection |

**Modules** (`Settings.Modules`) — the *Preset* menu next to the header (also the Host's context menu) picks a common set:
*Touch only*, *Gyro controller* (attitude + buttons) or *Second-screen controller* (everything). Presets change only the checkboxes.

A disabled module is ignored by the host: its input is dropped (no uGUI events, no `TouchReceived` / `AttitudeReceived` /
`ButtonsReceived`, nothing on the pad) and nothing is sent for it (no buttons, no haptics, no video).

| Module | Field | Default | What it does |
|---|---|---|---|
| **Touch** (`Settings.Touch`) | Drag Threshold Mode | EventSystem | How far a finger moves before a drag starts: *EventSystem* (same as the mouse), *Pixels*, or *PhoneMillimeters* (the same finger movement on any phone; converted per player from the phone's pixel ratio and screen size). |
| | Drag Threshold Mm / Px | 1.5 mm / 10 px | The distance for the chosen mode. Px is also the fallback when a phone's size is unknown. |
| | Raycast Only Player Screen | on | A player's touches only hit that player's screen (camera or display area). |
| | Alpha threshold | | Shortcut to the *Alpha Threshold* of every `PadPlayRaycaster` in the scene (touches pass through transparent pixels). |
| | Stale Timeout Seconds | 0 | Release a finger that sends nothing for this long (0 = never). |
| | More: Send Ui Events, Click Speed Seconds, Player Targets | | uGUI events on/off, double-tap interval, which screen each player touches (step 6). |
| **Attitude** (`Settings.Attitude`) | Max Extrapolation Ms | 150 | How far ahead the attitude is predicted when Wi-Fi packets are late. |
| | Correction Ms | 50 | How quickly the prediction error is blended out (0 = jump). |
| | Delay Buffer Ms | 0 | Deliberate delay to interpolate between packets (smoother, later). |
| | Smoothing | 0 | Extra smoothing of `pad.attitude` (0 = none, higher = steadier but laggier). |
| | Full Style | KeepGravity | How `Calibrate(Full)` defines forward. |
| | Recenter Mode | Yaw | How a *Recenter* button or the phone's corner long-press resets forward (step 8). |
| | Allow Phone Recenter | on | Accept the phone's corner long-press (step 8). Off = only your buttons and code. |
| **Buttons** (`Settings.Buttons`) | Layout | none | On-screen buttons for every phone (step 10). *New* creates a layout asset with a stick, A and B. |
| | Allow Haptics | on | Off = vibration is never sent (a running vibration stops). |
| | Haptic Strength Scale | 1 | Multiplies every vibration strength. |
| | Haptic Refresh / Duration / Min Change | 100 ms / 150 ms / 0.05 | How vibration is resent to the phone (rarely needs changing). |
| **Video** (`Settings.Video`) | Codec | Auto | *Auto* = H.264; JPEG for a phone that asks for it (its H.264 decoding is slow), and for everyone when this PC has no H.264 encoder. *H264* / *Jpeg* force one (a forced H264 still falls back to JPEG when this PC has no H.264 encoder). |
| | Adaptive Video / Min Kbps / Priority / Min Fps / Min Scale | on / 300 / Quality / 30 / 0.5 | Lowers the bitrate, then the frame rate or the size, when the Wi-Fi gets worse, and raises them again slowly. *Quality* keeps the size (frame rate first); *Smoothness* keeps the frame rate (size first). |
| | Total Max Kbps | 0 | Upper limit for all phones together; each phone gets at most this divided by the players (0 = none). |
| | Jpeg Scale / Fps / Quality | 0.5 / 30 / 75 | The JPEG fallback: size as a fraction of the view's size, frame rate, starting quality (20–90; then it follows the bitrate). |
| | Pacing Mbps / Max Playout Frames / Use Ltr Recovery | 20 / 2 / on | How fast a frame's pieces are sent (0 = all at once); the most frames a phone waits to smooth uneven arrival (0 = never; it waits only while the arrival is uneven); repair lost video with small recovery frames instead of key frames. **Read when the host starts.** |
| | Prefer Readback | off | Copy each frame through the CPU even on Direct3D 11 / 12 (a workaround if the GPU path misbehaves; slower). |
| | Views | | Every `PadPlayView` in the scene with its player, size, frame rate, bitrate limit and *Match Phone Aspect* (step 9). With none, *Create a camera and attach* adds one. |

Module settings may change while playing, except the three Video values marked above. Codec, Adaptive Video and the JPEG values
apply when a player's video (re)starts. Turning **Video** on or off, or changing a View, restarts the affected players' video
(from a key frame; the connection and the slot stay).

**Debug** (folded) — the Display 2 / 3 preview of each phone (`Settings.Preview`), **Open Debugger** and
**Add Debug Overlay**. See [Debugging](Debugging.md).

**Connection** (folded; usually left alone)

| Field | Default | What it does | While playing |
|---|---|---|---|
| Port | 7780 | UDP port for everything (join, input, video). If busy, the next free port is used and put in the QR. | No |
| Game Name | *(product name)* | Shown on the phone. | No |
| Host Addresses | *(automatic)* | IP addresses put in the QR. Set when the PC has several network adapters. | No |
| Loopback Only | off | Listen on 127.0.0.1 only (tests). | No |
| Verbose Log | off | Logs the connection steps and the join URL to the Console. Handy for the first try. | Yes |
| Display Language | Auto | Language of the texts players see (QR caption, Status View). Auto = Japanese on Japanese systems, otherwise English. | Yes |

The editor windows and Inspector tooltips can be shown in Japanese: pick *Auto* / *English* / *日本語* at the top right of the
Welcome window or in *Preferences → PadPlay* (Japanese terms: [Glossary-ja](Glossary-ja.md)).

From code the same values are under `host.Settings`, for example `host.Settings.Players.MaxPlayers`,
`host.Settings.SetEnabled(PadPlayModules.Touch, false)` or `host.Settings.ApplyPreset(PadPlayModulePreset.TouchOnly)`.
Scenes saved with an older version of the SDK are upgraded when loaded (the values move to their new place).

Player numbers are **slots starting at 0** (`PlayerSlot` 0 is "P1" on screen).

---

## 4. Show the QR code

*PadPlay Setup* (step 3) already did this. By hand:

1. *GameObject → UI → Raw Image* (any Canvas).
2. On the Raw Image: *Add Component → PadPlay → QR View*.
3. Optional: assign a legacy **Text** to *Caption* to print the game name and the join URL under the code
   (players can type the URL if the camera cannot read the QR).

The QR View finds the Host in the scene, redraws whenever the join URL changes and hides itself while the Host is stopped.
For an on-screen list of connected players, add *PadPlay → Status View* to a Text.

The QR code carries the PC's address, the UDP port, a token and a fresh key. The key never travels over the network: the phone
uses it to encrypt and authenticate everything it sends and receives, so other devices on the Wi-Fi can neither read nor fake the input
or the video. It changes every time the host starts. There is no setting to turn encryption off.

To invalidate the current QR code (for example between matches) call `RenewToken()`: it makes a new token and key.
Phones that already joined stay connected. A joined phone whose Wi-Fi drops shortly after the renewal can still come back to
its slot with the old code for `host.LinkSettings.RenewGraceMs` (60 s by default, 0 = off); it proves itself with a secret
it received when it joined, so a new phone always needs the new QR code.

```csharp
using PadPlay.Unity;
using UnityEngine;

public class Lobby : MonoBehaviour
{
    [SerializeField] PadPlayHost host;

    void OnEnable()
    {
        host.PlayerReady += p => Debug.Log($"P{p.PlayerSlot + 1} ready: {p.Device.Model} (resumed: {p.Resumed})");
        host.PlayerLeft += left => Debug.Log($"P{left.PlayerSlot + 1} left ({left.Reason}), may come back");
        host.PlayerRemoved += removed => Debug.Log($"P{removed.PlayerSlot + 1} is gone ({removed.Reason})");
    }

    public void NewQrCode() => host.RenewToken();

    public void StartMatch() => host.AcceptNewPlayers = false;   // players who drop can still come back
}
```

---

## 5. Connect the phone

1. Install the **PadPlay** app on the phone. There is no store release yet: build it from `client/`
   in the SDK repository (Flutter; on Android `flutter build apk` and install the APK) — step by step in `client/README.md`.
2. Press **Play** in Unity.
3. On first run Windows may ask whether Unity may use the network: choose **Allow** (Private networks).
4. In the app, scan the QR code (or paste the URL shown under it) and tap connect.
   *(The app follows the phone's language: the connect button reads "Connect" in English and 「つなぐ」 in Japanese.)*

`PlayerJoined` fires when the phone is accepted and gets its slot; `PlayerReady` fires right after it,
when input starts flowing (once per join). If it never becomes ready, see [Troubleshooting](#11-troubleshooting).

A player slot goes through these states (`host.GetPlayerState(slot)`):

| State | Entered by | Meaning |
|---|---|---|
| `Connecting` | `PlayerJoined` | Slot assigned, input not flowing yet. `PlayerJoined.Resumed` is true when the same phone came back. |
| `Ready` | `PlayerReady` | Input arrives. `GetPad(slot)` already returns the player's pad. |
| `Reconnecting` | `PlayerLeft` | The phone dropped; the slot and the pad are kept for *Resume Hold Seconds*. |
| `Gone` | `PlayerRemoved` | The slot is free for good (`bye`, `stopped`, or `expired`). The pad is already removed: remove the player's character here. |

A phone also gets its old slot back when its app was closed and opened again (the app remembers a device id),
as long as it comes back within *Resume Hold Seconds* and did not disconnect on purpose.

`host.AcceptNewPlayers = false` refuses new phones (they see "full") while letting dropped players resume.
`host.ScreenRotationChanged` / `host.GetScreenRotation(slot)` tell you when a player turns the phone sideways.

---

## 6. Touch arrives as normal uGUI events

You do not need to change your UI code. Touches on the phone are turned into the standard
`OnPointerDown`, `OnDrag`, `OnPointerClick`, … events, so **Buttons, Sliders, ScrollRects and your own handlers just work** —
and the mouse keeps working at the same time for debugging.

Only if you want to know *who* touched, check for `PadPlayPointerEventData`:

```csharp
using PadPlay.Unity;
using UnityEngine;
using UnityEngine.EventSystems;

public class WhoTouchedMe : MonoBehaviour, IPointerDownHandler
{
    public void OnPointerDown(PointerEventData eventData)
    {
        if (eventData is PadPlayPointerEventData phone)
            Debug.Log($"P{phone.PlayerSlot + 1}, finger {phone.FingerId}, at {phone.PhonePosition} on the phone");
        else
            Debug.Log("mouse / local touch");
    }
}
```

By default a phone touches the whole of Display 1. To give each player their own screen (split screen, a second display,
or a camera that renders into a RenderTexture), point the player at a camera:

```csharp
host.Touch.SetPlayerTarget(playerSlot: 1, camera: player2Camera);
```

or fill *Touch → More → Player Targets* in the Host Inspector. Touches are then hit-tested only against that player's
camera (its Canvases and, with a `PhysicsRaycaster` on the camera, its 3D objects).
`PhonePosition` is where the finger is on the phone screen (0…1, **y = 0 at the top**); `position` is the usual pixel
position on the target screen.

**Transparent parts of images:** to let touches pass through the transparent pixels of a sprite, replace the Canvas's
`GraphicRaycaster` with *PadPlay → Raycaster* and enable **Read/Write** on the sprite's texture
(see Troubleshooting). It works for the mouse too.

---

## 7. Read the phone as a Gamepad

Every ready phone becomes a `PadPlayPad` device in the Input System. `PadPlayPad` **is a `Gamepad`**, so existing
bindings like `<Gamepad>/buttonSouth` and `<Gamepad>/leftStick`, `Gamepad.all` and `PlayerInput` work unchanged.
On top of the Gamepad it adds:

| Control | Binding path | Type |
|---|---|---|
| attitude | `<PadPlayPad>/attitude` | Quaternion (Unity's left-handed space, calibrated) |
| angularVelocity | `<PadPlayPad>/angularVelocity` | Vector3, rad/s |
| custom14 … custom31 | `<PadPlayPad>/custom20` | Button (game-defined buttons, step 10) |
| playerSlot | `<PadPlayPad>/playerSlot` | Integer (from C#, use `pad.PlayerSlot`) |
| recenter | `<PadPlayPad>/recenter` | Button: the player asked to reset forward (step 8). The SDK already re-centers by itself |

Reading it directly from C#:

```csharp
using PadPlay.Unity;
using UnityEngine;

public class PadReader : MonoBehaviour
{
    [SerializeField] PadPlayHost host;

    void Update()
    {
        PadPlayPad pad = host.GetPad(0);          // P1, or null if not connected
        if (pad == null || !pad.IsConnected)
            return;

        if (pad.buttonSouth.wasPressedThisFrame)
            Debug.Log("A pressed");
        Vector2 move = pad.leftStick.ReadValue();
        Quaternion attitude = pad.attitude.ReadValue();
        transform.rotation = attitude;
    }
}
```

Notes:

- The phone shows buttons and sticks **only when you send a layout** (step 10). Without one, Gamepad buttons stay released.
- While a phone is reconnecting, its device is kept (`IsConnected == false`, buttons released, attitude frozen)
  so your `PlayerInput` pairing survives a Wi-Fi blip. It is removed just before `PlayerRemoved`.
- The attitude is identity when the phone is held upright in portrait with the screen facing you. On connect the SDK
  aligns the yaw so "where the player faces now" is forward. To re-center later: `host.Recenter(slot)` or `pad.Recenter()`
  (step 8).

---

## 8. Gyro camera and pointer

For the common case you do not need to read the attitude yourself.

**Gyro camera** — add *PadPlay → Gyro Camera* to a Camera (or its parent) and set **Player Slot**.
Its starting rotation is "forward". Modes (*Settings → Mode*, changeable while playing):

| Mode | Feels like |
|---|---|
| Window | The phone is a window into the scene: turn the phone, the view turns with it. |
| LookAround | Yaw and pitch only, no roll; sensitivity and pitch limit are adjustable. |
| Tilt | Tilting the phone turns the camera at a speed (like a stick). `Tilt` (−1…1) is also exposed for moving characters. |

```csharp
var gyroCamera = GetComponent<PadPlayGyroCamera>();
gyroCamera.Mode = PadPlayGyroMode.LookAround;
Vector2 tilt = gyroCamera.Tilt;
```

**Gyro pointer** — *PadPlay → Gyro Pointer* turns the phone into a laser pointer:
`ViewportPosition` (viewport space: **y = 0 at the bottom**, center = 0.5, 0.5), `GetScreenPosition()`, `GetRay(camera)`
and an optional **Reticle** (`RectTransform`) that follows it.

Naming: the components are called *Gyro* (what you add), the data is called *attitude* (`AttitudeReceived`,
`pad.attitude`, the `Attitude` module).

All tuning values (sensitivity, smoothing, dead zones, re-center blend time) are in *Settings* and can be tweaked while playing.

### Re-centering ("make this direction forward")

**Why it is needed.** The phone fuses gyro and accelerometer (Android Game Rotation Vector, iOS Core Motion). Gravity keeps
pitch and roll correct, but nothing anchors the **heading (yaw)**, so "forward" slowly drifts over minutes of play
(faster near magnets or after quick spins). Restarting the app also changes the phone's yaw reference.

A re-center resets the player's forward **everywhere at once**: their `PadPlayPad` attitude and every Gyro Camera /
Gyro Pointer reading that player (each blends over its own *Recenter Blend Ms*, so the view does not jump). Other players
are not affected. `Recenter Mode` *Yaw* (default) keeps gravity (the horizon stays level); *Full* makes the whole current pose forward.

Three ways to give players a re-center button — pick one:

1. **No code: a layout button with a role.** In your button layout asset, set the element's **Role** to *Recenter*
   (e.g. a Select button labelled "Recenter"). Pressing it re-centers that player. The button still reaches the game as a normal button.
2. **One line of code**, e.g. from your pause menu:
   ```csharp
   host.Recenter(playerSlot);                       // Yaw; or host.Recenter(slot, AttitudeCalibrationMode.Full)
   host.RecenterAll();                              // everyone
   pad.Recenter();                                  // same as host.Recenter(pad.PlayerSlot)
   ```
3. **Your own InputAction**: bind `<PadPlayPad>/recenter`. It is pressed while a *Recenter* role button is held and for
   one update when the player long-presses the phone's corner. Use it to react (sound, hint); the SDK has already re-centered.

**Phone-side fallback.** Even without any button, the player can **long-press the top-left corner of the phone screen for
1 second** (a faint crosshair marks it). The phone sends a `recenter` request; turn *Attitude → Allow Phone Recenter* off
if your game must not allow it.

**Event.** `host.Recentered` tells you who and why (`Reason`: Code, Button, LongPress, Connect):

```csharp
host.Recentered += r => Debug.Log($"P{r.PlayerSlot + 1} recentered by {r.Reason}");
```

`gyroCamera.Calibrate()` still re-centers **that component only**. The old *Calibrate With Button* (Select) option on the
Gyro components is now off by default; use a *Recenter* role button instead.

---

## 9. Second screen (video)

To show a picture on the phone — the controller's second screen — add *PadPlay → View* to any GameObject
(or press *Create a camera and attach* in the Host Inspector's **Video** module). The Video module must be ticked (it is by default):

- **Source Camera**: a camera whose output goes to the phone only (the SDK creates its RenderTexture), **or**
  **Source Texture**: a RenderTexture you render yourself.
- **Player Slot**: who sees it (`-1` = everyone gets the same picture).
- Width / Height (default 1280 × 720), Frame Rate (60), Max Bitrate Kbps (0 = 2500). The host lowers the bitrate,
  frame rate and size from there when the Wi-Fi gets worse (*Video → Adaptive Video*).
- **Use As Touch Target** (on): that player's touches land on this camera's view, so tapping the picture on the phone
  hits the UI / objects shown in it.

While nobody is watching, the source camera is disabled so it costs nothing.

**How the picture is sent.** H.264 by default, through Windows Media Foundation (the GPU's hardware encoder when one works).
The graphics API decides the path: Direct3D 11 and 12 hand the frame to the encoder without leaving the GPU; Vulkan copies it
through the CPU (about one frame later); OpenGL, macOS and Linux have no H.264 path and send JPEG. A phone whose H.264 decoding
turns out slow asks for JPEG by itself (*Codec = Auto*). Lost pieces are repaired with forward error correction; what it cannot repair is
fixed with a small recovery frame (when the encoder supports it, *Use Ltr Recovery*) or else a key frame. `host.VideoStatus` says which path and encoder are used, or why
H.264 cannot be used; `host.GetVideoChoice(slot, out choice)` tells whether a player gets H.264 or JPEG and why.

**Render pipelines.** The frame is taken after every camera has drawn it, so overlays drawn by other cameras into the same
texture are included. In the Built-in Render Pipeline a hidden camera does this; in URP (and other Scriptable Render Pipelines)
it happens at the end of the frame's rendering (`RenderPipelineManager.endContextRendering`). Nothing to set up either way.
In URP the *Source Camera* must be a **Base** camera: an Overlay camera only draws as part of its Base camera's stack.

---

## 10. On-screen buttons and haptics

### Button layout

1. *Assets → Create → PadPlay → Button Layout*.
2. Add **Elements**. Each element has an **Id** (`South`, `East`, …, `DpadUp`, `Select`, `Custom14`…`Custom31`,
   `LeftStick`, `RightStick`), a **Shape** (Circle / Rect; sticks are always sticks), **Center** and **Size**,
   a **Label**, **Opacity**, an optional **Group** and a **Role**.
   Center and Size are fractions of the video area on the phone, with **(0, 0) at the top-left**.
   Buttons in the same Group (for example a D-pad or ABXY) let a finger slide from one to the next.
   Role *Recenter* makes the SDK reset that player's forward when the button is pressed (step 8); *None* (default) does nothing extra.
3. Assign the asset to the Host: *Buttons → Layout* in the Host Inspector (everyone), or per player with `SetLayout`.

Editing the asset while playing updates the phones on the next frame, so you can tune positions by feel.

```csharp
using PadPlay.Unity;
using UnityEngine;

var layout = PadPlayButtonLayout.Create(
    new PadPlayButtonLayout.Element(PadPlayButtonId.LeftStick, PadPlayButtonShape.Circle,
        new Vector2(0.15f, 0.7f), new Vector2(0.25f, 0.45f)),
    new PadPlayButtonLayout.Element(PadPlayButtonId.South, PadPlayButtonShape.Circle,
        new Vector2(0.85f, 0.75f), new Vector2(0.1f, 0.18f), "A"),
    new PadPlayButtonLayout.Element(PadPlayButtonId.Custom20, PadPlayButtonShape.Rect,
        new Vector2(0.5f, 0.9f), new Vector2(0.2f, 0.1f), "Map"));

host.SetLayout(0, layout);       // P1 only; null = back to Settings.Buttons.Layout
// After changing layout.Elements from code, call layout.MarkChanged().
```

The phone app makes every button a little easier to hit: a finger up to 2.5 mm outside a button still presses it
(the nearest button wins), and a pressed button lights up and gives a short vibration. This is set in the app, not by the game.

Short taps are not lost: button changes are also sent on the reliable channel, and a press and release that arrive
in the same frame are spread over two frames, so `wasPressedThisFrame` sees every press.

Ids 0–13 map to the matching Gamepad buttons. Any id is read as `pad.GetButton(PadPlayButtonId.Custom20).isPressed`
(or `pad.GetButton(20)`), and game-defined ids are bound as `<PadPlayPad>/custom20`.

### Haptics

`PadPlayPad` implements the Gamepad rumble API and forwards it to the phone's vibration motor:

```csharp
pad.SetMotorSpeeds(0.6f, 0.6f);   // strength = max(low, high); phones have one motor
pad.SetMotorSpeeds(0f, 0f);       // stop
```

`PauseHaptics`, `ResumeHaptics`, `ResetHaptics` and `InputSystem.ResetHaptics()` work as usual.
The vibration continues until you set it to zero (and stops by itself shortly after the Host stops).

Without the Input System (or with *Players → Create Devices* off), use the Host:

```csharp
host.SetHaptics(playerSlot: 0, strength: 0.6f);   // same throttling as SetMotorSpeeds
host.SetHaptics(playerSlot: 0, strength: 0f);     // stop
```

For a single buzz of a set length (a hit, a pickup), use `Pulse`. The phone times it itself, so a hitch in the game does not
stretch or cut it, and you do not have to send 0 afterwards. A continuous vibration keeps running: the phone plays the pulse
on top and goes back to it when the pulse ends.

```csharp
host.Pulse(playerSlot: 0, durationMs: 250, strength: 1f);   // false if the player is away or haptics are off
```

The phone app caps one vibration at 1 second. Older phone apps play a pulse like a `SetHaptics` command (a running continuous
vibration then cuts it short).

---

## 11. Troubleshooting

For seeing what each phone shows and what arrives from it — the Display 2 / 3 preview, *Window → PadPlay → Debugger*
and an in-game overlay — see [Debugging](Debugging.md).

**Cannot connect? Open *Window → PadPlay → Connection Check*.** It checks the items below in this order, says what it found
and what to do next, and gives you commands to copy. It only reads: it never changes the firewall, the network or the host.
*Copy report* puts the result on the clipboard (handy for a bug report; it contains no token or key).
Network and firewall are checked at any time; the port and the connection attempts while playing.

1. **Host.** A `PadPlayHost` is in the scene, running, and *Connection → Loopback Only* is off.
   → No host: *GameObject → PadPlay → PadPlay Setup*. Not running: turn on *Auto Start* or call `host.StartHost()`.
2. **Network and QR address.** The QR code lists this PC's IPv4 addresses; the phone tries them in order.
   The first one should be on your **Wi-Fi or Ethernet** adapter, and the phone must be on that same network
   (not a guest network, no "AP isolation" on the router).
   → A virtual adapter (Hyper-V, WSL, VirtualBox, VMware, Docker) or a VPN (Tailscale, Radmin, ZeroTier, …) comes first,
   or the QR has no Wi-Fi address: put your Wi-Fi IP in *Connection → Host Addresses* (or disable that adapter).
   An address that is no longer this PC's: fix or clear *Host Addresses* (empty = automatic).
3. **Listening port.** The host listens on UDP port 7780 (*Connection → Port*; if it is busy, the next free port is used and put in the QR).
   → Nothing listens: stop and press Play again and look for PadPlay errors in the Console.
4. **Windows Firewall** — for the **Unity Editor**, and for your **built game** (the last Windows build location) if there is one.
   They are separate programs and each needs permission. Permission is per program, not per port (everything uses one UDP port).
   - *Allowed*: fine.
   - *No rule yet*: Windows asks on the first connection; choose **Allow** on **Private** networks.
   - *Blocked*: someone pressed *Cancel* on that dialog, which creates a **Block** rule — Block rules win over Allow rules.
     Disable it in *Windows Defender Firewall → Advanced settings → Inbound Rules* (the rule for `Unity.exe` / your game's exe), then allow the program.
   - *Allowed only on Private networks, but this network is Public*: make the Wi-Fi a **Private** network.
   - *Only TCP allowed*: nothing gets through (PadPlay uses UDP); allow UDP or all protocols.
   - *Could not read*: check by hand in *Windows Security → Firewall & network protection → Allow an app through firewall*.

   The commands it offers (run them yourself in an **administrator** Command Prompt / PowerShell):

   ```
   netsh advfirewall firewall add rule name="PadPlay Unity Editor" dir=in action=allow program="C:\Program Files\Unity\Hub\Editor\<version>\Editor\Unity.exe" enable=yes profile=private
   ```
   ```powershell
   Set-NetConnectionProfile -InterfaceAlias "Wi-Fi" -NetworkCategory Private
   ```
   Without administrator rights the check cannot tell TCP rules from UDP rules; it then says so.
5. **Last connection attempt** — what reached the host since it started (also `host.Diagnostics.LastJoinAttempt`):

   | Result | Meaning | Next |
   |---|---|---|
   | Nothing reached this PC | The phone's connection never arrived. | Same Wi-Fi (2), the QR address (2), the firewall (4). |
   | Old QR code | The phone used a code from an earlier Play or from before `RenewToken()` (its token or key does not match). Joined phones get a grace period after `RenewToken()` (step 4); new phones do not. | Scan the QR code that is on screen now. |
   | Full | All *Max Players* slots are taken, or *Accept New Players* is off. | Raise *Max Players* or accept new players. |
   | Other protocol version | The phone app and the SDK do not match. | Update the app (or the SDK). |
   | Joined | The join worked. | — |
   
The Debugger (*Window → PadPlay → Debugger*) has a **Check** button next to the join URL that opens the same window,
and its Events list shows every refused join (*JoinRejected*). *Verbose Log* prints the connection steps and the join URL to the Console.

**Touch ignores transparent pixels / "texture is not readable" warning.**
`PadPlayRaycaster` needs to read the sprite's pixels. Enable **Read/Write** in the texture's import settings
(*Advanced → Read/Write*); for sprites in a Sprite Atlas enable it on the atlas. Unreadable textures fall back to
rectangle hit-testing.

**Touches hit the wrong UI.**
A player with a camera target only hits that camera's view. Check *Touch → More → Player Targets* and
`PadPlayView.UseAsTouchTarget`. `RaycastOnlyPlayerScreen = false` restores "hit whatever is in front".

**Video is black, slow or blurry.**
- Use Direct3D 11 / 12 (fastest) or Vulkan (one frame later). OpenGL, macOS and Linux send JPEG only.
- The hardware encoder is tried first, then Windows' software encoder (slower). Only NVIDIA's hardware encoder has been tested so far.
- The Inspector and `host.VideoStatus` say which path and encoder are used, or why H.264 cannot be used (then JPEG is sent);
  `host.GetVideoChoice(slot, out choice)` tells which one a phone gets and why (for example, the phone asked for JPEG).
- On a weak Wi-Fi keep *Adaptive Video* on (it lowers the bitrate, then the frame rate or size), pick the *Priority* you prefer,
  or lower the view's *Max Bitrate Kbps*. A view sent to several players is encoded once, at the quality of the weakest connection.
- Black even in the Display preview: the source camera or RenderTexture. In PadPlay Lite there is no video.

**"Forward" is off after the player restarts the app.**
That is expected (the phone's yaw reference changes, and yaw slowly drifts during play). Re-center (step 8): a layout
button with Role *Recenter*, `host.Recenter(slot)`, or the player long-presses the top-left corner of the phone screen.

**The phone does not vibrate.**
Check that *Buttons* is ticked and *Allow Haptics* is on (vibration belongs to the Buttons module) and that
*Haptic Strength Scale* is above 0. On the phone, the system vibration setting or Do Not Disturb can silence it.
The Debugger's Events list shows *HapticSent* when the host sent a vibration.

---

## 12. Samples

Import a sample from *Package Manager → PadPlay SDK → Samples → Import*, then open its scene —
or press **Import and open** in *Window → PadPlay → Welcome*.

| Sample | Scene | Shows |
|---|---|---|
| Treasure Map | `Scenes/TreasureMap` | Asymmetric play: the TV shows the island everyone shares, each phone a private map with that player's own treasure. Per-player second screen, touch on the map, haptics. The best way to see what PadPlay is for. |
| Floating Island | `Scenes/FloatingIsland` | Hold the phone up like a window: it shows a floating island in the direction you point it, while the TV circles the island and draws where each phone is looking. Find five lights that only phones can see. `PadPlayPoseCamera`, taps on 3D objects, `PadPlayPhoneOverlay`, `host.Pulse`. Up to two players; needs a layer named PhoneOnly at index 8 (see below). |
| Connect Demo | `Scenes/ConnectDemo` | QR, touch on uGUI, buttons, attitude, haptics and a per-player screen. The simplest connection test. |
| Gyro Window | `Scenes/GyroWindow` | Two players look into a 3D room through their phones (`PadPlayGyroCamera` + `PadPlayView`); the three gyro modes. |
| Panel Dive | `Scenes/PanelDive` | A small two-player co-op game that uses every module. Also playable with mouse and keys, without a phone. |

**In a URP project.** Treasure Map, Floating Island and Panel Dive use the Built-in *Standard* shader for their 3D objects,
which URP draws pink. Convert them once after importing: select the materials in the sample's `Materials` folder and use
*Edit → Rendering → Materials → **Convert Selected Built-In Materials to Current SRP*** (Standard becomes *Universal Render Pipeline/Lit*;
colors and textures are kept). Avoid *Convert All…* if your project has Built-in materials you want to keep as they are.
Connect Demo and Gyro Window use *Unlit/Color*, which URP draws as is.
The samples' scripts already set both `_Color` and `_BaseColor`, so highlights still work after the conversion.

### Treasure Map (asymmetric play)

Open `Scenes/TreasureMap` and press Play. The TV (Game view) shows an island seen from above; each phone shows
**a map that only its player sees**.

**How to play**

- Scan the QR code with one or more phones. A round starts when the first phone joins and lasts 150 seconds.
- Walk with the **stick** on the phone. Your map shows you (▲), the other players (●) and **your treasure (X)** —
  nobody else sees your X, neither on the TV nor on their phones.
- **Tap the map** to drop a pin (▼): a pillar appears on the TV and the pin on everyone's map. Use it to guide each other.
- The phone buzzes softly when you are near your treasure and strongly when you find it (just walk onto it).
  A new treasure is then buried somewhere else. The player with the most treasures wins the round.
- There is no way to play without a phone: the maps exist only on the phones. To see them in the Editor, use the
  Display 2 / 3 preview ([Debugging](Debugging.md)).

**What it shows**

| SDK feature | Where in the sample |
|---|---|
| Per-player second screen | An overhead orthographic camera renders the island into a RenderTexture; each player's phone UI shows it with that player's own marks, sent by a `PadPlayView` |
| Touch on a `RawImage` | `TreasureMapPhoneMap.OnPointerDown`: `PadPlayPointerEventData.PlayerSlot` tells whose finger it is; `RectTransformUtility` turns it into a map position |
| Buttons | `Layouts/TreasureMapButtons`: the left stick only |
| Haptics without the Input System | `host.SetHaptics(slot, strength)` |
| Choosing modules | Touch, Buttons and Video ticked; Attitude unticked (the sample does not use the gyro) |

All feel values (walking speed, how near counts as "found", vibration strengths, round length) are in
*TreasureMapGame → Settings* and can be changed while playing.

### Floating Island (the phone as a window)

Open `Scenes/FloatingIsland` and press Play. The TV (Game view) slowly circles a floating island; each phone is a window into
the same world, in the direction the player points it ([Pose Camera](PoseCamera.md)). Up to two players.

**Before you play.** The hidden lights are on layer 8, which the TV camera leaves out. Name layer 8 **PhoneOnly** in
*Project Settings → Tags and Layers*. Layers work by number, so the sample runs without the name, but it logs a warning once
per Play, and objects of your own on layer 8 are hidden from this sample's TV.

**How to play**

- Scan the QR code with one or two phones. The QR board on the TV shows while more players can join.
- Point the phone to look around. Drag with one finger to turn sideways; pinch or hold **+** / **−** to zoom.
  **Front** re-centers the view and resets the added turn and the zoom.
- Tap something to inspect it: its name shows on your phone and it glows in your player color for a moment; the phone buzzes softly.
- Five lights are hidden around the island, and only phones can see them. Tap one to find it: the phone buzzes strongly and
  the light appears on the TV too. The TV shows how many were found and what each player inspected last.
- When all five are found, a short celebration plays (a beam of light, spiraling pieces, flowers); after 20 seconds the lights
  hide again. They also hide again when everyone leaves.
- The TV draws a glowing frame where each phone is looking.
- Only phone fingers count, so the mouse alone does not play it. Without a phone, use simulated phones
  (*Window → PadPlay → Virtual Phones*; right-drag on a phone's screen to turn it).

**What it shows**

| SDK feature | Where in the sample |
|---|---|
| Pose Camera | `PadPlayPoseCamera` + `PadPlayView` on each player's camera: the PC renders for the phone's orientation and the phone corrects the turn made since that frame |
| Taps on 3D objects in the frame the player saw | `FloatingIslandPhone.OnPointerUp` → `PadPlayPoseCamera.ScreenPointToRay(eventData)` → `FloatingIslandGame.Inspect` (a physics raycast) |
| Where each phone looks | `FloatingIslandFrustum` reads `PadPlayPoseCamera.TryGetLastFrame` and draws the frame on a layer the phone cameras leave out (UI), so only the TV shows it |
| Text fixed to the phone screen | `PadPlayPhoneOverlay`: the phone draws the text over the video, so it does not move with the correction; its rectangle follows `VisibleViewportRect` |
| Haptics of a set length | `host.Pulse(slot, durationMs, strength)` (the phone times it) |
| Objects only phones see | Layer 8 (PhoneOnly) is left out of the TV camera; a found light moves to *Default* |
| Buttons | `Layouts/FloatingIslandButtons`: Front (Role *Recenter*), + and − |
| Choosing modules | All four ticked (Touch, Attitude, Buttons, Video); *Max Players* 2 |

All feel values (drag turn, zoom range and speed, what counts as a tap, vibration strengths and lengths, how long names and
highlights stay, the celebration length) are in *FloatingIslandGame → Settings* and can be changed while playing.

### Panel Dive (two-player co-op)

*Package Manager → PadPlay SDK → Samples → Panel Dive → Import*, then open `Scenes/PanelDive`.
A small playable game that uses every module of the SDK at once, modeled on the student project behind it:
the TV shows one comic page with four panels, and each phone is a window into the panel its player is in.

**How to play**

- Scan the QR code with one or two phones. Each player dives into a panel (reading order: top-right, top-left, bottom-right, bottom-left).
- Tilt the phone to look around the room and find the hidden stars. Touch a star, keep your finger on it and carry it into the
  yellow **exit** frame to collect it (tilting while holding carries the star with your view). The phone buzzes softly when a star is
  near the center of the view and strongly when you collect one.
- With two players, **your stars are visible only while the other player holds A** — talk to each other.
  Alone, the stars are always visible. Collect all stars on the page to turn to the next one.
- **Select** ("Recenter", Role *Recenter*) re-centers the view. If a phone drops, its panel is kept and the partner waits until it reconnects.
- No phone? Press Play anyway: the PC player appears in the first panel. Drag stars with the **mouse**, look around with
  **arrow keys / WASD**, **R** to face the exit.

**What it shows**

| SDK feature | Where in the sample |
|---|---|
| Per-player second screen | `PadPlayView` + `PadPlayGyroCamera` (Window mode) on each player's camera |
| Touch on 3D objects as standard events | `PanelDiveStar` implements only `IBeginDragHandler` / `IDragHandler` / `IEndDragHandler`; the camera has a `PhysicsRaycaster`. The same code runs for the mouse |
| Who touched | `eventData is PadPlayPointerEventData p` → `p.PlayerSlot` (`PanelDiveGame.CanCarry`) |
| Buttons | `Layouts/PanelDiveButtons` (A = `South`, Recenter = `Select` with Role *Recenter*); A is read as `pad.buttonSouth.isPressed` |
| Haptics | `pad.SetMotorSpeeds(strength, strength)` |
| Player states | `host.GetPlayerState(slot)`: anything but `Gone` keeps its panel, so a dropped player is waited for |
| QR and status | `PadPlayQrView` (hidden once two phones joined) and `PadPlayStatusView` |

All feel values (vibration strengths, the angle that counts as "near", carry distance, page-turn delay, key look speed) are in
*PanelDiveGame → Settings* and can be changed while playing. The scene itself is generated by `PanelDiveSceneBuilder`
in the SDK repository (not part of the imported sample).
