# CHAT-1017-PAD-07

- 着手日時: 2026-10-17
- 対象issue: なし
- ブランチ: work/1017-pad-doc
- 着手時HEAD: 42c384b5d

## 指示

【Claude作成】Claude Code 向け指示：振り返り（2026-10-17、PAD のチャット）の申送り — チャット側の文書に3つの規則を足す（ドキュメントのみ）
Chat-Ref: CHAT-1017-PAD-07
マージ: ドキュメントのみ（docs/notes/・docs/logs/・docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（PAD-06 と同じセッションに続けて貼ってよい）
作業ブランチ: クラウドセッションで実行する。work/1017-pad-doc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-pad-doc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-pad-doc の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。

## 目的

2026-10-17 の PAD のチャットの振り返り（Fable 5.1 のレビュー済み）で、往復が増えた原因のうち、手順に落とせる3つをチャット側の文書に足す。

### 決定（2026-10-17、平野さん）

1. 振り返りの提案 B・C・D を、Fable 5.1 のレビューの推奨どおり進める（下の表の3行）
2. 予定を【済】にするとき、その予定に通知（リマインダー）が残っていればオフにする（2026-10-17、平野さんの指定）

### 前提（チャット側。平野さんの決定ではない）

足す規則と置き場所（知見 → 選んだ層 → 理由）:

| 知見（2026-10-17） | 選んだ層 | 置き場所 | 理由 |
| --- | --- | --- | --- |
| B: iPhone の実機確認を「写真カードのスクショ」とだけ頼んだら、上の見出しの部分が撮られて1往復増えた | ガイド文 | `docs/notes/design.md`「7. チャット側の決め方」に1行: 実機確認を頼むときは、写すべきもの（どの要素が画面に入っているか）・スクロールの位置・文字の大きさの段階をページごとに書く | 平野さんの手作業への頼み方で、テスト・hook・ワークフローでは確かめられない。受け手（Code）の手順でもない |
| C: 起票の指示に「無いラベルは作らず近いものにする」と書いたら、別のページを指すラベル（`対象: video_live`）が付き、直すのに2往復かかった | ガイド文 | `docs/notes/chat-routines.md`「指示の種類ごとの確かめ」の起票の項に1行: 起票の指示でラベルを案として書くときは、無ければ「付けずに報告に書く（作るかは判断待ち）」とし、近いもので代えさせない | 指示文の書き方の規則。`docs/instruction-template.md` は 14,274 バイトで警告域（14KB = 14,336 バイト）の直前のため避けた（要確認: 作業する版で測り直す） |
| D: 【済】にした予定に通知が残っていた | ガイド文 | `docs/notes/chat-routines.md`「期日とカレンダー」の「予定が済んだら…【済】…グラファイト」の行に足す: 通知（リマインダー）が残っていればオフにする | カレンダーはチャット側だけが触るので、受け手側の手順やワークフローでは確かめられない |

- 振り返りで挙げた「待つ確認を平野さんに頼まず予約（send_later）にする」は、`docs/notes/chat-routines.md`「期日とカレンダー」に既にある（チャット側が読み落とした）。足さない
- 振り返りの提案 A（本番の確かめが要るマージの push の順番）は、ルールにする前に別の指示（CHAT-1017-PAD-08）で原因を切り分ける。この指示では触らない
- 規則と理由の一句だけを書き、事例（PAD-0x の経緯）は書かない。事例を残すなら `docs/notes/handover-archive-2026.md` へ
- 決定の書き先は `docs/decisions/operations.md`（要確認: チャット側の運用の決定が別のファイルにあればそちら）
- 使う skill: writing-for-agents（ガイド文を書くため）

## 手順

1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。語の例: 「実機確認」「スクショ」「ラベル」「通知」「リマインダー」）、あれば止まって報告する。追記先の3か所の今の内容を読み、同じ趣旨の記述があれば置き換え・拡張する（処理を報告に書く）
2. 上の表のとおり3か所に足す。`docs/instruction-template.md` の大きさを測ってログに書く（変えない）
3. 決定 1・2 を `docs/decisions/operations.md` に足す

## 止まる条件

- 同じ論点の open issue がある
- 追記先の今の記述と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述があるだけなら止めず、置き換え・拡張してよい）
- 未マージの work/ ブランチが追記先の同じ節を変えている、または取り込みで衝突する（両方の変更が両立する衝突〈追記どうし・隣り合う行〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（変わるのは docs/notes/design.md・docs/notes/chat-routines.md・docs/decisions/・docs/logs/ だけの見込み）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-PAD-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-PAD-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref（`CHAT-1017-PAD-07`）のコミット: 0件。識別子 PAD はこのセッションの PAD-01 で確認済み
- 作業ブランチ: ローカル・リモートとも `work/1017-pad-doc` が無いため `git checkout -b work/1017-pad-doc origin/cloudflare`（42c384b5d）
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順の4つとも有る
- 0. 「指示」欄の末尾は指示文の最後の行と一致
- ガイド文に規則を足す指示で、「知見 → 選んだ層 → 理由」の表がある（CLAUDE.md「Chat-Ref」の止まる条件に当たらない）

## 報告

- 状態: 作業中
- ブランチ: work/1017-pad-doc
- ログ: https://github.com/retroeater/mj/blob/work/1017-pad-doc/docs/logs/CHAT-1017-PAD-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-pad-doc
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 95555949）: https://github.com/retroeater/mj-logs/tree/main/guide/95555949

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/95555949/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/95555949/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/95555949/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/95555949/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/95555949/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/95555949/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/42c384b5.md
