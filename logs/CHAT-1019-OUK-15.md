# CHAT-1019-OUK-15

- 着手日時: 2026-10-19
- 対象issue: #591
- ブランチ: work/1019-ouk15（origin/cloudflare から作成）
- 着手時HEAD: 8c9ed13cb

## 指示

【Claude作成】Claude Code 向け指示：女流桜花（ouka/）を公開する形にする（navbar・白地のナビ・旧3ページの 301・noindex 解除・jpml_pros の列）。プレビューで止まる Chat-Ref: CHAT-1019-OUK-15 マージ: 判断待ちで止まる（公開は本番の navbar・転送・jpml_pros を変えるため、平野さんがプレビューで確かめてから、別のマージの指示で cloudflare へ入れる） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-ouk15 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1019-ouk15 を origin/cloudflare から作る。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」。rebase・force push はしない。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 続いて、未マージの work/1017-lvp（放送対局）が origin/cloudflare に入っているかを確かめてログに書く（入っていなくても止まらない。下の「前提」のとおり進める）。 自律開発の続き。着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書く。止まるのは「止まる条件」に当たるときだけ。

目的
#591（女流桜花の公開）。ouka/ は CHAT-1018-OUK-12 で cloudflare に入り、未公開（noindex・navbar に無い）のまま。houou/ の公開（#540）と同じ形で公開に切り替える。プレビューで止まる。
決定（平野さん）

* 2026-10-17: navbar の「女流桜花」を `/ouka/` への1段にする
* 2026-10-19: ouka/ にも houou/ と同じ白地のナビ（`navbar.js` の `.mj-nav-new`）を当てる。公開の navbar の変更と一緒に入れる
* 2026-10-19（#591 の事前の質問への回答）:
   * Q1: 旧3ページはページごとに対応する所へ 301。`ouka_results.html` → `ouka/players/`（`?name=` を引き継ぐ）、`ouka_ranking.html` → `ouka/ranking/total/`、`ouka_leagues.html` → `ouka/players/` のリーグのタブ
   * Q2: jpml_pros（連盟 > プロ）の桜花の3列を1列「桜花<最新の期>期」（今は「桜花21期」）にまとめ、個人成績の期別へのリンクにする。同時に「鳳凰」の見出しを「鳳凰<最新の期>」（今は「鳳凰43後」）にする（今期出場していない人は表示されないため）。見出しの期は、期が進むたびに最新の期へ自動で変わる
   * Q3: 公開の指示は今作り、work/1017-lvp が未マージなら `navbar.js` の衝突を解いて進める
   * Q4: #591 と jwrc/ の公開（#599）は別々に行う。先に公開した方が navbar を直し、後の方はそれを取り込む
   * Q5: 公開後に X の告知文の下書きを作る（チャット側で作る。この指示では作らない）
* 消した部門のページ（`ouka/ranking/promotion-streak/`）には転送を足さない（2026-10-18）

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 手本: houou/ の公開（#540）の指示とログ、`docs/new-page-checklist.md`（段2 まで）、`docs/notes/redirects.md`、`docs/decisions/page-release.md`。houou/ と違うやり方をする所は理由をログに書く
* navbar: 「女流桜花」の項目（今は旧3ページへの段か）を `/ouka/` への1段にする。`navbar.js` の新しいナビを当てる判定に `/ouka/` を足す。work/1017-lvp が未マージで同じ分岐を変えていても、こちらは origin/cloudflare の形に足すだけにする（LVP 側が後で取り込むときに衝突する所を、ログの報告に「LVP への引き継ぎ」として引用して書く）。jwrc/ の項目と判定には触らない（#599 で行う。後で取り込む WRC 側のために、変えた行をログの報告に「WRC への引き継ぎ」として書く）
* 301: `_redirects` に3行。`?name=` の引き継ぎは houou/ の旧ページの転送と同じやり方（Cloudflare の転送はクエリを引き継ぐか、を `docs/notes/redirects.md` で確かめる）。リーグのタブは `ouka/players/` の既定のタブの指定のしかた（例: `?view=league`）を実物から決める。旧ページのファイル・生成（`generate_ouka_leagues.py` など）の扱いは houou/ の前例のとおりにする。転送の一覧（`docs/notes/redirects.md`）を直す
* 公開の切り替え: ouka/ の全ページの noindex を外す、サイトマップ・`llms.txt` に載せる、`docs/new-page-checklist.md` の段2 の項目を済ませる（houou/ の公開と同じ範囲）。トップのページ一覧などに女流桜花の入口があれば、旧ページから ouka/ に替える（サイト内の旧3ページへのリンクを検索して全部替える）
* jpml_pros: 桜花の列を1列にし、値は名前の横のチップと同じ決め方（進行中の期のリーグ、AMP と同じ）、リンク先は `ouka/players/?name=<今の登録名>` の期別。今期に出ていない人は空欄。「鳳凰」の見出しも同じく最新の期を付ける。見出しの期は生成のときにデータから決める（手で書かない）。鳳凰の列の値・リンクは変えない
* 質問欄: ouka/ の質問欄は公開とともに本番で使われる。Worker の1日の上限（鳳凰戦と共有）に変更は要らない見込み。変える所があればログに書く
* `.github/workflows/` は変えない

手順

1. 作る: navbar・白地のナビ・301・noindex 解除・サイトマップ・`llms.txt`・サイト内リンク・jpml_pros の列を直す。テストを合わせて足す（jpml_pros の見出しの期がデータから決まること、転送の3行、noindex が無いこと）。`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を回す
2. 確かめる: `python3 scripts/regenerate.py all` を作業ブランチと cloudflare のクローンで回し、生成物の差分が「ouka/ の noindex を外した行」「jpml_pros の列」「navbar を読み込むページの変化（あれば）」「サイトマップ・`llms.txt`」だけであることを確かめ、種類ごとに件数と例を書く。houou/ の生成物は差分0。375px・1280px で、navbar（女流桜花の1段、ouka/ の白地のナビ、houou/ と他ページのナビが変わらないこと）、jpml_pros（桜花・鳳凰の列の見出しと値・リンク）を headless Chrome で見て書く。転送3行は、プレビューで確かめられるなら確かめる（できなければマージ後に確かめる旨を書く）
3. 記録: プレビュー（確認用 URL）を出す。#591 に進み具合を1件コメントする（末尾に `Chat-Ref: CHAT-1019-OUK-15`）。`docs/notes/ouka-top.md`・`docs/decisions/houou.md`（または page-release.md）を今の形に直す

止まる条件

* houou/ の生成物に差分が出て直しで消せない。手順2の種類以外の差分が出て直しで消せない
* テスト（scripts と Worker）が落ちて直せない
* 未マージの work/ ブランチ（work/1017-lvp を除く）が、変えるファイルの同じ行・同じ関数を変えている（`git branch -r --no-merged origin/cloudflare` から確かめる。同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push はしない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、新しく仮置きしたこと（リーグのタブの指定のしかた、旧ページのファイルの扱い、jpml_pros の列の値の細部など）を、(1) 見た目の値 と (2) 見ただけでは判断しにくいもの に分けて書く。「LVP への引き継ぎ」「WRC への引き継ぎ」を書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。最終報告に確認用の URL（ouka/ のトップ・navbar が分かるページ・jpml_pros）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-OUK-15.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-OUK-15 を書く

着手後は質問せず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1019-ouk15
- ログ: https://github.com/retroeater/mj/blob/work/1019-ouk15/docs/logs/CHAT-1019-OUK-15.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-ouk15
- 確認用URL:
- マージ: 未
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0f18afda）: https://github.com/retroeater/mj-logs/tree/main/guide/0f18afda

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f18afda/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f18afda/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f18afda/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f18afda/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f18afda/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0f18afda/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4afe5cd1.md
