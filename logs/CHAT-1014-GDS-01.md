# CHAT-1014-GDS-01

- 着手日時: 2026-10-14
- 対象issue: #492
- ブランチ: work/1014-gds
- 着手時HEAD: 191d9dbde

## 指示

【Claude作成】Claude Code 向け指示：GitHub Actions の文書 docs/notes/github-actions.md を新設し、散っているワークフローの記述を集約する（#492） Chat-Ref: CHAT-1014-GDS-01 マージ: 判断待ちで止まる 貼る時機: いつでも（新しいセッションは任意。続きでも可） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1014-gds を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-gds origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
GitHub Actions のワークフローの記述が docs/notes/static-generation.md（85KB）・branch-operations.md・chat-side-operations.md に散っている。上限の無い1つの文書に集め、元は参照の1行にする。あわせて「一覧の表 = `.github/workflows/` の実物」を機械で確かめるテストを足す（表に無いワークフローがあることをチャット側が見つけたため）。
決定（2026-10-14、平野さん）

* GitHub Actions の記述は docs/notes/github-actions.md（新設。容量の上限を置かない）に1つにまとめる
* ワークフローの一覧の表と `.github/workflows/` の実物が一致することを `scripts/tests/` のテストで確かめる
* GitHub Actions の失敗通知メールは1件ずつ扱う。本番稼働済みの Actions のエラーは、基本的に Actions のフォローアップのチャットで解決する（題材の系列のチャットへ引き継ぐのは、そのチャットが直し中のときなど）（2026-10-12 の決定。文書に書く場所が無かったため、メモリーに控えていたもの）

前提（チャット側。平野さんの決定ではない）

* 集約の対象（チャット側が guide/522a4323 で読んだ版。実物で確かめる）:
   * docs/notes/static-generation.md「regenerate-page.yml」（約 2.6KB）・「ワークフローの一覧」（約 8.8KB）・「ワークフローを手動実行するとき」（約 4.0KB）
   * docs/notes/branch-operations.md「ワークフローを変更したとき」（約 1.4KB）
   * docs/notes/chat-side-operations.md の3か所: 「読み方」の表の「Actions の実行結果」の行の本文、「書く前に実物で確かめる」の「ワークフローを変える指示: …」の項、「ほかのセッションへの共有」の「Actions の失敗通知を受けたら…」の項。元の場所には参照の1行（表の行は「`docs/notes/github-actions.md`『〜』」の形）を残す。この文書は警告域の直前（26,556 バイト、警告域 26,624）なので、純増させない
* 残す（移さない）もの: docs/notes/scheduler-worker.md（Worker 側）、docs/notes/cloudflare.md（Workers Builds）、docs/decisions/automation.md（決定）、docs/notes/live-channel-write.md など各ワークフロー固有の文書。github-actions.md からはこれらを参照する
* 新しい文書の構成の案（見出しは変えてよい）: 冒頭に「正は `.github/workflows/`。本数はここに書かない」／「ワークフローの一覧」（表）／各ワークフローの詳しい動き（regenerate-page.yml の節をここへ）／「手動実行するとき」／「変更するとき」（branch-operations.md から。CLAUDE.md「ブランチ運用」の参照先になる）／「チャット側」（実行結果の確かめ方、ワークフローを変える指示の前提の書き方、失敗通知の扱い〈上の決定の2行を含む〉）
* 表に無いワークフロー（チャット側が `.github/workflows/` の一覧と表を見比べた。要確認）: `update-live-channel.yml`・`write-live-channel-candidate.yml`。行を足し、内容は docs/notes/live-channel-write.md を参照の形でよい。mj-logs 側の `sync-from-mj.yml` は mj のファイルではないので、表の行にせず今の箇条のまま
* テストの案: `scripts/tests/test_workflows_table.py`。`.github/workflows/*.yml` の各ファイル名が github-actions.md「ワークフローの一覧」の表の行に ``<名前>`` として出てくること、表の行の名前が実物にあること（消したワークフローの行が残らないため）。型は `test_handover_notes_table.py` と同じ（標準ライブラリのみ、自己検知のテストを1つ入れる）
* 参照の付け替えが要る場所（チャット側の `grep`。要確認）: CLAUDE.md「判断・作業の原則」（「ワークフローを手動実行するとき」）・「ブランチ運用」（「ワークフローを変更したとき」）、docs/handover.md「本番反映」の2行と6章の表（static-generation.md・branch-operations.md の行の説明、github-actions.md の行の追加〈`test_handover_notes_table.py` が無いと落ちる〉）、docs/notes/cloudflare.md「ビルドが Cloning で失敗したとき」付近、docs/notes/saikyo-page-design.md、docs/notes/static-generation.md 内の「下の『regenerate-page.yml』」。`git grep` で移した節の見出しの文字列を洗い、漏れなく直す
* チャット側は作業ブランチのファイルを GitHub の API で直接読めるので、新しい文書の全文をログに貼る必要は無い。ログには、移した節の対応表（元の文書・節 → 新しい文書の節）と、文書ごとの前後のバイト数を書く

手順

1. 同じ論点の open issue を検索し（`gh issue list --state all --search "ワークフロー 文書"` など。クローズ済みも含めて確認。#492 は関連で、同じ論点の別 issue があれば止まる）、あれば止まって報告する。#492 に着手中のコメントを書く
2. docs/notes/github-actions.md を作り、前提の対象の節を移す（規則・issue 番号・止まる条件は消さない。同じ趣旨の記述は1つにまとめてよく、まとめたものは対応表に書く）。元の場所は参照の1行に置き換え、参照の付け替えを `git grep` で洗って直す。chat-side-operations.md は `wc -c` で前後を測り、純増していれば止まる。表に無いワークフローの行を足す
3. `scripts/tests/test_workflows_table.py` を足し、`python3 -m unittest discover -s scripts/tests` が全件通ることを確かめる（足したテストが、表の行を1つ消すと落ちることも確かめる）。docs/handover.md 6章の表に github-actions.md の行を足す

止まる条件

* 同じ論点の issue が別にある、または他セッションの着手中コメントが #492 にある
* 前提の対象の節が実物に無い・見出しが違う（文字列で探し直し、それでも無ければ止まる）
* chat-side-operations.md が手順の前より大きくなった（純増）
* 未マージの work/ ブランチが、移す節と同じ行、または `.github/workflows/` の同じファイルの説明を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）
* `python3 -m unittest discover -s scripts/tests` が、今回の変更と無関係な理由で落ちた（原因を報告に書いて止まる。自分の変更で落ちるテストは直してよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（対応表と文書ごとの前後のバイト数を含める）
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる。マージの指示は別に出す）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-GDS-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-GDS-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `git fetch --unshallow` 後、全ブランチのコミットと docs/logs/ の履歴に `GDS` は無い（初出）
- 作業ブランチ: `origin/work/1014-gds` はリモートに無く、ローカルにも無いため `git checkout -b work/1014-gds origin/cloudflare` で作成
- 0. 指示欄の末尾は指示文の最後の行「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」と一致
- 指示文の冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 前のワーカーが再起動された（作業は何も残っていなかった）ため、ここから着手

### 手順1: issue の確認

- GitHub MCP の `search_issues`（「GitHub Actions ワークフロー 文書 集約 github-actions.md」、クローズ済みを含む）: #430・#308・#217・#326・#305。どれも別の論点（鍵・Node・checkout・手動実行）で、同じ論点の別 issue は無い
- #492 のコメント: 着手中のコメントは CHAT-1009-REV-01・CHAT-1012-RVC-01 の2つで、どちらも後のコメントでマージ済みの報告がある。他セッションの着手中は無い
- #492 に着手中のコメントを書いた（issuecomment-6159143604）
- 未マージの work/ ブランチ（5本: 1009-swp-526・1011-swp-hnd・1011-swp-nav・1013-acf-xsize と自分）のうち、今回触るファイル（static-generation.md・branch-operations.md・chat-side-operations.md・`.github/workflows/`・CLAUDE.md・handover.md・cloudflare.md・saikyo-page-design.md・`scripts/tests/`）を変えているものは無い。目的の重なりも無い（件名は SWP の報告と ACF-15 の中断の記録）

### 手順2: 集約

前提の対象の節はすべて実物にあった（見出しも同じ）。サイズは cloudflare 191d9dbde の実測で、static-generation.md の3節は約 2.6KB・8.8KB・4.0KB の見込みどおり。

移した節の対応表:

| 元の文書・節 | 新しい文書（docs/notes/github-actions.md）の節 | 元に残したもの |
|---|---|---|
| static-generation.md「ワークフローの一覧」の表と箇条2つ（`sync_all_logs.py`・mj-logs の `sync-from-mj.yml`） | 「ワークフローの一覧」 | 見出しと参照の1行 |
| static-generation.md「ワークフローの一覧」の冒頭「実行の契機と内容。正は `.github/workflows/`（本数はここに書かない）。」 | 冒頭（「正は `.github/workflows/`。本数はここに書かない。」）と「ワークフローの一覧」の冒頭「実行の契機と内容。」に分けた | 同上 |
| static-generation.md「regenerate-page.yml」 | 「regenerate-page.yml」（表の行の「詳細は上の」を「下の」に） | 見出しと参照の1行 |
| static-generation.md「ワークフローを手動実行するとき」（待機は上限15分の項を含む） | 「ワークフローを手動実行するとき」 | 見出しと参照の1行 |
| branch-operations.md「ワークフローを変更したとき」 | 「ワークフローを変更したとき」（末尾に「表の行も同じコミットで直す」を1行足した） | 見出しと参照の1行 |
| chat-side-operations.md「読み方」の表「Actions の実行結果」の行の本文 | 「チャット側」の「実行結果の確かめ方」 | 表の行（本文は「`docs/notes/github-actions.md`『チャット側』」） |
| chat-side-operations.md「書く前に実物で確かめる」の「ワークフローを変える指示: …」 | 「チャット側」の「ワークフローを変える指示」 | 参照の1行 |
| chat-side-operations.md「ほかのセッションへの共有」の「Actions の失敗通知を受けたら…」 | 「チャット側」の「失敗通知メールは1件ずつ扱う」（決定の2行と1つにまとめた。下） | 参照の1行 |

- まとめたもの: 失敗通知の項。元の規則「原因を調べる前に、その題材を扱っているチャットを特定し、そこへ引き継ぐ」は、決定（基本は Actions のフォローアップのチャットで解決し、題材の系列のチャットへ引き継ぐのはそのチャットが直し中のときなど）と向きが違うため、決定を優先して「原因を調べる前に、その題材を直し中のチャットがあるかを確かめ、あればそこへ引き継ぐ」に書き換えた。「並行するチャットで同じ系列の Chat-Ref を出さない」は残した
- 見出しは元と同じ「ワークフローを手動実行するとき」「ワークフローを変更したとき」にした（参照の書き換えを文書名だけにするため）。元の文書の見出しを残したので、直さなかった参照（下）も参照先の1行を経てたどれる
- 冒頭に、置かないもの（scheduler-worker.md・cloudflare.md・decisions/automation.md・各ワークフロー固有の文書・cloud-sessions.md）への参照を書いた
- 表に無いワークフロー: 実物（19本）と表を突き合わせ、`update-live-channel.yml`・`write-live-channel-candidate.yml` の2つだけが無かった（チャット側の見立てどおり）。`regenerate-saikyo.yml` の行の後に2行を足した（契機は `.github/workflows/` の `on:`〈cron `43 21 * * *` = 06:43 JST〉と docs/notes/live-channel-write.md から）。`sync-from-mj.yml` は箇条のまま

参照の付け替え（`git grep` で移した見出しの文字列を洗った）:

- CLAUDE.md「判断・作業の原則」: static-generation.md「ワークフローを手動実行するとき」→ github-actions.md（「生成スクリプトの構成」は static-generation.md のまま）
- CLAUDE.md「ブランチ運用」: 同（branch-operations.md）「ワークフローを変更したとき」→ docs/notes/github-actions.md「ワークフローを変更したとき」
- docs/handover.md「自動化」（2章、「本番反映」の節の下）の1行目: static-generation.md → github-actions.md。2行目の「同『ワークフローを手動実行するとき』」は1行目に続くのでそのまま正しい
- docs/handover.md 6章の表: static-generation.md・branch-operations.md の行から「ワークフローの一覧」「ワークフローの変更」を外し、github-actions.md の行を足した
- docs/notes/cloudflare.md（「ビルドが Cloning で失敗したとき」付近、`notify-build-failure.yml` の有効化の run）、docs/notes/saikyo-page-design.md、docs/notes/cloud-sessions.md「gh の代わりに GitHub MCP」: 参照先を github-actions.md に
- docs/notes/static-generation.md の2か所「下の『regenerate-page.yml』」→ `docs/notes/github-actions.md`「regenerate-page.yml」
- **直さなかった参照**: `.github/workflows/` のコメント4か所（check-saikyo-unregistered.yml・delete-merged-branches.yml・notify-build-failure.yml の「branch-operations.md『ワークフローを変更したとき』」、fetch-gsc.yml の「static-generation.md『ワークフローを手動実行するとき』」）。ワークフローを変えると「ワークフローを変更したとき」の手動実行が要り、コメントだけのために作業を広げないため。元の見出しと参照の1行を残したのでたどれる。docs/logs/・handover-archive-2026.md の過去の記述も直していない

文書ごとのバイト数（`wc -c`。前は cloudflare 191d9dbde、後は 9354fb247）:

| 文書 | 前 | 後 | 差 |
|---|---:|---:|---:|
| docs/notes/github-actions.md | — | 20,551 | +20,551（新設） |
| docs/notes/static-generation.md | 85,168 | 70,193 | −14,975 |
| docs/notes/branch-operations.md | 23,012 | 21,783 | −1,229 |
| docs/notes/chat-side-operations.md | 26,556 | 25,729 | −827（純増なし。警告域 26,624 まで 895） |
| CLAUDE.md | 22,444 | 22,500 | +56 |
| docs/handover.md | 23,794 | 23,943 | +149 |
| docs/notes/cloudflare.md | 64,992 | 64,989 | −3 |
| docs/notes/saikyo-page-design.md | 78,437 | 78,434 | −3 |
| docs/notes/cloud-sessions.md | 15,200 | 15,197 | −3 |

### 手順3: テスト

- `scripts/tests/test_workflows_table.py` を足した（標準ライブラリのみ。型は `test_handover_notes_table.py`）。github-actions.md「ワークフローの一覧」の本文（次の `## ` まで）の表の行の最初の列 `` `<名前>.yml` `` を集め、`.github/workflows/` の `*.yml`・`*.yaml` と両向きに突き合わせる（表に無い実物・実物に無い行）。自己検知のテスト（表に無い実物と、消した行の残りの両方を見つけること。箇条や次の節の表は数えないこと）を1つ入れた
- `python3 -m unittest discover -s scripts/tests`: 1010 件 OK
- 表の `fetch-gsc.yml` の行を1つ消すと `['fetch-gsc.yml'] != []`（「表に行を足す」）で落ち、戻すと OK
- 足した2行（update-live-channel・write-live-channel-candidate）を除いた表では `(['update-live-channel.yml', 'write-live-channel-candidate.yml'], [])` が返る（変更前の表ではこのテストが落ちることの確かめ）
- docs/handover.md 6章の表に github-actions.md の行を足した（`test_handover_notes_table.py` も OK）
- 公開対象: `docs` と `scripts` は `.assetsignore` にあり、新しいファイルは配信されない

### 決定の記録

docs/decisions/operations.md に「2026-10-14（CHAT-1014-GDS-01）」を足した（指示文の決定3項。作業中の回答なし）。

## 報告

- 状態: 判断待ち
- ブランチ: work/1014-gds
- ログ: https://github.com/retroeater/mj/blob/work/1014-gds/docs/logs/CHAT-1014-GDS-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-gds
- 確認用URL: なし
- マージ: 未（平野さんの判断待ち）
- issue: #492
- 判断が必要なこと:
  - work/1014-gds の cloudflare へのマージ（指示文の「マージ: 判断待ちで止まる」）。移した節の対応表と前後のバイト数は `## 経過` の「手順2」
  - 失敗通知の項は、元の規則「原因を調べる前に題材のチャットへ引き継ぐ」を決定に合わせて「直し中のチャットがあればそこへ引き継ぐ」に書き換えた（`## 経過`「手順2」）。この読み方でよいか
  - `.github/workflows/` のコメント4か所の参照（branch-operations.md・static-generation.md の旧節）は直していない。元の見出しと参照の1行でたどれる。直すならワークフローの変更として別に扱う
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 66e8343c）: https://github.com/retroeater/mj-logs/tree/main/guide/66e8343c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/66e8343c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/66e8343c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/66e8343c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/66e8343c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/66e8343c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/66e8343c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/191d9dbd.md
