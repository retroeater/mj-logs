# CHAT-1110-LGV-17

- 着手日時: 2026-11-10
- 対象issue: #675・#636（参照）
- ブランチ: work/1110-lgv-l3check
- 着手時HEAD: 9df3ca3ca

## 指示

【Claude作成】Claude Code 向け指示：「ログ」の【3】の差し替えの後の確かめと文書の直し、閉業の3件を載せない、椿屋珈琲 新宿店の place_id を調べる Chat-Ref: CHAT-1110-LGV-17 マージ: 承認済み（チャットで、2026-11-10、平野さん）。ただし手順2の違いが見込みどおり（下の止まる条件に当たらない）ときに限る。当たったらマージせずに止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1110-lgv-l3check の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1110-lgv-l3check を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1110-lgv-l3check origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1107-LGV-16 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1110-LGV-17` を足す。

目的
CHAT-1107-LGV-16 の後、平野さんが3層のシートの【3】のタブを差し替えた（今の【3】手動補正は8列の案）。差し替えた後も log/ が見込みどおりに作られるかを確かめ、文書を今の形に直す。あわせて #675 の平野さんの回答を反映する。
決定（2026-11-07〜10、平野さん）

* 【3】のタブを差し替えた（旧を「【3】手動補正（旧）」、案を「【3】手動補正」に）。列の順は平野さんが入れ替えて「投稿ID・表示・判定・日付・place_id・駅・店名・メニュー」
* 差し替えの後の確かめで違いが見込みどおりなら、そのまま cloudflare へマージしてよい
* #675 の (1): 地図のリンクが無くなる3件の店はすべて閉業。載せない: 1615197432467566592（牡蠣屋うらら）・1772580856504472001（為セバ成ル）・1845395661509824631（かめや）
* #675 の (2): 椿屋珈琲の新宿店（投稿 1450394631586279428）の店は、平野さんが送った Google マップの共有リンク https://share.google/tl4U1BsACdudrlyty の店

前提（チャット側。平野さんの決定ではない）

* 【3】は見出しの名前で読む（LGV-12 で確かめた）ので、列の順の入れ替えは結果を変えない見込み
* 見込みの違い（LGV-16 の (d)）: 店の地図のリンクだけ。旧「ログ」の共有リンク（maps.app.goo.gl・goo.gl）と写真の URL 1件が検索の形に替わる（約1,978件）。リンクが無くなる3件は上の決定で載せなくなる。ほかに毎朝の取り込みの差が入りうる。チャット側が 2026-11-10 08:50 JST 頃に本番の search.json を見たときは、店 1,718・投稿 2,389、リンクのホストは maps.app.goo.gl 903・goo.gl 447・[www.google.com](https://www.google.com) 354・pbs.twimg.com 1・なし 13 で、まだ差し替えが反映されていない見込み
* 閉業の3件は `HIDDEN_POSTS` に理由「閉業（店名）」で足す（place_id が無いので毎月の閉業の確かめでは外れない）
* 椿屋珈琲 新宿店の place_id: 共有リンクの転送先（Google マップの URL）から店名と場所を読み、Places の Text Search（IDs Only。docs/notes/google-cloud.md の決まりのとおり。保存するのは place_id だけ）で place_id を得る。転送先を読むのは1回だけ。候補が1つに決まらないとき（結果が複数で、転送先の URL に含まれる手がかりで選べない）は、選ばずに候補の数を書く。得た place_id はシートに書かず、ログと #675 のコメントに書く（平野さんが【3】の place_id の列に入れる）。日比谷店の place_id `ChIJn1k_ZdqMGGARatQHM4XEIjk` と同じなら止まる
* docs/notes/log-page.md: 3層の【3】の列（8列）と、【2】の「表示」の規則を古い行にも当てたこと（2026-11-07、LGV-16。【2】の 2,030件を N→Y）、【3】には補正の要る行だけを置くこと、「店URL」は【3】に持たず生成で組み立てること（旧「ログ」の共有リンクは【2】に残る分だけになる）を書く。「【3】手動補正（旧）」のタブは残っている（消す時期は別に決める）と書く

手順

1. `HIDDEN_POSTS` に3件を足す。テストを回す。docs/notes/log-page.md を直す
2. `regenerate-page.yml` を作業ブランチの ref・`target_page: log_pages` で手動実行して log/ を作り直し、cloudflare の生成と比べる。(a) 店・投稿の数の差 (b) 外れた・増えた投稿の全件（規則ごと）(c) 店の地図のリンクのホストごとの数の前後 (d) リンク以外で値が変わった店・投稿の数と例（毎朝の取り込みの差は分けて書く）
3. 椿屋珈琲 新宿店の place_id を上の前提のとおり調べる。#675 に、(1) の3件を載せないようにしたことと、(2) の place_id（平野さんが【3】の place_id の列に入れる）をコメントする
4. 止まる条件に当たらなければ cloudflare へマージする（CLAUDE.md「ブランチ運用」）。マージの後、check-run と push の検知で走る自動処理の結果を書き、本番の `https://ryoei.pro/log/search.json?v=<SHA>`（UA に `mj-internal`）で店・投稿の数とリンクのホストごとの数を確かめる

止まる条件

* 手順2の (b) に、閉業の3件と毎朝の取り込みで説明できない投稿の増減がある
* 手順2の (d) に、リンク以外の値の違いがある（毎朝の取り込みの差は除く）
* 手順2の (c) で、検索の形に替わったリンクが見込み（約1,978件）から大きく外れる（±10% を超える）
* 生成が止まる・失敗する（一時的な接続の失敗は、失敗したジョブだけを1回やり直して success なら止まらない）
* テストが通らない
* 未マージの work/ ブランチが `HIDDEN_POSTS`・docs/notes/log-page.md の同じ箇所を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* 手順3で place_id が日比谷店と同じになった（手順3だけ止め、ほかは進めてよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんが【3】の place_id の列に入れる値（投稿 1450394631586279428）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1110-LGV-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1110-LGV-17 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1110-lgv-l3check
- ログ: https://github.com/retroeater/mj/blob/work/1110-lgv-l3check/docs/logs/CHAT-1110-LGV-17.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1110-lgv-l3check
- 確認用URL: なし
- マージ: 未
- issue: #675・#636（参照）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj fc46d17c）: https://github.com/retroeater/mj-logs/tree/main/guide/fc46d17c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/fc46d17c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/fc46d17c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/fc46d17c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/fc46d17c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/fc46d17c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/fc46d17c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/9df3ca3c.md
