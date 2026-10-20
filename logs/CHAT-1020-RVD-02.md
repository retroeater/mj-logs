# CHAT-1020-RVD-02

- 着手日時: 2026-10-20
- 対象issue: #492
- ブランチ: work/1020-rvd
- 着手時HEAD: 70c0cc5f3270eca6482841381655d7235df6fde2

## 指示

【Claude作成】Claude Code 向け指示：ガイド4文書から Codespace 前提の記述を外す（RVD-01 の続き、同じ作業ブランチ、判断待ちで止まる） Chat-Ref: CHAT-1020-RVD-02 マージ: 判断待ちで止まる（RVD-01 と合わせて整理後の全文をチャット側が読み比べてから、マージの指示を出す） 貼る時機: CHAT-1020-RVD-01 の報告（判断待ち）の後、同じセッションに貼る 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1020-rvd の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1020-rvd を続けて使う（RVD-01 の整理の上に重ねるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1020-RVD-01 のログの `## 報告` が「判断待ち」で仕上がっていることを確かめ、そうでなければ何もせず止まる。

目的
Claude Code の実行環境はクラウドセッションだけになったので、容量制限のある4文書から Codespace を前提にした記述を外し、サイズを減らす（#492。RVD-01 の続き）。
決定（2026-10-20、平野さん）

* Code の実行環境は 2026-10-14 からクラウドセッションのみで、Codespace は使っていない。CLAUDE.md・docs/handover.md・docs/instruction-template.md・docs/notes/chat-side-operations.md の Codespace の記述（worktree の手順、Codespace 用の「作業ブランチ」の文面）は外してよい

前提（チャット側。平野さんの決定ではない）
チャット側が guide/e18c2138 を `grep -i "codespace\|worktree\|/workspaces/mj"` で見つけた箇所（RVD-01 の整理で行が動いていることがある。実物で確かめる）:

* CLAUDE.md「ブランチ運用」の「Codespace: `/workspaces/mj`は全セッションが共有する。…worktree の中で作業し…」の項とその続き（「クラウドセッション（…）の読み替えはdocs/notes/cloud-sessions.md」）: Codespace の部分を外し、「クラウドセッションで実行する。手順は docs/notes/cloud-sessions.md」の趣旨の1行にする。「複数セッションが並行編集するため、セッションごとに作業ブランチと作業ディレクトリを分ける（#198）」の「作業ディレクトリ」が Codespace だけの話なら外す
* CLAUDE.md「引き継ぎ」の「起動時に読み込んだこのファイルは古いことがある（確かめ方: …（Codespace））」: クラウドセッションでも起こるか（起動時の CLAUDE.md が origin/cloudflare の最新と違いうるか）を docs/notes/cloud-sessions.md で確かめ、起こらないなら外し、起こるなら参照先をクラウドの手順に直す
* CLAUDE.md「更新ルール」の「作業する worktree の上で測る」: 「作業ブランチの上で測る」に
* docs/handover.md 3章「Claude Code の実行環境は Codespace（…）かクラウドセッション（…）」: 「クラウドセッション（`docs/notes/cloud-sessions.md`）」だけに。6章の表の `docs/notes/session-network.md`・`docs/notes/branch-operations.md` の行は、Codespace だけの内容（Rebuild・gh の認証・worktree の分離）を「旧 Codespace の記録」と分かる書き方にする（文書自体は消さない）
* docs/instruction-template.md「作業ブランチ」の行の文面3つのうち「Codespace で既存の作業ブランチを続ける」の1つを外し、残る2つ（クラウドで新しく作る・未マージを続ける）はそのまま。冒頭の「式の向きが2つで逆なので」は残る2つで成り立つかを確かめて直す。雛形の「共通手順」の行の「〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉」は、CLAUDE.md の直しと整合する短い形（「〈手順は docs/notes/cloud-sessions.md〉」など）にする。この共通手順の行は多くの指示文が写すため、直した後の文面を報告に書く（チャット側が以後の指示文で使う）
* docs/notes/chat-side-operations.md: 「確認用 URL」の「ビルドが走らなかったときは Codespace の確認用 URL（…）」は外す（クラウドの代替があれば書き換える）。「場面ごとに読む文書」の表の「マージ・ブランチ操作・`/workspaces/mj` の操作を含む指示」から `/workspaces/mj` を外す。同表の「Code に環境に依存する操作…」の行の `docs/notes/session-network.md`（Codespace）はクラウドの文書だけにする。「読み方」の表の「PC では Chrome でも読める」は Codespace と無関係なので残す
* docs/notes/cloud-sessions.md が CLAUDE.md の「読み替え」として書かれている箇所（「`/workspaces/mj` と読み替える」など）は、CLAUDE.md から元の記述が消えると意味が通らなくなる。cloud-sessions.md の該当の節を読み替えでない手順として書き直す（容量の上限は無い）
* 外した記述は `docs/notes/handover-archive-2026.md` の「2026-10-20 の整理で4文書から外した記述（#492）」の節（RVD-01 で作った）に「Codespace 前提の記述」として足す

手順

1. RVD-01 と同じ論点の確認は済んでいるため、`git branch -r --no-merged origin/cloudflare` の各ブランチのうち4文書・cloud-sessions.md を変えているものだけを確かめ直し、ログに書く（重なっても止まらない）。#492 の着手中のコメントは RVD-01 のものがあるため足さない
2. 前提の箇所を実物で確かめて直し、grep で4文書に Codespace・worktree・`/workspaces/mj` が残っていないことを確かめる（残すものは理由を報告に書く）。対応表（箇所 → 処理）を経過に書く
3. 4文書の前後のサイズ（バイト、RVD-01 の後 → この指示の後）を報告に書き、整理後の4文書の全文と cloud-sessions.md・archive の差分を経過に貼る。`python3 scripts/check_asset_limits.py` と `python3 -m unittest discover -s scripts/tests` を通す。RVD-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1020-RVD-02` を足す

止まる条件

* 0章で RVD-01 の報告が判断待ちで仕上がっていない
* Codespace の記述を外すと規則（クラウドでも要る手順）が失われる箇所がある（外さずに報告に書く。止まるのはその箇所だけ）
* 取り込みで、両立しない衝突が出た（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる。この指示ではマージしない）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告の「判断が必要なこと」に、マージの可否と、直した後の「共通手順」の行の文面を書く
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1020-RVD-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1020-RVD-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 03eef856）: https://github.com/retroeater/mj-logs/tree/main/guide/03eef856

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/03eef856/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/03eef856/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/03eef856/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/03eef856/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/03eef856/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/03eef856/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/03eef856.md
