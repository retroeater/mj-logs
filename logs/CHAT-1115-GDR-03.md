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

## 経過

- 0章: 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は欠けなし（指示文は改行が詰まった形で貼られたが、行の中身はそろっている）
- 着手前の確認: `CHAT-1115-GDR-03` のコミット・ログは0件（`GDR` はこのセッションの3本目で、識別子の重複の確認は最初の指示〈GDR-01〉で済み）。#492 は open
- 作業ブランチ: work/1115-gdr はローカル・リモートとも無く、`git checkout -b work/1115-gdr origin/cloudflare`（8b94f7eec）で作った
- 手順1:
  - `gh api search/issues`（「上限」「警告域」）と #492・#292・#301・#421: #292・#301 は open で進行中のコメントなし、#421・#297・#336 は closed。食い違う決定・進行中の作業は無い。#492 の最新の着手中は 2026-11-13 の GDR-01（同日にマージ済み）。#492 に着手中のコメントを書いた
  - docs/notes/github-actions.md「ワークフローを変更したとき」を読んだ（手動実行で確かめる。`workflow_dispatch` は 42行目にある）
  - yml の今の値は前提どおり（147〜150行の `26624 28672`×3・`14336 16384`、163行の `FLOOR=24576`）
  - 未マージの work/ ブランチで check-assets.yml を変えているのは `origin/work/1101-prp-pub` だけで、89行目の allowed のコメント（「未公開。」を消す）。今回の 147〜150・163行とは別の箇所なので止まらない
- 手順2: 5つの値を `25600 27648`×3・`12288 14336`・`FLOOR=23552` にした（ec91e3692）。値に触れるコメント「FLOOR(警告域より 2KB 下)」は 25600−2048=23552 で合うので変えていない
  - `git grep -n -w -E '26624|28672|16384|24576'`（*.md・*.py・*.yml、除外は指示のとおり）は0件。`(26|28|14|16|24)\s?KB` は CLAUDE.md の最終目標「handover.md 24KB 前後」（目標値で上限ではない）と ask-worker.md の別の意味だけ
  - 桁区切りの形（`24,576` など）を見落としていた。サブエージェントのレビューで、docs/notes/github-actions.md「ワークフローの一覧」の check-assets.yml の行に「24,576 バイト超」が残っていると分かり、値を書かずに「FLOOR〈警告域より 2KB 下、値は yml〉」にした。同じ行の「CLAUDE.md・handover.md のサイズ」も4文書の名前に直した（77cd8dd95。yml と同じコミットにはできなかった）。`git grep -E '26,624|28,672|16,384|24,576|14,336'` の残りは0件
  - `python3 -m unittest discover -s scripts/tests`: 2164件 OK
- 手順3（手動実行、run 43660522043、ref work/1115-gdr、head ec91e3692）: completed success。注釈は ubuntu-latest の移行の notice だけで、warning・error は無い。ログの出力:

```
CLAUDE.md: 23299 bytes (警告域 25600 / 上限 27648)
docs/handover.md: 23619 bytes (警告域 25600 / 上限 27648)
docs/notes/chat-side-operations.md: 22041 bytes (警告域 25600 / 上限 27648)
docs/instruction-template.md: 10075 bytes (警告域 12288 / 上限 14336)
docs/notes/chat-side-operations.md: 比較元 8b94f7eec の 22041 bytes → 22041 bytes(警告は 23552 bytes 超で純増のとき)
```

- サブエージェントのレビュー（general-purpose、差分・目的・決定・コード規約を渡した、1回）:

| 指摘 | 扱い |
|---|---|
| github-actions.md 28行目に旧値「24,576 バイト超」が残る（値を2か所に持つ） | 直した（77cd8dd95、値を書かずに yml の FLOOR を指す形） |
| 同じ行の「CLAUDE.md・handover.md のサイズ」が実際の4文書と違う（今回より前から） | 直した（77cd8dd95） |
| 5つの値・コメント・ほかのステップ・ほかのワークフロー・テスト・security.md の観点 | 指摘なし |

  2回目のレビューは、直しが docs のみ（self-review.md の対象外）なので行わない
- 最終の HEAD（f4e3606e2、ログとテストの件数の直しを含む）でもう一度手動実行した（run 43660796546）: completed success、注釈は notice だけ。出力の4文書の行と純増の行は上の run と同じ値
- マージ: `git diff --stat origin/cloudflare...HEAD` は check-assets.yml・docs/decisions/operations.md・docs/logs/CHAT-1115-GDR-03.md・docs/notes/github-actions.md だけ。push 直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD` が真を確かめ、`git push origin work/1115-gdr:cloudflare` でマージした。push の出力の範囲: `8b94f7eec2..f4e3606e25  work/1115-gdr -> cloudflare`
  - f4e3606e2 の check-run: 「check」completed success。「Workers Builds: mj」は付いていない（変わったのが `.github/` と `docs/` だけで、ビルドの watch の対象外と見られる。配信するファイルは変えていない）
- #492: 本文の「上限の見直し」を済み（新しい値と 2026-11-15）にし、`assets-check.yml` の2か所を `check-assets.yml` に直し、`Chat-Ref: CHAT-1115-GDR-03` を足した。マージした旨と前後の値をコメントした（状態・ラベルは変えていない）

## 報告

- 状態: 完了
- ブランチ: work/1115-gdr
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1115-GDR-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1115-gdr
- 確認用URL: なし
- マージ: 済（f4e3606e2）
- issue: #492（本文の「上限の見直し」を済みにし、コメントを追加）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1bb9d125）: https://github.com/retroeater/mj-logs/tree/main/guide/1bb9d125

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1bb9d125/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1bb9d125/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1bb9d125/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1bb9d125/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1bb9d125/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1bb9d125/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1bb9d125.md
