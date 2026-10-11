# CHAT-1011-ABM-01

- 着手日時: 2026-10-11
- 対象issue: #425（関連: #501・#502）
- ブランチ: work/1011-abm
- 着手時HEAD: 1245b908301a7974e4a023786d02a255325363c8

## 指示

【Claude作成】Claude Code 向け指示：#425（最強戦の対局・卓の個別ページと ABEMA）の着手前の調査（読むだけ） Chat-Ref: CHAT-1011-ABM-01 マージ: ドキュメントのみ（ログ・docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい。コード・ワークフロー・シート・生成物・issue の本文は変えない 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1011-abm を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-abm origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#425 を現行サイトで作る前に、決めることを /grill-me で詰めるための材料を集める。この指示では何も作らず、調べた事実をログに書くだけにする。
決定（平野さん。正は docs/decisions/features.md の 2026-10-05 RUN-05〜07 の #425 の行。この指示で新しく決めたことは無い）

* 新サイト（#296）送りにせず現行サイトで作る。「最強戦」シートに動画の URL の列を足す。対局のページと卓のページの両方を作る（ABEMA の URL が卓ごとにあるため）
* 年度ページは「各対局の勝ち上がり者」だけにする。ページ数の増加と URL の入力の量は問題にしない（API などで取れればなおよい）
* ABEMA はリンクだけで始め、サムネイルは #502。ABEMA のページの取得は平野さんの PC（日本）で動かすスクリプト（#501）
* 今の `?match=` は対局のページへ 301 で畳む（案A）。手段は Cloudflare のリダイレクトルールで、個別ページを作る時に平野さんがダッシュボードで設定する

前提（チャット側。平野さんの決定ではない）

* #425・#501・#502 は 2026-10-09 の時点で Open（要確認）。実装は未着手（要確認）
* 平野さんは「最強戦」シートに動画の URL の列をまだ足していない（2026-10-11、平野さんの申告）
* 「最強戦」シートの列は A 対局日〜K 表示（docs/notes/saikyo-page-design.md「1. データ源」。要確認）。対局日が空の行（2011〜2020年度の全行と2021年度の一部）は、アンカーと `?match=` の対局識別子が `<年度>-n<連番>` 形式で、日付を埋めると変わる（同「アンカー」）
* work/1010-xap（CHAT-1011-XAP-09、作業中）が `generate_saikyo_pages.py` の選手写真の読み先（`load_name_book()` 経由）を SNS ブックへ切り替えている（要確認）。この指示は読むだけなので重なりでは止まらない。読むコードは origin/cloudflare の版にする
* `_redirects` はクエリ文字列で一致させられない、とチャット側は理解している（要確認。案A の手段がリダイレクトルールに決まった背景）

手順

1. issue と関係するものを洗い出す（読むだけ）
   * #425・#501・#502 の状態・本文・コメントの全文を読み、要点（「先に決めること」の各項目の今の記載、決定と食い違う記述の有無）をログに書く。sub-issue の登録が #425 に #501・#502 の2件か確かめる
   * 同じ論点の issue を、クローズ済みとコメントの決定まで含めて洗い出す。検索語に少なくとも「最強戦」「saikyo」「ABEMA」「アベマ」「match」「個別ページ」「動画」を入れる。#530・#458・#533・#442・#428・#343・#514 は必ず含め、#425 との関係を1行ずつ書く
   * `git branch -r --no-merged origin/cloudflare` の一覧を出し、`saikyo/`・`scripts/generate_saikyo_pages.py`・`assets/saikyo.js`（実名は確かめる）・`sitemap-saikyo.xml`・`_redirects` を変えているブランチを書く
2. 今の作りとデータを調べる（読むだけ。生成と同じ経路でシートを読む）
   * 「最強戦」シートの見出し行（全列）と行数、K=Y の行数。年度ごとに「対局の数・卓の数（ステージ×卓）・対局日が空の対局の数」を表にする。対局と卓の数え方は `generate_saikyo_pages.py` の今のまとめ方に合わせ、その関数名を書く。2回読んで行数が違えば止まる
   * 対局・卓の個別ページを作った場合のページ数の見込み（対局ページ・卓ページの別、合計）と、今のリポジトリの静的アセットのファイル数（`.assetsignore` を除いた数の求め方を書く）を並べる。上限 20,000 ファイルとの差を書く
   * 今の作りの要点を、ファイルと関数を指して書く: トップ・年度ページの生成、対局識別子とアンカーの作り方、`?match=` の扱い（JS 側・canonical・プルダウン・対局ごとの共有ボタン）、`sitemap-saikyo.xml` に載せる URL、OGP（年度ごとの画像の有無）、`llms.txt` の最強戦の記載、`_redirects` の saikyo の行、毎朝の再生成（`regenerate-saikyo.yml`）が対象にするファイルの範囲
   * 年度ページの「勝ち上がり者だけ」にした場合に、今の表示（卓ごとの4人・順位・バッジ）のどこが変わるかを、今の HTML の構造（クラス名）で書く。見た目の案は作らない
3. URL の形と転送の手段を調べる（読むだけ）
   * リポジトリとシート（「最強戦」・/live の3層〈ID は docs/notes/live-channel-write.md〉・連盟の予定表の取り込み〈docs/notes/yotei-sheet.md〉）に ABEMA の URL があるか探し、あれば形（対局・卓・番組の別が URL から分かるか）を例を挙げて書く。/live・予定表に最強戦の放送が載っているか、載っていれば最強戦の対局・卓と突き合わせられそうかを書く（件数と例。値の正しさは判定しない）
   * セッションから `https://abema.tv/` に届くか（HTTP の状態のみ）を1回試して書く。届かないことを根拠に結論を書かない
   * `_redirects` でクエリ付きの URL（`/saikyo/2026.html?match=…`）を別のパスへ転送できるか、Cloudflare の公式文書（Workers の静的アセットの `_redirects`、Single Redirects／Bulk Redirects）で確かめ、引用元の URL と一緒に書く。docs/notes/cloudflare.md にリダイレクトルールの今の使用の記録があるかも書く。ダッシュボードは触らない
   * 最後に、/grill-me で決めることの一覧（URL の形、日付未登録の対局の URL、対局と卓のページの中身、年度ページの勝ち上がり者の見せ方、サイトマップ・canonical・OGP・`llms.txt`、`?match=` の転送の対応表の作り方、#501 のスクリプトの入出力、シートの列の形）を、調べた事実から見て足すもの・不要になるものがあれば直して、ログの `## 報告` の「判断が必要なこと」に書く

止まる条件

* #425・#501・#502 のいずれかがクローズ済み、または他セッションの着手中コメントがある（読んだところまで書いて止まる）
* シートの行数が2回の読みで違う、またはシートを読めない
* ログ・docs/decisions/ 以外を変える必要が出た（変えずに止まる）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-ABM-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-ABM-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b2727163）: https://github.com/retroeater/mj-logs/tree/main/guide/b2727163

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2727163/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2727163/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2727163/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2727163/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2727163/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2727163/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b2727163.md
