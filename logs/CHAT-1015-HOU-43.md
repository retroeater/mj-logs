# CHAT-1015-HOU-43

- 着手日時: 2026-10-15
- 対象issue: #518
- ブランチ: work/1015-hou2
- 着手時HEAD: b2da47b94

## 指示

【Claude作成】Claude Code 向け指示：houou/ 個人成績 — 「部門別」タブ（ランキング9部門の値と順位）・要約3枚をやめる・最高到達の置き場の比較・昇級と特別昇級の演出を消す Chat-Ref: CHAT-1015-HOU-43 マージ: 判断待ちで止まる（最高到達の置き場を平野さんが比較ページで選び、見た目と動きをチャット側がプレビューで見るため） 貼る時機: いつでも（CHAT-1015-HOU-42 はマージ済み） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-hou2 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1015-hou2 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-hou2 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
未公開の個人成績 `houou/players/`（#518）に、ランキング9部門の値と順位を選手ごとに見る「部門別」タブを足し、重複する「期別」の要約3枚をやめて4つのタブの上端をそろえる。あわせて、リーグのタブの昇級・特別昇級の演出を消す。
決定（2026-10-15、平野さん）
リーグのタブの演出:

* 昇級・特別昇級の演出（昇級の紙吹雪、特別昇級の放物線で跳ぶ動き・着地の衝撃波の輪・画面の揺れ・段数に応じて増える紙吹雪・5段以上の金・7段以上の金の輪と星）を消す。到達（鳳凰位・A1 の決定戦）の王冠と、昇級の区間のカメラの動き（寄って下からあおる）・昇級の期の余韻（+0.5秒）は残す（2026-10-10・10-11 の昇級・特別昇級の演出の決定を置き換える。2026-10-14 の降級と同じ線引き）

「部門別」タブ:

* 個人成績に4つ目のタブ「部門別」を足す（期別・通算・リーグ・部門別）
* 中身はランキング9部門の、その選手の値と順位。部門名は正式名（通算得点・通算得点/期・期最高得点・期単位浮き率・期連続浮き回数・節最高得点・節単位浮き率・節連続浮き回数・連続昇級回数）
* 順位はランキングに入った部門だけ出す（ランキングの表示対象〈上限100件・同じ値の群れの規則〉に入ったときだけ）。規定に届いていてランキングに入っていない部門の順位欄は「—」
* 規定（出場10期以上・50節以上）に届かない部門は「規定未達」と分かるようにする
* タブの上に「出場 ○期・○節」の1行と注記（規定の内容と、順位は上位100件に入った部門だけであること）を置く
* 各部門の行からランキングのその部門のページへリンクする。押した先では、その選手がランクインしている部門ならその選手の行まで送り、行の地を薄く色付けする。順位の無い部門（— ・規定未達）は表の先頭から開く
* ランキングの選手の行を押したときは、個人成績を「部門別」タブで開く（2026-10-12 の「ランキングのリンク・共有は期別で開く」のうち、ランキングのリンクの分を置き換える。`?name=` だけの共有の URL は今どおり「期別」）

要約と最高到達:

* 「期別」の要約3枚（出場回数・通算成績・最高到達）をやめる（出場回数と通算成績は「部門別」に載るため。4つのタブの上端がそろう）
* 最高到達の置き場は、次の2案の比較ページを作って選ぶ: (a) 名前の面に足す（期間の下などに「最高到達 鳳凰位」）／(b) 「部門別」の上の行に足す（「出場 24期・312節・最高到達 鳳凰位」）

前提（チャット側。平野さんの決定ではない）

* URL の `view` の値は `view=division`（ランキングの部門の URL パラメータ `division` と言葉をそろえる案）。docs/new-page-checklist.md「URL パラメータの規約」の `view` の行に足す
* ランキングの行へ送る・色付けする選手の渡し方は、ランキングのページの URL に `?name=<選手名>` を付ける案（規約の `name` と同じ）。ほかの形がよければ理由をログに書いて変えてよい
* 9部門の並びはランキングの選ぶ部品と同じ順。値の書き方はランキングと同じ（得点の4部門は「+2074.4」「▲12.5」、連続回数は「15期」「16節」「5期」、浮き率は「0.917」）
* 値と順位は生成のときに `lib/ranking.py` と同じ集計から作り、成績 JSON（`houou/results/<選手名>.json`）に足す案（ランキングのページの順位と必ず一致させる）。在籍者だけで順位を付けることも、名寄せの扱いもランキングと同じ
* 「出場 ○期」は、ランキングの規定（`min_seasons`）と同じ数え方。今の要約の「出場回数」（A1・A2 の通期は1回）と同じになる見込み（要確認。食い違えば報告に書く）
* 4等分のタブは、375px で1つ約85px。「部門別」の3文字が1行に収まることを確かめる
* 正式名の部門名（最長は「節連続浮き回数」「通算得点/期」）が 375px で値・順位と1行に収まらないときは、部門名だけ2行に折り返してよい
* 比較ページは docs/notes/design.md「7. チャット側の決め方」の形（ページ内のラジオで切り替え、noindex・どこからもリンクしない・サイトマップに無い）。本体は (b) の形にしておき、決まった後に使わない案と比較ページを消す
* 「部門別」の見た目（行の高さ・区切り線・› ・字の4段）はランキングの表の行と個人成績の期の行の形（docs/notes/design.md の houou/ の行）に合わせる

手順

1. 着手の確かめ: #518・#540 に他のセッションの着手中のコメントが無いか。`git branch -r --no-merged origin/cloudflare` の各ブランチと cloudflare の差に、`assets/houou.js`・`scripts/generate_houou_pages.py`・`scripts/lib/ranking.py`・`style.css` の houou/ の節・`docs/notes/houou-top.md`・`docs/decisions/houou.md`・`docs/new-page-checklist.md` の同じ箇所の変更があるかを確かめ、ログに書く。
2. 実装:
   * 昇級・特別昇級の演出を決定のとおりに消す（王冠・カメラ・余韻は残す）
   * 「部門別」タブ（成績 JSON への値・順位・規定の可否・出場期数と節数の追加を含む）、要約3枚の削除、ランキングの行のリンク先（`view=division`）、ランキングのページで選手の行へ送って色付けする処理、最高到達の (a)・(b) の比較ページ
   * テストを足し・直し、`python3 -m unittest discover -s scripts/tests` を通す（少なくとも: 成績 JSON の9部門の値と順位がランキングのページの表と一致すること、規定未達・ランク外の判定）。`python3 scripts/regenerate.py houou_pages` で生成する
3. 確かめと文書:
   * 白鳥翔・平野良栄・坪原光と、規定未達の部門のある選手1人（入会の新しい選手。選んだ選手をログに書く）で、「部門別」の9部門の値・順位・「—」・「規定未達」がランキングのページと一致すること。成績 JSON の大きさの増え方（合計）
   * 375px・390px・430px × 明・暗で、4つのタブの上端の y がそろっていること、「部門別」の行が切れずに収まること。字 150%・200% で切れ・重なりが無いこと
   * 部門別の行 → ランキング（選手の行が画面に来て色付けされる）、ランキングの行 → 個人成績の「部門別」、の往復
   * 昇級・特別昇級の区間（特別昇級のある選手を1人選ぶ）で、紙吹雪・跳び・輪・揺れが出ないこと、王冠は出ること、再生の全体の時間（変わればその秒）
   * 決定を `docs/decisions/houou.md` に足す（置き換えた決定の扱いは docs/decisions/README.md のとおり）。`docs/notes/houou-top.md`（個人成績・リーグのタブの演出・ランキング・成績 JSON の形）、docs/new-page-checklist.md の URL パラメータの規約を今の内容を読んでから直す（最高到達の置き場は「比較中」と書く）

止まる条件

* #518・#540 に他のセッションの着手中のコメントがある
* 未マージの work/ ブランチが、手順1のファイルの同じ行・同じ関数・同じ節を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* 成績 JSON の9部門の順位が、ランキングのページの表と1件でも食い違う
* 生成の差分に、決定と前提で説明できない種類がある（見込み: `houou/players/`、`houou/results/*.json` の9部門の項目の追加、`houou/ranking/` の9ページの行のリンク先、比較ページの追加、`assets/houou.js` と `style.css` の houou/ の節）。houou/ の外の生成物が変わったら止まる。houou/ の noindex が外れる・navbar・サイトマップ・`llms.txt` に載る差分が出たら止まる
* 「期別」の期の行・「通算」「リーグ」のグラフの見え方や、昇級・特別昇級以外の再生の動きが変わってしまう
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、最高到達の置き場 (a)・(b) のどちらにするか（見る場所は比較ページ。URL は最終報告だけに書く）を書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-HOU-43.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-HOU-43 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-15 着手。HOU-37〜42 と同じセッションで続けた。`CHAT-1015-HOU-43` のコミットは無し。`work/1015-hou2` はローカル・リモートとも無かったので `origin/cloudflare`（b2da47b94）から作った
- 0. 指示欄の末尾は指示文の最後の行と一致。冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1015-hou2
- ログ: https://github.com/retroeater/mj/blob/work/1015-hou2/docs/logs/CHAT-1015-HOU-43.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-hou2
- 確認用URL: なし
- マージ: 未
- issue: #518
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 490aa1a5）: https://github.com/retroeater/mj-logs/tree/main/guide/490aa1a5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/490aa1a5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/490aa1a5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/490aa1a5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/490aa1a5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/490aa1a5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/490aa1a5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/66464371.md
