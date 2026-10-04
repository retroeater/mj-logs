# CHAT-1002-CLD-09

- 着手日時: 2026-10-04
- 対象issue: なし（#448 の関連）
- ブランチ: work/1002-cld
- 着手時HEAD: b03045fc

## 指示

【Claude作成】Claude Code 向け指示：CLD のチャットの振り返りの申送り（雛形の読み直しと必須の行の検査、行の指し方）を文書に書き残す Chat-Ref: CHAT-1002-CLD-09 マージ: 判断待ちで止まる（足した文面をチャット側が読み比べてから、マージの指示を出す） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1002-cld の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1002-cld を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1002-cld origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CLD のチャット（2026-10-02〜10-04、CHAT-1002-CLD-01〜08、#448 の関連）の振り返りで平野さんが採った規則を、チャット側と受け手側の文書に書き残す（申送り）。規則だけを書き、事例は archive へ置く。
決定（2026-10-04、平野さん）

* チャット側の提案「指示文を作る前に、読んだログの末尾のガイドの版が前回と違えば雛形を読み直す。受け手側で『貼る時機:』などの必須の行が無い指示を報告させる検査にもできる」に「OK」
* チャット側の提案「値の食い違いを調べさせるときは、その値がどの層（【2】か【3】）から来たかを行ごとに書かせる」への返答:「行を指定するときは動画ID（または行番号）で一意に特定できるようにしてください」
* 動画 `jt4E_u--mxg`（2026-04-17 第6期鸞和戦、D卓4回戦南場の27分の動画）の予定は、「【4】カレンダー非掲載」に入れて、全編（動画 `R3IZ244ANxQ`）だけにする
* 「申送り」（上の規則を文書に書き残す）

前提（チャット側。平野さんの決定ではない）

* 2つ目の決定は、チャット側の提案（層を行ごとに書かせる）に、平野さんが「動画ID（または行番号）で一意に」を加えたものと読んでいる。両方を1つの規則として書く
* 書き場所と文面の案（実物を読んで、既存の項目への統合・置き換えで書く。場所は変えてよい）:
   * (a) docs/notes/chat-side-operations.md「作業ログの読み方」の「ガイド文書は、ログを読んだその場で必要なものを読む」の項に統合: 指示文を作る前に、読んだログの末尾のガイドの版（`guide/<SHA>`）が前回読んだ版と違えば docs/instruction-template.md を読み直す
   * (b) CLAUDE.md の受け手側の規則（「Chat-Ref」節か「作業ログ」節。0章の末尾の確認と同じ場面）: 着手時に、指示文の冒頭に「マージ:」「貼る時機:」「共通手順:」の行があるかを確かめ、無い行があれば、止まらずに経過と報告の「判断が必要なこと」にその行の名前を書く。必須の行の正は docs/instruction-template.md の雛形
   * (c) docs/notes/chat-side-operations.md「止まる条件と検証の指定」か「書く前に実物で確かめる」: シートの行・カレンダーの予定を指すときは、指示文でも平野さんへの報告でも、動画ID（または行番号）で一意に特定する。値の食い違いを調べさせる指示では、その値がどの層（【2】か【3】）から来たかを行ごとに書かせる
* 事例（docs/notes/handover-archive-2026.md「docs/notes/chat-side-operations.md から」へ）: CHAT-1002-CLD-07・CLD-08 の指示文に「貼る時機:」の行が無かった（チャット側が、ガイドの版が進んだ後に雛形を読み直していなかった）／CHAT-1002-CLD-04 の表が、足される先の値が【2】由来か【3】由来かを行ごとに区別しておらず、チャット側が「7件が4名に減る」と誤って伝え、CHAT-1002-CLD-05 が手順1で止まった（実際は6件が【2】由来で変わらなかった）
* `jt4E_u--mxg` を【4】に入れるのは平野さんの手作業（この指示の実行時に済んでいるかは分からない）。Code はシートを書き換えない。決定の記録だけを docs/decisions/broadcast-calendar.md に足す
* 受け手側の検査 (b) を入れた後、古い指示文（「貼る時機:」の無い CLD-07 以前の形）が貼られても止まらない

手順

1. 同じ論点の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する。検索語は「雛形」「instruction-template」「貼る時機」「必須の行」「動画ID」「行番号」など、変える対象そのものの語を入れる。
2. 追記先（docs/notes/chat-side-operations.md・CLAUDE.md・docs/instruction-template.md・docs/notes/handover-archive-2026.md・docs/decisions/operations.md・docs/decisions/broadcast-calendar.md）の今の内容を読み、上限のある文書は今のバイト数と上限（`assets-check.yml` の警告・失敗の値）を測って書く。前提の (a)(b)(c) を、既存の項目への統合・置き換えで、規則と理由の一句だけで書く（writing-for-agents の skill を使う）。事例は archive へ、決定は docs/decisions/ の分野のファイルへ足す。同じ趣旨の記述があれば置き換え・拡張し、どう処理したかを報告に書く。
3. 足した・変えた文面を、文書ごとに変更前後が分かる形（差分）でログに貼り、変更後のバイト数を書いて、判断待ちで止まる。

止まる条件

* 手順1で、同じ論点の issue がある
* 追記先の記述と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述があるだけなら止めず、置き換え・拡張する）
* 足すと、上限のある文書が警告の値を超える（超えない書き方が無ければ、整理の案を書いて止まる。上限は上げない）
* docs/・CLAUDE.md 以外のファイル（ワークフロー・スクリプト・シート）を変える必要が出た
* ブランチの作成や push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」）。報告に、issue の検索の結果、文書ごとの変更前後の文面とバイト数（上限つき）、既存の記述をどう統合・置き換えたか、決定を足したファイルを入れる
* マージは冒頭の「マージ:」の行のとおり（しない）。作業ブランチは片付けずに残す
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1002-CLD-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1002-CLD-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: 全ブランチのコミットに `CHAT-1002-CLD-09` は無し
- 作業ブランチ: `origin/work/1002-cld`（d592a73f）は cloudflare へマージ済み。ローカルも cloudflare の祖先なので `git merge --ff-only origin/cloudflare` で b03045fc へ進めた

- 「指示」欄の末尾は指示文の最後の行と一致
- 指示文の冒頭の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある

### 1. issue の検索

GitHub MCP の `search_issues`（retroeater/mj、Open・Closed とも）で3回検索した:

- 「指示文の雛形 instruction-template 貼る時機 必須の行 検査」: 0件
- 「行を動画ID 行番号で一意に特定 【2】【3】どの層から来たか」: #399・#398（Open、/live の個別の書き方）、#482・#450・#446（Closed）。どれも別の論点
- 「チャット側 雛形を読み直す ガイドの版 指示文の必須の行が無い」: #294「チャット側の指示文テンプレートを版管理下に置く」（Closed）。テンプレートを docs/instruction-template.md に置いた issue で、読み直しや行の検査は扱っていない

同じ論点の issue は無かった。

### 2. 追記先とサイズ

上限は `assets-check.yml` の「ガイド文書のサイズを確認」（1KB=1024バイト）。

| 文書 | 変更前 | 変更後 | 警告域 | 上限 |
|---|---|---|---|---|
| CLAUDE.md | 27,227 | 27,630 | 30,720 | 32,768 |
| docs/notes/chat-side-operations.md | 24,476 | 25,116 | 26,624 | 28,672 |
| docs/handover.md | 23,212 | 23,212（変更なし） | 26,624 | 28,672 |
| docs/instruction-template.md | 12,720 | 12,720（変更なし） | 上限なし | |
| docs/notes/handover-archive-2026.md | 69,188 | 増える（上限なし） | | |
| docs/decisions/operations.md・broadcast-calendar.md | | 増える（上限なし） | | |

writing-for-agents の skill を読み、規則と理由の一句だけを、既存の項目に統合する形で書いた（新しい節は作らない）。事例は archive へ。

- (a) docs/notes/chat-side-operations.md「作業ログの読み方」の「ガイド文書は、ログを読んだその場で必要なものを読む」の項に、同じ項の2文目として統合した
- (b) CLAUDE.md「Chat-Ref」節の受け手側の規則に1項目足した（「指示文の記述同士が食い違ったときは」の次。0章の確認と同じ着手時の場面）。必須の行の正は雛形と書き、行の名前は雛形の冒頭の4行をそのまま挙げた。止まらずに報告する（古い形の指示文が貼られても止まらない）
- (c) docs/notes/chat-side-operations.md「書く前に実物で確かめる」の、近い趣旨の項（平野さんに判断を求める前提は先に照合させる）の直後に1項目足した。前提の2つ（行を一意に指す・層を行ごとに書かせる）を1項目にまとめた。「止まる条件と検証の指定」より、層の取り違えの事例（B卓の4名）がある項の隣のほうが読み合わせやすいため
- docs/instruction-template.md は変えていない。必須の行（マージ・貼る時機・共通手順）は雛形にすでにあり、(b) はそれを正として参照する
- CLAUDE.md・chat-side-operations.md には出典としての Chat-Ref を書かない規則（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」）に従い、事例の Chat-Ref は archive にだけ書いた
- 矛盾する既存の記述は無かった。同じ趣旨の記述も無く、置き換えたものは無い

### 3. 変更の差分

#### CLAUDE.md

```diff
diff --git a/CLAUDE.md b/CLAUDE.md
index a2fe18c5..769e8c1b 100644
--- a/CLAUDE.md
+++ b/CLAUDE.md
@@ -118,2 +118,4 @@ This file provides guidance to Claude Code (claude.ai/code) when working with co
 - **指示文の記述同士が食い違ったときは、個別の指定より前提・ルールの側を優先し、その旨を報告すること**
+- **着手時に、指示文の冒頭に docs/instruction-template.md の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）が揃っているかを確かめ、
+  無い行があれば止まらずに、その行の名前をログの経過と `## 報告` の「判断が必要なこと」に書く**（チャット側が雛形の改訂を写し漏らしたことを知らせるため）
 - **実行しない判断をした指示も、その旨を平野さんに伝える**（黙って落とすと「貼り忘れ」と区別がつかない）
```

#### docs/notes/chat-side-operations.md

```diff
diff --git a/docs/notes/chat-side-operations.md b/docs/notes/chat-side-operations.md
index 85622ae7..d8904cc8 100644
--- a/docs/notes/chat-side-operations.md
+++ b/docs/notes/chat-side-operations.md
@@ -43,3 +43,4 @@ Claude Code 側で確認できる範囲は `docs/notes/cloudflare.md`「ビル
 - **申告をそのまま信じない。** ログ・ブランチ・issue に実行の痕跡があるかを先に確かめる
-- **ガイド文書は、ログを読んだその場で必要なもの（マージを伴う指示なら CLAUDE.md「ブランチ運用」）を読む。** 後の手番では同じ URL を開けなくなることがある
+- **ガイド文書は、ログを読んだその場で必要なもの（マージを伴う指示なら CLAUDE.md「ブランチ運用」）を読む。** 後の手番では同じ URL を開けなくなることがある。
+  **指示文を作る前に、読んだログの末尾のガイドの版（`guide/<SHA>`）が前に読んだ版と違えば `docs/instruction-template.md` を読み直す**（雛形に足された必須の行を写し漏らさないため）
 - **ブランチ名は最終報告の「ブランチ:」の行か比較 URL から取る。** 推測して URL を組み立てない（指示文に書くときも同じ）。
@@ -99,2 +100,3 @@ Claude Code 側で確認できる範囲は `docs/notes/cloudflare.md`「ビル
 - **平野さんに判断を求める前提（「A を直せば B も不要になる」等）は、先に Claude Code に照合させるか、「未確認」と明記して問う。** 誤った前提で判断をもらうと決定が誤る（【3】の補正が【2】と同じになるという前提が誤りで、B卓の4名を落としかけた）
+- **シートの行・カレンダーの予定は、指示文でも平野さんへの報告でも、動画ID（または行番号）で一意に指す。値の食い違いを調べさせる指示では、その値がどの層（【2】か【3】）から来たかを行ごとに書かせる**（層を分けない表から「7件とも名前が減る」と誤って伝えたことがある）
 - **シートの値を変えるよう平野さんに頼む前に、今の値をログか Claude Code で確かめる**（#476: 【3】の完全版5本の掲載を Y にするよう頼んだが、元から Y だった）
```

#### docs/notes/handover-archive-2026.md（事例）

```diff
diff --git a/docs/notes/handover-archive-2026.md b/docs/notes/handover-archive-2026.md
index 2d970b46..9f7895bc 100644
--- a/docs/notes/handover-archive-2026.md
+++ b/docs/notes/handover-archive-2026.md
@@ -634,2 +634,5 @@ Builds が既に稼働しており不要かつ二重デプロイになるもの
   実際には Workers Builds の check-run が1回発生した（表示に影響する変更は無く実害なし。2026-09-22、CHAT-0922-MD の2回目の振り返り）
+- **行は動画ID（または行番号）で指し、値は層を行ごとに分けて書かせる**: CHAT-1002-CLD-04 の表は、説明欄に名前が足される先の値が【2】由来か
+  【3】由来かを行ごとに分けていなかった。チャット側がそれを読んで「7件が4名に減る」と平野さんに伝え、平野さんの判断（【3】を空欄にする）をもとに
+  CHAT-1002-CLD-05 を出したが、実際は6件が【2】由来で変わらず、残る1件（鸞和戦 jt4E_u--mxg）が条件に当たらず手順1で止まった（2026-10-03、CLD の振り返り）
 
@@ -672,2 +675,4 @@ Builds が既に稼働しており不要かつ二重デプロイになるもの
   （2026-09-22、CHAT-0922-MD の振り返り）
+- **ガイドの版が進んだら雛形を読み直す**: CHAT-1002-CLD-07・CLD-08 の指示文に「貼る時機:」の行が無かった。雛形に行が足された後も、
+  チャット側が雛形を読み直さずに前の指示文を写していた。受け手側は行の有無を確かめておらず、そのまま進んだ（2026-10-04、CLD の振り返り）
 
```

#### docs/decisions/operations.md

```diff
diff --git a/docs/decisions/operations.md b/docs/decisions/operations.md
index 2a0ca04e..a57525d4 100644
--- a/docs/decisions/operations.md
+++ b/docs/decisions/operations.md
@@ -111 +111,7 @@
 - 作業ブランチの実行で mj-logs にファイルが出たことを確かめてからマージしてよい
+
+## 2026-10-04（CHAT-1002-CLD-09、#448 の関連）
+
+- チャット側は、指示文を作る前に、読んだログの末尾のガイドの版が前回と違えば雛形（docs/instruction-template.md）を読み直す。受け手側は、雛形の行（Chat-Ref・マージ・貼る時機・共通手順）が無い指示を、止まらずに報告する
+- シートの行・カレンダーの予定を指すときは、動画ID（または行番号）で一意に特定できるようにする。値の食い違いを調べさせるときは、その値がどの層（【2】か【3】）から来たかを行ごとに書かせる
+- CLD のチャットの振り返りの規則を文書に書き残す（申送り）
```

#### docs/decisions/broadcast-calendar.md

```diff
diff --git a/docs/decisions/broadcast-calendar.md b/docs/decisions/broadcast-calendar.md
index b6cd1382..61abf41d 100644
--- a/docs/decisions/broadcast-calendar.md
+++ b/docs/decisions/broadcast-calendar.md
@@ -141 +141,5 @@
 - `tMwcjumwz-o` の予定の件名が空である件は、#491 に記録する（直さない）
+
+## 2026-10-04（CHAT-1002-CLD-09、#448）
+
+- 動画 jt4E_u--mxg（2026-04-17 第6期鸞和戦、D卓4回戦南場の27分）は「【4】カレンダー非掲載」に入れ、予定は全編（R3IZ244ANxQ）だけにする（【4】に入れるのは平野さん）
```

- jt4E_u--mxg を【4】に入れたかは確かめていない（平野さんの手作業。この指示はシートを読む手順を持たない）

## 報告

- 状態: 判断待ち
- ブランチ: work/1002-cld
- ログ: https://github.com/retroeater/mj/blob/work/1002-cld/docs/logs/CHAT-1002-CLD-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1002-cld
- 確認用URL: なし
- マージ: 未（足した文面をチャット側が読み比べてからマージの指示が出る）
- issue: なし（検索で同じ論点の issue は無かった。近いのは #294〈Closed、テンプレートを版管理下に置く〉）
- 判断が必要なこと:
  - 足した文面の読み比べ（差分は「経過」の「3. 変更の差分」）
    - (a) docs/notes/chat-side-operations.md「作業ログの読み方」の「ガイド文書は…読む」の項に統合: 指示文を作る前に、ガイドの版（`guide/<SHA>`）が前と違えば雛形を読み直す
    - (b) CLAUDE.md「Chat-Ref」節: 着手時に雛形の行（Chat-Ref・マージ・貼る時機・共通手順）が揃っているかを確かめ、無ければ止まらずに経過と「判断が必要なこと」に書く
    - (c) docs/notes/chat-side-operations.md「書く前に実物で確かめる」: 行・予定は動画ID（または行番号）で一意に指し、食い違いの調査では値の層（【2】か【3】）を行ごとに書かせる
  - 事例は docs/notes/handover-archive-2026.md の「docs/notes/chat-side-operations.md から」の「書く前に実物で確かめる」「指示文の書き方・渡し方」へ、決定は docs/decisions/operations.md（規則）と docs/decisions/broadcast-calendar.md（jt4E_u--mxg を【4】へ）に足した
  - サイズ: CLAUDE.md 27,630 バイト（警告域 30,720）、chat-side-operations.md 25,116 バイト（警告域 26,624）。どちらも警告域の手前
- 未確認の項目:
  - jt4E_u--mxg が「【4】カレンダー非掲載」に入ったか（平野さんの手作業。シートは読んでいない）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 14b02792）: https://github.com/retroeater/mj-logs/tree/main/guide/14b02792

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/14b02792/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/14b02792/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/14b02792/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/14b02792/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/14b02792/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/14b02792/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a8cd42e0.md
