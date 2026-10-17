# CHAT-1017-LVP-03

- 着手日時: 2026-10-17
- 対象issue: #362
- ブランチ: work/1017-lvp
- 着手時HEAD: d3a5c45c0

## 指示

【Claude作成】Claude Code 向け指示：小島武夫杯帝王戦の /live の掲載範囲を決める issue を起票する Chat-Ref: CHAT-1017-LVP-03 マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい（起票だけの指示。シート・コード・生成物は変えない） 貼る時機: いつでも（任意: LVP のセッションの続きでも新しいセッションでもよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-lvp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1017-lvp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-lvp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
【3】手動補正で掲載が空欄の小島武夫杯帝王戦の10行を、/live の正式公開（#362）の後に扱うため、掲載範囲を決める issue を起こす。
決定（2026-10-17、平野さん）

* 小島武夫杯帝王戦の第2・3・5期の10行（【3】の行 3537・3583・3587・3601・3656・3763・3861・4144・4179・4195）は、公開の後に回し、今は掲載を空欄のままにする
* 掲載範囲の決定は別の課題として起票する

前提（チャット側。平野さんの決定ではない）

* 10行の内訳と【2】の値は CHAT-1016-LVP-02 のログ `## 経過` の「【3】を埋めるための表」にある（第2期のベスト16 A〜D卓・決勝1〜2回戦・「ベスト16・決勝」の全編、第3期・第5期の「ベスト16・決勝」の全編。うち 3537 は冒頭のみ無料の公開版とみた行）
* /live には第3・4期の決勝のページ（`live/teiou/3/f.html`・`live/teiou/4/f.html`）がある。docs/notes/live-page-design.md「2-1」の掲載範囲の表には小島武夫杯帝王戦の行が無い（「3-5」の転記で決勝動画の分を入れた）。LVP-02 はこの表に無いため「範囲の外」と分けたが、これは誤り（要確認）
* 起票する issue の本文の案: 経緯（上の2点）、決めること（ほかのタイトル戦と同じ「ベスト16 以上」で載せるか／決勝だけか、冒頭のみ無料の版の扱い、2-1 の表への行の追加）、対象の10行（行番号・動画ID・題名。LVP-02 の表から引用）、関連（#362・#490）。ラベルは `分野: データ`・`対象: video_live`

手順

1. 同じ主題の issue を、クローズ済みも含めて検索する（「帝王戦」「teiou」）。あれば起票せずに止まって報告する。#362 に他セッションの着手中コメントが無いことを確かめる
2. 上の案で issue を起票し、#362 に「帝王戦の10行は公開の後に回し、#<新しい番号> で扱う」とコメントする（#362 の本文・状態・ラベルは変えない）
3. 上の「決定」を docs/decisions/live.md に足す

止まる条件

* 同じ主題の issue がある（手順1）
* #362 に他セッションの着手中コメントがある
* LVP-02 の表に、上の10行の行番号が無い・帝王戦でない行がある（食い違う行を書いて止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（起票した issue の番号を書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-LVP-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-LVP-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1017-LVP-03` のコミットは0件。`LVP` は同じセッションの続き
- 0. 「指示」欄の末尾は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- work/1017-lvp はローカル・リモートとも無く、origin/cloudflare（d3a5c45c0）から作成

### 手順1: 同じ主題の issue・#362・10行の確認

- MCP の `search_issues`（「帝王戦」「teiou」）は0件。念のため REST で全 issue（588件、クローズ済みを含む）の題・本文を「帝王」「teiou」で見た。当たったのは #222・#437・#452・#476・#487（すべてクローズ済み）で、どれも主題は別（#437 は掲載範囲の拡大〈鳳凰戦・女流桜花・日本シリーズ〉で、帝王戦の10行は転記の件数に出るだけ。#452・#476・#487 は title/ の期ページの決勝動画）。帝王戦の /live の掲載範囲を決める issue は無いので起票した
- 未マージの `work/` ブランチのコミットの件名に「帝王」「teiou」は無し（自分の work/1017-lvp を除く）
- #362 のコメント39件に他セッションの着手中は無し（最後の着手中は LVP-01・LVP-02 の自分のもの）
- LVP-02 の表に10行とも行番号があり、すべて小島武夫杯帝王戦（第2期8行・第3期1行・第5期1行）
- /live の生成物には `live/teiou/3/f.html`・`live/teiou/4/f.html`（と `3.html`・`4.html`・`index.html`）がある。前提どおり
- 前提の「LVP-02 が範囲の外と分けたのは誤り」は、2-1 の表に帝王戦の行が無いことを範囲の外とみた当てはめのためで、決勝のページがある以上、範囲が未決とみるのが正しい。issue の「経緯」にそう書いた

### 手順2: 起票とコメント

- #590「/live の小島武夫杯帝王戦の掲載範囲を決める（【3】の掲載が空欄の10行）」を起票。ラベル `分野: データ`・`対象: video_live`（付いたことを確認）。本文は経緯・決めること（範囲〈ベスト16 以上か決勝だけか〉・冒頭のみ無料の版・2-1 への行の追加）・対象の10行（行番号・動画ID・題名・【2】のステージ・まとめ単位）・関連（#362・#490・#437・#452・#476）
- 対象の表に、行3537・3601・4144・4195 は【3】でステージを「決勝」に補正済み（#476 の決定4）と注記した
- #362 に「帝王戦の10行は公開の後に回し、#590 で扱う」とコメントした（本文・状態・ラベルは変えていない）

### 手順3: 決定の記録

- docs/decisions/live.md に、この指示の「決定」2項目を足した

## 報告

- 状態: 完了
- ブランチ: work/1017-lvp
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1017-LVP-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-lvp
- 確認用URL: なし
- マージ: 済（64563a051。push 前に origin/cloudflare〈9d9b16c51〉を merge で取り込み、差分はこのログと docs/decisions/live.md だけ。この追記は docs/logs のみの追いの push）
- issue: #590（起票）、#362（コメント）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 14ed9b71）: https://github.com/retroeater/mj-logs/tree/main/guide/14ed9b71

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/14ed9b71/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/14ed9b71/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/14ed9b71/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/14ed9b71/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/14ed9b71/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/14ed9b71/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a8c1d96c.md
