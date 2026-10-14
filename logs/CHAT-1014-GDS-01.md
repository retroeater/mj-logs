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

## 報告

- 状態: 作業中
- ブランチ: work/1014-gds
- ログ: https://github.com/retroeater/mj/blob/work/1014-gds/docs/logs/CHAT-1014-GDS-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-gds
- 確認用URL: なし
- マージ: 未
- issue: #492
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 916667a9）: https://github.com/retroeater/mj-logs/tree/main/guide/916667a9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/916667a9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/191d9dbd.md
