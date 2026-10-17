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

### 1. 確かめたこと

- 同じ論点の issue: REST の `repos/retroeater/mj/issues?state=all` で全592件（#1〜#593）を取り、題名と open の本文を語で探した（「実機確認」「スクショ」「ラベル」「通知」「リマインダー」）。題名で当たったのは #525（閉、第3弾の実機確認）・#214・#180（閉、ページのラベルの文言）・#503・#432・#431・#395・#118（通知の仕組み）で、どれもチャット側の頼み方・起票の書き方・カレンダーの通知の論点ではない。open の本文の当たり（実機確認: #589・#529・#528・#527・#235、スクショ: #589・#555・#262 など）も別の論点。同じ論点の open issue は無い
- 追記先の今の内容:
  - `docs/notes/design.md`「7. チャット側の決め方」: 実機確認の頼み方の記述は無い（見た目の決め方・比較ページ・プレビューの見方など）。新しい行として足した
  - `docs/notes/chat-routines.md`「指示の種類ごとの確かめ」の「起票・方針の提案…」の項: ラベルの記述は無い。同じ項の続きの行として足した
  - 同「期日とカレンダー」の【済】の行: 通知の記述は無い。同じ行を拡張した
  - 「待つ確認を予約（send_later）にする」は同「期日とカレンダー」に既にある（前提のとおり）。足していない
  - 矛盾する記述は無い
- 未マージの work/ ブランチ: `origin/work/OUK` が `docs/notes/design.md` の 76 行目・103 行目付近（「3. 文字・寸法」「4. 部品」の表の行）を変えている。追記した「7. チャット側の決め方」とは別の節で、重ならない。ほかのブランチは3つのファイルを変えていない
- `docs/instruction-template.md` の大きさ（作業した版、42c384b5d）: **14,274 バイト**（警告域 14,336 バイトまで 62 バイト。変えていない）。参考: CLAUDE.md 23,801・handover.md 23,289・chat-side-operations.md 21,932 バイト（どれも変えていない）

### 2. 足したもの

- `docs/notes/design.md`「7. チャット側の決め方」:

  > - **平野さんに実機確認（スクショ）を頼むときは、ページごとに、画面に入れる要素・スクロールの位置・文字の大きさの段階を書く**（「〇〇のスクショ」だけでは別の部分が写り、撮り直しになる）

- `docs/notes/chat-routines.md`「指示の種類ごとの確かめ」の起票の項の続き:

  > 起票の指示でラベルを案として書くときは、案のラベルが無ければ「付けずに報告に書く（作るかは判断待ち）」とする（近いもので代えさせると、別のページを指すラベルが付く）

- 同「期日とカレンダー」の【済】の行（拡張）:

  > - **予定が済んだら・issue のクローズを知ったら、消さずに件名の冒頭に【済】を付けてグラファイトにし、通知（リマインダー）が残っていればオフにする。** 説明欄の残課題は行き先の予定へ移す

- 書き方: writing-for-agents の skill に沿い、規則（肯定形の行動）と理由の一句だけにした。事例（PAD-0x の経緯）は書いていない
- 決定 1・2 を `docs/decisions/operations.md` に足した（README の分野の一覧で「運用（チャット側と Claude Code のやり取り・指示文の書き方）」の分野）

### 3. テスト

- `python3 -m unittest discover -s scripts/tests` を4回回した: 1回目だけ `FAILED (errors=1)`、2〜4回目は 1350 件 OK。1回目の出力を残しておらず、どのテストかは分からない（変えたのは docs の3ファイルだけで、繰り返しで再現しないため、この変更によるものではないと見ている。推測）

## 報告

- 状態: 完了
- ブランチ: work/1017-pad-doc
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1017-PAD-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-pad-doc
- 確認用URL: なし
- マージ: 済（fast-forward、cloudflare 80f33a566）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 9dcbfcac）: https://github.com/retroeater/mj-logs/tree/main/guide/9dcbfcac

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/9dcbfcac/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/9dcbfcac/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/9dcbfcac/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/9dcbfcac/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/9dcbfcac/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/9dcbfcac/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/42c384b5.md
