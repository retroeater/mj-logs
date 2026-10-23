# CHAT-1024-SKP-09

- 着手日時: 2026-10-24
- 対象issue: #626
- ブランチ: work/1024-skp-def
- 着手時HEAD: 0a91c1251

## 指示

【Claude作成】Claude Code 向け指示：最強戦のランキング9部門の定義（数えるもの・数えないもの）を件数つきで一覧にする（調べるだけ、#626）
Chat-Ref: CHAT-1024-SKP-09
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（調査だけの指示。変わるのは docs/logs だけ）
貼る時機: いつでも（CHAT-1024-SKP-07 を止めたセッションに続けて貼ってよい。CHAT-1024-SKP-08 とは別のセッション）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1024-skp-def の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1024-skp-def を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1024-skp-def origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
平野さんが、ランキング9部門の定義をはっきりさせ、部門どうしの重なりを無くすために、今の実装が「何を数えて、何を数えていないか」を件数つきで知りたい。コード・ページ・issue は変えない（決め直しは、この報告を見て平野さんが決める）。CHAT-1024-SKP-07 で止まった質問欄の答えの直しは、この定義が決まってから行う。

### 決定（2026-10-24、平野さん）
- ランキング各部門について、何を数えて何を数えていないかを、それぞれの件数を含めて一覧にする。定義を明確にし、重複のないようにしたい

### 前提（チャット側。平野さんの決定ではない）
- 今の定義の正は、実装（`scripts/generate_saikyo_pages.py` の部門の集計）と docs/decisions/saikyo.md（SKP-02・SKP-06 の決定）。チャット側の理解は下の「チャット側の理解」で、実装と食い違えば実装を正として、食い違いを書く
- チャット側の理解:
  - 最強位: 「ファイナルトーナメント」（`FINAL_MATCH_NAME`）× ステージ「決勝」× 通過 の回数
  - ファイナル: 名前が「ファイナル」で始まる対局に出た年度の数
  - 予選優勝: 名前が「ファイナル」で始まらない対局のステージ「決勝」の通過の回数（決勝の無い対局は数えない）
  - 予選決勝: 同じ対局のステージ「決勝」の対局済みの卓の数
  - 予選出場: 対局済みの卓がある年度の数（ファイナルだけに出た年度も入るか、要確認）
  - 連続出場: 予選出場と同じ年度が続いた最長の数
  - 対局数: 対局済みの卓の数（ファイナルを含む）
  - 予選通過: ファイナル以外の卓の通過の数（予選の決勝の通過＝予選優勝を含むか、要確認）
  - 通過率: 全卓の通過 ÷ 対局数（規定は対局数10回以上）
  - 共通: 未対局の卓は数えない、「別名」で名寄せ、全出場者、載せる下限・上限（SKP-02 の13）

## 手順
1. 実装を読み、9部門それぞれについて、次を1部門1表で `## 経過` に書く: (a) 定義の文（実装どおり、1〜2文） (b) 数えるもの（対局・ステージ・卓・結果の条件）と、その件数（卓の数・選手の数・年度の数のうち合うもの） (c) 数えないもの（条件ごとに1行）と、その件数と、該当する対局名の一覧（10件を超えるなら年度ごとの数と例3件） (d) ランキングに載る件数・載らない件数（下限・上限・規定ごとに）。2025年度のように「ファイナル」が2段（1st Stage・2nd Stage）の年度・2016〜2019年度のプレミアトーナメント・敗者復活戦・アマチュアや著名人の枠の対局が、各部門でどちらに入るかは必ず書く
2. 部門どうしの重なりを表にする: 2部門の組ごとに「片方が他方を含む」「一部が重なる」「重ならない」と、重なる件数（例: 最強位はファイナルの部分、予選優勝は予選決勝の部分、予選優勝は予選通過に含まれるか、予選出場と連続出場は同じ年度を数えるか、通過率の分子と予選通過の差の件数）。同じ数え方の部門が別の名前で2つある場合は、それと分かるように書く
3. 「最強戦」シートの対局名を、「ファイナル」「予選（決勝あり）」「予選（決勝なし）」「その他」の4つに分けた一覧（年度・対局名・ステージの並び・卓数）を、全件 `## 経過` に書く（定義を決めるときの材料。どれに入るか迷うものは理由を書く）。シートは生成と同じ経路で読み、行数を書く。issue には書かない（着手中のコメントだけ #626 に書き、本文・状態・ラベルは変えない）

## 止まる条件
- シートの行数が読み直すたびに変わる（件数を書いて止まる）
- 実装の数えた値が、SKP-06 のログのランキングの表の値と食い違う（部門と件数を書いて止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1024-SKP-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1024-SKP-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1024-skp-def
- ログ: docs/logs/CHAT-1024-SKP-09.md
- 比較URL: なし
- 確認用URL: なし
- マージ: なし
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0a91c125）: https://github.com/retroeater/mj-logs/tree/main/guide/0a91c125

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0a91c125/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0a91c125/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0a91c125/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0a91c125/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0a91c125/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0a91c125/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c8aed737.md
