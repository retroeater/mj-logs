# CHAT-1013-HOU-33

- 着手日時: 2026-10-13
- 対象issue: #518
- ブランチ: work/1013-hou
- 着手時HEAD: 62f48a8b

## 指示

【Claude作成】Claude Code 向け指示：個人成績の直し5点（名前の右に現在のリーグのチップ、期別の行の左の余白、鳳凰位の行は開かない、通算のグラフをリーグと同じ大きさに、リーグの吹き出しを3行に）。プレビューで止まる Chat-Ref: CHAT-1013-HOU-33 マージ: 判断待ちで止まる（プレビューを平野さんが見て決める） 貼る時機: CHAT-1013-HOU-32 の完了（マージ）の後（新しいセッションに貼る） 作業ブランチ: クラウドセッションで実行する。work/1013-hou を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1013-hou origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-hou の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1013-HOU-32 のログの `## 報告` を読み、状態が「完了」で cloudflare へマージ済みであることを確かめる（でなければ何もせず止まる）。 この指示は新しいセッションで貼る前提。このセッションが新しく始めたものかをログの経過に書く。

目的
HOU-32 でマージした個人成績 `houou/players/` に、平野さんの直し5点を入れる。houou/ は未公開のまま。
決定（2026-10-13、平野さん）

* 名前のカード: 名前の右に、その選手の現在の「リーグ」のチップを出す（ほかの情報は足さない。ビジーにしない）。10-09 の「支部・入会期はカードに出さない」は変えない
* 「期別」の各行: 左端の空白をもう少し空ける。1行に収まる（折り返さない）前提で、余地があるときだけ
* 「期別」で「鳳凰位」の行は、下向きの矢印（開閉の印）を出さず、押しても開かない
* 「通算」のグラフの領域の大きさを「リーグ」と同じにする（リーグくらい大きく）
* 「リーグ」のグラフに触れたとき（ホバー）の吹き出しを、「通算」のように行を分ける。形は次の3行: 

```
38期後期E2 21位/24人 残留全体 371位/377人

```

前提（チャット側。平野さんの決定ではない）

* 現在のリーグの決め方（仮置き）: 期別の一番上（最新）の行のリーグと同じにする。その行がデータの最新の期（今は第43期前期）でなければ出さない（いま在籍していても最新の期に出ていない選手）。A1・A2・鳳凰位の通期の扱いは期別のカードと同じ。チップの形・色は期別のリーグのチップ（L3）と同じ。名前が長くてチップと1行に収まらないときは名前を折り返し、チップは名前の最後の行の右に置く。該当する選手の数と名前の例（最新の期に出ていない在籍者の数と名前の一覧）をログに書く
* 期別の左の余白（仮置き）: 今の 6px を下限にし、画面の幅に余りがあるほど広げる（上限を決める。例 16px）。375px・標準の文字の大きさで、今の期のカードの余り（HOU-26・HOU-31 の 7.6px〈IPA P ゴシック 2.4px〉）を食いつぶして2行にならないこと。375px・390px・430px・1920px での左の余白の実測値をログに書く。右の余白との釣り合い（左だけ広げるか、左右とも広げるか）はどちらにしたかと理由を書く
* 鳳凰位の行: 開閉のボタンにしない（`aria-expanded` を付けない、押しても何も起きない）。行の高さ・区画のそろい方は他の行と同じ（印の欄は見えないまま残すか詰めるかは、他の行と縦がそろう方）
* 通算のグラフ: 今の 380×250 を、リーグのグラフ（380×340）と同じ大きさにする。旗・星・下向きの印のラベルの置き場所の探し方は今のまま（縦が広がっても重ならないこと）
* リーグの吹き出し: 1行目は期（「38期後期」。A1・A2・鳳凰位は「42期」の書き方、#541 の暫定のまま）、2行目は「リーグ 順位/人数 結果」（結果のチップは通算の吹き出しと同じ置き方。結果の無い期はチップなし）、3行目は「全体 順位/人数」。出場していない期の「35期後期 出場なし」は1〜2行のままでよい。数字・文言は今の吹き出しの値をそのまま使う

手順

1. 確かめる（食い違えば止まる）: `git branch -r --no-merged origin/cloudflare` で未マージのブランチを一覧にし、`assets/houou.js`・`style.css` の `.mj-houou-*`・`scripts/generate_houou_pages.py` の同じ行・同じ関数を変えているものが無いこと。今のリーグの吹き出しの文言の組み立て（関数名）と、通算・リーグの SVG の大きさの定数の場所をログに書く
2. 直す: 上の「決定」「前提」のとおり。生成し直し、`--check` とテストを通す。headless Chrome の 375px・390px・430px・1920×1080 で次を確かめ、スクリーンショットを `docs/logs/CHAT-1013-HOU-33/` に残す: 白鳥翔・平野良栄・一森かなの名前のカード（チップ）、最新の期に出ていない在籍者1名の名前のカード（チップなし）、白鳥翔の期別（鳳凰位の行に印なし・押しても開かない）、平野良栄の通算とリーグ（同じ大きさ）、リーグの吹き出し（通常の期・A1 の期・結果の無い期・出場なしの期）
3. 文書を直す: docs/decisions/houou.md に上の「決定」を足す（10-09 の名前のカードの決定のうち、チップを足すことで変わる部分にその旨）。docs/notes/houou-top.md の「個人成績」「リーグのタブ」「仮置きの一覧」（追記先の今の内容を読んでから。同じ趣旨の記述は置き換える）。作業ブランチに push し、確認用 URL を出して止まる

止まる条件

* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 375px・標準の文字の大きさで、期のカード・名前とチップ・タブが1行に収まらない（名前の折り返しは許す）。直せなければ止まる
* `--check` やテストが今回の変更で落ちて直せない（無関係な失敗は原因を書いて進めてよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんに見てもらう画面（確認用 URL に付けるもの: `?name=白鳥翔`／`?name=平野良栄&view=total`／`?name=平野良栄&view=league`／最新の期に出ていない選手1名）と、仮置きにしたものの一覧を書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-HOU-33.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-HOU-33 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e2f06f23）: https://github.com/retroeater/mj-logs/tree/main/guide/e2f06f23

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2f06f23/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2f06f23/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2f06f23/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2f06f23/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2f06f23/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2f06f23/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2ae0e836.md
