# CHAT-1116-LVP-24

- 着手日時: 2026-11-16
- 対象issue: #495・#362
- ブランチ: work/1116-lvp-post
- 着手時HEAD: 1d3b51f17

## 指示

【Claude作成】Claude Code 向け指示：#495（【3】に無い【2】の候補が50行を超えたら /live の生成を止める）を入れ、#362 の本文の済んだ行を消して、条件つきで cloudflare へマージする Chat-Ref: CHAT-1116-LVP-24 マージ: 承認済み（チャットで）。生成物の差分が出たら、マージせずに止まる 貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1116-lvp-post の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1116-lvp-post を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1116-lvp-post origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1116-LVP-23 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1116-LVP-24` を足す。

目的
CHAT-1116-LVP-23 の判断待ちの3点を、平野さんの答えのとおりに片付ける。
決定（2026-11-16、平野さん）

* #495 の止める境は案A: /live の生成で、【3】に無い【2】の候補が50行（約1%）を超えたら止める（#495 のコメントの案A。前回の行数を残すファイルも、通すための入力も作らない）
* #475 の bot の文面の案内先は今のまま（`scripts/lib/live_extract.py`・docs/notes/live-channel-write.md「7. 毎日の取り込み」）でよい。2026-11-04 の「#490 に直す」は、この形で置き換える
* #362 の本文の「公開の後に回したもの」のうち済んだ行（LV-04 の差4・#396・#397・#475）は消す（経緯は #362 のコメントに残っている）

前提（チャット側。平野さんの決定ではない）

* 毎日の取り込みは、生成の前に【3】へ新しい行を追記するので、ふだん「【3】に無い【2】の候補」は0〜数行の見込み（要確認。今日の値と、ここ数日の run の値を書く）。50行の境は定数にする
* 止めたときの扱いは、ほかの /live の生成の止まる理由（例: 同じ動画IDのまとめ単位の重なり）と同じ形（要確認）

手順

1. #495 に着手中のコメントを書く。今の「【3】に無い【2】の候補」の行数を書く。上の決定どおり止める処理を入れ、テストを足す（49・50・51行の境、【3】が空になったとき）
2. #362 の本文から済んだ行を消す（ほかの行は変えない）。全ページを再生成し、生成物の差分が0であることを確かめる。テストを回す
3. 止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、Workers Builds の check-run の結果を確かめる。#495 に入れた形をコメントし、閉じる（「状況:」ラベルを外す）。上の「決定」を docs/decisions/live.md に足す

止まる条件

* 今の「【3】に無い【2】の候補」が50行を超えている（入れると次の生成から止まるため。行数と内訳を書いて、マージせずに止まる）
* 生成物に差分が出る（マージせずに止まる）
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが、`scripts/generate_live_pages.py`・`scripts/lib/live_layer3.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* #362・#495 に他セッションの着手中コメントがある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1116-LVP-24.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1116-LVP-24 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 67eac94e）: https://github.com/retroeater/mj-logs/tree/main/guide/67eac94e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/67eac94e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/67eac94e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/67eac94e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/67eac94e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/67eac94e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/67eac94e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2cedda30.md
