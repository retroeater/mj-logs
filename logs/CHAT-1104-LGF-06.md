# CHAT-1104-LGF-06

- 着手日時: 2026-11-04
- 対象issue: なし
- ブランチ: work/1104-lgf-stations
- 着手時HEAD: 1a06d89d7

## 指示

【Claude作成】Claude Code 向け指示：同じ place_id で投稿ごとに駅が違う店を洗い出す（読むだけ） Chat-Ref: CHAT-1104-LGF-06 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも。CHAT-1104-LGF-05 と並行して動かすので、別の新しいセッションに貼る 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lgf-stations の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-lgf-stations を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-lgf-stations origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1104-LGF-04 の店名と同じように、同じ店（同じ place_id）なのに投稿ごとに駅が違う店を全件洗い出し、店ごとに統一の案を出す。平野さんがどう直すかを決める材料にする。調べるだけで、シートにも書かない。
決定（2026-11-04、平野さん）

* 同じ place_id で異なる駅が付いている店を確かめる

前提（チャット側。平野さんの決定ではない）

* 店の駅は、駅を持つ最も新しい1件の投稿から取る（docs/notes/log-page.md の「店の店名・店の URL・ジャンル…」の項）。`log/search.json` には店ごとの駅が1つしか無く、投稿ごとの駅は入っていない。投稿ごとの駅は【2】【3】を重ねた行（`scripts/lib/log_rows.py` の merged_rows）にある（要確認）
* シートはセッションから読めない（CHAT-1031-LGN-11 のログ。鍵はワークフローにだけある）。読むには、作業ブランチの ref で動く一度きりの処理（例: `convert_x_posts.py` に読むだけの一度きりの mode を足し、`convert-x-posts.yml` の mode の選択肢にも足して手動実行）を使う。LGN-11 の `prune` と同じ進め方。シート（【1】【2】【3】ほか）には書かない、Places・Claude・X の API は呼ばない（費用と書き込みが要らない調べのため）
* 数える行は log/ の生成と同じ規則で絞った行（石立岳大・過去メシを除くなど、merged_rows と同じ）。place_id が空の行（「p<投稿ID>」）は1投稿1店なので対象外
* 駅の比べ方: log/ が使う突き合わせ用の駅（【県】を除いた一覧の名前、市ケ谷 → 市ヶ谷 の寄せ）で比べる。寄せると同じになる違いは「表記だけ」として別に数える。駅が空の投稿は数えない（空と駅ありの混在は数だけ書く）
* 種類: (a) 隣り合う駅など、どちらも店の近くでありうる違い（例: 神保町と九段下）(b) 離れた駅で、place_id の付け違いが疑われるもの（LGF-04 の要確認の10店と重なるかを書く）。距離は国土数値情報の駅の点どうし（`docs/notes/log-stations.md`、Google の座標は使わない）で、800m 以内を (a)、超えるものを (b) とする
* 案の決め方: 投稿の数が最も多い駅 → 同数なら最も新しい投稿の駅（今の店の駅）。案を出すだけで、決め方を変えるかは後で平野さんが決める
* 一度きりの mode とワークフローの選択肢は、使い終えたら同じ指示の中で消す（技術的負債を残さない）。消した後の cloudflare との差分が docs/logs・docs/decisions だけなら cloudflare へ入れる
* 店名・駅・place_id・X の投稿 ID は log/ で公開済みの値なので、ログに書いてよい。シートの ID・行の中身の生の値（本文など）は書かない

手順

1. `scripts/lib/log_rows.py` と `scripts/generate_log_pages.py` を読み、投稿ごとの駅がどの列・層から来るかを確かめてログに書く（前提と違えば止まる）。読むだけの一度きりの mode を足し、作業ブランチの ref で手動実行する。出力は実行サマリーかログのアーティファクトに、店ごとの表（鍵・駅ごとの投稿の数と最も新しい日付・投稿 ID・その駅がどの層〈【2】か【3】〉から来たか）を出す。行数（絞る前・絞った後・place_id のある行）も出す
2. 出力から、駅が2つ以上ある店を全件、表にしてログの `## 経過` に書く（「略」にしない）。列: 鍵（place_id）・今の店の駅（search.json）・駅ごとの投稿の数と最も新しい日付と投稿 ID と層・駅どうしの距離・種類（表記だけ／(a)／(b)）・案。あわせて種類ごとの店の数と、LGF-04 の要確認の10店（LGF-04 の表の 1・5・9・12・14・17・19・36・91・93番）との重なりを書く
3. 一度きりの mode とワークフローの選択肢を消し、`git diff origin/cloudflare...HEAD --stat` が docs/logs・docs/decisions だけであることを確かめる。統一の仕方の候補（【3】で直すセルの数／生成で店ごとに数の多い駅にそろえる場合の直す関数）を「判断が必要なこと」に書く

止まる条件

* 手順1で、絞った後の行数が今の log/ の投稿の数（origin/cloudflare の `log/search.json` の投稿の数）と ±1% を超えて違う（毎朝の取り込みで増えた分は除く。数を書いて止まる）
* 手順1の手動実行が一時的な接続の失敗で落ちた（失敗したジョブだけを1回やり直して success なら止まらない。それでも落ちたら止まる。最終報告に「失敗通知が届くが対応不要」と書く）
* 調べにシートへの書き込み・有料の API の呼び出しが要ると分かった
* 未マージの work/ ブランチが `scripts/convert_x_posts.py`・`.github/workflows/convert-x-posts.yml`・`scripts/lib/log_rows.py` の同じ関数・同じ行を変えている、または取り込みで衝突する（並行の work/1104-lgf-alias は `generate_log_pages.py`・`assets/log.js` を変えるので、重ならない見込み。重なればログに書いて止まる）
* 手順3の後の差分に docs/logs・docs/decisions 以外が残る
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LGF-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LGF-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1a06d89d）: https://github.com/retroeater/mj-logs/tree/main/guide/1a06d89d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1a06d89d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1a06d89d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1a06d89d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1a06d89d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1a06d89d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1a06d89d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1a06d89d.md
