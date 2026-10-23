# CHAT-1023-WKP-04

- 着手日時: 2026-10-23
- 対象issue: なし
- ブランチ: work/1023-wkp
- 着手時HEAD: 8255bdad8

## 指示

【Claude作成】Claude Code 向け指示：WKP 系列の振り返りの申送り（指示文の issue の state の確認、常設 issue の担当、値の洗い直しの grep の範囲）
Chat-Ref: CHAT-1023-WKP-04
マージ: ドキュメントのみ（docs/notes・docs/decisions・docs/logs・CLAUDE.md・docs/instruction-template.md）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（WKP の同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1023-wkp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1023-wkp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1023-wkp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
WKP 系列（CHAT-1021-WKP-01〜03、Workers Paid への切り替えと段④の前半）の振り返り・Fable 5.1 のレビュー（2026-10-23）の申送り。機械的に確かめられる手順に落とせる 3 点だけを書き残す。

### 決定（2026-10-23、平野さん）
- 申送りを行う（平野さんが「申送り」と言った。中身はチャット側の案）

### 前提（チャット側。平野さんの決定ではない）
振り返りで分かったこと（ログ・API で確かめた事実）:
- WKP-01 の指示文は閉じた #363（2026-09-29 にクローズ）を開いている前提で書かれ（「#363 は閉じない」「#363 にコメントする」）、Code は閉じた issue に着手・結果のコメントを書いた。#304 の本文 (13) にも「#363 にコメントする」が 2 か所入っている（#304 の担当は MCK 系列なので、この指示では直さない。チャット側が MCK に伝言する）
- WKP-01 は #304（常設、担当は MCK 系列）に項目を直接コメントさせ、同日の BIL 系列の追加と重なった（docs/decisions/operations.md の BIL-04・MCK-21・BIL-05）
- WKP-01 の前提の「Free の値が残る箇所」の grep の範囲が docs と workers だけで、`scripts/check_asset_limits.py` が漏れ、判断待ちが1回増えた（WKP-03 で処理）

知見ごとの層（#492）:

| 知見 | 選んだ層 | 理由 |
| --- | --- | --- |
| 指示文に書かれた issue が閉じていることがある（チャット側が会話の記憶から書く） | 推論的センサー: Code の着手前の確認（docs/notes/branch-operations.md「Chat-Ref の着手前の確認」に節を足し、CLAUDE.md「Chat-Ref」の着手前の確認の行に1語足す） | issue の state は着手時に API で確かめられる。テスト・hook では指示文の中身を読めないので計算的センサーにできない |
| 常設 issue（#304・#506・#568 など）への項目の追加は、担当の系列でないチャットから直接入れると重なる | ガイド文（docs/notes/chat-side-operations.md） | 担当はチャットの分け方で決まり、リポジトリの中に機械で照合できる情報が無い。チャット側が指示文を作る前に読む規則 |
| 同じ値（上限・旧プランの数など）の洗い直しを指示するとき、grep の範囲を狭めると漏れる | ガイド文（docs/instruction-template.md） | 指示文の書き方の規則。受け手は指示の範囲で grep するので、範囲は書く側で決まる |

書く文面の案（実物に合わせて短くしてよい。規則と理由の一句だけ。事例は書かない）:
- branch-operations.md「Chat-Ref の着手前の確認」に節「指示文の issue の state」: 「指示文が着手・コメント・クローズの対象にする issue は、着手前に state を確かめる（`issue_read` など）。閉じていれば着手せず、その issue 番号を報告する（チャット側は会話の記憶から issue を書くため、#319 と同じ型）。経緯の参照として挙げただけの issue は対象外」
- CLAUDE.md「Chat-Ref」の「着手前の確認」の行: 「…の3点」を「…・指示文の issue の state の4点」にする（細目は branch-operations.md のまま）
- chat-side-operations.md（指示文の書き方の節の近く）: 「常設 issue（#304・#506・#568 など）に項目やコメントを足す指示は、その issue の担当の系列のチャットで作る。担当が別なら伝言（.md）で頼む。担当が分からなければ平野さんに聞く」
- instruction-template.md: 「同じ値（上限・旧プランの数など）の洗い直しを指示するときは、範囲を `git grep -n '<値>'`（リポジトリ全体）にし、除外（docs/decisions・docs/logs・handover-archive の過去の行）だけを書く。範囲を docs や workers に絞らない」
- 足す行は、docs/notes/handover-archive-2026.md の「外した記録」に前に意図して外した同じ趣旨の行が無いかを先に確かめる
- サイズ（2026-10-23 の cloudflare 8255bda、チャット側の計測。作業ブランチの上で測り直す）: CLAUDE.md 22,913 バイト・chat-side-operations.md 21,980・instruction-template.md 11,978。警告域は 26KB（26,624）・14KB（14,336）。足すのは各1〜2行の見込み
- 決定の書き場所: docs/decisions/operations.md（この指示の「決定」と、上の3点を行ったこと）

## 手順
1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。語の例: 「issue の state」「閉じた issue」「常設 issue 担当」「grep 範囲」）、あれば止まって報告する。未マージの work/ ブランチに、上の4文書の同じ節を変える作業が無いことを確かめる
2. 上の3点を書く。追記先の今の内容を読み、同じ趣旨の記述があれば置き換え・拡張する（処理を報告に書く）。作業ブランチの上で4文書のサイズを測り、ログに前後を書く
3. ドキュメントのみなので完了報告のうえ cloudflare へ入れる。決定を docs/decisions/operations.md に足す

## 止まる条件
- 同じ論点の open issue がある
- 追記先の記述と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述があるだけなら止めず、置き換え・拡張してよい）
- 変更後に CLAUDE.md・chat-side-operations.md・instruction-template.md のどれかが警告域を超える
- 未マージの work/ ブランチが、上の文書の同じ節を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-WKP-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-WKP-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 指示欄の末尾: ログの「## 指示」の最後の行が指示文の最後の行と一致することを確かめた
- 同じ Chat-Ref のコミット: 0 件。`WKP` は同じセッションの続き。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行ともあり
- 作業ブランチ: `work/1023-wkp` はローカル・リモートとも無かったので `git checkout -b work/1023-wkp origin/cloudflare`（8255bdad8）
- 指示文の issue の state（今回足す規則を今回にも当てた）: 着手・コメント・クローズの対象にする issue は無い（#363・#304・#506・#568 は経緯と例示の参照だけ）
- 同じ論点の issue: `search_issues` で「指示文の issue が閉じている 着手前に state を確かめる」「常設 issue 担当 系列 チャット 項目の追加が重なる」「grep 範囲 洗い直し 漏れ 指示文」「closed issue referenced in chat instruction」を引いた（open・closed とも）。当たったのは #496（カレンダーの予定を済みにする）・#304（月次チェック）・#310（常設 issue の探索の破綻）で、どれも別の論点
- 未マージの work/ ブランチ: 4文書と docs/decisions/operations.md を変えているのは `work/1018-log-367-migrate` の docs/decisions/operations.md だけ（末尾への追記。今回も末尾に足すので、取り込みで衝突すれば両方を残して解ける形）。4文書の同じ節を変える作業は無い
- handover-archive-2026.md: 同じ趣旨の行を前に意図して外した記録は無い（「外した」の行のうち issue・状態・常設・担当・grep・範囲に当たる 2 行は、例示の削除とラベルの理由で別の趣旨）

### 追記先の今の内容と処理

- CLAUDE.md「Chat-Ref」の着手前の確認の行: 「…の3点」を「…／指示文の issue の state の4点」にした（細目は branch-operations.md のまま）
- docs/notes/branch-operations.md「Chat-Ref の着手前の確認」: 同じ趣旨の記述は無かった。節「指示文の issue の state」を「作業を再開するとき」の前に足した（文面は案のとおり。「止まる」を足した）
- docs/notes/chat-side-operations.md: 「書く前に実物で確かめる」に「issue の状態（Open/Closed・本文・コメント）を確かめられないまま、その issue の作業の指示を書かない」が既にある（チャット側の規則）。今回の Code 側の確認はその受け手側の対で、矛盾しない。常設 issue の担当の規則は同じ趣旨の記述が無く、「指示文の書き方・渡し方」の伝言（.md）の行の次に1行足した
- docs/instruction-template.md: 同じ趣旨の記述は無かった。「既存の文書への追記を含む指示」の項の後に1行足した

### サイズ（作業ブランチの上、バイト）

| 文書 | 前 | 後 | 警告域 |
|---|---:|---:|---:|
| CLAUDE.md | 22,913 | 22,945 | 26,624 |
| docs/notes/branch-operations.md | 23,359 | 23,781 | （上限なし） |
| docs/notes/chat-side-operations.md | 21,980 | 22,290 | 26,624（純増の警告は 24,576 超） |
| docs/instruction-template.md | 11,978 | 12,352 | 14,336 |

`python3 -m unittest discover -s scripts/tests` 成功。成果物のコミットは b61a18e7e。決定は docs/decisions/operations.md に「2026-10-23（CHAT-1023-WKP-04）」を足した。

## 報告

- 状態: 完了
- ブランチ: work/1023-wkp
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1023-WKP-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1023-wkp
- 確認用URL: なし
- マージ: 済（ドキュメントのみ。SHA は cloudflare の先頭〈このログを含むコミット〉）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 67a3af2b）: https://github.com/retroeater/mj-logs/tree/main/guide/67a3af2b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/67a3af2b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/67a3af2b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/67a3af2b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/67a3af2b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/67a3af2b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/67a3af2b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/67a3af2b.md
