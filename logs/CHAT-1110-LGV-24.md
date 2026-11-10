# CHAT-1110-LGV-24

- 着手日時: 2026-11-10
- 対象issue: #678
- ブランチ: work/1110-lgv-fujiclose
- 着手時HEAD: 500391e12

## 指示

【Claude作成】Claude Code 向け指示：「ログ」のコーヒー FUJI の【3】の直しを確かめ、#678 を閉じる Chat-Ref: CHAT-1110-LGV-24 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（一度きりの mode は戻してからマージし、差分を docs だけにする）。生成物（log/）に差が出たときは、push の検知の再生成に任せ、作業ブランチの生成物はマージしない 貼る時機: 平野さんが【3】の 2047232238555906256 の「place_id」を入れてから 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1110-lgv-fujiclose の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1110-lgv-fujiclose を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1110-lgv-fujiclose origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1110-LGV-23 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1110-LGV-24` を足す。

目的
平野さんが【3】に入れたコーヒー FUJI の place_id が log/ の生成に正しく出るかを確かめ、#678 を閉じる。
決定（2026-11-10、平野さん）

* 投稿 2047232238555906256 の【3】の「place_id」に `ChIJweLmJvuLGGARVhUj1iX8McM`（コーヒー FUJI）を入れた
* #678 の残りの直し（表示 N の8件・place_id の2件）は LGV-23 で確かめ済み

前提（チャット側。平野さんの決定ではない）

* チャット側が 2026-11-10 に Chrome で `ChIJweLmJvuLGGARVhUj1iX8McM` の地図を開き、「コーヒー FUJI」（千代田区丸の内3丁目、平野さんの共有リンクの店と同じ所）であることを確かめた
* シートはセッションから読めないので、`convert_x_posts.py` に読むだけの一度きりの mode を足し、作業ブランチの ref・dry_run で手動実行して結果を読む（LGV-23 と同じ）。終わったら戻すコミットで消す。Places・Claude・X の API は呼ばない
* 確かめること: log/ の生成と同じ処理で (a) 2047232238555906256 が `ChIJweLmJvuLGGARVhUj1iX8McM` の店に入る（店名・店の駅・店の投稿の数も書く）(b) `ChIJtxODuv6LGGARI9lDb1Ij4E8` の店が無くなる（ほかの投稿が付いていれば投稿 ID を書く）(c) LGV-23 の (a)〜(c)（表示 N の8件が出ない、2085272539753042380・1850006626511753277・1896776432048848946 の place_id）が変わっていない (d) 載る投稿・店の数（LGV-23 の 2,366件・1,698店との差と、差の説明。毎朝の取り込みの差は分けて書く）
* 本番の `https://ryoei.pro/log/search.json?v=<SHA>`（UA に `mj-internal`）で、#678 の直しが入った後の店・投稿の数も書く（入っていなければ「未反映」と書き、止まらない）

手順

1. 未マージの work/ ブランチで `convert_x_posts.py`・`convert-x-posts.yml` を変えているものを確かめる
2. 一度きりの mode を足し、作業ブランチの ref・dry_run で手動実行する。(a)〜(d) をログに書く
3. (a)〜(c) がすべてそのとおりなら、#678 に結果をコメントして閉じる。違えば閉じずに書く
4. 足したコミットを戻すコミットで一度きりの mode を消し、`git diff origin/cloudflare -- scripts/ .github/` が空であることを確かめてから、ログを cloudflare へマージする

止まる条件

* 一度きりの mode の実行が失敗する（一時的な接続の失敗は1回だけやり直してよい）
* 未マージの work/ ブランチが `convert_x_posts.py`・`convert-x-posts.yml` の同じ箇所を変えている、または取り込みで衝突する
* 戻した後の `git diff origin/cloudflare -- scripts/ .github/` が空でない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1110-LGV-24.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1110-LGV-24 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1110-lgv-fujiclose
- ログ: https://github.com/retroeater/mj/blob/work/1110-lgv-fujiclose/docs/logs/CHAT-1110-LGV-24.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1110-lgv-fujiclose
- 確認用URL: なし
- マージ: 未
- issue: #678
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f2ff86b9）: https://github.com/retroeater/mj-logs/tree/main/guide/f2ff86b9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f2ff86b9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f2ff86b9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f2ff86b9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f2ff86b9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f2ff86b9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f2ff86b9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/500391e1.md
