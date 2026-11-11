# CHAT-1111-LGV-27

- 着手日時: 2026-11-11
- 対象issue: #661
- ブランチ: work/1111-lgv-stcheck
- 着手時HEAD: 05165e667

## 指示

【Claude作成】Claude Code 向け指示：#661 の「駅の照合_1029」の不一致の行を、平野さんが確かめやすいリンク付きの一覧にして #661 にコメントする（読むだけ）
Chat-Ref: CHAT-1111-LGV-27
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（一度きりの mode は戻してからマージし、差分を docs だけにする）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1111-lgv-stcheck の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1111-lgv-stcheck を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1111-lgv-stcheck origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1111-LGV-26 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1111-LGV-27` を足す。

## 目的
#661（「駅の照合_1029」の不一致を確かめて消す）を平野さんが進められるよう、照合のシートの「不一致」の行のうち今ログに載っている投稿を、X の投稿とログの店へのリンク付きのチェックリストにして #661 にコメントする。シートにもページにも書かない。

### 決定（2026-11-11、平野さん）
- #661 に着手する。確かめやすいよう、クリックできるリンク付きの一覧がほしい

### 前提（チャット側。平野さんの決定ではない）
- 照合のシート「駅の照合_1029」（LGN-07 で作成）の列: 【2】の行・【3】の行・投稿ID・今の駅・読み直した駅・新しい駅・一致。#661 の起票時（LGN-20）の数は不一致 54行・空 246行。今の【2】の「駅」は読み直した駅（本文から読んだ駅）が元。「今の駅」の列は Google 由来の値なので、この列の値はログ・issue に書かない（平野さんはシートで見る）
- 平野さんが確かめること（チャット側の案）: 不一致の行の投稿について、ログに出ている駅（【3】の上書きの後）が X の投稿の内容に合っているか。違えば【3】の「駅」に正しい駅を入れる（【3】にその投稿 ID の行が無ければ足す）。ログに載っていない投稿（石立岳大の行は3層から消した・催事・再掲・表示 N・閉業など）は確かめなくてよい
- シートはセッションから読めないので、`convert_x_posts.py` に読むだけの一度きりの mode を足し、作業ブランチの ref・dry_run で手動実行して結果を読む（LGV-23〜26 と同じ）。終わったら戻すコミットで消す。Places・Claude・X の API は呼ばない
- 一度きりの mode で出すもの: 照合のシートの「一致」の列ごとの行数。不一致の行ごとに、照合のシートの行番号・投稿 ID・【2】の今の行番号・【3】の行番号（あれば）・ログに載るか（載らないなら理由: 3層に無い・表示 N・催事/再掲・閉業・店名が空 など）・載るなら店名・ログに出ている駅・読み直した駅（照合のシートの値。【2】と違えば両方）
- #661 のコメント: 冒頭に確かめ方（上の案）と「終わったらチャットに伝える。シートは後の指示で Code が消す」。続けて、載っている投稿だけをチェックボックスの行にする: `- [ ] 照合の行 <n>｜<店名>｜ログの駅 <駅>｜[X の投稿](https://x.com/i/status/<投稿ID>)｜[ログの店](<log/ の店のページへのリンク。今の log/ の生成と同じ形の URL>)`（【3】の行番号があれば添える）。載っていない投稿は `<details>` に件数と理由ごとの数・投稿 ID だけを書く。本文は書かない
- 一度きりの mode の結果に「今の駅」の値は出さない（ログが public の mj-logs に写るため）

## 手順
1. 未マージの work/ ブランチで `convert_x_posts.py`・`convert-x-posts.yml` を変えているものを確かめる
2. 一度きりの mode を足し、作業ブランチの ref・dry_run で手動実行する。結果の数（一致の列ごとの行数、不一致のうち載る・載らない〈理由ごと〉の数）をログに書く
3. #661 に上の形でコメントする（閉じない）。ログの店のリンクが正しく開くかを、2〜3件、本番の log/ で確かめる（UA に `mj-internal`）
4. 足したコミットを戻すコミットで一度きりの mode を消し、`git diff origin/cloudflare -- scripts/ .github/` が空であることを確かめてから、ログを cloudflare へマージする

## 止まる条件
- 照合のシート「駅の照合_1029」が無い、または列が上と違う（見出しを書いて止まる）
- 一度きりの mode の実行が失敗する（一時的な接続の失敗は1回だけやり直してよい）
- 未マージの work/ ブランチが `convert_x_posts.py`・`convert-x-posts.yml` の同じ箇所を変えている、または取り込みで衝突する
- 戻した後の `git diff origin/cloudflare -- scripts/ .github/` が空でない
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんが #661 のチェックリストを確かめて【3】の「駅」を直すことを書く
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1111-LGV-27.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1111-LGV-27 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1111-lgv-stcheck
- ログ: https://github.com/retroeater/mj/blob/work/1111-lgv-stcheck/docs/logs/CHAT-1111-LGV-27.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1111-lgv-stcheck
- 確認用URL: なし
- マージ: 未
- issue: #661
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 05165e66）: https://github.com/retroeater/mj-logs/tree/main/guide/05165e66

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/05165e66/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/05165e66/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/05165e66/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/05165e66/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/05165e66/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/05165e66/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/500391e1.md
