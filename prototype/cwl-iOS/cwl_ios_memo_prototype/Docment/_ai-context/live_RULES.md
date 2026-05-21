# RULES.md — ChatworkLive 会議中画面（挙動・ルール）

> ChatworkLive の「会議中メイン画面」のインタラクション・状態遷移・実装ルール。
>
> - 構造仕様：`live_ARCHITECTURE.md`
> - 見た目（色・寸法）：`live_DESIGN.md`
> - 関連プロトタイプ：`cwl_ios_memo_prototype.html`
> - 最終更新：2026-05-07

---

## 1. 全体方針

- **行動を妨げない・強要しない**：マイク／カメラ／参加者の選択は何度でもやり直せる。意図しない確定は出さない
- **状態は分かりやすく**：On/Off、選択／未選択は色とアイコンの両方で示す。色だけに頼らない
- **撤回可能にする**：トグル操作（Mic / Video / Member 選択）は単独タップで戻せる。退出など破壊的な操作のみ確認を入れる

---

## 2. マイク（Mic）トグル

### 操作

- ボタンをタップで On ⇄ Off を反転
- 既定は **On**
- アクセシビリティ：`role="button"` 相当、`aria-pressed` を `data-state` と同期（Off のとき `true`）

### 状態と表示

| 状態 | 背景 | アイコン |
|---|---|---|
| On | `rgba(0, 0, 0, 0.7)` | `MicOn.svg` |
| **Off** | `rgba(0, 89, 255, 0.25)` | `MicOff.svg` |

### 副作用

- なし（Mic のトグルは Content / Members の状態に影響しない）

### 制約

- 退出後はボタン自体が無効化される予定（プロト未実装）

---

## 3. カメラ（Video）トグル

### 操作

- ボタンをタップで On ⇄ Off を反転
- 既定は **On**

### 状態と表示

| 状態 | ボタン背景 | アイコン |
|---|---|---|
| On | `rgba(0, 0, 0, 0.7)` | `VideoOn.svg` |
| **Off** | `rgba(0, 89, 255, 0.25)` | `VideoOff.svg` |

### 副作用（Content への伝播）

- カメラを Off にすると `device-screen` に `data-camera="off"` が付く
- その結果：
  - Content 背景が `#C42760`（ピンク）→ **`#13202F`**（濃紺）に切り替わる
  - 自分のアバター（144 × 144 / 正円）が Content の中央に表示される
- カメラを On に戻すと `data-camera="on"` になり、上記が解除される

### 注意

- Member が選択されていても、Camera Off の挙動は同様（仕様未確定領域。`live_ARCHITECTURE.md` の今後の拡張参照）

---

## 4. 画面共有（Share）

- 単発アクション（トグルではない）
- タップで「画面共有開始」フローに入る（プロト未実装）
- スタイル変化：`:hover` / `:active` のみ

---

## 5. 退出（End call）

- **破壊的アクション**：色は Danger（`#D10015`）
- 本来は「本当に退出しますか？」のような確認ダイアログを挟むのが望ましい（`_ai-context/DESIGN.md` セクション 6 の「破壊的な操作」に従う）
- プロトでは確認ダイアログ未実装、タップ時のフィードバックのみ
- 退出フローの拡張：会議終了後にチャットへ「会議が終了しました」メッセージを自動投稿（今後）

---

## 6. Member（参加者）選択

### 操作

- Member item をタップ／Enter / Space で **選択**
- 同じ item を再操作 → **解除**
- 別の item を操作 → 前の選択を解除して新しい方を選択（**同時選択は 1 人のみ**）
- アクセシビリティ：`role="button"` + `tabindex="0"`、フォーカス可能

### 状態と表示

| 状態 | 表示 |
|---|---|
| 未選択（既定） | 80 × 80 の濃紺フレーム + 円形アバターのみ |
| **選択中（is-selected）** | 1 px の水色ボーダー（`#7AA9FF`）+ 暗オーバーレイ（`rgba(0,0,0,0.7)`）+ ユーザーネーム（白、Hiragino W3 / 15）が中央表示 |

### 副作用（今後）

- 現状：Members 列の見た目変化のみ
- 発展：選択ユーザーの映像／アバターを Content 中央に表示する（実装予定）
  - Camera Off との優先順位：Member 選択を優先（自分の映像より相手の映像のほうが重要）

---

## 7. NavBar の操作

### 戻る `<`

- タップで前画面に戻る（プロトはダミー）

### AI メモ

- AI 会議メモのモーダル／シートを呼び出す（プロト未実装）

### スピーカー

- スピーカー出力の On/Off トグル（プロト未実装）
- 想定挙動：Mic / Video と同パターン（タップで `SperkerOnIcon.svg` ⇄ `SperkerOffIcon.svg` の切替、`aria-pressed` を同期）
- 副作用なし（NavBar 内のアイコン表示変更のみ）

### カメラ切替

- フロントカメラ / リアカメラ切替（プロト未実装）

### その他

- メニューを開く（プロト未実装）

---

## 8. テーマ・モードの切替

プロトタイプ上部のタブで切替できるが、**会議中画面では NavHeader と Toolbar のスタイルは Mode/OS の影響を受けない**（黒と Off ブルーで固定）。Mode 切替は Content の副次要素や、今後追加するシート等に対して効くようにする。

| 切替 | 影響 |
|---|---|
| Mode（Light / Dark） | NavHeader、Toolbar、Content（Camera Off 時の濃紺） は固定。今後追加するパネルに反映予定 |
| OS（iOS 18 / iOS 26） | NavBar の見た目のみ切替（Classic フラット ⇄ Liquid Glass Pill）。Toolbar は両モードで同じ |

---

## 9. レイアウト共通ルール

- スペーシングは 8 px グリッド（`_ai-context/DESIGN.md` の Spacing System に準拠）
- 例外的に iOS の慣習に合わせて 22 px / 10 px / 4 px 等を許容（Toolbar gap 22、Pill 間 gap 10、Member 間 gap 4）
- 画面下から固定の絶対配置（Toolbar / Members）は **下からの距離（bottom）で指定**。Top 基準にしない
- アイコンは `currentColor` を使い、CSS の `color` で着色する

---

## 10. アクセシビリティ

- すべてのトグル・選択ボタンに `role="button"` または実 `<button>` を付ける
- `aria-pressed` を On/Off トグルに付ける（Off のとき `true`）
- `aria-label` で日本語のラベルを付ける（`戻る` / `マイク` / `カメラ` / `共有` / `退出` / `AIメモ` / `スピーカー` / `カメラ切替` / `その他`）
- キーボード操作：Member item は Enter / Space で選択可能
- 装飾用の SVG には `aria-hidden="true"` を付ける
- 色だけで状態を伝えない：On/Off は背景色 + アイコン形状の両方で示す

---

## 11. プロトタイプ実装上の制約

- 単一 HTML ファイル（`cwl_ios_memo_prototype.html`）に CSS / JS をインライン
- 外部依存：アセット PNG / SVG はローカルファイル参照のみ
- 本物のビデオ映像は使えないため、**Content 既定背景はピンクで代用**（後で映像差し替え）
- backdrop-filter を多用するため、Safari / Chrome 等のモダンブラウザで確認すること
- ローカル localStorage 等のブラウザストレージは使わない

---

## 12. 拡張時のチェックリスト

新しい状態・画面を追加する際は以下を順守する：

- [ ] `live_ARCHITECTURE.md` に画面構成・配置を追加
- [ ] `live_DESIGN.md` に色・寸法・タイポを追加
- [ ] `live_RULES.md` に挙動・遷移・破壊的操作の扱いを追加
- [ ] アセット追加時は `Docment/Asset/icon` または `Docment/Asset/image` に配置し、`ARCHITECTURE.md` に表を更新
- [ ] アクセシビリティ（role / aria-label / aria-pressed / キーボード）を確認
- [ ] iOS 18 / iOS 26、Light / Dark の 4 通りで崩れないか確認
- [ ] Camera Off 時 / Member 選択時に矛盾しないか確認
