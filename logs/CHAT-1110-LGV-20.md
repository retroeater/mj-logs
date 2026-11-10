# CHAT-1110-LGV-20

- 着手日時: 2026-11-10
- 対象issue: #675
- ブランチ: work/1110-lgv-chain
- 着手時HEAD: 62459843e

## 指示

【Claude作成】Claude Code 向け指示：「ログ」の【3】の直し（椿屋珈琲 日比谷・店名が空の13件）を確かめて #675 を閉じ、チェーン店で別の店舗の place_id が付いている見込みの店を洗い出す（読むだけ） Chat-Ref: CHAT-1110-LGV-20 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（一度きりの mode は戻してからマージし、差分を docs だけにする）。生成物（log/）に差が出たときは、push の検知の再生成に任せ、作業ブランチの生成物はマージしない 貼る時機: 平野さんが下の「【3】の直し」を終えてから 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1110-lgv-chain の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1110-lgv-chain を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1110-lgv-chain origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1110-LGV-19 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1110-LGV-20` を足す。

目的

1. 平野さんが【3】で行った直しが log/ に正しく出るかを確かめ、#675 を閉じる
2. 椿屋珈琲（新宿茶寮と日比谷離れ）のように、チェーン店で別の店舗の place_id が付いている見込みの店がどれくらいあるかを洗い出す。調べるだけで、シートにもページにも書かない

決定（2026-11-10、平野さん）

* 【3】の直し（平野さんが行った）:
   * 投稿 1855425650372542512（椿屋珈琲 日比谷）の「place_id」に `ChIJcY3qtu-LGGARLhso8rUcoeo`（椿屋珈琲 日比谷離れ）を入れた
   * 店名が空の13件（CHAT-1110-LGV-18 の経過「4.」の (b)）のうち、2047878234197692813（2026-04-25）は直前の投稿の「自家製麺 ほんま」。【3】の「店名」と「place_id」（ほんまの店 `ChIJU2_3IuyNGGARnH3k1nqmpoA`、駒込）を入れて載せる
   * 残りの12件は載せない（【3】の「表示」に N を入れた）
* 「【3】手動補正（旧）」のタブは平野さんが消した（2026-11-10）
* チェーン店で別の店舗の place_id が付いている見込みの店を洗い出す。本文に駅名などがあって店舗が特定できているものは問題ない（数に入れない）

前提（チャット側。平野さんの決定ではない）

* 洗い出しの考え方（チャット側の案。今の作りを確かめてから、より確かな方法があれば変えてよい。変えたら理由を書く）:
   * まず、変換（`convert_x_posts.py`）が place_id を Text Search で決めるときの問い合わせの組み立て（店名だけか、駅・本文の地名も入れるか）をコードで確かめて書く
   * 「店舗が特定できていない」投稿: place_id があり、【2】の「駅」が本文から取れていない（本文に駅名が無く、駅を place_id から決めたか空の）投稿。駅の出どころの列や判定の方法が無ければ、本文に駅の一覧（`data/stations_n02.json`）の駅名が出てくるかで決める
   * 「チェーン店」の手がかり（どれか1つ）: (i) 同じ店名（`shop_names()` と同じ正規化）が2つ以上の place_id に付いている (ii) 同じ place_id の投稿の駅が互いに離れている（駅の一覧の座標で 2km より遠い。LGF-06 の「離れた駅」と同じ考え方）
   * 出す表: (A) 店舗が特定できていない投稿のうち、チェーン店の手がかりがある店（店の鍵・店名・駅・投稿の数・手がかりの種類・投稿 ID）(B) 店舗が特定できている投稿でも、同じ place_id で駅が離れている店（(ii)。椿屋珈琲と同じ形）。どちらも全件（多ければ件数と例 30件と、全件は `<details>` に）
   * Google の座標・住所は使わない・保存しない（docs/notes/log-page.md の決まり）。駅の座標は駅の一覧（国土数値情報）を使う
* シートはセッションから読めないので、`convert_x_posts.py` に読むだけの一度きりの mode を足し、作業ブランチの ref・dry_run で手動実行して結果を読む（LGV-07・12 と同じ）。終わったら戻すコミットで消す。Places・Claude・X の API は呼ばない
* 【3】の直しの確かめ: 一度きりの mode の中で、log/ の生成と同じ処理を回し、(a) 1855425650372542512 が `ChIJcY3qtu-LGGARLhso8rUcoeo` の店（駅 日比谷）、1450394631586279428 が `ChIJn1k_ZdqMGGARatQHM4XEIjk` の店（駅 新宿）に分かれる (b) 2047878234197692813 が `ChIJU2_3IuyNGGARnH3k1nqmpoA`（ほんま）の投稿に入る (c) 残りの12件が出ず、毎月の一覧の「店名が空の投稿」が0件、を確かめる

手順

1. 未マージの work/ ブランチで `convert_x_posts.py`・`convert-x-posts.yml` を変えているものを確かめる。place_id の決め方と駅の決め方をコードで確かめて書く
2. 一度きりの mode を足し、作業ブランチの ref・dry_run で手動実行する。【3】の直しの (a)〜(c) と、洗い出しの (A)(B) をログに書く
3. (a)〜(c) がすべてそのとおりなら、#675 に結果をコメントして閉じる。違えば閉じずに書く
4. 足したコミットを戻すコミットで一度きりの mode を消し、`git diff origin/cloudflare -- scripts/ .github/` が空であることを確かめてから、ログを cloudflare へマージする

止まる条件

* 一度きりの mode の実行が失敗する（一時的な接続の失敗は1回だけやり直してよい）
* 未マージの work/ ブランチが `convert_x_posts.py`・`convert-x-posts.yml` の同じ箇所を変えている、または取り込みで衝突する
* 戻した後の `git diff origin/cloudflare -- scripts/ .github/` が空でない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、(A)(B) の店をどう直すか（平野さんが【3】の place_id で直す、載せない、など）を平野さんが決めることを書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1110-LGV-20.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1110-LGV-20 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1110-lgv-chain
- ログ: https://github.com/retroeater/mj/blob/work/1110-lgv-chain/docs/logs/CHAT-1110-LGV-20.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1110-lgv-chain
- 確認用URL: なし
- マージ: 未
- issue: #675
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0d3df830）: https://github.com/retroeater/mj-logs/tree/main/guide/0d3df830

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d3df830/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d3df830/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d3df830/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d3df830/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d3df830/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0d3df830/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/535e861a.md
