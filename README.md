# Featherio

34キー一体型キーボード Featherio の ZMK ファームウェア設定。

- MCU: Seeed Studio XIAO nRF52840 Plus（非Sense）
- キー: 4×10 疎行列・34キー、col2row
- ポインティング: せきごん氏 LPPS（I2C、SDA=P0.04 / SCL=P0.05 / MOTION=P1.11）
- 電源: AAAA（NiMH）モジュール。セル電圧を P0.02/AIN0（1MΩ/470kΩ）で監視
- キーマップ: aerogu34 準拠（5レイヤー、8コンボ、日本語配列OS向け定義）
- DYA Studio、Prospector scanner 向け状態配信、XIAO 基板上 RGB LED ウィジェット

## ビルドターゲット（build.yaml）

| Artifact | 内容 |
|---|---|
| `featherio_prospector.uf2` | DYA Studio + Prospector 配信あり |
| `featherio.uf2` | DYA Studio のみ |
| `settings_reset.uf2` | BLE ペアリングと保存設定の全消去 |

GitHub に push すると `.github/workflows/build.yml` が `build.yaml` の全エントリをビルドする。

## 書き込み

XIAO のリセットボタンを素早く2回押して UF2 ドライブを出し、`.uf2` をコピーする。

## 操作メモ

- Scroll レイヤー（`D` / `K` / 親指2番目のホールド）
  - `A` の位置: Studio ロック解除
  - `S` の位置: LPPS 手動キャリブレーション（スティックに触れずに押す）
  - 上段左5キー: BLE プロファイル 0〜4
  - `Q`+`T`: BLE ペアリング消去、`W`+`E`+`R`: ブートローダー
- スティックを動かすと Mouse レイヤーに入り、止めて1秒で戻る。Scroll レイヤー中はスクロールになる。
- ポインタの向き・速度・自動マウスレイヤーは DYA Studio から変更できる。デッドゾーン（overlay の `lpps_deadzone`）は再ビルドが必要。

## 未調整・注意点

- 電池残量の変換表（overlay の `&vbatt`）は NiMH の一般的な放電カーブによる仮の値で、実測照合はしていない。
- LiPo モジュールでは P0.02 が未接続になるため、`&vbatt` の上書きを外してボード既定の電池監視に戻す必要がある。
- スリープは無効。
- ルートの `CMakeLists.txt` は prospector-zmk-module の不具合（BLE 未接続時に配信できない）の回避策。`ZMK_EXTRA_MODULES` でこのリポジトリを渡すローカルビルドでのみ効く。モジュール側の修正が入れば不要。

## ピン割り当て

| 用途 | nRF GPIO |
|---|---|
| Row0〜3 | P0.09（NFC1）, P0.03, P0.28, P0.29 |
| Col0〜9 | P1.12, P1.13, P1.14, P1.15, P0.15, P0.19, P1.01, P1.03, P1.05, P1.07 |
| LPPS SDA / SCL / MOTION | P0.04 / P0.05 / P1.11 |
| セル電圧 ADC | P0.02（AIN0） |
