# CHAT-1009-NEN-10

- 着手日時: 2026-10-09
- 対象issue: #277
- ブランチ: work/1009-nen-year
- 着手時HEAD: e7f8284f

## 指示

【Claude作成】Claude Code 向け指示：#277 の本実装とマージ。年の切り替えは S1（プルダウンだけ）に確定して比較の仕組みを消し、選択肢の文字色・「現タイトルホルダー」の文言・年を選んだときのタブの題名を直し、title/ の共有ボタンを全ページから外す。cloudflare へマージし #277 を閉じる。共有ボタンのサイト全体の見直しを別 issue に起票する Chat-Ref: CHAT-1009-NEN-10 マージ: 承認済み（チャットで、2026-10-09。プレビューを見ずに本番に出してよい。平野さんが本番で確かめる）。条件: 生成物の差分が決定とシートの変化で説明できるものだけ（見込み: NEN-08・NEN-09 の分〈入口の年の切り替え、大会ページ・期ページの固定バーのプルダウンの削除、`title/years.json` の追加、`title/timeline/`・`img/ogp/title/timeline-black.png`・`_redirects` の `/title/timeline` の1行の削除〉と、この指示の分〈title/ の全ページ〈入口1・大会20・期363〉から共有ボタンを外す、入口の文言〉。`sitemap-title.xml`・`title/search.json` は変わらない見込み。title/ 以外のページ〈live/・saikyo/・wayhome/ など〉の生成物が変われば止まる） 貼る時機: CHAT-1009-NEN-09 が「判断待ち」で止まった後（済んでいる） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1009-nen-year の使用と push、cloudflare へのマージ（上の「マージ:」の行の範囲）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1009-nen-year を続けて使う（NEN-08・NEN-09 の試作がある）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1009-NEN-09.md の `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。NEN-09 と NEN-08 の `## 報告` の状態の末尾に `/ 続き: CHAT-1009-NEN-10` を足す（NEN-08 に既に続きの印があれば NEN-09 だけ。`## 指示` 欄より後ろの見出しを相手にする）。

目的
平野さんが NEN-09 の比較を見て S1 を選び、文言と共有ボタンの扱いを決めた。決定どおりに本実装し、本番に出して #277 を閉じる。
決定（2026-10-09、平野さん）

* 年の切り替えの見た目は S1（プルダウンだけ、矢印なし、ピル型）。ただし、プルダウンを開いたときの選択肢の文字色が薄すぎて読めない（白い一覧に薄い文字）ので直す
* 「タイトルホルダー」を「現タイトルホルダー」に変える（選択肢、既定の表示の見出し h1、ブラウザのタブの題名 `<title>`）
* 年を選んだときは、見出しに加えてタブの題名も変える
* title/ の共有ボタン（ページ全体に付いている、ブラウザの共有ボタンと同じ挙動しかしないもの）は、入口・大会ページ・期ページのすべてから外す（2026-09-29 の #409 の「title/ に共通の共有ボタンを置く」を title/ について置き換える）
* サイト全体の共有ボタンは、別の issue で包括的に見直す（最強戦の対局ごとの共有のように、ページの一部を指すものは残るのではないか）
* プレビューを見ずに本番に出してよい。平野さんが本番で確かめる

前提（チャット側。平野さんの決定ではない）

* 消すもの: 比較のラジオボタン（`year_switcher_compare_html()`・`YEAR_SWITCHER_STYLES`・`.mj-title-year-compare`）、S2〜S4 の CSS（`.mj-title:has(#title_ys_s…)` の規則）、矢印のボタンの HTML・JS（S1 では使わない）、関係するテストの検査。NEN-09 の報告の一覧のとおり
* 選択肢の文字色: `<select>` 自体の文字（固定バーの黒地に白）は今のまま。開いた一覧の `<option>` は、ブラウザの既定の白い一覧に読める色（本文の文字色 `#212529` 程度）を指定するか、`<option>` の背景も固定バーと同じ黒地にする。Windows の Chrome（平野さんのスクリーンショット）と iPhone の Safari（OS の選択の画面になる）で読めること。コントラスト比を WCAG 2.x の式で計算して書く
* 題名の形（案。実物に合わせてよい）: 既定は「現タイトルホルダー | タイトル戦 | 日本プロ麻雀連盟 | ryoei.pro」の形か、今の `<title>` の「現在のタイトルホルダー」の部分を「現タイトルホルダー」に置き換えた形（今の実物を読んで、置き換えで済む方）。年を選んだときは `document.title` を「2025年優勝者 | タイトル戦 | 日本プロ麻雀連盟 | ryoei.pro」の形にし、「現タイトルホルダー」に戻したら元の題名に戻す。og:title・og:image・canonical は変えない（`?year=` を付けた URL を X・LINE に貼ってもカードは入口のまま。年の切り替えはブラウザの中だけで起きるため）
* 共有ボタンを外す: `scripts/lib/share.py`・`assets/share.js`・`style.css` の共有の部品は live/・saikyo/・wayhome/ がまだ使うので消さない。title/ の生成から呼ぶのをやめるだけにする（title/ だけが使っている CSS・JS があれば消してよい）。固定バーの高さの仕組み（`--mj-title-filter-h`）は変えない。スマホの入口の固定バーが1段に収まるようになれば、それでよい（収まらなければ2段のまま）
* 別 issue（共有ボタンのサイト全体の見直し）: 題は「サイト全体の共有ボタンを見直す（ページ全体の共有ボタンは外す方向）」の形。本文に平野さんの決定（上の2つ）と、洗い出しの候補（`scripts/lib/share.py` を使うページ〈live/・saikyo/・wayhome/〉、手書きのページ、books/〈凍結中、旧実装〉、最強戦の対局ごとの共有のようにページの一部を指すもの〈残す候補〉、#409・#82〈新サイトの選手個人ページの共有ボタン〉との関係）を書く（要確認の印）。起票の前に同じ主題の issue をクローズ済みも含めて検索する。この指示では直さない
* 決定の記録: docs/decisions/title.md に上の「決定」を「2026-10-09（CHAT-1009-NEN-10）」として足し、NEN-09 の決定（S1〜S4 を見比べる）を S1 に決めたことが分かるようにする（README「書き方」）。#409 の決定がどこに記録されているかを確かめ、そこに「→ title/ は外した: 2026-10-09（CHAT-1009-NEN-10）」を付ける（記録が無ければ title.md の追記だけでよい）。docs/notes/title-pages.md（共有ボタン・年の切り替えの記述）、docs/handover.md（4章の共有ボタンの行「live/・saikyo/・wayhome/・title/ に共通の共有ボタン」から title/ を外す。5章の #277 を完了に。警告域 26,624 の外であることを確かめる）を直す
* 取り込みの衝突の扱い: docs/decisions/ の追記どうし、docs/handover.md・docs/notes/・docs/new-page-checklist.md の隣り合う行で両立する衝突（NEN-08 の報告の `work/1008-hou` の URL パラメータの行など）は、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。生成物だけの衝突は CLAUDE.md「ブランチ運用」のとおり生成し直す。それ以外の衝突は解かずに止まる
* マージ後、`work/1009-nen-year` は `delete-merged-branches.yml` に任せる（クラウドセッションでは削除できない）

手順

1. 確かめる・作る: #277 が Open で、他セッションの着手中コメントが無い。未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`）が `title/`・`scripts/generate_title_pages.py`・`assets/title.js`・`style.css` の `.mj-title*` 節・`scripts/lib/share.py`・`assets/share.js` を変えていない（`work/1008-hou` の houou/ の行・節と docs の隣り合う行は当たらない）。上の前提のとおり消す・直し、`python3 scripts/regenerate.py title_pages` で生成し直す。テストを直し・足し（選択肢の文言、題名、title/ の全ページに共有ボタンが無いこと、live/・saikyo/・wayhome/ には残っていること）、`python3 -m unittest discover -s scripts/tests` を通す。headless Chromium（手元）で 375px・1280px の入口の固定バー・年の切り替え・題名の変化・JS のエラーを確かめる。生成物の差分を種類に分けて表にし、「マージ:」の行の見込みと突き合わせる
2. 記録する: 決定・文書・handover を直し、共有ボタンの見直しの issue を起票する（番号を報告と #277 に書く）
3. マージして確かめる: CLAUDE.md「ブランチ運用」のとおりマージし、check-run（Workers Builds、上限15分。超えたらその時点の状態を書いて「未確認の項目」に回す）を待つ。本番（`curl`、URL に `?v=<未使用の値>`）で、`/title/` が 200 で年のプルダウンがあり共有ボタンが無いこと、`/title/houou/`・`/title/houou/42.html` の固定バーが検索欄だけで共有ボタンが無いこと、`/title/years.json` が 200、`/title/timeline/` が 404、`/live/` などに共有ボタンが残っていること、`assets/title.js`・`style.css` が手元と同じことを確かめ、表でログに書く。#277 に結果をコメントして閉じる（「状況:」ラベルがあれば外す。末尾に Chat-Ref の行）。結果は docs/logs のみの追いの push で入れる。平野さんに本番で見てもらう手順（直接開けるリンク: https://ryoei.pro/title/ 、https://ryoei.pro/title/?year=2025 、https://ryoei.pro/title/houou/42.html 。スマホと PC。プルダウンを開いて文字が読めること、年を選ぶとタブの題名が変わること）を報告に書く

止まる条件

* #277 が閉じている、他セッションの着手中コメントがある、未マージの `work/` ブランチが上のファイルを変えている（上の例外を除く）
* 表示する大会が 20〜21、期が 363〜365 の範囲外（件数を書いて止まる）
* 生成物の差分が「マージ:」の行の見込みの外（title/ 以外のページの生成物が変わった、など。シートの変化で説明できるもの〈写真 URL の差し替えなど〉は種類と件数を書いて進んでよい）
* 「現タイトルホルダー」の表示のカードの並び・中身が本番と変わる
* 既存のテストが落ちる（title/ の共有ボタン・年の切り替えの比較・文言を前提にした検査は直してよい。それ以外は止まる）
* headless Chromium で JS のエラーが出る
* check-run が失敗した（原因を調べず報告に書いて止まる）
* docs/handover.md が警告域に入る
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告に、生成物の差分の種類と件数の表、本番の確かめの表、起票した issue の番号、平野さんに本番で見てもらう手順を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1009-NEN-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1009-NEN-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 指示欄の末尾は指示文の最後の行と一致。NEN-09 の状態は「判断待ち」だった。NEN-08 には既に「/ 続き: CHAT-1009-NEN-09」があるため、NEN-09 にだけ「/ 続き: CHAT-1009-NEN-10」を足した。このセッションは NEN-01〜09 から続けて受けたもの
- 識別子: `git log --all --grep="CHAT-1009-NEN-10"` は0件
- ブランチ: ローカルの `work/1009-nen-year` は `origin/work/1009-nen-year` と一致（42573ecc）。`origin/cloudflare` が祖先でなかった（他セッションの辞書・Search Console・放送予定などのマージで進んでいた）ため `git merge origin/cloudflare`（衝突なし、e7f8284f）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

### 手順1 確かめ（止まる条件に当たった）

- 未マージの `work/` ブランチ: `origin/work/1009-swp-fix`（7701ba1e）は対象のファイルを変えていない。`origin/work/1008-hou`（37e4c501、CHAT-1009-HOU-04）は `_redirects`・`style.css`（houou/ の行・節。`.mj-title*` を含む差分の行は0、`mj-share` を含むのは `.mj-houou … .mj-share-btn-round` の houou/ の規則だけ）に加えて、**`assets/share.js` を変えている**
  - 中身: 共有ボタンの URL・文言を、読み込み時ではなく押したときに `data-share-url`・`data-share-text` から読むようにする（houou/ の `assets/houou.js` が表示中の状態に合わせて書き換えるため）。3か所・数行
- 止まる条件の例外は「`work/1008-hou` の houou/ の行・節と docs の隣り合う行」で、`assets/share.js` は対象のファイルに挙がっている。この指示では `assets/share.js` を変えない（title/ の生成から共有ボタンを呼ぶのをやめるだけ）ので、git の取り込みで衝突は起きない見込みだが、止まる条件の文言どおり、実装に入らず平野さんに聞いて止まる

## 報告

- 状態: 判断待ち（work/1008-hou の `assets/share.js` の変更との重なりを平野さんに質問中） / 続き: CHAT-1009-NEN-11
- ブランチ: work/1009-nen-year
- ログ: https://github.com/retroeater/mj/blob/work/1009-nen-year/docs/logs/CHAT-1009-NEN-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1009-nen-year
- 確認用URL: なし
- マージ: 未
- issue: #277
- 判断が必要なこと:
  - `work/1008-hou`（未マージ、CHAT-1009-HOU-04）が `assets/share.js` を変えている（共有の URL・文言を押したときに読む）。この指示は `assets/share.js` を変えないので衝突しない見込み。このまま本実装からマージまで進めてよいか
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj becd9199）: https://github.com/retroeater/mj-logs/tree/main/guide/becd9199

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/becd9199/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/23f4ac3e.md
