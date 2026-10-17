# CHAT-1017-PAD-01

- 着手日時: 2026-10-17
- 対象issue: #428
- ブランチ: work/1017-pad
- 着手時HEAD: 6e1da5ac7

## 指示

【Claude作成】Claude Code 向け指示：#428 の残り — `:has()` の成立待ちで遅れて当たる上余白・固定・幅の洗い出しと計測（調査のみ）
Chat-Ref: CHAT-1017-PAD-01
マージ: ドキュメントのみ（ログと docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（新しいセッションに、この指示だけを貼る。PAD は新しい識別子で、このチャットの最初の指示）
作業ブランチ: クラウドセッションで実行する。work/1017-pad を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-pad origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-pad の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

#428 の残り（最強戦の分は 2026-10-10 にマージ済み、2026-10-15 に対象から外した）として、`body:has(...)` / `html:has(...)` が本文のパースまで成立せず、上余白・固定・幅が遅れて当たってレイアウトがずれる形を、サイト全体で洗い出して計測する。直すのは結果を見て決めるので、この指示ではコード・CSS・生成物を変えない。

### 決定（2026-10-17、平野さん）

- 今日（10/17）の作業は「#428 の残り」と「`live/` の写真カードの iPhone の実機確認」とする（第3弾〈#525〉の実機確認は SWP-17 で済んでいる。`live/` の分だけが残っていた）
- #428 の洗い出しの範囲は、issue の3種類（`title/`・`/live`・表のページ〈`body:has(.mj-table)`〉）に、動画のページ（`.mj-video-page` / `.mj-video-list`。帰り道・Mつく・女流勉強会）を足した全部にする。直すのは計測でずれが出たものだけ（案A）

### 前提（チャット側。平野さんの決定ではない）

- チャット側が 2026-10-17 に origin/cloudflare の `style.css` を grep した結果（要確認）:
  - `position: fixed` ＋ `body` の `padding-top` を `:has()` で当てている: `body:has(.mj-table)`（`padding-top: var(--content-offset, 90px)`、`html:has(.mj-table)` の `scroll-padding-top`）、`body:has(.mj-title)`（`padding-top: calc(var(--mj-title-nav-h, …) + var(--mj-title-filter-h, 61px))`、幅で値が変わる）。#428 の本題と同じ形に見える
  - `position: sticky` を `:has()` で当てている: `body:has(.mj-video-page) nav.navbar`、`body:has(.mj-video-list, .mj-live) .mj-filterbar`。sticky は成立前も通常の流れの位置に置かれるので、上余白のずれは出にくいと見ている（見込み。計測で確かめる）
  - 幅を `:has()` で決めている: `body:has(.mj-live) .mj-lead`・`body:has(.mj-video-list) .mj-lead`・`body:has(.mj-table) .mj-lead`・`body:has(#pros_table)` など。最強戦で `.mj-lead` の幅が `:has()` 待ちで変わり CLS が増えた例がある（`style.css` の 1700 行目付近のコメント、CHAT-0921-SG-04）。これも同じ形として数える
  - `:has()` のうち、レイアウトを変えないもの（色・フォーカスの見た目、`a:has(> img)` など）は対象外
- 対象のページは、上の各セレクタのクラスを持つ HTML を grep で拾って種類ごとに代表を選ぶ（例: `title/` の入口・大会・期、`live/` の入口・大会・卓〈`live/ourai/3/f.html` など〉、表のページ数枚〈`rh_results_detail` など〉、`video_wayhome.html`・`wayhome/` の個別1枚・`mtsuku/`。`study/` は未公開なので origin/cloudflare の生成物で測る）
- 計測の方法は #428 の saikyo/ の分（CHAT-1010-SKS-01、#428 の 2026-10-10 のコメント）にそろえる: headless Chromium、幅 390px と 1100px、各ページ・各幅 10回。見るのは (1) rAF ごとに、`<body>` があるのに `:has()` で当てる値がまだ当たっていない（`padding-top` が 0、`.mj-lead` の幅が既定のまま など）フレームの数、(2) CLS の「0 でない回数」と中央値（1回ごとの最大値と「すべて 0」は条件にしない）。シフトがあれば動いた要素（`main#main` など）を書く。計測スクリプトは `docs/logs/CHAT-0919-HG-01/cdp.py` を使ってよい
- 環境は本番（`https://ryoei.pro/`、キャッシュ回避のクエリ付き）か `file://`（origin/cloudflare の生成物）のどちらか一方に決め、全ページを同じ環境で測る。本番に届かなければ `file://` にする（どちらにしたかと理由をログに書く）。外部の画像が届かないときは saikyo/ のときと同じ扱いでよい
- 直し方の候補の参考: saikyo/ は生成スクリプトで `<body class="mj-saikyo-page">` を付けて `:has()` をやめた（`scripts/lib/page.py` の `render_content()` には移さない、2026-10-10 の平野さんの決定）。表のページ・`title/`・`/live`・動画のページで同じ形がとれるか（生成スクリプトか手書き HTML か、どのファイルを変えるか）を、計測でずれが出た種類についてだけ案にする
- 決定の書き先は、第3弾の決定と同じ `docs/decisions/site-review.md` を想定（要確認。#428 の分野に合うファイルが別にあればそちら）
- 使う skill は無い

## 手順

1. 確かめる: #428 の本文と最新のコメント（2026-10-15、CHAT-1015-SKR-01 まで）を読み、他セッションの着手中コメント（未解除のもの）があれば止まる。#428 に着手中のコメントを書く（本文・題・ラベルは変えない）。上の前提の `style.css` の grep を origin/cloudflare で行い直し、`:has()` を使うセレクタを「レイアウトを変える／変えない」に分けた一覧をログの経過に書く（セレクタ・当てる値・持つページ・JS が後から同じ値を入れるか〈`assets/title.js` の `--mj-title-nav-h` など〉）。前提と食い違えば、食い違いを書いて実物に従う
2. 測る: 上の前提の方法で、レイアウトを変える種類ごとに代表のページを測り、種類 × ページ × 幅 の表にする（遅れたフレームの数、CLS の 0 でない回数／中央値、動いた要素）。JS 無効のときに本文が固定バーの下に隠れないかも、`position: fixed` の種類で見る
3. まとめる: ずれが出た種類だけ、直し方の案（変えるファイル・クラス名の案・ほかのページへの影響）を書く。ずれが出なかった種類は「直さない」理由を1行で書く。結果を #428 にコメントする（範囲を動画のページまで広げたこと〈2026-10-17、平野さんの決定〉を冒頭に書く。表はログの `## 経過` の計測の表から引用する。末尾に `Chat-Ref: CHAT-1017-PAD-01`）。直すかどうか・どの案にするかは「判断が必要なこと」に書く

## 止まる条件

- #428 に他セッションの未解除の着手中コメントがある
- `style.css` に `:has()` で上余白・固定・幅を当てるセレクタが1つも無い（前提が大きく食い違う）
- 計測の環境が途中で変わる（本番と `file://` が混ざる）ことになったら、混ぜずに止まり、そこまでの結果を書く
- 未マージの work/ ブランチが `style.css` の、上の一覧のセレクタと同じ行を変えている場合は止まらず、ブランチ名と行をログに書く（この指示はコードを変えないため）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（変わるのは docs/logs/ と docs/decisions/ だけの見込み。ほかが出たら入れずに止まる）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-PAD-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-PAD-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子の確認: `git fetch --unshallow origin` の後、`PAD` のコミット・`docs/logs/` の履歴ともに0件。同じ Chat-Ref のコミットも0件
- 作業ブランチ: ローカル・リモートとも `work/1017-pad` が無いため `git checkout -b work/1017-pad origin/cloudflare`
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 揃っている
- 0. 「指示」欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致

## 報告

- 状態: 作業中
- ブランチ: work/1017-pad
- ログ: https://github.com/retroeater/mj/blob/work/1017-pad/docs/logs/CHAT-1017-PAD-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-pad
- 確認用URL: なし
- マージ: 未
- issue: #428
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6e1da5ac）: https://github.com/retroeater/mj-logs/tree/main/guide/6e1da5ac

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e1da5ac/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e1da5ac/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e1da5ac/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e1da5ac/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e1da5ac/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6e1da5ac/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/6e1da5ac.md
