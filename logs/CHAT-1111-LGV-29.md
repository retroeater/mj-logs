# CHAT-1111-LGV-29

- 着手日時: 2026-11-11
- 対象issue: #661
- ブランチ: work/1111-lgv-stfix
- 着手時HEAD: 27980a0bc

## 指示

【Claude作成】Claude Code 向け指示：「ログ」の【3】の直し（淡路町・駅の一覧に無い駅の8件・写真の13件）を確かめ、海外の駅の表記を洗い出して #661 を閉じる Chat-Ref: CHAT-1111-LGV-29 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（一度きりの mode は戻してからマージし、`scripts/`・`.github/` の差を残さない）。log/ は毎朝の再生成に任せる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1111-lgv-stfix の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1111-lgv-stfix を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1111-lgv-stfix origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1111-LGV-28 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1111-LGV-29` を足す。

目的

1. 平野さんが【3】に入れた直し（駅・閉業・写真）が log/ の生成に出るかを確かめる
2. 海外の店で、駅が「Marble Arch (London)」の形（【London】付き）になっていないものを洗い出す
3. 駅の一覧に無い駅も海外の表記の漏れも残っていなければ #661 を閉じる

決定（2026-11-11、平野さん。【3】にはすべて平野さんが入れた）

* 麺巧 潮（1855447329450725837・1725057691960439060）の駅は淡路町
* 駅の一覧に無い駅の8件（LGV-28 の (c)）:
   * 1792770665789218884（なかじま）・1587297994986369025（三河屋）: 閉業。【3】の「表示」を N
   * 1626782256747667457（ロッジ赤石）・1472186441480675328（弁天）: 浅草
   * 1776616654870298808（かおたんラーメン）: 乃木坂
   * 1761237983766921551（卯作）: 新宿
   * 1613273507970023424（The Coach Makers Arms）: 【3】の駅 `【London】Bond Street`（出し方は Bond Street (London)）
   * 1585156830619369473（Carriages）: 【3】の駅 `【London】Paddington`
* 海外の駅は「Bond Street (London)」の形で出す（2026-11-04 の決定のとおり）。この形になっていない海外の店があれば直す
* 既存の店のカードの写真を平野さんが目で確かめた。1枚目が料理でない13件を【3】で直した（【3】に「画像」の列を足した）:
   * 載せない（【3】の「表示」N）: 2098633777597796602（人物のみ）・2098616287144681796（人物のみ）・2068288304588697974（外観のみ）・2048951946263122285（外観のみ）
   * 2枚目の写真にする（【3】の「画像」）: 1979417211812090351（1枚目は犬）・1924681153400992030（外観）・1780109306995921315（外観）・1707984709865865408（外観）・1706624921349321070（同行者）・1622846868152795136（同行者）
   * 4枚目の写真にする（【3】の「画像」）: 1620388477807493120（1〜3枚目は同行者）・1607712256686714882（同）・1548570841071230976（1〜2枚目は同行者、4枚目は好み）
* この13件の1枚目の写真は、料理でない写真を見つける仕組み（次の指示 CHAT-1111-LGV-30）の見本にする

前提（チャット側。平野さんの決定ではない）

* 海外の駅の出し方は `generate_log_pages.py` の `shown_station()`（【London】Marble Arch → Marble Arch (London)）。【…】が無い海外の駅は、括弧の地名が付かない
* 【3】の「画像」の列は、見出しの名前で読む `merged_rows()` がそのまま上書きする見込み（コードの直しは要らない）
* シートはセッションから読めないので、`convert_x_posts.py` に読むだけの一度きりの mode を足し、作業ブランチの ref・dry_run で手動実行して結果を読む（LGV-23〜28 と同じ）。終わったら戻すコミットで消す。Places・Claude・X の API は呼ばない
* 確かめること（log/ の生成と同じ処理で）:
   * (a) 駅と閉業の直し: 麺巧 潮の2件の駅、6件の駅、閉業の2件が載らない（その店にほかの投稿があれば投稿 ID を書く）、ロンドンの2件が「(London)」付きで出る
   * (b) 写真の直し: 【3】の見出しに「画像」があり、9件の載る写真（投稿の画像）が【1】の「写真のURL」の決定どおりの番目（2枚目・4枚目）と同じ。載せない4件が載らない（その店にほかの投稿があれば投稿 ID と、その店のカードの写真がどの投稿のものかを書く）。9件の店のカードの写真がどの投稿の写真になるかも書く
   * (c) ログに載る投稿で、駅が駅の一覧に無いもの（【県】・【London】などの【…】付きを除く）が0件
   * (d) 海外の店で【…】の地名が付いていないものの候補: 駅が空の投稿（LGV-28 で 45件）と、【…】付きでない駅の投稿のうち、店名・駅・本文の地名から海外と見込めるもの（投稿 ID・店名・駅・見込みの理由。本文は書かない）。見分ける手がかりが無ければ、駅が空の投稿の一覧（投稿 ID・店名・X の投稿のリンク）を出す
   * (e) 載る投稿・店の数（LGV-28 の 2,366件・1,699店との差と、差の説明。毎朝の取り込みの差は分けて書く）
* (d) の候補があれば #661 にチェックボックスの行（X の投稿・ログの店のリンク）でコメントし、【3】の駅に `【地名】駅名` を入れる直しを平野さんに頼む（Code はシートに書かない）。候補が0件で (c) も0件なら #661 を閉じる（「状況:」ラベルを外す）

手順

1. 未マージの work/ ブランチで `convert_x_posts.py`・`convert-x-posts.yml` を変えているものを確かめる
2. 一度きりの mode を足し、作業ブランチの ref・dry_run で手動実行する。(a)〜(e) をログに書く
3. #661 にコメントする（閉じるか残すかは上の前提のとおり）
4. 一度きりの mode を戻すコミットで消し、`git diff origin/cloudflare -- scripts/ .github/` が空であることを確かめてから、ログを cloudflare へマージする

止まる条件

* (a)(b) のどれかが決定と違う（違う投稿と値を書いて止まる。#661 は閉じない）
* 一度きりの mode の実行が失敗する（一時的な接続の失敗は1回だけやり直してよい）
* 未マージの work/ ブランチが `convert_x_posts.py`・`convert-x-posts.yml` の同じ箇所を変えている、または取り込みで衝突する
* 戻した後の `git diff origin/cloudflare -- scripts/ .github/` が空でない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1111-LGV-29.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1111-LGV-29 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1111-lgv-stfix
- ログ: https://github.com/retroeater/mj/blob/work/1111-lgv-stfix/docs/logs/CHAT-1111-LGV-29.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1111-lgv-stfix
- 確認用URL: なし
- マージ: 未
- issue: #661
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 27980a0b）: https://github.com/retroeater/mj-logs/tree/main/guide/27980a0b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/27980a0b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/27980a0b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/27980a0b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/27980a0b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/27980a0b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/27980a0b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/500391e1.md
