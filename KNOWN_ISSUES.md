# Known issues

[English](#english) | [日本語](#日本語)

For PadPlay SDK / PadPlay Lite 1.0. The full list is in the [documentation](https://petaf.dev/padplay/docs/) (Quick Start, *Requirements* and *What it cannot do (yet)*).

## English

### Connection
- The SDK and the app talk over **PadPlay Link**, the SDK's own encrypted UDP protocol for input, vibration, layouts and video. No other package is needed.
- The phone must reach the PC over **UDP** (port 7780 by default). Windows Firewall must allow the Unity Editor / your game on Private networks (*Window → PadPlay → PadPlay Debug → Connection* checks it).
- H.264 video is encoded by Windows Media Foundation (Windows x64, Editor and Standalone). Where H.264 cannot be used, video is sent as JPEG.

### Tested setup
| | Tested |
|---|---|
| PC | Windows, NVIDIA GPU (hardware H.264), Direct3D 12 |
| Unity | 6000.6 with a phone. 2022.3.62f3 and 6000.0.84f1 pass the same automated tests |
| Graphics API | Direct3D 12 with a phone; Direct3D 11, Vulkan and OpenGL Core (JPEG) in automated tests |
| Render pipeline | Built-in; URP 17.6 (Unity 6000.6) and URP 14.0.12 (2022.3.62f3) in automated tests |
| Android | Android 10, Samsung Galaxy S9+ (one device) |
| iPhone | Not yet tried on a device with this version (the app builds and passes its encryption tests in automated builds) |

Other GPUs, Windows' software H.264 encoder, macOS / Linux hosts, HDRP and other phones may work but have not been tested yet. Reports from other devices are welcome.

### Limitations
- **Same LAN only.** No internet play. Guest Wi-Fi and "AP isolation" often keep devices from seeing each other.
- **macOS / Linux hosts**: not tested; the second screen is sent as JPEG only (larger, slower).
- **HDRP**: not tested yet.
- **Joining from a web browser**: not in this version; every player needs the PadPlay app.
- **Sound on the phone**: the phone does not play the game's sound.
- **One view for several players** is encoded once, at the quality of the player with the weakest Wi-Fi.
- **iPad**: the app runs in the iPhone layout.
- **PadPlay Lite** has no second-screen video (`PadPlayView`) and includes only the *Sample Hub* (with *Target Pop*) and *Minimal* samples.
- **The app is not in the stores yet.** Its UI is English or Japanese (it follows the phone's language).

## 日本語

### 通信
- SDK とアプリは、入力・振動・ボタンの配置・映像を運ぶ SDK 独自の暗号化した UDP の通信 **PadPlay Link** でつながります。ほかのパッケージは要りません。
- スマホから PC へ **UDP**(既定のポート 7780)が届く必要があります。Windows のファイアウォールで Unity のエディタ・ゲームをプライベートネットワークで許可してください(*Window → PadPlay → PadPlay Debug →「接続」* で確かめられます)。
- H.264 の映像は Windows Media Foundation(Windows x64 の Editor と Standalone)で作ります。H.264 が使えない時は JPEG で送ります。

### 確かめた環境
| | 確かめたもの |
|---|---|
| PC | Windows・NVIDIA の GPU(ハードウェアの H.264)・Direct3D 12 |
| Unity | 6000.6 は実機で。2022.3.62f3 と 6000.0.84f1 は同じ自動テストが通る |
| グラフィックス API | Direct3D 12 は実機で。Direct3D 11・Vulkan・OpenGL Core(JPEG)は自動テストで |
| 描画パイプライン | Built-in。URP 17.6(Unity 6000.6)と URP 14.0.12(2022.3.62f3)は自動テストで |
| Android | Android 10、Samsung Galaxy S9+(1 台) |
| iPhone | 今の版は実機で未確認(自動のビルドでビルドと暗号のテストは通る) |

ほかの GPU・Windows のソフトウェアの H.264 符号化器・macOS / Linux のホスト・HDRP・ほかのスマホでも動く可能性はありますが、まだ確かめていません。ほかの機種での結果の報告を歓迎します。

### 制限
- **同じ LAN の中だけ**で使えます。インターネット越しには使えません。ゲスト用の Wi-Fi や「AP アイソレーション」では端末どうしが見えないことが多いです。
- **macOS / Linux のホスト**: 未確認で、第二画面は JPEG だけで送ります(大きく、遅い)。
- **HDRP**: まだ確かめていません。
- **Web ブラウザからの参加**: この版にはありません。遊ぶ人はみな PadPlay のアプリが要ります。
- **スマホの音**: スマホからはゲームの音は出ません。
- **複数の人への同じ映像**は 1 回だけ符号化し、いちばん Wi-Fi の弱い人に合わせた画質になります。
- **iPad**: アプリは iPhone の画面の形で動きます。
- **PadPlay Lite** には第二画面の映像(`PadPlayView`)がなく、見本は *Sample Hub*(*Target Pop* を含む)と *Minimal* だけです。
- **アプリはまだストアにありません。** 画面は英語か日本語です(スマホの言語に合わせます)。
