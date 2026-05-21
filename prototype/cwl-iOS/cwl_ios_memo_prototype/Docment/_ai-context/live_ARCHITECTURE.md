# ARCHITECTURE.md — ChatworkLive 会議中画面（仕様・構成）

> ChatworkLive の「会議中メイン画面」の仕様・構造を定義する。
>
> - 色・タイポ・寸法：`live_DESIGN.md`
> - 挙動ルール：`live_RULES.md`
> - 関連プロトタイプ：`cwl_ios_memo_prototype.html`
> - 関連 Figma：ChatworkLive_iOS_prototype（node-id 1:475 / 12:1607 / 16:243 / 17:339 / 18:316）
> - 最終更新：2026-05-07

---

## 1. 概要とスコープ

会議中メイン画面はビデオ会議の中核 UI。映像の上に NavHeader / Toolbar / Members がオーバーレイされ、参加者の選択・マイク／カメラのトグル・退出などのコア操作を集約する。

| 項目 | 値 |
|---|---|
| 対象デバイス | iPhone 14（390 × 844 px） |
| 対応テーマ | iOS 18（Classic）／ iOS 26（Liquid Glass） |
| 対応モード | Light / Dark（Mode トグル） |
| 状態 | Mic On/Off、Camera On/Off、Member 選択中 |
| iPad | スコープ外（今後検討） |

---

## 2. 画面構成（4 層 + デバイスフレーム）

```
┌────────────────────────────┐
│ ① NavHeader (StatusBar+NavBar)│  102px ←黒固定
├────────────────────────────┤
│                            │
│ ② Content (映像/自分/選択者)  │  flex:1 ←背景は状態で変化
│                            │
├────────────────────────────┤
│ ③ Toolbar (Mic/Video/Share/End)│  56  ←絶対配置 bottom:152
├────────────────────────────┤
│ ④ Members (参加者列)         │ 142  ←絶対配置 bottom:10
└────────────────────────────┘
                             space 10
```

| 領域 | 高さ | 配置 |
|---|---|---|
| NavHeader（StatusBar 54 + NavBar 48） | 102 | flex 先頭、`#000` 固定 |
| Content | 残り | flex:1 |
| Toolbar | 56 | `position: absolute / bottom: 152` |
| Members | 142 | `position: absolute / bottom: 10` |
| 画面下端の余白（space） | 10 | bottom 0 |

Toolbar / Members は `position: absolute` で配置するため flex フローを取らない。Content は flex:1 で画面最下部まで占有するが、`padding-bottom: 208px`（= Toolbar bottom 152 + height 56）で見える領域の中心に揃える。

---

## 3. NavHeader（共通ラッパー）

`StatusBar` + `NavBar` を1つの `<div class="nav-header">` で包む。背景・テキスト色はラッパーが持ち、子要素は透過のまま色を継承する（共通の `_ai-context/ARCHITECTURE.md` の NavHeader パターンに準拠）。

### StatusBar

- 高さ：54
- 構成：`時刻（左）` + `インジケータ群（右）`
- インジケータ：Cellular（4本バー）+ WiFi（3 弧）+ Battery（外枠 + 充電量 + キャップ）

### NavigationBar — Classic（iOS 18）

- 高さ：48
- 構造：`Leading（戻る）` + `Trailing（4 アイコン）`
- フラットな1本帯。Leading・Trailing のアイコンは平置き。

### NavigationBar — Liquid Glass（iOS 26）

- 高さ：54、`align-items: flex-start`
- 構造：3 つのフローティング Pill
  - `Leading` Pill：戻る（44×44 正円）
  - `Trailing` Pill 1：AI メモ（44×44 正円）
  - `Trailing` Pill 2：スピーカー / カメラ切替 / その他（横長カプセル）

### NavBar アイコン割当

| 位置 | 役割 | 使用アセット |
|---|---|---|
| Leading | 戻る `<` | インライン SVG（chevron） |
| Trailing 1 | AI メモ | `aiMemoIcon.svg` |
| Trailing 2 | スピーカー | `SperkerOnIcon.svg` / `SperkerOffIcon.svg` |
| Trailing 3 | カメラ切替 | `CameraChangeIcon.svg` |
| Trailing 4 | その他 | `OtherIcon.svg` |

---

## 4. Content（映像エリア）

状態によって背景・中身が変化する。挙動ルールは `live_RULES.md` を参照。

| 状態 | 背景 | 中央表示 |
|---|---|---|
| 既定（Camera On / 未選択） | `#C42760`（accent/system）※映像差し替え予定 | なし |
| Camera Off | `#13202F`（surface/base/default） | 自分のアバター 144×144 正円 |
| Member 選択中（発展） | TBD | 選択ユーザーの映像／アバター（今後実装） |

---

## 5. Toolbar（Live コントロール）

- 配置：4 つの 56 px 円ボタンを 22 px gap、`justify-content: center` で水平中央
- 構成順（左→右）：Mic / Video / Share / End call
- 各ボタンは独立。Mic / Video のみ On/Off トグル可能（`live_RULES.md`）
- Members との上下距離：Toolbar 底辺 = Members 上辺。視覚的にはアバターまで 39 px の空き

| ボタン | 役割 | 使用アセット |
|---|---|---|
| Mic | マイクのトグル | `MicOn.svg` / `MicOff.svg` |
| Video | カメラのトグル | `VideoOn.svg` / `VideoOff.svg` |
| Share | 画面共有 | `ShareIcon.svg` |
| End call | 退出（破壊的アクション） | `phoneIcon.svg` |

---

## 6. Members（参加者アイコン列）

- 横並び。画面幅を超えたら横スクロール（スクロールバー非表示）
- 各 Item は **濃紺の四角フレーム + 円形アバター** 
- 選択時は枠 / 暗オーバーレイ / 名前表示にスタイルが変化（`live_DESIGN.md`）
- 選択ロジックは「単一選択（同じものを再タップで解除）」（`live_RULES.md`）

### Member item の構造

```
┌───────────────┐  ← 80×80 濃紺の四角
│   ┌───────┐   │
│   │ 64×64 │   │  ← 正円アバター
│   │ avatar│   │
│   └───────┘   │
└───────────────┘
   ↑ overflow:hidden、p:8
   ↑ is-selected 時：1px ボーダー + dark overlay + name 表示
```

---

## 7. アセット参照

### アイコン（`Docment/Asset/icon/`）

| ファイル | 用途 |
|---|---|
| `MicOn.svg` / `MicOff.svg` | Toolbar Mic（On/Off） |
| `VideoOn.svg` / `VideoOff.svg` | Toolbar Video（On/Off） |
| `ShareIcon.svg` | Toolbar 画面共有 |
| `phoneIcon.svg` | Toolbar 退出 |
| `aiMemoIcon.svg` | NavBar AI メモ |
| `SperkerOnIcon.svg` / `SperkerOffIcon.svg` | NavBar スピーカー（On/Off） |
| `CameraChangeIcon.svg` | NavBar カメラ切替 |
| `OtherIcon.svg` | NavBar その他 |
| `BatteryIcon.svg` / `WifiIcon.svg` / `CellularConnectionIcon.svg` | StatusBar インジケータ（参考） |

> 注：StatusBar のインジケータはプロトタイプではインライン SVG で描画している。`WifiIcon.svg` と `CellularConnectionIcon.svg` はファイル名と中身が逆転している（`WifiIcon.svg` の中身は実態として Cellular 4 本バー）。今後リネーム予定。

### ユーザー画像（`Docment/Asset/image/`）

`Demo User=*.png`（中村浩一 / 佐藤直樹 / 木村香 / 田中優子 / 石井太郎 / 鈴木裕二 / ライブ用（男性）/ ライブ用（女性）/ デフォルト）

- 自分用（Camera Off 時の中央）：`石井太郎`
- Members 列：上記 6 人（中村浩一 / 佐藤直樹 / 木村香 / 田中優子 / 石井太郎 / 鈴木裕二）

---

## 8. 状態とトグルの伝播

```
[ Toolbar ]                       [ Content ]              [ Members ]
   Mic On ──tap──▶  Mic Off                                    
   Video On ──tap──▶ Video Off ──▶ data-camera="off"
                                   bg: #13202F
                                   self-avatar 表示
   ── tap ──▶ 戻す
                                                              ── tap user ──▶ is-selected
                                                              （発展）Content にも反映
```

詳細フローは `live_RULES.md` を参照。

---

## 9. 今後の拡張（構造側）

- Member 選択時、Content に選択ユーザーの映像／アバター 144 を表示する
- Toolbar も iOS 26 で Pill / Liquid Glass 化を検討（NavBar と統一感を出す）
- 着信 / 発信前 / 画面共有中 / AI 要約画面の追加
- 退出フロー（確認ダイアログ → チャットへの「会議終了」メッセージ投稿）
- iPad（Master/Detail）対応（現状スコープ外）
