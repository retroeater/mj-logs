# CHAT-1019-OUK-17

- 着手日時: 2026-10-19
- 対象issue: なし（OUK 系列の申送り）
- ブランチ: work/1019-ouk17（origin/cloudflare から作成）
- 着手時HEAD: fbae687ec

## 指示

【Claude作成】Claude Code 向け指示：OUK の振り返りの申送り（3大会の比較の文書を大会の追加で必ず更新させるテスト・0章の許す差分の書き方・シートに貼る手順の書き方）
Chat-Ref: CHAT-1019-OUK-17
マージ: 判断待ちで止まる（容量制限のある文書を変えるため、足した行の全文をログに貼り、平野さんが読み比べてから別のマージの指示で cloudflare へ入れる）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-ouk17 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1019-ouk17 を origin/cloudflare から作る。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
2026-10-19 の OUK 系列（女流桜花 ouka/ の作り直しと公開、CHAT-1017-OUK-01〜CHAT-1019-OUK-16）の振り返りと、その Fable 5.1 のレビューで出た知見のうち、機械的に確かめられる手順に落とせるものだけを書き残す（docs/notes/chat-routines.md「申送り」）。

### 決定（2026-10-19、平野さんが「申送り」と指示）
- 振り返りの知見を、docs/notes/chat-routines.md「申送り」の手順で残す

### 知見 → 選んだ層 → 理由（chat-routines.md「申送り」の表。仮置き。実物に合わせて変えてよい）

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| A. 3大会の比較（`docs/notes/league-pages-compare.md`、CHAT-1018-OUK-13）は、大会を足す・大会ごとの設定を変えるときに更新しないと古くなる。今は読む仕組みが無い | 計算的センサー（テスト）＋ガイド文1行 | `generate_houou_pages.py` の大会の設定（`Contest` の各インスタンス）の名前が、比較の文書の表の列にすべてあるかをテストで確かめられる。新しい大会を足すと落ちるので、文書の更新を忘れない。ガイド文は `docs/new-page-checklist.md` に「大会を横展開するときは比較の文書を読み、差を表に足す」の1行だけ |
| B. 0章の確かめに「許す差分」を行で列挙すると、平野さんの決定が1つ変わるたびに指示文が無効になる（OUK-05・07 が欠番になった） | ガイド文（`docs/instruction-template.md` の0章の注記に1句） | 指示文の書き方なので、テストやフックでは確かめられない。受け手の確認手順でも扱えない（指示文を書くのはチャット側）。規則だけを1句で書く: 「比べる相手（前の TSV・ログ）を指し、想定外の行は全部書いて止まる形にする。許す行を指示文に焼き付けない」 |
| C. 平野さんにシートへ貼ってもらう手順を1文にまとめると、2か所のうち1か所だけ貼られる（OUK-10 が0章で止まった） | 推論的センサー（受け手の0章の確認）は既にある（OUK-10 はこれで止まった）。足すのはガイド文1行（`docs/notes/chat-routines.md`「指示の種類ごとの確かめ」の「シートを用意したと言われた」の近く） | 受け手の確かめは働いたので、チャット側の書き方だけを足す: 「貼る手順は1列1行で書き、どのセルから貼るかと、貼った後の件数（例: Y が49行）を添える」 |

書かないもの（心得で、機械的に確かめられないため）: データの形を最初の質問に含めること、選択肢の幅を広く取ること、横展開では houou/ との一致をおすすめの基本にすること、件数を自分の数えで断定しないこと。

### 前提（チャット側。実物に合わせて変えてよい）
- A のテスト: `scripts/tests/` に足す。`Contest` のインスタンスの一覧の取り方（モジュールの属性を走査、または大会の一覧の定数があればそれ）は実物から決める。比較の文書の表の列見出し（例: houou/・ouka/・jwrc/）と、大会の設定の名前（または URL の頭）を突き合わせる。突き合わせのキーは実物の表から決め、文書の表の列見出しを変える必要があれば最小限にする
- 追記先の残り容量: `docs/instruction-template.md`・`docs/new-page-checklist.md`・`docs/notes/chat-routines.md` のそれぞれで、容量の上限・警告域があるかを確かめ（`scripts/` の容量の検査や CLAUDE.md の規則）、前後のサイズと上限・警告域との差をログに書く。上限を超えるなら書かずに止まる
- 規則と理由の一句だけを書く。事例（OUK-05・07・10 の経緯）は `docs/notes/handover-archive-2026.md` の該当の節に1行で残すか、書かない（archive の「外した記録」を先に確かめ、前に意図して外した行と重ならないか見る）

## 手順
1. A のテストを足し、今の cloudflare で通ることを確かめる。試しに大会の設定を1つ足した状態で落ちることも確かめる（確かめた後は元に戻す）
2. A・B・C のガイド文を足す。追記先の前後のサイズを測る
3. `python3 -m unittest discover -s scripts/tests` を回す。足した行・テストの全文をログの `## 経過` に貼る

## 止まる条件
- 追記先が容量の上限を超える
- A のテストが今の cloudflare で落ちる（比較の文書と大会の設定が既に食い違っている。食い違いを書いて止まる。文書は直さない）
- cloudflare への push はしない

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、足した行の要約、追記先の前後のサイズ、上の表から変えたこと（あれば）を書く
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-OUK-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-OUK-17 を書く

不明な点は仮置きで進め、報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1019-ouk17
- ログ: https://github.com/retroeater/mj/blob/work/1019-ouk17/docs/logs/CHAT-1019-OUK-17.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-ouk17
- 確認用URL:
- マージ: 未
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 06f1203b）: https://github.com/retroeater/mj-logs/tree/main/guide/06f1203b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/06f1203b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/06f1203b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/06f1203b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/06f1203b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/06f1203b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/06f1203b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/fbae687e.md
