# CHAT-1014-OHA-06

- 着手日時: 2026-10-14
- 対象issue: #504（関連）
- ブランチ: work/1014-oha-late
- 着手時HEAD: ab6bf27df

## 指示

【Claude作成】Claude Code 向け指示：morning.md の「2. 予約実行の未実行」に「遅れて実行」を足し、実行の対象を cloudflare に限る（マージまで） Chat-Ref: CHAT-1014-OHA-06 マージ: 承認済み（チャットで。2026-10-14 平野さん） 貼る時機: いつでも（OHA-05 と同じセッションの続きでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-oha-late の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1014-oha-late を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-oha-late origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
2026-10-14 04:30 の check-image-links は Cloudflare 側の定時の回が抜け、遅れて届いた回が元の予定時刻のまま 09:25 に起動した（CHAT-1014-ACF-16 で確定）。`scripts/morning_report.py` の 2節は「予定時刻以降に schedule／workflow_dispatch の実行が1件でもあれば実行あり」なので、5時間遅れの実行が「なし」に埋もれた。遅れた実行を書き、作業ブランチの試運転を数えないようにする。
決定（2026-10-14、平野さん）

* 2節「予約実行の未実行」の判定を次に変える
   * 実行の対象は、契機が schedule または workflow_dispatch で、ブランチが cloudflare の実行だけ（作業ブランチの手動実行は数えない）
   * 予定時刻以降の対象の実行のうち最初のものが、予定時刻＋3時間を超えて始まっていたら「遅れて実行（＋h:mm）」として書く（行の形はほかの節と同じ: 開始・ワークフロー・契機・ブランチ・結論・実行のリンク）
   * 対象の実行が無いときの「遅れの可能性」（＋3時間以内）／「未実行」（それより前）の区別は今のまま
* 2節の見出しの括弧の説明を「予定から3時間以内は『遅れの可能性』。3時間を超えて始まった実行は『遅れて実行』」の趣旨に直す
* 閾値（3時間）はほかの節と同じく定数で持ち、見直し（10/21）で変えやすくする

前提（チャット側。平野さんの決定ではない）

* 遅れて実行の行には予定時刻も書く（例: 「予定 04:30 → 開始 09:25（＋4:55）」）。差は開始時刻（`run_started_at`）− 予定時刻
* 同じ予定に対して対象の実行が複数あるときは最初の1件だけを見る（2件目以降は手動の再実行の見込み）
* テスト（`scripts/tests/test_morning_report.py`）に次の3件を足す: 遅れて実行（＋3時間超、cloudflare）／予定どおり（＋3時間以内）は書かない／作業ブランチの手動実行だけがあるときは「未実行」になる
* 書き出しの比較（中身が同じなら書かない）は変えない。見出しの文言が変わるので、マージ後の最初の写しでは必ず morning.md が書き直される
* docs/notes/cloud-sessions.md「作業ログ」の morning.md の説明（OHA-03 で足した2行）に、2節の判定の一句が入っていれば直す。入っていなければ文書は変えない

手順

1. `scripts/morning_report.py` の2節の判定と `scripts/tests/test_morning_report.py` を読み、「実行あり」の条件（契機・ブランチ・時刻）が目的欄のとおりかを確かめる（違えば止まる）。未マージの work/ ブランチが同じファイルを変えていないか確かめる
2. 決定のとおり直し、テストを足す。`python3 -m unittest discover -s scripts/tests` が通ること。OHA-03 と同じ手順（mj-logs を scratchpad にクローンし、cloudflare の版と作業ブランチの版を続けて実行）で手元で書き出し、2節に 10/14 04:30 の check-image-links が「遅れて実行」として出ること（26時間の範囲に入っていれば）と、status.md の差分が0（「書き出した時刻」を除く）であることを確かめる。morning.md の2節をログの「経過」に貼る
3. マージして確かめる: CLAUDE.md「ブランチ運用」のとおり cloudflare へマージして push し、check-run を待つ（上限15分）。マージ後の mj-logs の最初の写し（上限15分）で morning.md の2節の見出しが新しい文言になっていることを確かめる。docs/decisions/automation.md に決定を1行足す（docs のみの追いの push でよい）

止まる条件

* 手順1で「実行あり」の条件が目的欄の説明と違う（実物の条件をログに書いて止まる）
* テストが落ちる（自分の変更で落ちると分かっているものは直してよい。処理を報告に書く）
* 手元の実行で status.md の出力が変わる（戻して止まる）
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが `scripts/morning_report.py`・そのテストの同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* マージ後の check-run の失敗のうち「今回の変更による失敗」（`scripts/`・テスト）。無関係な失敗なら原因を報告に書いたうえで残りを進めてよい
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「未確認の項目」に「2026-10-15 朝の写しで、2節が新しい判定で出ること（Worker の起動が定時なら『なし』）」を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-OHA-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-OHA-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミット: 0件。雛形の4行は揃っている。指示欄の末尾は指示文の最後の行と一致
- 作業ブランチ: ローカル・リモートとも無いため `git checkout -b work/1014-oha-late origin/cloudflare`

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ab6bf27d）: https://github.com/retroeater/mj-logs/tree/main/guide/ab6bf27d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ab6bf27d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ab6bf27d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ab6bf27d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ab6bf27d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ab6bf27d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ab6bf27d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ec9e24c3.md
