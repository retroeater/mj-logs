# CHAT-1019-SKG-03

- 着手日時: 2026-10-19
- 対象issue: なし
- ブランチ: work/1019-skg
- 着手時HEAD: af238f10f

## 指示

【Claude作成】Claude Code 向け指示：団体分裂合戦の卓別動画（ABEMA）を saikyo/ に反映する（検査 → 差分の見込み → cloudflare で regenerate-saikyo.yml を手動実行） Chat-Ref: CHAT-1019-SKG-03 マージ: 承認済み（チャットで）。対象は手順3の cloudflare での regenerate-saikyo.yml の手動実行（Actions のコミットが本番反映になる）。作業ブランチの変更はログと docs/decisions だけなので、完了報告のうえ cloudflare へ入れてよい 貼る時機: CHAT-1019-SKG-02 の完了の後（2026-10-19 に完了を確認済み）、かつ平野さんが ABEMA ブック【1】に団体分裂合戦（2026年10月11日）の A卓・B卓・決勝卓の回を取り込み、【3】の同じ卓のライブ枠の行を消した後 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1019-skg〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-skg の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する場合は work/1019-skg を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1019-skg origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
ABEMA に団体分裂合戦（2026年10月11日）の卓別動画（A卓・B卓・決勝卓）が公開された（2026-10-19、平野さんが ABEMA の画面で確認）。CHAT-1019-SKG-02 の後も本番の A卓・B卓（`saikyo/2026/20261011-s1-a.html`・`-s1-b.html`）の「ABEMA で見る」はライブ枠（`abema.tv/channels/mahjong/slots/...`）のままだった（2026-10-19 にチャット側が本番を取得して確認）ので、毎朝の自動実行（run-daily.yml、04:00 JST）を待たずに saikyo/ を再生成して本番に反映する。
決定（2026-10-19、平野さん）

* 検査（手順1）で止まる理由が無く、差分が見込み（手順2）の範囲なら、cloudflare で regenerate-saikyo.yml を手動実行して本番へ反映してよい
* 団体対抗スペシャル合戦（2026年10月18日）の卓別動画はまだ ABEMA に出ていない（ライブ枠のままで正しい）

前提（チャット側。平野さんの決定ではない）

* 再生成の経路は docs/notes/github-actions.md「ワークフローの一覧」の `regenerate-saikyo.yml`（手動実行で ABEMA ブック【2】の作り直し〈ジョブ abema〉→ saikyo_pages の再生成。差分が無ければコミットしない）。手動実行の入力は abema=update・regenerate=true（CHAT-1017-SKG-01 のログの `## 経過` で既定値と確かめた値。今の既定値と違えばログに書き、この値で実行する）
* saikyo_pages は生成する環境で選手写真の結果が揺らぎ、本番は Actions の生成が正（handover.md「データの流れ」）。手順2のセッション内の生成は差分の種類の見込みを得るためだけで、生成物はコミットしない
* 卓別動画の元データの流れ（【1】元データ・【3】手入力・【2】の作り直し、【3】の行があるとライブ枠が出る）は docs/notes/abema-book.md と docs/decisions/saikyo.md による（要確認。食い違えば止まる）
* これまでの `--check` の警告のうち次の2種類は想定どおりで、止まる理由にも報告の論点にもしない（2026-10-18 に平野さんがチャットで説明）: 「別名」の行「柊かえで → 柊木かえで」を使わない警告（女流桜花で使う行）、2026 のベスト8 の卓の動画URLが ABEMA の放送枠（slots）である警告（卓ごとの動画は放送からおよそ2週間後に公開され、それまではライブ枠。ただし今回、団体分裂合戦の A卓・B卓についてはこの警告が消えるはず。セッションの `--check` は手順3の前の【2】を読むので、ここではまだ出てよい）。これ以外の警告は全部書く

手順

1. 平野さんの手作業の確かめ（読むだけ。ABEMA ブックに書かない）: docs/notes/abema-book.md を読み、`scripts/update_abema_book.py` と同じ読み取りの経路で【1】と【3】を読む（書き込みの道筋は通らない。読むだけの手段が無ければその旨を書いて止まる）。ログに次を書く: (i) 【1】にある 2026-10-11 団体分裂合戦の A卓・B卓・決勝卓の回（回のID・題）、(ii) 【3】に残っている同じ卓の行（URL がライブ枠〈slots〉かどうか）。続けて `python3 scripts/regenerate.py --check saikyo_pages` を流し、止まる理由・警告をすべてログの `## 経過` に書く。止まる理由が1件でもあれば、一覧を書いて止まる
2. 作業ブランチで `python3 scripts/regenerate.py saikyo_pages` を流し、`git status`・`git diff --stat` を種類に分けてログに書く（(a) 団体分裂合戦の卓・対局・年度・トップのページ、(b) 選手写真の URL だけの差、(c) sitemap*.xml、(d) その他の saikyo/ のページ、(e) saikyo/ と sitemap*.xml 以外）。セッションの生成は今の【2】を読むので、卓別動画の差は (a) に出なくてよい（【2】の作り直しは手順3のジョブ abema が行う）。(d) は、ほかの卓の【1】【3】の変化やシート側の変化で出ることがある。ファイル名と変わった値（前と後）を書く。生成物はコミットせず、docs/notes/branch-operations.md「未コミットの変更を戻すとき」に従って戻す（ログのコミットを消さない）
3. (e) が無ければ、`regenerate-saikyo.yml` を cloudflare ブランチで手動実行する（docs/notes/github-actions.md「ワークフローを手動実行するとき」を先に読む）。実行の run 番号・結論、cloudflare に入ったコミット（`chore: regenerate ...`）の SHA と `--stat`（ファイル名の一覧も）、そのコミットの Workers Builds の check-run の結論を書く。本番で `saikyo/2026/20261011-s1-a.html`・`20261011-s1-b.html`・`20261011-s2.html` を `?v=<未使用の値>` を付けて取得し、各ページの ABEMA へのリンク（「再生」「ABEMA で見る」）の URL をすべて引用する。3ページとも卓の回の URL（ライブ枠〈`/channels/.../slots/`〉でない）になっていれば完了

止まる条件

* 手順1で、【1】に団体分裂合戦の3卓の回のどれかが無い、または【3】に同じ卓のライブ枠の行が残っている（平野さんの手作業が済んでいない。手順2以降をせず、足りない卓と残っている行を書いて止まる）
* 手順1の `--check` で止まる理由がある
* 手順2で (e)（saikyo/ と sitemap*.xml 以外の変更）がある。手順3をせず、内訳を書いて止まる
* このセッションから cloudflare でワークフローを手動実行できない（権限・ツールが無い）。別の手段を試さずに止まり、平野さんが GitHub の Actions の画面から実行できるように、ワークフロー名と選ぶブランチ（cloudflare）・入力の値を `## 報告` の「判断が必要なこと」に書く
* 手順3の実行が失敗した。今回のデータによる失敗（生成で止まった等）と無関係な失敗（ネットワーク等）を分けて書く。ジョブ abema が失敗すると【2】が作り直されず卓別動画は出ないので、その場合は再生成が成功していても「判断が必要なこと」に書いて止まる
* 手順3の後、3ページのどれかがライブ枠の URL のまま、または ABEMA へのリンクが無い。もう一度手動実行はせず、ページごとの URL と、ジョブ abema のログの該当行を書いて止まる
* 未マージの work/ ブランチが `scripts/generate_saikyo_pages.py`（または saikyo_pages の生成スクリプト）・`scripts/update_abema_book.py`・`.github/workflows/regenerate-saikyo.yml` の同じ関数・同じ行を変えている（`git branch -r --no-merged origin/cloudflare` で確かめる。別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（作業ブランチはログと docs/decisions のみ）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-SKG-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-SKG-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 0. ログの「指示」欄の末尾は指示文の最後の行と一致
- `git fetch --unshallow origin` の後、`git log --all --grep="CHAT-1019-SKG-03"` と `docs/logs/CHAT-1019-SKG-03*` の履歴は0件。SKG は同じセッションの SKG-01・02 で使ったもの
- `origin/work/1019-skg`（45ff921d7）は `origin/cloudflare` の祖先（マージ済み）。ローカルの `work/1019-skg` も祖先なので、docs/notes/cloud-sessions.md「作業ブランチの用意」のとおり `git merge --ff-only origin/cloudflare` で進めた（af238f10f）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- 未マージの work/ ブランチで `scripts/generate_saikyo_pages.py`・`scripts/update_abema_book.py`・`scripts/*saikyo*`・`scripts/lib/*abema*`・`.github/workflows/regenerate-saikyo.yml` を変えているものは0本
- `regenerate-saikyo.yml` の workflow_dispatch の既定値は abema=`update`・regenerate=`true` のまま
- 前提の確かめ: docs/notes/abema-book.md を読んだ。【1】元データ・【2】最強戦（機械が毎朝作り直す）・【3】手動補正（空でない URL が【2】を上書き。卓ごとの動画が無い卓にライブ枠を入れ、出たら消す）の流れは指示文の前提と一致

### 手順1: ABEMA ブックの【1】【3】（読むだけ）と `--check`

`scripts/update_abema_book.py` の `read_source()` と同じ `lib.sheets.fetch_table()`（gviz、読むだけ）で読んだ。書き込みの道筋は通っていない。

- (i) 【1】（676行）にある 2026 団体分裂合戦の回: 3回すべてある
  - `68-38_s26_p22` 「麻雀最強戦2026 団体分裂合戦 A卓」（`abema.tv/video/episode/68-38_s26_p22`）
  - `68-38_s26_p23` 「麻雀最強戦2026 団体分裂合戦 B卓」（`abema.tv/video/episode/68-38_s26_p23`）
  - `68-38_s26_p24` 「麻雀最強戦2026 団体分裂合戦 決勝卓」（`abema.tv/video/episode/68-38_s26_p24`）
- (ii) 【3】（6行）に団体分裂合戦の行は無い。残っている6行は 団体対抗スペシャル合戦（10-18）・Mの乱（10-25）・超技巧合戦（11-01）の各 A卓・B卓で、すべてライブ枠（slots）

`python3 scripts/regenerate.py --check saikyo_pages`: 終了コード 0、「検査: 通りました」。止まる理由は0件。警告 6件、すべて想定どおりの種類（2026 のベスト8 の卓の動画URLが slots）: 超技巧合戦・Mの乱・団体対抗スペシャル合戦の各 A卓・B卓。団体分裂合戦の A卓・B卓の警告は既に消えた（【3】の行が消えたため）。それ以外の警告は無し。
ほか: シート 2580件、動画URLのある卓 612（SKG-01 の時点は 614）、司会・実況・解説のある卓 605（同 607）、年度・対局・卓のページ 880件。

### 手順2: `python3 scripts/regenerate.py saikyo_pages`（作業ブランチ）

終了コード 0。`git status`: 変更 2ファイル、新規 0。`git diff --stat`: 2 files changed, 10 deletions(-)。

- (a) 団体分裂合戦のページ 2件:
  - `saikyo/2026/20261011-s1-a.html`・`saikyo/2026/20261011-s1-b.html`: 前 = 「ABEMA で見る」（`abema.tv/channels/mahjong/slots/DH8JMAKRUUTVZq`）と「司会・実況・解説」の節（司会 小山剛志 ほか）→ 後 = どちらも無い（各5行の削除）
  - 理由: 【3】のライブ枠の行が消え、今の【2】にはまだ卓別の回が無いため（【2】の作り直しは手順3のジョブ abema が行う。指示文の見込みどおり）
- (b) 選手写真の URL だけの差: 0件
- (c) sitemap*.xml: 0件
- (d) その他の saikyo/ のページ: 0件
- (e) saikyo/ と sitemap*.xml 以外: 0件

生成物はコミットせず `git checkout -- saikyo/` で戻した（`git status --short` が空）。

## 報告

- 状態: 対応中
- ブランチ: work/1019-skg
- ログ: https://github.com/retroeater/mj/blob/work/1019-skg/docs/logs/CHAT-1019-SKG-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-skg
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj af238f10）: https://github.com/retroeater/mj-logs/tree/main/guide/af238f10

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/af238f10/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/af238f10/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/af238f10/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/af238f10/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/af238f10/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/af238f10/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4e223c8f.md
