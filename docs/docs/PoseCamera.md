---
title: Pose Camera
lang: en
---

# Pose Camera: a window held in the player's hands

> This is the online copy of the documentation that ships inside the package (`Documentation~`). SDK 0.2.0. [All pages](./) · [Support](https://github.com/Petaf000/PadPlay)

**PadPlayPoseCamera** rotates a camera with a player's phone and sends what it sees to that phone. The player holds the phone
up and looks around the scene through it, like a window. Add one component; the game needs no code for it.

Rendering, encoding, sending and decoding always take time (typically 50 to 100 ms on Wi-Fi). If the phone turned during that
time, a plain video would lag behind the hand. PadPlay hides this: the PC renders a little more than the phone shows
(**overscan**), and right before showing a frame the phone turns the picture by how much it turned since that frame was
rendered. This is the same idea as "timewarp" in VR headsets.

## Set up

1. Put a **PadPlay View** in the scene and set its **Source Camera** to the camera you want on the phone
   (see [Second screen](QuickStart.md)). Set the View's **Player Slot** to the player who holds it.
2. Add **PadPlay → Pose Camera** to the same camera. Set **Player Slot** to the same player.
3. Play and connect the phone. The camera follows the phone; the phone shows the camera.

The camera's rotation when the scene starts is "forward". On connect, the direction the player faces becomes forward
(yaw only; gravity stays down). `Recenter()`, `host.Recenter(slot)`, a layout button with Role = Recenter and a long press on
the phone's corner do the same.

## Settings and API

| Setting / member | Default | What it does |
|---|---|---|
| `Settings.FieldOfView` / `FieldOfView` | 60 | Vertical field of view of what the phone shows (degrees). Change it to zoom. The camera's `fieldOfView` is set wider by the overscan. |
| `Settings.OverscanPercent` | 10 | Extra picture on each side, in percent of the visible size. More hides faster turns, but the visible part gets fewer pixels (10% leaves 83% of the render for the visible part; raise the View's resolution if you need the detail back). |
| `Settings.PhoneCorrection` | on | Let the phone turn the picture. Turn off to compare. |
| `Settings.Predict` | on | Predict the orientation at render time from the angular velocity (limits: Host Settings > Attitude). |
| `Settings.FollowScreenRotation` | on | Follow the phone's display rotation (landscape). |
| `Settings.RecenterOnConnect` / `FollowPadRecenter` | on / on | When forward is reset. |
| `YawOffsetDegrees` | 0 | Yaw added on top of the phone, e.g. drag to look behind while seated. |
| `ScreenPointToRay(eventData)` | | World ray for a phone touch, through **the frame the player was looking at** (see below). |
| `ScreenPointToRay(phonePosition, poseSeq)` | | The same from raw values (`PadPlayTouch.ViewPoseSeq`). |
| `TryGetView(out PadPlayPoseView)` / `host.TryGetPoseView(slot, …)` | | Motion-to-photon and the correction the phone applied. |
| `VisibleViewportRect` / `VisiblePixelRect` | | The part of the picture the phone shows (inside the overscan), in viewport (0 to 1) or camera pixels. The overscan is the same on every side, so the rectangle is centered. |

Defaults are first guesses; tune them by feel on a real phone.

## Taps

The phone shows a frame that it turned and cropped. When the player taps, the phone undoes the turn and the crop and sends
the point **of that frame** (0 to 1 over the whole rendered image, y = 0 at the top), together with the number of the frame
(`PadPlayPointerEventData.ViewPoseSeq`). The Pose Camera keeps the camera of the last 256 frames, so
`poseCamera.ScreenPointToRay(eventData)` returns the ray the player actually aimed at: at the edges, while zooming and while
turning fast.

```csharp
public void OnPointerClick(PointerEventData e)
{
    Ray ray = poseCamera.ScreenPointToRay(e);   // PadPlay touches use the frame the player saw
    if (Physics.Raycast(ray, out RaycastHit hit))
        Debug.Log("Tapped " + hit.collider.name);
}
```

uGUI on the View's render texture also lines up, because the touch position is a point of the rendered image. Raycasters
that use the camera as it is *now* (for example a `PhysicsRaycaster` on the camera) can be off by how much the camera moved
since that frame; use `ScreenPointToRay(eventData)` when it matters.

## Text on the phone: in the picture or fixed to the screen

Anything the camera renders — including a Screen Space - Camera canvas on the Pose Camera — is part of the picture, so the phone
turns it with the picture. Keep such UI inside `VisibleViewportRect` (anchor its RectTransform to it), or it lands in the overscan
the phone never shows.

Text that should **stay still on the phone screen** (a score, "3 / 5 found", a crosshair) belongs in a **Phone Overlay**:

1. Make a RectTransform that stands for the phone's video area — for a Pose Camera, anchor it to `VisibleViewportRect`.
2. Add **PadPlay → Phone Overlay** to it and set **Player Slot** (−1 = everyone).
3. Put ordinary uGUI **Text** (and **Image** for a plain colored board) under it and change them from your scripts as usual.

The component reads them every `RefreshSeconds` (0.05 s) and, when something changed, sends them to the phone, which draws them
over the video and outside the correction. It sends the text, font size, alignment, bold / italic, color and rich text
(`<b>`, `<i>`, `<color>`; other tags are removed); images are sent as solid boards (no sprites — use characters such as `+`
or `●` for marks). Up to 64 items, 1024 bytes of text each. *Hide In Picture* (on) makes the layer invisible and click-through
in the game's own rendering, so it is not drawn twice. From code: `host.SetPhoneOverlay(slot, items)` with
`LinkOverlayItem.CreateText` / `CreateRect` (positions 0 to 1 of the video area, y = 0 at the top). The host keeps the last
overlay per player and sends it again when the phone reconnects. It works for any `PadPlayView`, not only a Pose Camera.
Older phone apps ignore it.

## What the correction can and cannot hide

Hidden:
- Turning the phone (looking around), up to the overscan. Faster turns are held at the edge of the rendered picture
  (the Debug Overlay shows "(edge)") and the rest arrives late.

Not hidden:
- **Moving** the phone (stepping, leaning). The correction is rotation only.
- Things **moving in the game** (characters, effects). They are where they were when the frame was rendered.
- The delay itself for everything else: reaction to taps, sound, game logic.
- Changes made by the game between render and display: `YawOffsetDegrees` (dragging), recentering.

The correction makes the window feel attached to the hand; it does not make the connection faster.

## Measuring

The phone reports, for the frame it shows:
- **motion-to-photon**: the time from the attitude sample the frame was rendered with to the moment the phone builds the frame
  on screen (both on the phone's clock, so no clock sync is involved; the photons appear about one display refresh later).
- **correction**: how many degrees it turned the picture, and whether it hit the edge.

They appear in the **Debug Overlay** (`window: m2p 48 ms  fix 3.2°`) and in `host.TryGetPoseView(slot, out var view)`.

## Trying it without a phone

In **Window → PadPlay → Virtual Phones**, right-drag on a phone's screen to turn it. Set **Delay** (for example 150 ms) to
make the game render an old orientation, then switch **Correction** on and off to see the difference. The window shifts and
zooms the picture as an approximation (no roll or perspective); a real phone applies the exact projection.
A Phone Overlay is drawn on top, fixed. To try a pinch, Ctrl-drag (Cmd on a Mac) for two fingers mirrored around the center,
or use the mouse wheel over the screen.

## Requirements and compatibility

- The phone app must support it (an app that supports it declares it when it connects). Older apps get the same video without
  the pose: they show the whole overscanned picture and taps map to it as before.
- The pose is sent only to the player whose Pose Camera rendered the frame.
- Works with H.264 and the JPEG fallback.

## How it works (for the curious)

Each video frame of a Pose Camera carries 30 extra bytes: the pose number, the time of the attitude sample, the orientation it
was rendered with (in the phone's own coordinates), the visible field of view, the overscan and the display rotation.
Because the camera's rotation is *forward × added yaw × recenter × phone orientation × display rotation*, and everything
left of the phone orientation is fixed in the world, the difference between the rendered camera and the camera "now" depends
only on the phone's own orientations. The phone computes it from its sensor and draws the frame with the matching
homography. The protocol is in `docs/design/06-padplay-link.md` §5.11 of the source repository.

Not to be confused with **Gyro Camera** (`PadPlayGyroCamera`), which turns any camera (for example the TV view) with a phone in
Window, Look Around or Tilt mode with smoothing and sensitivity. Its rotation is not a plain function of the phone's
orientation, so the phone could not correct it; use Pose Camera for the camera whose picture goes to that phone.
