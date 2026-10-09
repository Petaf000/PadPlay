# Known issues

[English](#english) | [日本語](#日本語)

Last updated: 2026-10-09 (SDK 0.2.0, early development)

## English

### Communication: PadPlay Link (WebRTC removed)
- The SDK and the app now talk over **PadPlay Link**, a dedicated encrypted UDP protocol for input, haptics, layouts and video. The package no longer depends on `com.unity.webrtc`, and the app no longer contains WebRTC. If you added `com.unity.webrtc` only for PadPlay, you can remove it.
- The phone must reach the PC over **UDP** (default port 7780). Windows Firewall must allow the Unity Editor / your game to receive UDP (*Window → PadPlay → Connection Check*).
- H.264 video uses the bundled `padplay_encoder.dll` (Windows Media Foundation; Windows x64, Editor and Standalone only). When H.264 cannot be used, video falls back to JPEG.
- Older phone apps (the WebRTC versions) cannot connect to this version. Update the app as well.

### Tested environments
| | Tested |
|---|---|
| Unity | Unity 6 (6000.6) |
| Host PC | Windows, NVIDIA GPU, Direct3D 12 |
| Phones | One Android 10 phone (Samsung Galaxy S9+). iPhone: the current version builds and passes its encryption tests in CI, but has not been tried on a device yet (an earlier version ran on one iPhone) |

Other setups may work but have not been tested yet. Reports from other devices are welcome.

### Limitations
- **Same LAN only.** No internet play (no STUN / TURN). Guest Wi-Fi often blocks devices from seeing each other.
- **Hosts other than Windows + NVIDIA are not tested.** With other GPUs, Windows Media Foundation uses the GPU's hardware H.264 encoder when one works, otherwise Windows' software encoder (performance not measured). macOS / Linux hosts are untested.
- **The app is not in the stores yet.** Its UI is Japanese or English (it follows the phone's language).
- Feel values (attitude smoothing, vibration, button hit area) are starting points and are still being tuned on devices.
- APIs may change before 1.0.

## 日本語

### 通信は PadPlay Link(WebRTC は外しました)
- SDK とアプリは、入力・振動・ボタンの配置・映像を 1 本で運ぶ専用の暗号化した UDP の通信 **PadPlay Link** でつながります。パッケージは `com.unity.webrtc` に依存しなくなり、アプリにも WebRTC は入っていません。PadPlay のためだけに `com.unity.webrtc` を入れていた場合は外せます。
- スマホから PC へ **UDP**(既定のポート 7780)が届く必要があります。Windows のファイアウォールで Unity のエディタ・ゲームの UDP の受信を許可してください(*Window → PadPlay → Connection Check*)。
- H.264 の映像は同梱の `padplay_encoder.dll`(Windows Media Foundation。Windows x64 の Editor と Standalone だけ)で作ります。H.264 が使えない時は JPEG に切り替わります。
- 前のアプリ(WebRTC の版)はこの版にはつながりません。アプリも新しくしてください。

### 確かめた環境
| | 確かめたもの |
|---|---|
| Unity | Unity 6(6000.6) |
| ホストの PC | Windows・NVIDIA の GPU・Direct3D 12 |
| スマホ | Android 10 の 1 台(Samsung Galaxy S9+)。iPhone は今の版を実機で未確認(CI でビルドと暗号のテストは通る。前の版は 1 台で動いた) |

ほかの環境でも動く可能性はありますが、まだ確かめていません。ほかの機種での結果の報告を歓迎します。

### 制限
- **同じ LAN の中だけ**で使えます。インターネット越しには使えません(STUN / TURN なし)。ゲスト用の Wi-Fi では端末どうしが見えないことが多いです。
- **Windows + NVIDIA 以外のホストは未確認です。** ほかの GPU では、Windows Media Foundation がその GPU のハードウェアの H.264 符号化器を使い、使えなければ Windows のソフトウェアの符号化器を使います(性能は未計測)。macOS・Linux のホストは未確認です。
- **アプリはまだストアにありません。** 画面は日本語か英語です(スマホの言語に合わせます)。
- 手触りの値(姿勢のなめらかさ・振動・ボタンの当たり判定)は初期値で、実機で調整を続けています。
- 1.0 までに API が変わることがあります。
