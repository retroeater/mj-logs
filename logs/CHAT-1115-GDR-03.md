# CHAT-1115-GDR-03

- 着手日時: 2026-11-15
- 対象issue: #492
- ブランチ: work/1115-gdr
- 着手時HEAD: 8b94f7eec

## 指示

【Claude作成】Claude Code 向け指示：ガイド4文書のサイズの上限・警告域を下げる（check-assets.yml の5つの値） Chat-Ref: CHAT-1115-GDR-03 マージ: 承認済み（チャットで） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1115-gdr〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1115-gdr の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1115-gdr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1115-gdr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
4文書のレビュー（CHAT-1113-GDR-01・02）で文書が小さくなったので、#492 の残り「上限の見直し」を行い、`.github/workflows/check-assets.yml` の上限・警告域と、chat-side-operations.md の純増の警告の下限（FLOOR）を下げる。
決定（2026-11-15、平野さん）

* CLAUDE.md・docs/handover.md・docs/notes/chat-side-operations.md は警告域 25,600／上限 27,648 バイト（25KB／27KB）にする。docs/instruction-template.md は警告域 12,288／上限 14,336 バイト（12KB／14KB）にする（一律 24KB／26KB の案、template だけ下げる案は採らない）
* 次の条件をすべて満たしたら、判断を待たずに cloudflare へマージしてよい: 差分が check-assets.yml の5つの値（と docs）だけ／作業ブランチで check-assets.yml を手動実行して success／その実行のログに4文書とも新しい警告域・上限が出て、警告が出ていない
* （記録だけ。2026-11-14 の決定で docs/decisions に未記録）handover.md の4章・6章の直し（閉じた #591・#599・#611 の記述、4章の経緯の括弧）は、pro/ の公開（#641）のマージの後に1本の指示で行う（今すぐ直す案・PRP 系列に頼む案は採らない）
* （記録だけ。2026-11-14 の決定で docs/decisions に未記録）CLAUDE.md「作業ログ」節の「ログの節を書き換えるとき」の項は、今のまま CLAUDE.md に残す（`docs/logs/_template.md` への参照だけにはしない）

前提（チャット側。平野さんの決定ではない）

* 2026-11-15、origin/cloudflare（8d9cccb6）の4文書は CLAUDE.md 23,299・docs/handover.md 23,619・docs/notes/chat-side-operations.md 22,041・docs/instruction-template.md 10,075 バイト（チャット側が `git cat-file -s` で測った）
* 今の値は check-assets.yml の「ガイド文書のサイズを確認」の `check` の4行（3文書が `26624 28672`、template が `14336 16384`）と、「chat-side-operations.md の純増を確認」の `FLOOR=24576`（同じ版で読んだ）。同じ数値は、docs/logs・docs/decisions・handover-archive-2026.md を除くリポジトリのほかの場所には無かった（`git grep` で確かめた。手順2で確かめ直す）
* FLOOR は yml のコメントで「警告域より 2KB 下」とされているので、新しい警告域 25,600 に合わせて 23,552 にする（チャット側の案。平野さんには値の表で示した）
* 未マージの work/1101-prp-pub は docs/handover.md を変えている（マージ後の handover.md は 23,800 バイト前後の見込み〈要確認〉。新しい警告域の内側）
* check-assets.yml は `workflow_dispatch` を持ち、既定ブランチにあるので作業ブランチの ref で手動実行できる見込み
* #492 の本文の残りに「上限の見直し（2026-11-16）」があり、ワークフローの名前が古い `assets-check.yml` のままになっている（朝のチャットの伝言による。要確認）

手順

1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。#492・#292・#301・#421 と「上限」「警告域」で）、この変更と食い違う決定・進行中の作業があれば止まって報告する。#492 に着手中のコメントを書く。docs/notes/github-actions.md「ワークフローを変更したとき」を読む
2. check-assets.yml の5つの値を変える: 3文書の `check` の行を `25600 27648`、template の行を `12288 14336`、`FLOOR=23552`。コメントは値に触れている所だけ合わせる。`git grep -n -E '26624|28672|14336|16384|24576'`（リポジトリ全体。除外は docs/logs・docs/decisions・docs/notes/handover-archive-2026.md の過去の行と、今回書き換える yml）で残りを確かめ、ガイド文書・テストに古い値があれば同じコミットで直す（`.claude/skills/` の Cloudflare の資料の「16KB」などは別の意味なので触らない）
3. 作業ブランチで check-assets.yml を手動実行し、ログの「ガイド文書のサイズを確認」「chat-side-operations.md の純増を確認」の出力をログの `## 経過` に引用する。止まる条件に当たらなければ cloudflare へマージする（マージ後の Workers Builds の check-run は15分で結果が出なければ「未確認の項目」に回して完了にする）。#492 の本文の残りの「上限の見直し」を済みにし（新しい値と日付を書く）、ワークフローの名前を `check-assets.yml` に直して、コメントを残す（状態・ラベルは変えない）

止まる条件

* 0章・手順1の確認に当たった（食い違う決定、他セッションの着手中コメント）
* yml の今の値が前提と違う（別のセッションが先に変えた）
* 手動実行が success でない、4文書のどれかに警告・エラーが出た、ログの警告域・上限が新しい値でない（一時的な接続の失敗は、失敗したジョブだけを1回再実行して success なら止まらない。今回の変更と無関係な失敗〈ほかのステップ〉は原因を報告に書き、容量の2ステップが通っていれば止まらない）
* 手動実行の起動が 403 などでできない（平野さんに頼む実行の URL を報告に書いて止まる）
* 差分に、check-assets.yml の5つの値・手順2で見つけた古い値の直し・docs（docs/decisions/・docs/logs/ を含む）以外がある
* 未マージの work/ ブランチが check-assets.yml の同じ行を変えている、または取り込みで衝突する（別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1115-GDR-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1115-GDR-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8b94f7ee）: https://github.com/retroeater/mj-logs/tree/main/guide/8b94f7ee

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b94f7ee/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b94f7ee/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b94f7ee/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b94f7ee/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b94f7ee/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8b94f7ee/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/8b94f7ee.md
