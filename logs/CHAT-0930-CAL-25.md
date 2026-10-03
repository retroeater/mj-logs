# CHAT-0930-CAL-25

- 着手日時: 2026-10-03 12:33（JST）
- 対象issue: なし
- ブランチ: work/1003-cal-mos
- 着手時HEAD: 16b2dff5（origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：CAL のチャットの振り返りから、仕組みに落とせる規則を指示文の雛形・チャット側の手順書・handover に書き足す（マージはしない） Chat-Ref: CHAT-0930-CAL-25 貼る時機: いつでも（CAL-24 と並行で可。触るのは文書だけ） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチの作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1003-cal-mos を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済みなら origin/cloudflare から作る（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる マージ: 未承認（平野さんが差分を読み比べて決める。判断待ちで止まる）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-0930-CAL のチャット（9/30〜10/3、#448 系）の振り返りのうち、受け手かチャット側が機械的に確かめられる形にできるものだけを、規則として書き残す。抽象的な心得は書かない。事例は1行の参照（Chat-Ref）にとどめる。
決定（2026-10-03、平野さん）

* 振り返りの申送りとして、下の規則を書き残す。マージは差分を読み比べてから決める。

前提（チャット側の案。平野さんの決定ではない。規則の文面は追記先の書きぶりに合わせてよい）
書き残す規則（4つ）と、直す古い記述（1つ）:

1. 指示文の「貼る時機」行（docs/instruction-template.md）: 共通手順の行の手前に「貼る時機: …」の行を置く。ほかの指示の完了・毎朝の実行の後など、前提がある指示はそれを書き、手順0で前提が満たされているかを確かめさせる（満たされなければ何もせず止まる）。前提が無ければ「いつでも」。事例: CAL-19 が CAL-09 より先に貼られ、番号を1つ使った。
2. 見込みとの許すずれを数で書く（docs/instruction-template.md）: 見込みと比べて止まる条件は「大きく違えば」と書かず、許すずれを数で書く（例: 「直すは見込み ±3件まで、作る・消すは見込みと同じ」）。事例: CAL-15 で直すが見込み1件に対して2件となり、受け手の判断で進んだ。
3. 同じチャットから並行で出す指示はブランチを分ける（docs/notes/chat-side-operations.md）: 同じチャットから、前の指示の完了を待たずに次の指示を出すときは、作業ブランチを `work/<MMDD>-<識別子>-<短い名前>` のように分ける。1つのセッションには1つの指示だけを貼るよう平野さんに伝える。事例: CAL-04 と CAL-05 が同じ work/0930-cal を使いかけた。CAL-11 と CAL-12 が同じセッションで動き、ブランチの切り替えで止まった。
4. 後の実行の結果に頼る指示は、結果が出てから作る（docs/notes/chat-side-operations.md）: 毎朝の実行や別の指示の結果を見込みとして使う指示（確認の指示など）は、その結果が出てから作る。前もって作るときは「貼る時機」に前提を書き、見込みは「<Chat-Ref> のログの `### 手順1: 確かめ

- 同じ文書を触る未マージのブランチ: 無い（`git branch -r --no-merged origin/cloudflare` の各ブランチの分岐点からの差分に3文書が無い）
- 大きさ（`wc -c`）と上限（`assets-check.yml`）:

  | 文書 | 前 | 後 | 警告域 | 上限 | 残り（後） |
  |---|---:|---:|---:|---:|---:|
  | docs/instruction-template.md | 11,146 | 12,253 | なし | なし（検査の対象外） | — |
  | docs/notes/chat-side-operations.md | 22,793 | 23,585 | 26,624 | 28,672 | 5,087（約18%） |
  | docs/handover.md | 23,009 | 22,988 | 26,624 | 28,672 | 5,684（約20%） |

  - どれも上限を超えず、残りも1割を切らない
- 既存の記述との重なり:
  - 規則1（貼る時機）:
    - 雛形の0章の注記「続きの指示で前の指示の結果が要るときだけ、0章に『<Chat-Ref> のログの `## 報告` を読み、完了していなければ止まる。』を足す」が近い
    - 置き換えて「『貼る時機』に前提を書いたときは、0章にその確かめを足す」に拡張した。同じ趣旨を2か所に書かないため
  - 規則2（許すずれを数で）: 同じ意味の記述は無い
    - chat-side「止まる条件と検証の指定」に「止まる条件で数値の増減を見るときは、正しい理由で動く場合を想定して書く」がある。これは補い合う関係で、食い違わない
  - 規則3（並行の指示はブランチを分ける）: 同じ意味の記述は無い
    - **食い違いに近いもの**: chat-side「指示文の書き方・渡し方」の先頭に「ブランチ名を具体的に書かない。『ブランチ運用』の規則どおりと書く」がある
    - 一方、雛形のクラウドセッションの行は `work/<識別子>` を書く形で、実際の指示文も具体名を書いている
    - 規則3は具体名を書く前提の規則なので足した。古い行は直していない（Codespace 時代の規則で、直すかは判断が要る。下の「判断が必要なこと」）
  - 規則4（結果が出てから作る）: chat-side の「並行する指示の中で、ほかの実行中の指示の進み具合に触れない。結果が要るなら…の形にする」と同じ系統
    - 置き換えず、その直後に足した（前の行は並行中の書き方、新しい行は作る時機で、別の規則のため）
  - 規則5: handover 5章の「未対応の注意」の行を、対応済みの1行に直した
- **指示とルールの食い違い**: 指示は「事例は Chat-Ref 1つの参照にとどめる」だが、CLAUDE.md「CLAUDE.md / handover.md の更新ルール」は CLAUDE.md・handover.md・chat-side-operations.md に出典としての Chat-Ref を書かないと定めている
  - ルールの側を優先した: chat-side と handover には日付（と #448）を書いた
  - `docs/instruction-template.md` はこの3文書に入らないので、事例に Chat-Ref を書いた（既存の項目も Chat-Ref を書いている）

### 手順2: 追記（d26166bc）

- `docs/instruction-template.md`:
  - 注意書きに「貼る時機」と「許すずれを数で」の2項目を足した
  - 雛形の Chat-Ref・マージの行の下（共通手順の手前）に「貼る時機:」の行を足した
  - 0章の注記を拡張した
  - 「止まる条件」に数で書く例の行を足した
- `docs/notes/chat-side-operations.md`「指示文の書き方・渡し方」に2項目を足した: 後の実行の結果に頼る指示は結果が出てから作る、並行の指示はブランチを分け「1つのセッションに貼る指示は1つ」と伝える
- `docs/handover.md` 5章: 1000行の注意を対応済みの1行に直した
- 決定の記録: `docs/decisions/operations.md`（運用の分野。既にある）に足した
- 参考（範囲外、変えていない）: operations.md に別のチャットの決定「#491 の『MAX_DELETES の件』を済にする」がある。CAL-24（カレンダーの削除の上限、未マージ）と同じ論点の可能性がある

### 手順3: 読み比べの用意（`git diff origin/cloudflare -- <ファイル>`）

```diff
=== docs/instruction-template.md
diff --git a/docs/instruction-template.md b/docs/instruction-template.md
index 9f5e860d..2ddfd197 100644
--- a/docs/instruction-template.md
+++ b/docs/instruction-template.md
@@ -25,6 +25,8 @@
 - **ワークフロー・ビルドの完了など外部の状態を待つ処理がある指示は、待つ上限（15分）と、超えたときの扱い（その時点の状態を書き「未確認の項目」に回して先へ進む）を書く。**上限の無い待機を許すと、受け手が待ち続ける。自分のコマンドが処理を進める「実行」（テスト・生成など）は上限の対象にしない
 - **試運転（dry-run の手動実行など）が失敗で止まる見込みのある指示は、そのことを指示文に書き、最終報告に「失敗通知が届くが対応不要」と書かせる。** 試運転の失敗でも平野さんに通知メールが届き、別のチャットが原因を調べ直すことになる
 - **複数のスクリプトが使う定数・関数（シートのクエリ・列の定義など）を変える指示は、その名前を import・参照している所の洗い出しと、マージ前の全ページの再生成・差分の確認を手順に入れる。**変える側のページだけ確かめると、借りて使っているほかのページのずれを見落とす
+- **共通手順の行の手前に「貼る時機:」の行を置く。** ほかの指示の完了・毎朝の実行の後など前提があればそれを書き、0章でその前提を確かめさせる（満たされなければ何もせず止まる）。前提が無ければ「いつでも」（CHAT-0930-CAL-19 が前提の CAL-09 より先に貼られ、番号を1つ使った）
+- **見込みと比べて止まる条件は、許すずれを数で書く。**「大きく違えば」と書かない（例: 「直すは見込み ±3件まで、作る・消すは見込みと同じ」）。数が無いと、ずれたときに進むかを受け手が決めることになる（CHAT-0930-CAL-15 は直すが見込み1件に対し2件で、受け手の判断で進んだ）
 
 ルールの本文と書くときの注意は `docs/notes/chat-side-operations.md`「指示文を書くときの注意」。
 
@@ -33,11 +35,12 @@
 （題の行は必ず入れる。平野さんの発言と区別するため。docs/notes/chat-side-operations.md「指示文を書くときの注意」冒頭）
 Chat-Ref: CHAT-MMDD-XXX-nn
 マージ: 承認済み（チャットで）／判断待ちで止まる（どちらかを残す。docs/notes/chat-side-operations.md「平野さんの判断とマージの許可」）
+貼る時機: いつでも／<前提>の後（例: CHAT-MMDD-XXX-nn の完了の後、毎朝の実行〈02:43 JST の schedule〉の後）
 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
 
 0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
 （0章のこの一文は必ず入れる。途中切れの確認のため。docs/notes/chat-side-operations.md「指示文の書き方・渡し方」）
-（続きの指示で前の指示の結果が要るときだけ、0章に「<Chat-Ref> のログの `## 報告` を読み、完了していなければ止まる。」を足す。docs/notes/chat-side-operations.md「指示文の書き方・渡し方」）
+（「貼る時機」に前提を書いたときは、0章にその確かめを足す。前の指示の結果が前提なら「<Chat-Ref> のログの `## 報告` を読み、完了していなければ何もせず止まる。」。docs/notes/chat-side-operations.md「指示文の書き方・渡し方」）
 
 ## 目的
 （何のために何を変えるか。1〜2行）
@@ -56,6 +59,7 @@ Chat-Ref: CHAT-MMDD-XXX-nn
 
 ## 止まる条件
 - （前提が食い違う、同じ論点の issue がある、他セッションの着手中コメントがある 等）
+- （見込みと比べるときは許すずれを数で。例: 直すは見込み ±3件まで、作る・消すは見込みと同じ）
 - （マージを伴う指示では）cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
 
 ## 完了条件
=== docs/notes/chat-side-operations.md
diff --git a/docs/notes/chat-side-operations.md b/docs/notes/chat-side-operations.md
index 728fcc56..d361e5c1 100644
--- a/docs/notes/chat-side-operations.md
+++ b/docs/notes/chat-side-operations.md
@@ -133,6 +133,10 @@ Claude Code 側で確認できる範囲は `docs/notes/cloudflare.md`「ビル
 - **1本の指示は3項目程度まで。** 多いと、途中で止まったときに何が済んだか分かりにくい
 - **配った後の指示を直すときは、先に平野さんがもう貼ったかを確かめる。** 貼った後なら新しい番号で出し直す
 - **並行する指示の中で、ほかの実行中の指示の進み具合に触れない。** 結果が要るなら「`<Chat-Ref>` のログの `## 報告` を読み、完了していなければ止まる」の形にする
+- **後の実行の結果（毎朝の実行・別の指示）を見込みに使う指示は、その結果が出てから作る。** 前もって作るときは「貼る時機」に前提を書き、
+  見込みは「`<Chat-Ref>` のログの `## 報告` に従う」の形にして数を写さない（2026-09-30 の確認の指示は、前提が変わるたびに3回書き直した）
+- **同じチャットから、前の指示の完了を待たずに次の指示を出すときは、作業ブランチを分ける**（`work/<MMDD>-<識別子>-<短い名前>` など）。
+  平野さんには「1つのセッションに貼る指示は1つ」と伝える（2026-09-30、#448 系で同じブランチを使いかけ、同じセッションでブランチの切り替えが止まった）
 - **既定モデルは Opus 5.5。別モデルは指示文の手前のチャット本文で伝える**（指示文に「推奨モデル」欄は入れない）。
   単発の確認は `claude --model haiku`。haiku でも共通手順とログは省かない
 
=== docs/handover.md
diff --git a/docs/handover.md b/docs/handover.md
index dd559142..21cac173 100644
--- a/docs/handover.md
+++ b/docs/handover.md
@@ -188,7 +188,7 @@ CSPでは `script-src` にのみ必要で、送信先は自ドメインの `/cdn
 (2) #408 (3) #475 の未登録55名（平野さんのシート作業）と #446 の未決 U1〜U4 (4) #485（11/2 に、廃止後はじめての旧表 URL への着地を見る）。
 次のチャットは新しい識別子で始める（DUP は使い切った）。
 
-**未対応の注意:** 予定表のジョブ `yotei` は、「【1】元データ」の1000行の上限で失敗したことがある。#479 の担当の会話に回した。対応済みかは未確認
+予定表のジョブ `yotei` の「【1】元データ」の1000行の上限での失敗は、書く前にシートの行を足す修正（2026-10-01 マージ）で対応済み。
 
 **期限付き・確認待ちタスク**
 
```

## 報告

- 状態: 判断待ち（追記まで。マージは未承認）
- ブランチ: work/1003-cal-mos
- ログ: https://github.com/retroeater/mj/blob/work/1003-cal-mos/docs/logs/CHAT-0930-CAL-25.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1003-cal-mos
- 確認用URL: なし
- マージ: 未（未承認）
- issue: なし
- 判断が必要なこと:
  - **足した規則**（差分は「手順3」）:
    1. 雛形に「貼る時機:」の行と注意書き
    2. 見込みとの許すずれを数で書く（雛形の注意書きと「止まる条件」の例）
    3. 並行の指示はブランチを分け、1セッション1指示と伝える（chat-side）
    4. 後の実行の結果に頼る指示は結果が出てから作り、見込みは `## 報告` に従う形にする（chat-side）
    5. handover の1000行の注意を対応済みに直した
  - **既存の記述と重なったものの扱い**:
    - 雛形の0章の注記は、貼る時機に合わせて拡張した（置き換え）
    - chat-side の「並行する指示の中で…触れない」は残し、直後に規則4を足した
  - **食い違いの判断**: chat-side の「ブランチ名を具体的に書かない」（Codespace 時代の規則）は、雛形のクラウドセッションの行・規則3と合わない。直すか（例「クラウドセッションの指示では作業ブランチ名を書く」）を決めてほしい。今回は直していない
  - **ルールを優先した点**: 指示の「事例は Chat-Ref で参照」は、chat-side と handover では CLAUDE.md の規則（出典としての Chat-Ref を書かない）に従い、日付と #448 にした
  - マージしてよいか（差分を読み比べて）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj fee9a96e）: https://github.com/retroeater/mj-logs/tree/main/guide/fee9a96e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/fee9a96e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/fee9a96e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/fee9a96e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/fee9a96e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/fee9a96e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/fee9a96e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ffc4839a.md
