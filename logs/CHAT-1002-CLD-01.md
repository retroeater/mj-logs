# CHAT-1002-CLD-01

- 着手日時: 2026-10-02
- 対象issue: #448（想定。確認後に更新）
- ブランチ: work/1002-cld
- 着手時HEAD: fe63c7d3

## 指示

【Claude作成】Claude Code 向け指示：「mj_放送対局」の 10/1 第1期鳳匠戦ベスト16 A卓が2件ある原因を調べる（調査だけ） Chat-Ref: CHAT-1002-CLD-01 マージ: 判断待ちで止まる（調査だけ。コード・シート・カレンダーは変えず、ログも cloudflare へ入れない。続きの指示で同じ作業ブランチを使う） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1002-cld の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1002-cld を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1002-cld origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。`git branch -r --no-merged origin/cloudflare` で未マージのブランチを一覧にし、放送対局カレンダーの同期（`scripts/sync_live_calendar.py`・それが使う `scripts/lib/`・予定表の取り込み・関係するワークフロー）に触れているものを書く。

目的
公開カレンダー「mj_放送対局」に、2026-10-01 の「第1期鳳匠戦 ベスト16 A卓」が2件ある（平野さんが気づいた）。原因と、同じ形の重複がほかにあるか・今後も起きるかを調べ、直し方の案を出す。この指示では何も直さない。
決定（2026-10-02、平野さん）

* なし（平野さんからは「重複しているようなので確認してほしい」という依頼だけ）

前提（チャット側。平野さんの決定ではない）

* チャット側が 2026-10-02 に Google カレンダーの連携で「mj_放送対局」の 2026-10-01 を読んだ結果（3件）:
   * 「第1期鳳匠戦 ベスト16 A卓」 説明欄の URL の動画 `lRfK1G89L-M`、10:55:06〜17:14:17、作成 2026-09-28T07:54:50Z、更新 2026-10-01T22:03:18Z
   * 「第1期鳳匠戦 ベスト16 A卓」 動画 `dFOIYiIeQy4`、10:55:12〜13:48:45、作成 2026-10-01T22:03:22Z
   * 「第1期鳳匠戦 ベスト16 B卓」 動画 `hEcZKGw62pA`、17:15:13〜21:59:59、作成 2026-10-01T22:03:21Z
   * A卓の2件は対局者・実況・解説が同じ。作成者は3件とも同期のサービスアカウント
* チャット側は動画の題名・公開範囲・版の判定を読めていない。「`dFOIYiIeQy4` は一部無料版の枠で、過去分の同期（#450）が完全版として拾った」はチャット側の推測で、未確認
* 関係しそうな決定は docs/decisions/broadcast-calendar.md の 2026-09-30（CHAT-0930-CAL-12）: 「同じ日・同じ題名で同じ種類の版が2本以上あるときは、それぞれ別の予定にする（grill Q3）」「予定表由来と動画由来で、同じ放送を二重に出さない」「完全版のライブ配信は全部載せ、除外に入れたものだけ載せない（grill Q1）」
* #448 は「10-08 の第1期鳳匠戦ベスト16CD卓の切り替えを確かめてクローズする」ことになっている（CHAT-0930-CAL-03）。今も Open かは確かめていない。#450・#453・#479 の今の状態も確かめていない
* カレンダーやシートを読む手段は指定しない。docs/notes/yotei-sheet.md・docs/notes/live-channel-write.md・docs/notes/cloud-sessions.md を読み、同期と同じ経路で読む。ワークフローの手動実行が要るなら書き込みなしの実行に限り、先に docs/notes/static-generation.md「ワークフローを手動実行するとき」を読む（待つのは15分まで。超えたらその時点の状態を書き「未確認の項目」に回す）

手順

1. 現状を確かめる。
   * #448・#450・#453・#479 の Open/Closed と最新のコメント（他セッションの着手中コメントの有無）を書く。Open の親（#448 を想定。違えば実物に合わせる）に着手中コメントを残す
   * カレンダーの 2026-10-01 の予定を読み、上の「前提」の3件（件名・動画ID・開始・終了）と一致するかを書く。食い違えば止まる
   * /live の3層のスプレッドシート（【1】【2】【3】と「【4】カレンダー非掲載」）で、2026-10-01 の鳳匠戦の動画をすべて挙げる（上の3本のほかにあればそれも）。1本ごとに、動画ID・YouTube の原題・公開範囲（公開／メンバー限定など）・ライブ配信か・版の判定（完全版／一部無料版など、同期が使う値）・予定開始・実際の開始と終了・長さ・【3】の掲載・【4】にあるか、を表にする。シートは読んだ行数を書き、2回読んで件数が違うなら止まる
2. 原因を特定する。推測でなく、シートの行とコードの条件を引用して書く。
   * 同期が `dFOIYiIeQy4` を予定にした判定の経路（関数名と条件）。`lRfK1G89L-M` とは「同じ種類の版が2本」（CAL-12 grill Q3）として別々の予定になったのか、版の判定が誤っているのか、枠の予定から過去分への切り替えで二重になったのか
   * B卓が1件だけの理由（B卓にも2本目の動画があるか。あれば、なぜ載らなかったか）
   * 2件目と B卓を作った毎朝の実行（2026-10-01 22:03 UTC 頃）の run を特定し、ログでこの3件の作成・更新がどう出ているかを書く
3. 広がりを数え、直し方の案を書く（実装しない）。
   * カレンダー全体で「同じ日・同じ件名の予定が2件以上」の組を数え、一覧にする（日付・件名・動画ID・長さ・作成日時）。CAL-12 grill Q3 の時点で分かっていた13組に当たるものと、それ以外を分ける
   * 今日以降の YouTube の枠で、同じ日・同じ題名の枠が2本以上あるものを一覧にし（10-08 の鳳匠戦 C卓・D卓を含めて確かめる）、放送後に同じ重複が起きるかの見込みを書く
   * 直し方の案を2〜3個。案ごとに、変える場所（規則・シートのどのタブ・決定のどれ）、カレンダーで消える予定と残る予定の件数、平野さんの判断か手作業が要る点を書く。CAL-12 の決定（grill Q1・Q3）の置き換えが要る案はその旨を書く

止まる条件

* 0章の一覧に、同期に触れている未マージのブランチがある。#448・#450・#453・#479 に他セッションの着手中コメントがある
* カレンダーの 2026-10-01 が「前提」の3件と食い違う（重複がすでに無い、件数や動画IDが違うなど）。読めた内容を書いて止まる
* シートを2回読んで件数が違う
* 調べるために、コード・ワークフロー・シート・カレンダーを書き換える必要が出た（しない）。書き込みありの実行もしない
* ブランチの作成や push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」）。報告に、issue の状態、2026-10-01 の鳳匠戦の動画の表、重複の原因（引用つき）、B卓が1件の理由、同じ形の重複の件数と一覧、今日以降の見込み、直し方の案、読めなかった項目（「未確認の項目」）を入れる
* マージは冒頭の「マージ:」の行のとおり（しない）。作業ブランチは片付けずに残す
* 手動実行が失敗で終わったときは、最終報告に「失敗通知のメールが届くが、この指示の実行によるもので対応不要」か、対応が要るならその内容を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1002-CLD-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1002-CLD-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認: `git fetch --unshallow origin` の後、全ブランチのコミット・`docs/logs/` の履歴に `CLD` の識別子は無し
- 作業ブランチ: ローカル・リモートとも `work/1002-cld` 無し → `git checkout -b work/1002-cld origin/cloudflare`

## 報告

- 状態: 作業中
- ブランチ: work/1002-cld
- ログ: https://github.com/retroeater/mj/blob/work/1002-cld/docs/logs/CHAT-1002-CLD-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1002-cld
- 確認用URL: なし
- マージ: 未
- issue: 未確認
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj fe63c7d3）: https://github.com/retroeater/mj-logs/tree/main/guide/fe63c7d3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe63c7d3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe63c7d3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe63c7d3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe63c7d3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe63c7d3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/fe63c7d3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/b308711e.md
