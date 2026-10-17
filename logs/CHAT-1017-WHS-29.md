# CHAT-1017-WHS-29

- 着手日時: 2026-10-17
- 対象issue: #592
- ブランチ: work/1017-whs-date
- 着手時HEAD: 0ba90e7d5

## 指示

【Claude作成】Claude Code 向け指示：CHAT-1017-WHS-28（日付を年月日の形に）をマージし、本番を確かめて #592 をクローズする
Chat-Ref: CHAT-1017-WHS-29
マージ: 承認済み（チャットで。この指示文を貼ることが承認）
貼る時機: いつでも（CHAT-1017-WHS-28 が判断待ちで止まっていること。0章で確かめる）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-whs-date の push と cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1017-whs-date を続けて使う（WHS-28 の変更をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、docs/logs/CHAT-1017-WHS-28.md の `## 報告` の状態が「判断待ち」で、origin/work/1017-whs-date の先頭が WHS-28 のログ・コミット（828f21d3f・03f9169f8 を含む）から進んでいない（ログだけの追いのコミットは可）ことを確かめ、食い違えば何もせず止まる。

## 目的
WHS-28 の変更（帰り道・Mつく・女流勉強会のカード・前後の回の日付を「2026年8月27日」の形に、一覧の `data-info` に両方の形）を本番に入れ、#592 を閉じる。

### 前提（チャット側。平野さんの決定ではない）
- マージの承認は、平野さんがこの指示文を貼ることで行う（冒頭の「マージ:」の行）。表示の決定は WHS-28 の「決定」のまま
- チャット側はプレビュー（ブランチ名の別名 URL、`?v=03f9169f8`）で帰り道の各話 `8w7SW0XgX64.html` の前後の回（2025年12月22日・2025年12月8日）と他の回のカード（2026年10月11日 など）、女流勉強会の一覧のカードの日付が年月日の形で出ているのを見た
- WHS-28 の差分は `wayhome/`・`mtsuku/`・`study/` の 146 ファイル（足した行 642：`data-info` 143・カードの日付 219・前後の回の日付 280）と、`scripts/lib/wayhome.py`・6つの生成スクリプト・テスト3件・docs/notes/video-wayhome.md・docs/decisions/wayhome.md・mtsuku.md・study.md（WHS-28 のログ `## 経過`「手順2」）
- マージの後に `regenerate-sitemap.yml` が `wayhome/`・`mtsuku/` の lastmod を更新する見込み（`study/` は未公開でサイトマップに無い見込み。要確認）。WHS-28 と関係のない再生成の差分は見込みに入れない

## 手順
1. WHS-28 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1017-WHS-29` を足す。origin/cloudflare を作業ブランチに merge で取り込む（生成物が衝突したら CLAUDE.md「ブランチ運用」のとおり取り込んだ後のスクリプトで作り直す。両方の変更が両立する文書の衝突〈追記どうし・隣り合う行〉は両方を残して解き、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる）
2. 取り込んだ後に、6つのページ（video_wayhome・wayhome_episodes・mtsuku・mtsuku_episodes・study・study_episodes）を再生成し、WHS-28 と同じ確かめ（テスト全件・本文の表示に `YYYY-MM-DD` が 0 件・一覧のカードの `data-info` が全件で両方の形を持つ〈件数＝カードの数〉・`uploadDate` の行の差分 0）を行う。通れば `git push origin work/1017-whs-date:cloudflare` でマージする
3. 本番（`?v=<未使用の値>` を付ける）で帰り道・Mつく・女流勉強会の一覧と各話を各1ページ開き、日付が年月日の形で出ていることを確かめる。`regenerate-sitemap.yml` の実行と、その差分が `wayhome/`・`mtsuku/`（と `study/`）の lastmod だけかを確かめる。通れば #592 に結果を書いてクローズし（残る作業は無い見込み。あれば起票してから閉じる）、着手中のコメントの扱いは CLAUDE.md「issueの着手ルール」のとおり。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、そのとおりに行う

## 止まる条件
- 0章の確かめが食い違う
- 取り込みの後の差分に、WHS-28 の範囲（上の前提）と取り込みで説明できないものが出る（新しい回の追加で件数が増えたのは理由を書いて進めてよい）
- 手順2の確かめが1つでも通らない
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージ後の check-run の失敗: 今回の変更による失敗なら止まる。無関係な失敗なら原因をログに書いたうえで手順3を進めてよい

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-WHS-29.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-WHS-29 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 13c870a9）: https://github.com/retroeater/mj-logs/tree/main/guide/13c870a9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/13c870a9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/13c870a9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/13c870a9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/13c870a9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/13c870a9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/13c870a9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/13c870a9.md
