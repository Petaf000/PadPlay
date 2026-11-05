# FAQ

[English](#english) | [日本語](#日本語)

## English

**Is the source code here?**
No. This repository is only for support: questions, bug reports, ideas and known issues.

**Where can I buy the SDK / get the app?**
The Unity Asset Store pages (PadPlay SDK and the free PadPlay Lite) and the app's Google Play and App Store pages are coming soon.

**What is the difference between PadPlay SDK and PadPlay Lite?**
Lite is free and leaves out only second-screen video (and the samples that use it). The number of players, touch, gyro, buttons, vibration and encryption are the same, with no time limit or watermark. PadPlay SDK also includes the phone app's source code.

**What do I need?**
Unity 2022.3 LTS (2022.3.62f3) or newer, including Unity 6, with Built-in or URP. A Windows 10 / 11 PC (the Editor or a Windows x64 build). The free PadPlay app on each phone. Details: [documentation](https://petaf.dev/padplay/docs/).

**Does it work over the internet?**
No. The PC and the phones must be on the **same Wi-Fi / LAN**. They connect directly over one encrypted UDP port (7780 by default). No internet connection, server or account is used.

**The phone cannot connect.**
- Make sure the PC and the phone are on the same network. Guest Wi-Fi and routers with "AP isolation" (privacy separator) keep devices apart. On apartment, school or office Wi-Fi, connect the PC to the phone's hotspot.
- When Windows asks, allow Unity (and your built game) on **Private** networks in Windows Firewall. If a strip appears under the QR code, press its button.
- Turn off VPN on the phone and the PC. On iPhone, allow Local Network for PadPlay (Settings → Privacy & Security → Local Network).
- In the Unity Editor, open *Window → PadPlay → PadPlay Debug → Connection*. It shows the QR addresses, the port, Windows Firewall and the last connection attempt, each with a next step.
- The app's *Can't connect?* button checks the phone side and lists what to try.

**Which phones are supported?**
Android 7.0 or newer, or iPhone with iOS 15 or newer. A gyroscope is needed only for the gyro features. On iPad the app runs in the iPhone layout. See [Known issues](KNOWN_ISSUES.md) for the devices we have actually tested.

**Does the app collect my data?**
No accounts, no ads, no analytics. The app talks only to the game PC you choose. A problem report leaves the phone only when you send it yourself (by email, or by copying it). See the [privacy policy](https://petaf.dev/privacy/).

**Which language can I use for questions?**
English or Japanese.

## 日本語

**ここにソースコードはありますか?**
ありません。このリポジトリは問い合わせ用(質問・不具合の報告・要望・既知の問題)です。

**SDK はどこで買えますか? アプリはどこで入手できますか?**
Unity Asset Store のページ(PadPlay SDK と無料の PadPlay Lite)と、アプリの Google Play・App Store のページを準備中です。

**PadPlay SDK と PadPlay Lite の違いは?**
Lite は無料で、ないのは第二画面の映像(とそれを使う見本)だけです。人数・タッチ・ジャイロ・ボタン・振動・暗号は同じで、時間の制限や透かしはありません。PadPlay SDK にはスマホのアプリのソースも入っています。

**何が要りますか?**
Unity 2022.3 LTS(2022.3.62f3)以降(Unity 6 も。Built-in か URP)。Windows 10 / 11 の PC(エディタか Windows x64 のビルド)。遊ぶ人のスマホに無料の PadPlay アプリ。詳しくは [文書](https://petaf.dev/padplay/docs/ja/) をご覧ください。

**インターネット越しに使えますか?**
使えません。PC とスマホは**同じ Wi-Fi・LAN** につないでください。暗号化した 1 つの UDP のポート(既定は 7780)で直接つながります。インターネットへの接続・サーバー・アカウントは使いません。

**スマホがつながりません。**
- PC とスマホが同じネットワークにあるか確かめてください。ゲスト用の Wi-Fi や「AP アイソレーション(プライバシーセパレーター)」を有効にしたルーターでは、端末どうしが見えません。マンション・学校・会社の Wi-Fi では、PC をスマホのテザリングにつないでください。
- Windows に聞かれたら、Windows ファイアウォールで Unity(とビルドしたゲーム)を**プライベート**ネットワークで許可してください。QR コードの下に帯が出たら、そのボタンを押してください。
- スマホと PC の VPN を切ってください。iPhone では PadPlay の「ローカルネットワーク」を許可してください(設定 → プライバシーとセキュリティ → ローカルネットワーク)。
- Unity エディタで *Window → PadPlay → PadPlay Debug →「接続」* を開いてください。QR のアドレス・ポート・Windows ファイアウォール・最後の接続の試みを調べ、次にすることを示します。
- アプリの「つながらない時は」のボタンでも、スマホの側を調べて試すことを示します。

**どのスマホで使えますか?**
Android 7.0 以降、または iOS 15 以降の iPhone です。ジャイロはジャイロの機能を使う時だけ要ります。iPad では iPhone の画面の形で動きます。実際に確かめた機種は [既知の問題](KNOWN_ISSUES.md) にあります。

**アプリは私の情報を集めますか?**
アカウント・広告・利用状況の解析はありません。アプリが通信するのは、あなたが選んだゲームの PC だけです。問題の報告は、あなたが自分で送った時(メール・コピー)だけスマホの外に出ます。[プライバシーポリシー](https://petaf.dev/ja/privacy/) をご覧ください。

**何語で質問できますか?**
日本語か英語でどうぞ。
