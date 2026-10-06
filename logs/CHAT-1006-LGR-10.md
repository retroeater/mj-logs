# CHAT-1006-LGR-10

- 着手日時: 2026-10-06
- 対象issue: #508（#507）
- ブランチ: work/1006-lgr-10
- 着手時HEAD: d66a5861

## 指示

【Claude作成】Claude Code 向け指示：鳳凰戦「リーグ別成績推移」（houou_race）の公開（#508）の変更を作業ブランチに作り、プレビューを出して判断待ちで止まる。あわせて 24後 A1 のシートの直しを確かめる Chat-Ref: CHAT-1006-LGR-10 マージ: 判断待ちで止まる（プレビューを平野さんが見て決める。docs/new-page-checklist.md 段2「cloudflare へのマージは平野さんの確認の後」。cloudflare へはマージしない） 貼る時機: いつでも 作業ブランチ: クラウドセッションで実行する。work/1006-lgr-10 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1006-lgr-10 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1006-lgr-10〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1006-lgr-10 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
未公開で本番に入っている houou_race（https://ryoei.pro/houou_race.html 、#507）を公開する変更（#508、docs/new-page-checklist.md 段2）を作業ブランチに作る。この指示はプレビューまでで、マージ（＝公開）は平野さんがプレビューを見た後の別の指示で行う。
決定（2026-10-06、平野さん）

* 24後 A1 のシートの直しは修正済み（平野さんが直した）
* CHAT-1006-LGR-07・CHAT-1006-LGR-09 の直しは、本番で確かめて OK（色を付ける時機、前原雄大のアイコン、吉岡美音の動き、同じ速さで数える見え方）
* 正式な公開の作業は、24後 A1 の直しが終わってから行う（直しが済んだので、公開の作業に移る）
* メニューは「鳳凰戦」の末尾でよい（2026-10-06）。メニューの名前は「リーグ別成績推移」（2026-10-05、「鳳凰戦 > リーグ別成績推移」）
* 説明文は「日本プロ麻雀連盟の鳳凰戦について、期・リーグごとの順位変動を節単位で閲覧できます。」（2026-10-06）

前提（チャット側。平野さんの決定ではない）

* mj-logs のログ・ガイド（guide/569dab20）で確かめたこと: houou_race は bd4fc8b8（CHAT-1006-LGR-09）まで cloudflare に入っている。houou_race.html は noindex、navbar.js・`sitemap-pages.xml`・`llms.txt` に載っていない。公開の issue は #508（#507 を待つ）。docs/notes/static-generation.md「ページの一覧」は「noindex・メニュー未掲載。公開は #508」。#507・#508 の本文とコメントはチャット側で読めていない（要確認）
* 公開の条件について、チャットで確かめたこと: 平野さんは本番で iPhone の Safari を含めて動きを確かめ、OK とした（2026-10-06）。24後 A1 のシートは直した。#508 の本文にほかの条件（先に済ませる issue など）が書かれていて、満たされていなければ止まる
* 24後 A1 の確かめ: CHAT-1006-LGR-02 の表のとおりなら、直した後は10名とも第1節から8個の値が詰まり、空欄を挟む行と、途中で終わる選手（老月貴紀・山田浩之）が無くなる。生成と同じ経路で「鳳凰」タブを読んで確かめる。23後 C2 の山田圭・28後 D3 の丹羽卓哉の行（空欄を挟む）は直さない方針で、そのままでよい
* 公開の変更は docs/new-page-checklist.md 段2 のとおり: noindex を外して再生成（「公開していない」と書いたコメント・docstring・説明も直す）／navbar.js「鳳凰戦」の末尾に「リーグ別成績推移」（href はルート相対）／`sitemap-pages.xml` に足す（lastmod は手で書かない。push 前に parse する）／`llms.txt` に入口の行（説明は上の説明文に合わせる）／title・description・h1・og:title・og:image を確かめる（canonical は出さない）／「ページの一覧」・docs/notes/houou-race.md・docs/handover.md の未公開の記述を直す。置き換える旧ページは無い
* 共有ボタンは、この指示では足さない（平野さんの決定は live/・saikyo/・wayhome/・title/ の4系統。houou_race に付けるかは決まっていない）。今、付いているかどうかだけ報告に書く
* og:image と og:title が今どうなっているか（サイトの既定の画像か、専用か）を報告に書く。専用の画像は、この指示では作らない
* 再生成で `houou_race/` の JSON がシートの今の値になる（24後 A1 の直しの反映を含む）。ほかの期のファイルに差分が出たら、シートの変化として扱い、どの期かを報告に書く
* 使う skill は無い

手順

1. 確かめる。#508 と #507 を読み、他セッションの着手中コメントが無ければ #508 に着手中のコメントを残す。#508 の公開の条件が揃っていることを確かめ、揃ったことを #508 にコメントする（docs/new-page-checklist.md 段2「着手前」）。「鳳凰」タブを読み、24後 A1 の10名の節の値の入り方を表にしてログに書く
2. 公開の変更を作る。上の「前提」と docs/new-page-checklist.md 段2「変更」のとおり。unittest と CLAUDE.md の検証を通す
3. プレビューで確かめ、判断待ちで止まる。スマホの幅（390px）と PC の幅で、ほかのページのメニュー「鳳凰戦」の末尾から houou_race が開けること、noindex が無いこと、既定（43前 B1）と 24後 A1（全員に順位が付き、最後まで途中で外れる選手がいない）の再生を確かめる。`sitemap-pages.xml` と `llms.txt` に URL があることを確かめる。報告には、確認用の URL、変えたファイル、24後 A1 の表、title・description・h1・og の今の値、共有ボタンの有無、公開の後に平野さんが行うこと（docs/new-page-checklist.md「公開後の確かめ」のうち平野さんの分）を書く

止まる条件

* #508・#507 に他セッションの着手中コメントがある。未マージの `work/` ブランチに houou_race・navbar.js・`sitemap-pages.xml`・`llms.txt` を触るものがある
* #508 の本文に書かれた公開の条件が揃っていない
* 24後 A1 に、空欄を挟む行か、途中で終わる選手が残っている（表を書いて止まる）
* 「鳳凰」タブの行数が、読み直すたびに変わる
* 変更が次の外に及ぶ: houou_race.html・`houou_race/`・scripts/generate_houou_race.py・scripts/tests/test_houou_race.py・navbar.js・`sitemap-pages.xml`・`llms.txt`・docs/
* unittest か CLAUDE.md の検証が通らない。プレビューで横のはみ出し・ページのエラー（Cloudflare Web Analytics の beacon を除く）がある
* cloudflare へは push しない（この指示は判断待ちで止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1006-LGR-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1006-LGR-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref: `git log --all --grep="CHAT-1006-LGR-10"` は0件（同じチャットの LGR の続き）
- ブランチ: work/1006-lgr-10 はローカルにもリモートにも無い。`git checkout -b work/1006-lgr-10 origin/cloudflare`（d66a5861）
- 手順0: 指示欄の末尾の行は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4つとも揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1006-lgr-10
- ログ: https://github.com/retroeater/mj/blob/work/1006-lgr-10/docs/logs/CHAT-1006-LGR-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1006-lgr-10
- 確認用URL: なし（作業中）
- マージ: 未
- issue: #508（#507）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 569dab20）: https://github.com/retroeater/mj-logs/tree/main/guide/569dab20

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/569dab20/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/569dab20/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/569dab20/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/569dab20/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/569dab20/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/569dab20/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/824dc807.md
