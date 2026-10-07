# Known issues

[English](#english) | [日本語](#日本語)

Last updated: 2026-10-07 (SDK 0.2.0, early development)

## English

### Communication is moving to PadPlay Link
- The current version uses WebRTC. In the Unity project it depends on **`com.unity.webrtc` 3.0.0**, which the Package Manager labels **deprecated** on recent Unity versions. It still works in our tests.
- We are replacing it with **PadPlay Link**, a dedicated UDP-based protocol for input, haptics, layouts and video. After the move, `com.unity.webrtc` will no longer be required.

### Tested environments
| | Tested |
|---|---|
| Unity | Unity 6 (6000.6) |
| Host PC | Windows, NVIDIA GPU, Direct3D 12 |
| Phones | One Android 10 phone (Samsung Galaxy S9+), one iPhone |

Other setups may work but have not been tested yet. Reports from other devices are welcome.

### Limitations
- **Same LAN only.** No internet play (no STUN / TURN). Guest Wi-Fi often blocks devices from seeing each other.
- **Hosts other than Windows + NVIDIA are not tested.** Other GPUs encode video in software (performance not measured). macOS / Linux hosts are untested.
- **The app is not in the stores yet.** Its UI is Japanese or English (it follows the phone's language).
- Feel values (attitude smoothing, vibration, button hit area) are starting points and are still being tuned on devices.
- APIs may change before 1.0.

## 日本語

### 通信を PadPlay Link に移行中
- 今の版は WebRTC で通信しています。Unity のプロジェクトでは **`com.unity.webrtc` 3.0.0** に依存しており、最近の Unity では Package Manager に **deprecated** と表示されます。私たちの試験では動いています。
- これを、入力・振動・ボタンの配置・映像を 1 本で運ぶ専用の UDP の通信 **PadPlay Link** に置き換えている途中です。移行後は `com.unity.webrtc` は要らなくなります。

### 確かめた環境
| | 確かめたもの |
|---|---|
| Unity | Unity 6(6000.6) |
| ホストの PC | Windows・NVIDIA の GPU・Direct3D 12 |
| スマホ | Android 10 の 1 台(Samsung Galaxy S9+)・iPhone 1 台 |

ほかの環境でも動く可能性はありますが、まだ確かめていません。ほかの機種での結果の報告を歓迎します。

### 制限
- **同じ LAN の中だけ**で使えます。インターネット越しには使えません(STUN / TURN なし)。ゲスト用の Wi-Fi では端末どうしが見えないことが多いです。
- **Windows + NVIDIA 以外のホストは未確認です。** ほかの GPU では映像をソフトウェアで符号化します(性能は未計測)。macOS・Linux のホストは未確認です。
- **アプリはまだストアにありません。** 画面は日本語か英語です(スマホの言語に合わせます)。
- 手触りの値(姿勢のなめらかさ・振動・ボタンの当たり判定)は初期値で、実機で調整を続けています。
- 1.0 までに API が変わることがあります。
