# CHAT-1007-LGR-12

- 着手日時: 2026-10-07
- 対象issue: #507（閉じたまま）
- ブランチ: work/1007-lgr
- 着手時HEAD: c73e1800

## 指示

【Claude作成】Claude Code 向け指示：houou_race の名前を「リーグ別成績推移」から「順位変動」に変え、メニューの順番と説明文を直して、cloudflare へマージする Chat-Ref: CHAT-1007-LGR-12 マージ: 承認済み（チャットで、2026-10-07。文字と並びの変更なので、プレビューで止めず、確かめが通ればそのまま本番へ入れてよいと平野さんが決めた）。条件は「止まる条件」のとおりで、どれかに当たったらマージせず判断待ちで止まる 貼る時機: いつでも 作業ブランチ: クラウドセッションで実行する。work/1007-lgr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1007-lgr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1007-lgr の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
公開した houou_race（#507・#508）の名前が、隣のメニュー「リーグ推移」と紛らわしい。名前を「順位変動」に変え、あわせてメニューの順番と説明文を直す。
決定（2026-10-07、平野さん）

* ページとメニューの名前を「リーグ別成績推移」から「順位変動」に変える（「リーグ推移」との違いをはっきりさせるため）
* メニュー「鳳凰戦」の順番は、ランキング → リーグ推移 → 順位変動 → 成績詳細（今は 順位変動に当たる項目が末尾）
* ページの見出しは「鳳凰戦 順位変動」。title と X・LINE のカードの題は「順位変動 | 鳳凰戦 | ryoei.pro」。llms.txt の項目名は「順位変動」
* 説明文は「日本プロ麻雀連盟の鳳凰戦（第23期〜第43期）について、リーグごとの順位変動を節単位でたどれます。」にする（今は「日本プロ麻雀連盟の鳳凰戦について、期・リーグごとの順位変動を節単位で閲覧できます。」）
* 説明文の期の範囲の数字は、期が進むたびに手で直さずに済むよう、データから自動で入れる（第44期が入れば「第23期〜第44期」に変わる）
* llms.txt の並びをメニューと同じ順に直す（順位変動を成績詳細の前へ）
* URL（houou_race.html）は変えない
* リポジトリ内の文書の呼び名も「順位変動」にそろえる
* マージは、プレビューで止めず、確かめが通ればそのまま本番へ入れてよい

前提（チャット側。平野さんの決定ではない）

* チャット側が本番で確かめたこと（2026-10-07）: navbar.js の「鳳凰戦」は、ランキング（/houou_ranking.html?sheet=鳳凰）・リーグ推移（/houou_leagues.html）・成績詳細（/houou_results.html）・リーグ別成績推移（/houou_race.html）の順。houou_race.html は title・og:title が「リーグ別成績推移 | 鳳凰戦 | ryoei.pro」、見出しが「鳳凰戦 リーグ別成績推移」。llms.txt は「リーグ別成績推移」の行が「成績詳細」の行の後ろにある
* CHAT-1006-LGR-11 のログ（mj-logs）で確かめたこと: #507 と #508 は閉じてある。新しい issue は起票せず、#507 に着手中のコメントと、直した内容（日付・マージの SHA）のコメントを残す（閉じたまま。開け直さない）。実物で閉じていなければ、状態を報告に書いたうえで同じように進めてよい
* 「リーグ別成績推移」の残りは、リポジトリ全体を検索して洗い出す（箇所と件数をログに書く）。チャット側がガイドで見つけた所: docs/notes/houou-race.md（題と本文）、docs/notes/static-generation.md「ページの一覧」、docs/decisions/README.md の表、docs/decisions/houou.md の冒頭の説明、style.css の節の名前。ほかに scripts/generate_houou_race.py・houou_race.js・テスト・docs/handover.md にあれば直す（要確認）
* 直さない所: docs/logs/ の過去のログ、docs/decisions/ の過去の日付の節（その時点の決定の記録）、docs/notes/handover-archive-2026.md。旧い名前が残ってよい。docs/decisions/houou.md には、今回の決定を新しい節として足す（CLAUDE.md「作業ログ」節のとおり）
* ファイル名・URL・JSON の置き場（`houou_race/`）・CSS のクラス名・JS の関数名など、名前に race を含む識別子は変えない（表示と文書の呼び名だけを変える）
* navbar は navbar.js だけで決まり、ほかのページの HTML は変わらない見込み（要確認）。「鳳凰戦」以外のメニュー（「女流桜花」など）は変えない
* 説明文が出る所は meta description と og:description の見込み（要確認）。ページの本文やほかの所に同じ説明文が出ていれば、そこも同じ文にする
* 期の範囲は、生成スクリプトが書き出すデータ（`houou_race/` に出す期）の最初の期と最後の期から作る。今のデータでは第23期と第43期になり、決定の文と一字一句同じになる見込み。「〜」は決定の文のとおりの文字（U+301C）を使う
* 期の範囲がデータから作られることのテストを scripts/tests/test_houou_race.py に足す（期を1つ増やしたデータで、最後の期の数字が変わること）
* llms.txt は手書き（CLAUDE.md「構成」）。変えるのは項目名と行の位置だけで、行の説明の文（「期・リーグごとの順位変動を節単位で閲覧できる（第23期前期以降）。」）は変えない
* `sitemap-pages.xml` は変えない（URL が同じで、並びは表示に出ない）
* 公開後に平野さんが行うこと（チャット側から伝える）: X の投稿画面に `https://ryoei.pro/houou_race.html?x=<未使用の数字>` を貼って、カードの題が新しい名前になっているかを見る。Search Console の URL 検査でインデックス登録をもう一度リクエストする
* 使う skill は無い

手順

1. 洗い出しと着手。#507 の状態と、他セッションの着手中コメントが無いことを確かめ、#507 に着手中のコメントを残す。`git branch -r --no-merged origin/cloudflare` で、同じファイル（navbar.js・llms.txt・houou_race の一式）に触る未マージのブランチが無いかを確かめる。「リーグ別成績推移」をリポジトリ全体で検索し、直す所と直さない所を分けてログに書く
2. 直す。名前（見出し・title・og:title・メニュー・llms.txt・文書・コメント）、メニューの順番、llms.txt の並び、説明文（期の範囲はデータから）を直し、テストを足して、houou_race.html を生成し直す。生成物の差分が、名前・説明文と、シートの変化の反映だけであることを確かめる
3. プレビューで確かめ、マージし、本番で確かめる。プレビューで、390px と 1280px の幅で、メニュー「鳳凰戦」が決定の順に並び「順位変動」から開けること、見出し、title・og:title・meta description・og:description が決定の文と一字一句同じであること、再生が動くこと、「女流桜花」のメニューが変わっていないことを見る。止まる条件に当たらなければ、origin/cloudflare を取り込んでから cloudflare へマージする。check-run と、本番の houou_race.html・navbar.js・llms.txt が新しい名前・順番・説明文になっていることを確かめる（待つのは15分まで。超えたらその時点の状態を「未確認の項目」に書いて先へ進む）。#507 に、直した内容・日付・マージの SHA をコメントする

止まる条件

* #507 に他セッションの着手中コメントがある。同じファイルに触る未マージのブランチがある
* データから作った期の範囲が「第23期〜第43期」にならない（作られた文を書いて止まる。マージしない）
* メニューの順番を変えると、「鳳凰戦」以外のメニューの表示や、ほかのページの生成物が変わる
* cloudflare に入る変更が、次で説明できる差分だけでない: houou_race.html・houou_race.js・`houou_race/`・style.css の houou_race の節のコメント・scripts/generate_houou_race.py・scripts/tests/test_houou_race.py・navbar.js・`llms.txt`・docs/・再生成によるシートの変化の反映
* unittest か CLAUDE.md の検証が通らない。プレビューで横のはみ出し・ページのエラー（Cloudflare Web Analytics の beacon を除く）がある
* origin/cloudflare の取り込みで、自分で直せない衝突が出た（navbar.js・`llms.txt` は、ほかのセッションの変更と重なりやすい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる。ほかのセッションの push と重なって拒否されたときは、取り込み直して差分を確かめ、もう一度だけ行ってよい）
* 本番の確かめで、旧い名前・旧い順番・旧い説明文が残っている（状態を書いて止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告には、「リーグ別成績推移」を直した所と残した所の一覧、説明文の期の範囲をどこから作ったか、本番で確かめた結果、平野さんが行うこと（X のカードの確認と Search Console の再リクエスト。本番の URL を添える）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1007-LGR-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1007-LGR-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref: `git log --all --grep="CHAT-1007-LGR-12"` は0件（同じチャットの LGR の続き）
- ブランチ: work/1007-lgr はローカルにもリモートにも無い。`git checkout -b work/1007-lgr origin/cloudflare`（c73e1800）
- 手順0: 指示欄の末尾の行は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4つとも揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1007-lgr
- ログ: https://github.com/retroeater/mj/blob/work/1007-lgr/docs/logs/CHAT-1007-LGR-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1007-lgr
- 確認用URL: なし（作業中）
- マージ: 未
- issue: #507（閉じたまま）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b5f76164）: https://github.com/retroeater/mj-logs/tree/main/guide/b5f76164

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b5f76164/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b5f76164/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b5f76164/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b5f76164/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b5f76164/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b5f76164/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1257323c.md
