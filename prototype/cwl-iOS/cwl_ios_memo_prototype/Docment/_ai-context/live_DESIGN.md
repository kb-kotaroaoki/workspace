# DESIGN.md — ChatworkLive 会議中画面（色・タイポ・レイアウト）

> ChatworkLive の「会議中メイン画面」で使う色・タイポグラフィ・レイアウト寸法を定義する。
>
> - 構造・仕様：`live_ARCHITECTURE.md`
> - 挙動ルール：`live_RULES.md`
> - 関連プロトタイプ：`cwl_ios_memo_prototype.html`
> - 最終更新：2026-05-07

---

## 1. カラートークン早見表

### 基本色（このスクリーンで使うもの）

| 用途 | 値 | Figma トークン |
|---|---|---|
| NavHeader 背景 | `#000000` | colors/Black |
| Content 既定（主映像） | `#C42760`（BarbiePink） | background/accent/system |
| Content（Camera Off） | `#13202F` | background/surface/base/default |
| Member item フレーム | `#13202F` | background/surface/base/default |
| Member 選択ボーダー | `#7AA9FF` | color/ceruleanblue/40 |
| Member 選択時の暗シャドウ | `rgba(0, 0, 0, 0.7)` | background/feedback/hidden |

### Toolbar

| 用途 | 値 | Figma トークン |
|---|---|---|
| Mic / Video / Share 既定背景 | `rgba(0, 0, 0, 0.7)` | background/feedback/hidden |
| **Mic / Video Off 背景** | `rgba(0, 89, 255, 0.25)` | background/feedback/selected/default |
| End call 背景 | `#D10015` | background/action/danger/default |
| Mic / Video / Share アイコン色 | `#C9DAF7` | text/action/tertiary/default |
| End call アイコン色 | `#FFFFFF` | text/general/on-background |

### NavBar — Liquid Glass（iOS 26）の Pill

| 用途 | 値 |
|---|---|
| Pill 背景 | `rgba(255, 255, 255, 0.16)` |
| Pill 内側ハイライト | `inset 0 0 0 1px rgba(255, 255, 255, 0.12)` |
| Pill ドロップシャドウ | `0 1px 2px rgba(0, 0, 0, 0.25)` |
| backdrop-filter | `blur(20px) saturate(180%)` |

### テキスト色

| 用途 | 値 |
|---|---|
| StatusBar / NavBar | `#FFFFFF` |
| Member 名（選択時） | `#FFFFFF` |
| 自分名（Camera Off） | `#FFFFFF` |

---

## 2. タイポグラフィ

iOS Dynamic Type（HIG 準拠）に従う。和文は Hiragino Sans、欧文は SF Pro。

| 用途 | フォント | サイズ | line-height | letter-spacing | weight |
|---|---|---|---|---|---|
| StatusBar 時刻 | SF Pro Text / Display | 17 | 22 | -0.43 | Semibold (590) |
| Member 名（選択時） | Hiragino Sans | 15 | 21 | -0.31 | W3（300） |
| NavBar タイトル（必要時） | SF Pro Text | 17 | 22 | -0.43 | Semibold (600) |

すべて：
- `font-variant-numeric: tabular-nums`（数字幅固定）— 時刻表示で必須
- `font-style: italic` は禁止（DESIGN.md 全体ルールに従う）
- `line-height` は **絶対値（px）** で指定（iOS 仕様）

---

## 3. レイアウト寸法

### スクリーン全体

| 要素 | 値 |
|---|---|
| iPhone 14 ビューポート | 390 × 844 |
| デバイスフレーム角丸 | 56（外側）/ 44（内側スクリーン） |

### NavHeader

| 要素 | 値 |
|---|---|
| StatusBar 高さ | 54 |
| StatusBar padding（左／右） | 33 / 22 |
| StatusBar 時刻〜インジケータの間 | flex justify-between |
| StatusBar インジケータ間 gap | 5 |
| StatusBar Cellular サイズ | 17 × 11 |
| StatusBar WiFi サイズ | 17 × 12（vertical offset -1.5px） |
| StatusBar Battery サイズ | 25 × 12 |
| Dynamic Island 配置 | top: 11、center、124 × 36 |

### NavigationBar — Classic

| 要素 | 値 |
|---|---|
| 高さ | 48 |
| padding | 0 16 0 8 |
| icon-btn サイズ | 24 × 24 |
| Trailing アイコン間 gap | 16 |
| `align-items` | center |

### NavigationBar — Liquid Glass

| 要素 | 値 |
|---|---|
| 高さ | 54 |
| padding | 0 16 10 16（`pb-10 px-16`） |
| `align-items` | flex-start |
| Pill 高さ | 44 |
| Pill 角丸 | 999 |
| Pill 単独（戻る / AIメモ） | 44 × 44 正円、`min-width: 44`、`padding: 0`、`justify-content: center` |
| Pill グループ（3 アイコン） | 44 高さ、`padding: 0 8`、内部 gap 12 |
| Pill 間 gap | 10 |
| LG icon-btn のタップ領域 | 36 × 36（中の SVG は 24 × 24） |

### Content（映像エリア）

| 要素 | 値 |
|---|---|
| 配置 | flex: 1 |
| `padding-bottom`（中央寄せ用） | 208（= Toolbar bottom 152 + height 56） |
| 自分のアバター（Camera Off） | 144 × 144、`border-radius: 50%`、`object-fit: cover` |

### Toolbar

| 要素 | 値 |
|---|---|
| 配置 | `position: absolute / bottom: 152` |
| 高さ | 56 |
| ボタン間 gap | 22 |
| ボタンサイズ | 56 × 56 |
| ボタン角丸 | 50%（正円） |
| 内側 SVG サイズ | 56 × 56（中央 24 × 24 にアイコンが描画されている設計） |

### Members

| 要素 | 値 |
|---|---|
| 配置 | `position: absolute / bottom: 10` |
| 高さ | 142 |
| padding | 0 8（左右） |
| 縦の中央寄せ | flex `align-items: flex-start` + `justify-content: center` |
| Item サイズ | 80 × 80 |
| Item padding | 8 |
| Avatar サイズ | 64 × 64、`border-radius: 50%` |
| Item 間 gap | 4 |
| アバター下端の画面下からの距離 | 49（= 10 + 31 + 8） |

### Member の選択状態

| 要素 | 値 |
|---|---|
| ボーダー | `box-shadow: inset 0 0 0 1px #7AA9FF` |
| オーバーレイ | `inset: 1px`、`background: rgba(0, 0, 0, 0.7)` |
| ネーム表示 | 中央、Hiragino W3 / 15 / 21 / -0.31、`color: #FFFFFF` |
| z-index | overlay: 1、name: 2 |

---

## 4. アイコン

### NavBar 系

| 用途 | サイズ | viewBox | アセット |
|---|---|---|---|
| 戻る `<` | 24 × 24 | 0 0 24 24 | インライン SVG |
| AI メモ | 24 × 24 | 0 0 24 24 | `aiMemoIcon.svg` |
| スピーカー On | 24 × 24 | 0 0 24 24 | `SperkerOnIcon.svg` |
| スピーカー Off | 24 × 24 | 0 0 24 24 | `SperkerOffIcon.svg` |
| カメラ切替 | 24 × 24 | 0 0 24 24 | `CameraChangeIcon.svg` |
| その他 | 24 × 24 | 0 0 24 24 | `OtherIcon.svg` |

### Toolbar 系

| 用途 | サイズ | viewBox | アセット |
|---|---|---|---|
| Mic | 56 × 56 | 0 0 56 56 | `MicOn.svg` / `MicOff.svg` |
| Video | 56 × 56 | 0 0 56 56 | `VideoOn.svg` / `VideoOff.svg` |
| Share | 56 × 56 | 0 0 56 56 | `ShareIcon.svg` |
| End call | 56 × 56 | 0 0 56 56 | `phoneIcon.svg` |

すべての SVG は `fill="currentColor"` を使い、CSS の `color` プロパティから色を制御する。

---

## 5. インタラクション時のフィードバック寸法

| 状態 | 効果 |
|---|---|
| Toolbar `:hover` | 背景濃度 +0.15（On 0.7→0.85 / Off 0.25→0.35） |
| Toolbar `:active` | 背景濃度 +0.25 + `transform: scale(0.96)` |
| Toolbar Danger `:hover` | `#D10015` → `#BA0013` |
| Toolbar Danger `:active` | `#A50011` + `transform: scale(0.96)` |
| NavBar icon-btn `:hover` | `opacity: 0.75` |
| NavBar icon-btn `:active` | `opacity: 0.55` |

---

## 6. 配色決定の参考メモ

- **NavHeader を黒固定**：iOS 26（Liquid Glass）でも、ChatworkLive では NavHeader を黒で揃える。Liquid Glass の自由度より「映像の上に乗る帯」としての視認性を優先
- **Member item の濃紺四角**：映像が後ろにある状態でも参加者を見分けやすくするためのフレーム。`#13202F` は `surface/base/default` と同値
- **Member 選択時の濃いオーバーレイ**：暗い半透明（70%）でアバターを覆い、その上に白テキストで名前を出すことで、誰を選んだかを最優先に伝える
- **Off 状態のブルー（`feedback/selected/default`）**：「選択中」「アクション中」を表す iOS の標準的なヒント色。マイク／カメラがオフであることを明示
