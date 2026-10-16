# CHAT-1016-LVP-01

- 着手日時: 2026-10-16
- 対象issue: #362
- ブランチ: work/1016-lvp
- 着手時HEAD: dde7793

## 指示

【Claude作成】Claude Code 向け指示：/live に出ていない候補の動画を、出ない理由ごとに洗い出す（【3】の掲載の未記入の一覧） Chat-Ref: CHAT-1016-LVP-01 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（調査だけの指示。シート・コード・生成物・issue の本文は変えない） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-lvp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1016-lvp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1016-lvp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
/live の正式公開（#362）の前に、層2（【2】自動変換後）の候補なのに /live のページに出ていない動画を洗い出し、出ない理由ごとに分ける。平野さんが【3】手動補正の「掲載」を埋めて、公開の日に新しい対局が並ぶようにするため。
決定（2026-10-16、平野さん）

* 公開の前に、【3】の掲載の未記入を片付ける（チャットでの A2 の案1）

前提（チャット側。平野さんの決定ではない）

* チャット側の簡易の数え方（`data/live_channel_raw.jsonl` の題名を正規表現で見て、生成物の live/ に動画IDが無いもの）では、2026-09-03 以降の対象らしい動画が約56本あった（例: 第24期プロクイーン決定戦〈2026-10-07〉、第43期十段戦のベスト16・ベスト8 の回戦動画〈2026-09-16〜10-15〉、第43期十段位決定戦 最終日のメンバー限定版）。冒頭のみ無料の公開版（N が正しい）と、範囲が未決の鳳匠戦（#490）が混ざる（要確認）
* 生成は【3】の「掲載」を「表示」として読み、`Y` の行だけを公開する（`scripts/lib/live_layer3.py` の `merge_records()`。要確認）
* 3層のスプレッドシートは `scripts/lib/live_layer3.py` の `SPREADSHEET_ID`、【3】は同 `SHEET_NAME`。ID・URL はログに書かない

手順

1. #362 に着手中のコメントを書く（本文・状態・ラベルは変えない）。生成と同じ経路（`live_layer3.fetch_matches()` など）で【2】【3】を読み、行数をログに書く。生成が「表示」を【3】の「掲載」から取ることをコードで確かめ、違えば止まる
2. 【2】の候補（放送対局候補の動画）のうち、/live の生成が公開しない動画を、出ない理由ごとに数える。理由の分け方: (a) 【3】の掲載が空欄 (b) 掲載が `N`・`除外` (c) 掲載は `Y` だが公開の条件（docs/notes/live-page-design.md「3-1」: 名前の検査・回戦の対局者の人数・まとめ単位の変換・配信予定・タイトル戦シート）で落ちる（落ちた条件も書く）。理由ごとの件数を、【3】の「追加日」の月ごとにも出す
3. (a) の全行を、平野さんが【3】を埋めるための表にして `## 経過` に書く。列は【3】の行番号・動画ID・追加日・公開日（層1）・題名（層1）・【2】のタイトル戦・期・ステージ・卓・まとめ単位・チャット側の案（下の分け方）。並びは追加日の古い順。チャット側の案の分け方: 「N 案（冒頭のみ無料の公開版で、同じまとめ単位にメンバー限定版がある）」「保留（達人戦・昇龍戦・鳳匠戦、#490）」「N 案（掲載範囲の外。docs/notes/live-page-design.md「2-1」）」「Y 案（それ以外）」。Y 案の行は、掲載を Y にしたとき (c) で落ちるか（未登録の名前・まとめ単位が変換できない等）も見て書く。(c) の行も同じ列で別の表にする

止まる条件

* 【2】【3】の読み込みが失敗する、または読み直すたびに行数が変わる（件数を書いて止まる）
* 生成が「表示」を【3】の「掲載」以外から取っている（手順1）
* #362 に他セッションの着手中コメントがある
* (a) が 300 行を超える（表を作らず、理由ごとの件数と追加日の月ごとの内訳だけを書いて止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、(a) の件数と案ごとの件数、未記入を今後知らせる仕組みが要りそうか（追加日の分布から見た所見）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-LVP-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-LVP-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `git fetch --unshallow` の後、`CHAT-*-LVP-*` のコミット・`docs/logs/` の履歴とも0件。`work/1016-lvp` はローカル・リモートとも無く、origin/cloudflare（dde7793）から作成
- 0. 「指示」欄の末尾は指示文の最後の行（「不明な点があれば、…この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1016-lvp
- ログ: https://github.com/retroeater/mj/blob/work/1016-lvp/docs/logs/CHAT-1016-LVP-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1016-lvp
- 確認用URL: なし
- マージ: 未
- issue: #362
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj dde77936）: https://github.com/retroeater/mj-logs/tree/main/guide/dde77936

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/dde77936/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/dde77936.md
