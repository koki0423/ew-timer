# timer

Arduino Nano を用いた 4 桁 7 セグメント表示付きタイマーの KiCad プロジェクトです。4 個の 4 ビット DIP スイッチで 16 ビットの設定値を入力し、開始/停止・リセット用の押しボタンで操作します。

このリポジトリには基板設計と製造データを収録しています。Arduino 用ファームウェア（`.ino` など）は含まれていません。

## 主な構成

| ブロック | 部品 | 役割 |
| --- | --- | --- |
| 制御 | A2: Arduino Nano v3.x | タイマー処理、表示走査、入力読取り |
| 表示 | U1: OSL-40391-LX | 4 桁・コモンカソードの 7 セグメント LED 表示器（コロン付き） |
| 表示駆動 | U2: 74HC595、R1-R8: 1 kΩ | シリアル出力を 8 本のセグメント信号（A-G、DP）へ展開。R1-R8 は各セグメントの電流制限抵抗 |
| 設定入力 | SW3-SW6: A7D-206-1、U3/U4: 74HC165、RN1-RN4: 10 kΩ | 4 個の 4 ビット DIP スイッチを 2 個の PISO シフトレジスタ経由で読み取る。各入力はプルダウンされ、スイッチ ON 時に High となる |
| 操作 | SW1、SW2 | リセット、開始/停止入力 |
| 状態表示 | U5: 74HC00、D1、R9: 300 Ω | DIP スイッチの一部を NAND 論理で判定し、エラー LED を点灯 |
| 電源安定化 | C1-C4: 0.1 µF | +5 V-GND 間のデカップリング |

基板外形は **120 mm × 90 mm**、板厚は **1.6 mm** です。表裏 2 層の配線を使用しています。

## Arduino Nano の接続

ファームウェアを作成する際は、以下の回路接続に合わせてピンを設定してください。

| Nano ピン | 接続先 | 用途 |
| --- | --- | --- |
| D2 | U2 SER (pin 14) | 7 セグメントのシリアルデータ |
| D3 | U2 RCLK (pin 12) | 74HC595 のラッチクロック |
| D4 | U2 SRCLK (pin 11) | 74HC595 のシフトクロック |
| D5 | U3/U4 `~PL` (pin 1) | DIP 入力のパラレルロード制御 |
| D6 | U3/U4 CP (pin 2) | DIP 入力のシフトクロック |
| D7 | U4 Q7 (pin 9) | 16 ビット DIP 入力のシリアル出力 |
| D9 | U1 DIG1 (pin 1) | 1 桁目の共通カソード選択 |
| D10 | U1 DIG2 (pin 2) | 2 桁目の共通カソード選択 |
| D11 | U1 DIG3 (pin 6) | 3 桁目の共通カソード選択 |
| D12 | U1 DIG4 (pin 8) | 4 桁目の共通カソード選択 |
| D13 | U1 COLON (pin 4) | コロン表示制御 |
| A0 | SW2 | 開始/停止ボタン。押下時は Low |
| A1 | SW1 | リセットボタン。押下時は Low |

U2 の出力は、`QA`-`QH` の順に `A`、`B`、`C`、`D`、`E`、`F`、`G`、`DP` セグメントへ接続されています。U3 の Q7 は U4 のシリアル入力へカスケード接続され、U4 の Q7 を Nano の D7 で読み取ります。

## 部品表

製造用の詳細 BOM は [production/bom.csv](production/bom.csv) を参照してください。主要部品は次のとおりです。

| 数量 | 部品 |
| ---: | --- |
| 1 | Arduino Nano v3.x |
| 1 | OSL-40391-LX 4 桁 7 セグメント LED 表示器 |
| 1 | 74HC595（DIP-16 ソケット） |
| 2 | 74HC165（DIP-16 ソケット） |
| 1 | 74HC00（DIP-14 ソケット） |
| 4 | A7D-206-1 4 ビット DIP スイッチ |
| 4 | 10 kΩ SIP 抵抗アレイ（5 ピン） |
| 8 | 1 kΩ 抵抗 |
| 1 | 300 Ω 抵抗 |
| 4 | 0.1 µF コンデンサ |
| 1 | 3 mm LED（エラー表示） |
| 2 | 12 mm タクトスイッチ |

## ファイル構成

| パス | 内容 |
| --- | --- |
| [timer.kicad_pro](timer.kicad_pro) | KiCad プロジェクト設定 |
| [timer.kicad_sch](timer.kicad_sch) | 回路図 |
| [timer.kicad_pcb](timer.kicad_pcb) | 基板レイアウト |
| [production/timer.zip](production/timer.zip) | 基板製造用 Gerber / ドリルデータ一式 |
| [production/bom.csv](production/bom.csv) | 製造・部品調達用 BOM |
| [production/positions.csv](production/positions.csv) | 部品実装座標 |
| [docs/](docs/) | 表示器、シフトレジスタ、DIP スイッチおよび LED ブラケットの資料 |

## 開き方と製造

1. KiCad で [timer.kicad_pro](timer.kicad_pro) を開きます。PCB ファイルは KiCad 10.0 形式です。
2. 回路図または基板を変更したら、ERC/DRC を実行してから製造データを再生成します。
3. 変更を加えず基板を発注する場合は、[production/timer.zip](production/timer.zip) を基板メーカーへアップロードします。
4. 実装には [production/bom.csv](production/bom.csv) と [production/positions.csv](production/positions.csv) を使用します。

## 注意事項

- `fp-lib-table` は A7D-206-1 用フットプリントについて、この PC 上の `C:/Users/koki/Downloads/...` を参照しています。このライブラリはリポジトリに含まれていないため、フットプリントを再割り当て・更新する場合は、同等のライブラリをローカルに追加して参照先を更新してください。
- U1 の回路図シンボルは OSL-40391-LX ですが、基板フットプリント名は `LTC-4627Jx` です。置換部品を選ぶ前に、同梱の [表示器データシート](docs/OSL40391-XX.pdf) でピン配列・極性・寸法を必ず照合してください。
- Nano の +5 V を基板全体の電源として使用します。USB 給電以外を用いる場合も、ロジック電源は +5 V、GND 共通にしてください。
- 7 セグメント表示は Nano による桁走査を想定しています。表示の極性、桁選択のタイミング、DIP スイッチのビット順は、実機で確認しながらファームウェア側で定義してください。
