# CHAT-1009-NEN-08

- 着手日時: 2026-10-09
- 対象issue: #277、#521
- ブランチ: work/1009-nen-year
- 着手時HEAD: cd4e3d2c

## 指示

【Claude作成】Claude Code 向け指示：#277 の方針転換。年表ページ `/title/timeline/` をやめ、title/ の入口の「すべてのタイトル戦」プルダウンを「現在のタイトルホルダー／2026／2025…」の年の断面の切り替えに差し替える（大会ページ・期ページのプルダウンも外す）。未マージ・判断待ちで止まる Chat-Ref: CHAT-1009-NEN-08 マージ: 判断待ちで止まる（公開中の入口・大会ページ・期ページが変わるため、プレビューを平野さんが見てからマージする） 貼る時機: CHAT-1009-NEN-07 が「判断待ち」で止まった後（済んでいる） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-nen-year の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1009-nen-year を使う（未マージの work/1009-nen〈NEN-06・07 の年表の試作〉は採らないので、その上には積まない）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1009-nen-year origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。`origin/work/1009-nen` の docs/logs/CHAT-1009-NEN-07.md の `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。NEN-06・NEN-07 のログ2つを `git checkout origin/work/1009-nen -- docs/logs/CHAT-1009-NEN-06.md docs/logs/CHAT-1009-NEN-07.md` でこのブランチに持ってきて、両方の `## 報告` の状態を「取り下げ（年表ページをやめ、入口の年の切り替えに方針を変えた） / 続き: CHAT-1009-NEN-08」に直す（`## 指示` 欄より後ろの見出しを相手にする）。ログ先行の push にこの2つを含めてよい。

目的
平野さんが #277 の目的を「ある年の全タイトル獲得者を見られること」と定めた（同じタイトル戦の歴代は大会ページで見られる）。入口の「現在のタイトルホルダー」が今の年の断面なので、同じ画面で年を遡れる形にし、年表ページはやめる。この指示では試作までで、マージしない。
決定（2026-10-09、平野さん）

* #277 の目的は「ある年の全タイトル獲得者を見られる」こと。同じタイトル戦の歴代は大会ページで見る
* 年表ページ `/title/timeline/` はやめる（NEN-01〜07 の年表の決定を置き換える）。#521（年表の公開）は閉じる
* title/ の入口の左上の「すべてのタイトル戦」プルダウン（あまり使わない。写真を見て選べばよい）を、「現在のタイトルホルダー」→「2026」→「2025」…と年の断面を切り替える部品に差し替える
* 年を選んだときに出すのは、その年に決勝があった期すべて（WRC リーグのように年2回ある大会は2枚とも出す。どこにも載らない優勝者を作らないため）
* 大会ページ・期ページの固定バーからも「すべてのタイトル戦」プルダウンを外す（固定バーは検索欄だけになる。入口へはパンくずの「タイトル戦」で戻る）
* 同じ年に同じ大会が2〜3期あるときは、そのカードだけ帯に期まで出す（例「JPML WRCリーグ 第14期」）。1期だけの大会は大会名だけ
* 部品の形（チャット側の提案を平野さんが了承）: 固定バーの左に「◀」「年のプルダウン（現在のタイトルホルダー・2026〜1973）」「▶」。URL は `/title/?year=2025`。年の中の並びは入口と同じ決勝日の降順。年を選んだときのカードを押すとその期のページへ（「現在」のカードは今どおり大会ページへ）

前提（チャット側。平野さんの決定ではない）

* 「現在のタイトルホルダー」（既定、`?year` なし）は今の入口の表示・HTML のまま（生成時に焼き込む。SEO・初期表示の重さは変えない）。canonical は `/title/` のまま。`?year=` の表示は sitemap に載せない
* 年の断面のデータ: 既存の `title/search.json` に、各期の1位・写真・期ページの URL・決勝日・大会の表示順が揃っていれば、それを年を選んだときに1回だけ読んで組み立てる（新しいファイルを増やさない）。足りなければ小さな `title/years.json` を生成する。どちらにしたかと理由・大きさを報告に書く。外部依存は増やさない
* カードは入口と同じ写真カード（`photo_card_html()` と同じ見た目、帯に大会名、写真下部に選手名）。JS で組み立てるときも同じクラス・同じ構造にする（帯の字の大きさの式 `--mj-title-taikai-em` も合わせる）。1位が2名の期（第26期王位戦）は2枚。決勝日が `YYYY-XX-XX` で月日が無い期は、同じ年の中で月日のある期の後ろに、大会の表示順で並べる（実物の件数を報告）
* 年のプルダウンは標準の `<select>`（ラベルを付ける。#178〜#185 の指摘を避ける）。選んだら再読み込みせずに表示を替え、`history.replaceState` で `?year=` を書き換える。◀ は新しい年へ、▶ は古い年へ（「現在」の左の ◀ と 1973 の右の ▶ は無効）。決勝の無い年は無い見込み（NEN-01 の経過で 1973〜2026 の毎年ある）だが、あれば選択肢から外す。`?year=` が範囲外・不正なら「現在」を出す
* 検索欄との関係: 今どおり、検索している間はページの本来の内容（`.mj-title-content`）を隠す。年の切り替えはその本来の内容を替える
* 大会ページ・期ページのプルダウンを外すと、`assets/title.js` のプルダウンの処理（`#title_select`）・`filterbar_html()` の引数・CSS が要らなくなる。入口の年のプルダウンと取り違えないよう、id・クラスは別名にする。固定バーの高さの仕組み（`--mj-title-filter-h`）は変えない
* URL パラメータ `year` を docs/new-page-checklist.md「URL パラメータの規約」の表に足す（作る前に足す規約）。旧表からの `?name=`（#441・#485）の受け取りは変えない
* 年表のやめ方: `title/timeline/`・`img/ogp/title/timeline-black.png`・`_redirects` の `/title/timeline` の1行・`generate_title_pages.py` の年表の生成（`NON_TAIKAI_DIRS` は空にするか、仕組みごと消すか。テストと合わせて Code が決め、報告に書く）・`scripts/tests/test_title_timeline.py`・`style.css` の年表の節・`assets/title.js` の年ジャンプを消す。公開していない（noindex・導線なし）ので転送は作らない。docs/notes/title-pages.md「年表」節・docs/notes/static-generation.md「ページの一覧」の年表の行を消し、入口の年の切り替えの記述を足す
* 決定の記録: docs/decisions/title.md に上の「決定」を「2026-10-09（CHAT-1009-NEN-08）」として足し、NEN-01・NEN-04 などの年表の決定の行に「→ 置き換え: 2026-10-09（CHAT-1009-NEN-08、年表ページをやめた）」を付ける（README「書き方」。行ごとでなく節の見出しに1つでもよい）。NEN-06・NEN-07 の決定はログにしか無いので、取り下げとして1行で足す
* issue: #521 に「年表ページをやめ、入口の年の切り替え（#277）にしたため閉じる」とコメントして閉じる（ラベル「状況:」があれば外す。この指示で閉じてよい。年表は本番に noindex で残るが、このブランチのマージで消える旨も書く）。#277 には方針の変更と試作の着手をコメントし、閉じない（マージの指示で閉じる）
* 取り込みの衝突の扱い: docs/decisions/ の追記どうし、docs/handover.md・docs/notes/ の隣り合う行で両立する衝突は、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。生成物だけの衝突は CLAUDE.md「ブランチ運用」のとおり生成し直す。それ以外の衝突は解かずに止まる
* 未マージの work/1009-nen は捨てる（CLAUDE.md「未マージのブランチは削除しない」の例外。平野さんの決定で試作を採らないため）。クラウドセッションでは削除できない（docs/notes/cloud-sessions.md「ブランチの削除」）ので、削除せず、先頭の SHA をログに書き、「平野さんが GitHub の画面で削除する」と報告に書く

手順

1. 確かめる: #277・#521 が Open で、他セッションの着手中コメントが無い。未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`）が `title/`・`scripts/generate_title_pages.py`・`assets/title.js`・`style.css` の `.mj-title*`／`.mj-tl-*` 節・`_redirects` の title の行を変えていない（`work/1009-nen` はこの指示で捨てるもの。`work/1008-hou` の houou/ の行・節は当たらない）。生成と同じ経路でシートを読み、表示する大会数・期数、年ごとの期数の最小・最大、同じ年に同じ大会が2期以上ある組の数、月日の無い期の数を報告する。`title/search.json` に年の断面に要る項目が揃っているかを確かめる。#277 にコメントする
2. 作る: 上の決定・前提のとおり、年表を消し、入口の年の切り替えを入れ、大会ページ・期ページのプルダウンを外し、`python3 scripts/regenerate.py title_pages` で生成し直す。生成物の差分を種類に分けて報告する（見込み: 入口・大会ページ・期ページの固定バーの変化〈全ページ〉、年表の削除、データのファイル〈足したなら〉。他に出たらシートの変化で説明できるか書く）。テスト（年の断面の組み立て〈2026・2025・1973、WRC の2期、王位戦 第26期の2枚、帯の期の有無〉・プルダウンが無いこと・年表の名残が無いこと）を足し・直し、`python3 -m unittest discover -s scripts/tests` を通す。決定・文書・docs/handover.md 5章（#277 の記述。警告域 26,624 の外）を直し、#521 を閉じる
3. 確かめる: Workers Builds のプレビュー（上限15分）を headless Chromium で開き、375px・1280px・1920×1080 で、入口の「現在」の表示が本番と同じこと、年の切り替え（プルダウン・◀▶・`?year=2025` で直接開く・不正な `?year=`）、2025 の WRC リーグ・WRC-R リーグの帯、1973、検索との行き来、大会ページ・期ページの固定バー（検索欄だけ、高さ）、JS のエラーが無いことを確かめ、表でログに書く。平野さんに見てもらう手順（確認用 URL、入口で ◀▶ とプルダウン、2025・2014・1973、大会ページ・期ページの固定バー、スマホと PC）を報告に書く

止まる条件

* #277 か #521 が閉じている、他セッションの着手中コメントがある、未マージの `work/` ブランチが上のファイルを変えている（上の例外を除く）
* 表示する大会が 20〜21、期が 363〜365 の範囲外（件数を書いて止まる）
* 生成物の差分に、上の見込みとシートの変化で説明できないものが出た
* 既存のテストが落ちる（年表のテストと、プルダウンを前提にしたテストは直してよい。それ以外は止まる）
* 入口の「現在」の表示（カードの並び・中身）が本番と変わる
* docs/handover.md が警告域に入る
* cloudflare への push が求められる状況になった（しない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんが確かめる点（部品の見た目・◀▶ の向き・帯の期の出し方・並び・大会ページ／期ページの固定バー）、見てもらう手順、work/1009-nen の削除（平野さんの手作業）、マージで #277 を閉じることを書く
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告に「確認用:」の行（プレビュー URL）を入れる
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-NEN-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-NEN-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。`origin/work/1009-nen` の NEN-07 の状態は「判断待ち」だった。NEN-06・NEN-07 のログを `git checkout origin/work/1009-nen -- …` で持ってきて、両方の状態を「取り下げ（年表ページをやめ、入口の年の切り替えに方針を変えた） / 続き: CHAT-1009-NEN-08」に直した。このセッションは NEN-01〜07 から続けて受けたもの
- 識別子: `git log --all --grep="CHAT-1009-NEN-08"` は0件
- ブランチ: `work/1009-nen-year` はローカル・リモートとも無かったため `git checkout -b work/1009-nen-year origin/cloudflare`
- 捨てる `work/1009-nen` の先頭: 50ace8968（`origin/work/1009-nen`、未マージ。NEN-06・NEN-07 の年表の試作）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1009-nen-year
- ログ: https://github.com/retroeater/mj/blob/work/1009-nen-year/docs/logs/CHAT-1009-NEN-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-nen-year
- 確認用URL: なし
- マージ: 未
- issue: #277、#521
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e1cabc36）: https://github.com/retroeater/mj-logs/tree/main/guide/e1cabc36

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cd4e3d2c.md
