# CHAT-1104-MNL-02

- 着手日時: 2026-11-04
- 対象issue: なし
- ブランチ: work/1104-mnl
- 着手時HEAD: e0203d14d

## 指示

【Claude作成】Claude Code 向け指示：chat-side-operations.md の手作業の項に「頼むときは操作するページへの直接リンクを付ける」を統合する（MNL-01 の貼り直し）
Chat-Ref: CHAT-1104-MNL-02
マージ: 承認済み（チャットで）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1104-mnl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-mnl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
チャット側が平野さんに手作業（ワークフローの手動実行・設定・画面の確認）を頼む・手順を答えるとき、各手順にその操作をするページへ直接飛べるリンクを付ける規則を、docs/notes/chat-side-operations.md に書く。CHAT-1104-MNL-01 は「知見 → 選んだ層 → 理由」の表が無く着手前に止まったため、表を付けて貼り直す（MNL-01 は欠番）。

### 決定（2026-11-04、平野さん）
- 手作業の手順を聞かれて答えるときは、直接そのページに飛べるリンクを付ける。チャットのルールとして徹底する（きっかけ: 「最強戦2026 超技巧合戦」の結果をシートに入れた後の regenerate-saikyo.yml の手動実行の手順に、リンクが無かった）
- 書き場所は docs/notes/chat-side-operations.md（MNL-01 の報告にあった chat-routines.md の案は採らない）
- この指示の変更は、差分をログに引用したうえで cloudflare にマージしてよい

### 知見 → 選んだ層 → 理由（CLAUDE.md「Chat-Ref」、#492）

| 知見 | 選んだ層 | 理由 |
| --- | --- | --- |
| チャット側が平野さんの手作業の手順・確認の依頼を出すとき、操作するページへの直接リンクを付け忘れる（2026-11-04、regenerate-saikyo.yml の手動実行の手順） | ガイド文（chat-side-operations.md。既存の「平野さんの手作業（画面を見るだけ等）が安価で…」の項へ統合） | 計算的センサー（テスト・hook・ワークフロー）は、チャット側の返答の文面を見られない。推論的センサー（Code の着手前の確認）も、チャットの返答は Code に渡らないので働かない。chat-routines.md は場面の表から開いたときしか読まれず、今回の漏れも「手順を聞かれた」場面で起きたので読み落としやすい。chat-side-operations.md は会話のたびに読まれる |

### 前提（チャット側。平野さんの決定ではない）
- 文面の案（既存の項への1句の追加）: 「…先に頼む。**頼む・手順を答えるときは、各手順にその操作をするページへの直接リンクを付ける**（ワークフローなら `https://github.com/retroeater/mj/actions/workflows/<ファイル名>`。パスは実物で確かめ、推測で組み立てない）」。実物に合わせて変えてよい
- chat-side-operations.md は上限のある文書（冒頭の注意）。純増した分は、同じ量を docs/notes/ の各文書へ移す
- チャット側が mj-logs の guide（96e0bc84）で探した範囲では、同じ趣旨の記述は docs/ に無かった（要確認）

## 手順
1. 同じ論点（チャット側が手作業の手順・確認の依頼に付けるリンク）の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する。あわせて、CHAT-1104-MNL-01 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1104-MNL-02` を足す。
2. docs/notes/chat-side-operations.md の今の内容（冒頭の上限の注意、「書く前に実物で確かめる」の「平野さんの手作業…」の項）と、`git grep -n -e 'リンク' -- CLAUDE.md docs/handover.md docs/instruction-template.md docs/notes/chat-side-operations.md docs/notes/chat-routines.md` で同じ趣旨の記述を確かめる。同じ趣旨の記述があれば、止めずにそれを置き換え・拡張する（処理を報告に書く）。上の文面の案で既存の項に統合し、純増した分は冒頭の規則どおり docs/notes/ の他の文書へ移す（移した箇所と行数・バイト数をログに書く）。
3. check-assets.yml（work/** への push で走る）の結果を確かめ、差分（変更した行の前後）をログに引用してから、cloudflare にマージする。

## 止まる条件
- 同じ論点の issue がある
- 足す規則が既存の記述と矛盾していて、どちらが正か平野さんかチャット側の判断が要る
- 上限を守るために移せる記述が見つからない（移す先の候補を報告に書く）
- check-assets.yml がこの変更による失敗・警告を出し、直せない
- 未マージの work/ ブランチが docs/notes/chat-side-operations.md の同じ節・同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-MNL-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-MNL-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべて揃っている。「知見 → 選んだ層 → 理由」の表がある
- Chat-Ref の重複: `git log --all --grep 'CHAT-1104-MNL-02'` は0件、`docs/logs/CHAT-1104-MNL-02.md` も無し（MNL-01 の続き）
- 作業ブランチ: `origin/work/1104-mnl` は `origin/cloudflare` の祖先（MNL-01 でマージ済み）。ローカルの `work/1104-mnl` も祖先なので、docs/notes/cloud-sessions.md「作業ブランチの用意」のとおり `git merge --ff-only origin/cloudflare`（Already up to date、HEAD e0203d14d）
- 未マージの work/ ブランチで docs/notes/chat-side-operations.md を変えているものは無い（`git branch -r --no-merged origin/cloudflare` の各 work/ ブランチと `origin/cloudflare...` の差分で確認）

### 1. issue の検索

- `search/issues` で `repo:retroeater/mj is:issue` に「リンク 手作業」「直接リンク」「手動実行 リンク」「link manual」（open・closed とも）。題名・内容で同じ論点（チャット側が手作業の依頼に付けるリンク）のものは無かった。近いのは #564（GitHub Actions の整理・ダッシュボード）だが、Actions の構成の話で別論点
- CHAT-1104-MNL-01 のログの状態を「判断待ち / 続き: CHAT-1104-MNL-02」に直した（99613f341）

### 2. 同じ趣旨の記述の確認と統合

- 指定の `git grep -n -e 'リンク'` の結果: CLAUDE.md:28（画像のリンク切れ）、handover.md:80・148・149（リンク切れ・リンク色）、chat-routines.md:116（カレンダーの説明に issue のリンク）、chat-side-operations.md:27・28・98（ログ末尾のリンク）。手作業の依頼に操作ページへのリンクを付ける趣旨の記述は無く、矛盾する記述も無い
- 既存の項「平野さんの手作業（画面を見るだけ等）が安価で…」（「書く前に実物で確かめる」）の末尾に、文面の案どおりの一句を足した（理由の「探す手間と別画面での操作を防ぐ」を括弧内に入れた）。リンクの例の `regenerate-saikyo.yml` は `.github/workflows/` にあることを確かめた
- 純増分の移動: chat-side-operations.md「止まる条件と検証の指定」の「マージ・止まる条件は、見込みで説明できる差分の範囲として書く」の項の続き3行（生成物を含む変更の細目、618 バイト）を、docs/notes/chat-routines.md「指示の種類ごとの確かめ」の「計測・試運転・本番へ書き込むワークフローの試し・データ更新後の生成を含む指示」の項の最後の子項目へ移し、元の項には参照の一句を残した。
  移した3行への参照は docs/ に無い（`git grep` で確認）。「確認用 URL」節も候補だったが、docs/decisions/operations.md などが見出しで参照しているので避けた
- バイト数: chat-side-operations.md 22746 → 22569（足した一句で +333、移して −510、計 −177）。行数は chat-side-operations.md +3 −5、chat-routines.md +3

差分（24a1e2e03）:

```diff
--- a/docs/notes/chat-side-operations.md
+++ b/docs/notes/chat-side-operations.md
@@ -86,7 +86,8 @@
   **issue の状態（Open/Closed・本文・コメント）を確かめられないまま、その issue の作業の指示を書かない**（読めなければ Code に確かめさせてログに書かせてから書く）
 - **触れる領域の資料を読んでから書く**（CLAUDE.md・handover.md に追記・変更するときは追記先の節の今の内容、ある領域に触れるときはその領域の `docs/notes/` の最近の変更。場面ごとの文書は上の表）
 - **仮説を確かめるときは、当たり外れを一発で分ける確認を先に行い、仮説が正しい前提での準備はその後にする**
-- **平野さんの手作業（画面を見るだけ等）が安価で結論を左右するなら、記録・文書の指示より先に頼む**（外部のデータ〈X のアイコン・連盟サイトなど〉が「変わっていない」という報告で判断を出す前も同じ。今の正〈プロフィールのページ・API〉を見ていなければ、それを頼むか確かめる手順を先に指示する）
+- **平野さんの手作業（画面を見るだけ等）が安価で結論を左右するなら、記録・文書の指示より先に頼む**（外部のデータ〈X のアイコン・連盟サイトなど〉が「変わっていない」という報告で判断を出す前も同じ。今の正〈プロフィールのページ・API〉を見ていなければ、それを頼むか確かめる手順を先に指示する）。
+  **頼む・手順を答えるときは、各手順にその操作をするページへの直接リンクを付ける**（ワークフローなら `https://github.com/retroeater/mj/actions/workflows/<ファイル名>`。パスは実物で確かめ、推測で組み立てない。探す手間と別画面での操作を防ぐ）
 - **平野さんに判断を求める前提（「A を直せば B も不要になる」等）や、シートの値を変えるよう頼む前の今の値は、先に Claude Code に照合させるか「未確認」と明記する**（誤った前提で判断をもらうと決定が誤る）
 
 ### 平野さんの判断とマージの許可
@@ -136,10 +137,7 @@
 ### 止まる条件と検証の指定
 
 - **「差分が N 行ならマージ」のような止める条件は、先に変更対象の参照箇所を調べさせてから決める**（容量の増減も実測を報告させる）
-- **マージ・止まる条件は、見込みで説明できる差分の範囲として書く。** 生成物を含む変更は差分を種類に分け、許す種類と確かめ方（変わった画像 URL は HTTP 200 など）を書き、ほかが出たら止める（再生成のたびにシートの変化が混ざる）。
-  許す行は焼き付けず、比べる相手（前の TSV・ログ）を指して想定外の行を全部書かせる（決定が変わると無効になる）。
-  「承認済み」の条件は「決定とシートの変化で説明できる差分だけ（見込み: ○○）」の形にする（表示の一部の列挙では、そこから決まる並びが外れる）。
-  見込みと比べる条件は許すずれを数で書き（無いと進むかを受け手が決める）、見込みの数は直前のログのどの表のどの数から計算したかを書く（推測の幅は表と食い違う）
+- **マージ・止まる条件は、見込みで説明できる差分の範囲として書く。** 生成物を含む変更は差分を種類に分け、許す種類と確かめ方（変わった画像 URL は HTTP 200 など）を書き、ほかが出たら止める（再生成のたびにシートの変化が混ざる）。細目は `docs/notes/chat-routines.md`「指示の種類ごとの確かめ」の生成を含む指示の項
 - **止まる条件で数値の増減を見るときは、別名の寄せなど正しい理由で動く場合を想定して書く。** 外の通信（HEAD・API）で動く数は、同じ実行をもう1回行い、それでも外れたら止まる、の形にする
--- a/docs/notes/chat-routines.md
+++ b/docs/notes/chat-routines.md
@@ -110,6 +110,9 @@
   - **シート・SNS ブックなどデータが変わった後にページを生成する指示では、手順1を `regenerate-page.yml` の作業ブランチの ref・入力 `check`・`target_page: <ページ>` の手動実行にし、止まる理由があれば一覧を書いて止まらせる**（…）
+  - **生成物を含む変更の止まる条件**（docs/notes/chat-side-operations.md「止まる条件と検証の指定」から移した）: 許す行は焼き付けず、比べる相手（前の TSV・ログ）を指して想定外の行を全部書かせる（決定が変わると無効になる）。
+    「承認済み」の条件は「決定とシートの変化で説明できる差分だけ（見込み: ○○）」の形にする（表示の一部の列挙では、そこから決まる並びが外れる）。
+    見込みと比べる条件は許すずれを数で書き（無いと進むかを受け手が決める）、見込みの数は直前のログのどの表のどの数から計算したかを書く（推測の幅は表と食い違う）
 
 ## 期日とカレンダー
```

### 3. check-assets.yml とマージ

- run 41850630928（99613f341、work/1104-mnl への push）: success。ジョブのログで `::warning` の出力は無し
  - `docs/notes/chat-side-operations.md: 22569 bytes (警告域 26624 / 上限 28672)`
  - `docs/notes/chat-side-operations.md: 比較元 9a6c9ba9a の 22746 bytes → 22569 bytes(警告は 24576 bytes 超で純増のとき)`
- docs のみの変更のため、新しい文脈のサブエージェントによるレビュー（CLAUDE.md「判断・作業の原則」、コード・生成物・ワークフロー・配信設定が対象）は行っていない
- docs/decisions/operations.md に決定を追記した（このログと同じコミット）
- 最初の cloudflare への push は non-fast-forward で拒否された（その間に CHAT-1104-LGF-04 の4コミットが cloudflare に入っていた。push 前の祖先の確認が偽だったのを見落として push を打ったが、拒否で何も変わっていない）。`git merge origin/cloudflare` で取り込み、docs/decisions/operations.md の末尾の追記どうしの衝突は CLAUDE.md「ブランチ運用」のとおり両方を残して解いた（LGF-04 の節の後に MNL-02 の節）
- マージは push 直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD` を確かめてから `git push origin work/1104-mnl:cloudflare` で行う（結果は `## 報告` の「マージ」）

## 報告

- 状態: 完了
- ブランチ: work/1104-mnl
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1104-MNL-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-mnl
- 確認用URL: なし
- マージ: 済（7460ba97e を fast-forward で push）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7c849017）: https://github.com/retroeater/mj-logs/tree/main/guide/7c849017

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c849017/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c849017/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c849017/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c849017/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c849017/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7c849017/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/96e0bc84.md
