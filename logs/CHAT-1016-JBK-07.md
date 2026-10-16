# CHAT-1016-JBK-07

- 着手日時: 2026-10-16
- 対象issue: #575（作る）、#576（公開）
- ブランチ: work/JBK
- 着手時HEAD: d0e8618230ad5ea9e644e8e9576d48c8213fa089

## 指示

【Claude作成】Claude Code 向け指示：女流勉強会の続き（URL を /benkyokai/ から /study/ に変え、「公開対象を検査する」の許可リストに study を足す） Chat-Ref: CHAT-1016-JBK-07 マージ: 判断待ちで止まる（女流勉強会のマージは平野さんがプレビューを確かめてから決める） 貼る時機: いつでも（JBK-01〜05 と同じセッションに貼る） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/JBK の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/JBK を続けて使う（CHAT-1016-JBK-05 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1016-JBK-05 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-JBK-07` を足す。 （CHAT-1016-JBK-06 は送る前に差し替えたため欠番）

目的
女流勉強会の URL を、仮置きの `/benkyokai/` から `/study/` に変える。あわせて、work/JBK への push ごとに `assets-check.yml`（公開対象を検査する）が「許可リストに無い最上位の項目: benkyokai」で失敗している件（Actions のフォローアップのチャット〈ACF〉からの伝言。例: run #3684 a770aa9、#3691 1e3214f）を、新しい名前 `study` を許可リストに足して直す。
決定（2026-10-16、平野さん）

* 女流勉強会の URL は `/study/`（一覧 `study/index.html`、各話 `study/<動画ID>.html`）。JBK-01 の仮置き `/benkyokai/` をやめる
* （ACF の伝言を平野さんがこのチャットに渡した。JBK-01 の「`.github/workflows/` を変えない」は、許可リストに1語を足すことに限って外す）

前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）

* 変えるのは URL に出るもの: 出力先（`regenerate.py` の `OUTPUT_OVERRIDES`）・`_redirects` の `/benkyokai`・`/benkyokai/` の行（→ `/study`・`/study/`）・各話の canonical・og:url・`BreadcrumbList`・`VideoObject` の URL・一覧と各話の中のリンク・テストの期待値・`docs/notes/redirects.md` の末尾スラッシュの行。生成物の `benkyokai/` は消して `study/` に作り直す（未公開なので旧 URL の転送は作らない）
* コードの中の名前（スクリプト名・`lib` のモジュール名・CSS のクラス名 `.mj-benkyokai-*`・ページ名など）は、URL に出ないものは変えなくてよい。変えるなら一括で変え、どちらにしたかを報告に書く
* 許可リスト: `.github/workflows/assets-check.yml` の `allowed()` の case（cloudflare では 82 行付近、`assets|books|dic|houou|houou_race|img|live|mtsuku|saikyo|title|wayhome`）に `study` を足す（`benkyokai` は足さない）。このファイルは Actions の整理（WFL のチャット、#564）も触るので、1語を足すだけにとどめる。女流勉強会は公開するもの（`.assetsignore` には足さない）
* 公開の issue（#576）の本文・作る issue（#575）にある `/benkyokai/` の記述、JBK の実装記録・`docs/decisions/` の URL の記述も `/study/` に直す（決定の記録には「仮置き /benkyokai/ を 2026-10-16 に /study/ に変えた」と書く）

手順

1. 未マージの work/ ブランチが `assets-check.yml`・`_redirects`・`regenerate.py` の `OUTPUT_OVERRIDES` の同じ行を変えていないかを `git branch -r --no-merged origin/cloudflare` の一覧から確かめる。URL を `/study/` に変え、`benkyokai/` の生成物を消して `study/` に生成し直し、テストを直す。`git grep -n benkyokai` で URL に出る箇所が残っていないことを確かめる（残した内部の名前は一覧をログに書く）。`regenerate.py video_wayhome wayhome_episodes mtsuku mtsuku_episodes title_pages` の生成物の差分0を確かめる
2. 許可リストに `study` を足して push し、そのコミットで「公開対象を検査する」と「Workers Builds: mj」が成功することを check-run で確かめる（run の番号と結論をログに書く）。プレビューの別名の URL で `/study/`・最新話・`/study`（301）を確かめる
3. 文書・決定・#575 と #576 の記述を直し、#575 に進み具合をコメントする

止まる条件

* 未マージの work/ ブランチ（work/JBK 以外）が上のファイルの同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 足した後も「公開対象を検査する」が失敗する（理由を書いて止まる）
* 帰り道・Mつく・title/ の生成物に差分が出る
* `/study/` がすでにほかのページ・ファイル・転送で使われている（使っている箇所を書いて止まる）
* cloudflare への push はしない（マージは判断待ち）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」は CHAT-1016-JBK-05 の報告の一覧を引き継ぐ（許可リストの項目は済みとして消す）
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。最終報告に確認用の URL（一覧と最新話）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-JBK-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-JBK-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1016-JBK-0[67]"` と `docs/logs/` に無し（JBK-06 は指示文のとおり欠番）
- 作業ブランチ: ローカル work/JBK はリモートと一致（d0e861823）。`origin/cloudflare` は祖先でない → 取り込む
- `/study/` の使用: cloudflare の最上位に `study` は無く、`_redirects`・`.assetsignore`・HTML/JS に `/study` は無い

## 報告

- 状態: 作業中
- ブランチ: work/JBK
- ログ: https://github.com/retroeater/mj/blob/work/JBK/docs/logs/CHAT-1016-JBK-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/JBK
- 確認用URL: 未
- マージ: 未
- issue: #575・#576
- 判断が必要なこと: 未
- 未確認の項目: 未
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b4879ad7）: https://github.com/retroeater/mj-logs/tree/main/guide/b4879ad7

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4879ad7/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4879ad7/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4879ad7/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4879ad7/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4879ad7/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b4879ad7/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/82bde681.md
