# CHAT-0930-BNG-04

- 着手日時: 2026-09-30
- 対象issue: #486（コメント）、#5・#283（1行コメント）
- ブランチ: work/0930-bng
- 着手時HEAD: 0fadc73a

## 指示

【Claude作成】Claude Code 向け指示：#486 に平野さんの決定と Bing の対象 URL を記録し、Bing の指摘とリポジトリの差を確かめる（読むだけ・issue 操作のみ） Chat-Ref: CHAT-0930-BNG-04 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/0930-bng を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/0930-bng origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-0930-BNG-03 のログの `## 報告` を読み、マージ済みでなければ止まる。

目的
#486（BNG-03 で起票）の「決めること」に平野さんの決定を記録し、BNG-03 で取れていなかった Bing の対象 URL の一覧を issue に残す。Bing の指摘（h1 無し 4・description 無し 2・h1 複数 1・alt 無し 1）は BNG-03 の HTML の照合と食い違うので、その差の理由を確かめてログと issue に書く。コードは変えない。
決定（2026-09-30、平野さん）

* タイトルの長さ: #486 では扱わず、#5（title 整備）に含める。短すぎるページ（9 文字台）だけ、#5 の中で共通の末尾（「｜ryoei.pro」のような形）を付けるか検討する。
* h1 の無い 11 ページ: #283（h1 と title の文言統一）と一緒に h1 だけ先に足す。順序は #283 → #486 の h1 → #7（Google Charts からの移行）。
* 短い description: 優先度は低く、#5 の title 整備と同時に見直す程度。`404.html` には description を足さない。
* `/jpml_logs.html`（本番 404）: 後継ページは無いので 404 のままにし、Bing から自然に消えるのを待つ（301 は張らない）。
* 被リンク不足・IndexNow は対応なし（#126 の結論のとおり）。
* この指示の変更は docs/logs のみ。ログの cloudflare へのマージ: 承認済み（チャットで）。push が権限判定で拒否されたら、別の手段を試さずに止まる。
* マージ: 承認済み（チャットで）

Bing Recommendations の対象 URL（2026-09-30、平野さんが CSV で取得。この環境からは確かめられない）

* `<h1>` タグがありません（4）: `/houou_results.html?name=滝沢和典`, `/ouka_ranking.html?sheet=桜花`, `/houou_ranking.html?sheet=鳳凰`, `/ouka_results.html`
* 説明がページのヘッド セクションにありません（2）: `/ouka_ranking.html?sheet=桜花`, `/ouka_results.html`
* ページに複数の `<h1>` タグがあります（1）: `/`
* `<img>` タグに ALT 属性がありません（1）: `/`
* Meta descriptions too short（5）・タイトルが短すぎる（14）・コンテンツ不足（8）: BNG-03 の一覧と同じ（変化なし）

前提（チャット側。平野さんの決定ではない）

* BNG-03 の HTML の照合では h1 複数 0・alt 無し 0・description 無しは `404.html` だけ。Bing の指摘との差は、(a) Bing がクロールした時点の古い HTML、(b) JS が描画・挿入した要素（トップページの帰り道サムネイル〈#150〉や /live の埋め込み、型B の描画後の見出し）、(c) `?sheet=`・`?name=` 付きの URL が同じ HTML を返すこと、のいずれかと考えられる。どれかを実態で確かめる。
* 対応の可否は決めない。差の理由と、直すなら何をどこで直すかの案を #486 に書く。

手順

1. #486 に「## 決定（2026-09-30、CHAT-0930-BNG-04）」のコメントを1件書く: 上の「決定」5点と、Bing の対象 URL の4つの一覧。#5 と #283 にはそれぞれ1行のコメント（#486 の決定で、title の長さ・共通の末尾の検討は #5 に、h1 の無い 11 ページへの h1 追加は #283 と一緒に行い順序は #283 → #486 → #7、と決まったこと）。#486 の本文の「決めること」は、決まった項目に「→ 決定済み（コメント参照）」を付ける程度で、本文の書き換えはしない。
2. 差の理由を確かめる（読むだけ）: (a) `index.html`（`/`）について、静的 HTML の h1 の個数と alt の無い `<img>` に加え、JS が挿入する要素（帰り道サムネイル・/live の埋め込み・その他 `innerHTML`/`createElement` で作る `h1`・`img`）を `scripts/` と `assets/` のコードから追い、描画後に h1 が2つ以上・alt の無い img が出るかを判定する。可能ならヘッドレスで描画して確かめる（環境に無ければコードの読み取りだけで判定し、そう書く）。(b) `ouka_results.html`・`ouka_ranking.html`（`?sheet=桜花`）・`houou_ranking.html`（`?sheet=鳳凰`）・`houou_results.html`（`?name=` 付き）について、description と h1 が HTML にあるか（BNG-03 の表）と、それらがいつから入ったか（`git log -S` で追加コミットの日付）を確かめ、Bing の最終クロール日（平野さんの画面: `ouka_results.html` は 2026-09-15、`houou_results.html?name=…` は 8〜9 月）より後なら「古い HTML を見ている」と判定する。(c) `?sheet=`・`?name=` 付きが同じ HTML を返すことを `_redirects`・生成物で確かめる。
3. 結果を #486 にコメント（見出し「## Bing の指摘との差の理由（CHAT-0930-BNG-04）」）: 項目ごとに「理由（a/b/c）」「直す必要があるか」「直すなら何をどこで（例: 帰り道サムネイルの img に alt を付ける〈#150〉、型B の h1 は #283 と一緒に）」を表で書く。alt 無しが JS 挿入の img なら、#150 か該当 issue にも1行のコメントを付ける。何も直さない。

止まる条件

* 作業ブランチの条件を満たさない、または BNG-03 がマージ済みでない。
* #486 に他セッションの「着手中」コメントがある。
* 変更が docs/logs と issue 操作以外に及ぶ。
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。手順3の表の要点（h1 複数・alt 無し・description 無しの理由と、直す案）を報告に書く。
* マージ: 承認済み（チャットで）。docs/logs のみの変更なので、完了報告のうえ cloudflare へマージする。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-BNG-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-0930-BNG-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前確認: 同じ Chat-Ref のコミット無し。BNG-03 は「マージ: 済」、`origin/work/0930-bng` は `origin/cloudflare` の祖先。`git merge --ff-only origin/cloudflare` で 0fadc73a へ進めた。指示欄の末尾は指示文の最後の行と一致。

## 報告

- 状態: 中断（着手直後）
- ブランチ: work/0930-bng
- ログ: https://github.com/retroeater/mj/blob/work/0930-bng/docs/logs/CHAT-0930-BNG-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-bng
- 確認用URL: なし
- マージ: 未
- issue: #486
- 判断が必要なこと: なし
- 未確認の項目: 手順1〜3すべて
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ae4d7104）: https://github.com/retroeater/mj-logs/tree/main/guide/ae4d7104

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ae4d7104/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ae4d7104/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ae4d7104/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ae4d7104/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ae4d7104/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ae4d7104/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ad723967.md
