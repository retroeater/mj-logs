# CHAT-1016-LVP-02

- 着手日時: 2026-10-16
- 対象issue: #362
- ブランチ: work/1016-lvp
- 着手時HEAD: 2bf94d99a

## 指示

【Claude作成】Claude Code 向け指示：【3】の掲載が空欄の約100行（日本シリーズ・保留3大会を除く）を、平野さんが埋めるための表にする Chat-Ref: CHAT-1016-LVP-02 マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい（調査だけの指示。シート・コード・生成物・issue の本文は変えない） 貼る時機: CHAT-1016-LVP-01 の判断待ちの後（同じセッションの続きでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-lvp の push と、cloudflare への docs のみのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1016-lvp を続けて使う（CHAT-1016-LVP-01 のログがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで docs/decisions/live.md が衝突し、両方の変更が両立する（追記どうし・隣り合う行）なら両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1016-LVP-01 のログの `## 報告` の状態が「判断待ち」でなければ止まる。

目的
/live の正式公開（#362）の前に、【3】手動補正の「掲載」が空欄の行のうち、今回片付ける範囲を、平野さんが【3】に入れやすい表にする（CHAT-1016-LVP-01 の続き）。
決定（2026-10-16、平野さん）

* 公開の前に、【3】の掲載の未記入を片付ける
* 公開前に片付ける範囲から、麻雀日本シリーズ・女流プロ麻雀日本シリーズ（予選・節・プレーオフなど）と、達人戦・昇龍戦・鳳匠戦（#490 で範囲が未決）を外す。日本シリーズの予選・節は公開の後に扱う（「第N節」などのまとめ単位の記号を足すコードの直しを含む）
* 旧 `video_live.html` は、公開と同時に `/live/` へ 301 で転送し、旧ページの生成を止める
* 旧ページへの `?name=<選手名>` の流入は、/live/ が `?name=` を `?q=` と同じに受けて、その選手で絞り込んだ状態で開く
* canonical は、トップを含む live/ の全ページに付ける（title/・saikyo/ と同じ）
* トップの題（`<title>`・og:title）は「放送対局 | 番組 | ryoei.pro」にする。タイトル戦・期・個別の題は今のまま
* トップ・タイトル戦・期の OGP 画像は、そのページのヒーローと同じサムネイル（その範囲で最新の動画）にする
* 構造化データは、公開の変更にパンくず（`BreadcrumbList`）だけを入れる。動画（`VideoObject` など）は公開の後に別に扱う

前提（チャット側。平野さんの決定ではない）

* 範囲の見込み: CHAT-1016-LVP-01 のログ「手順2」の「(a) のタイトル戦 × ステージ × 追加日の月」の表から、日本シリーズ2大会（643行）と達人戦・昇龍戦・鳳匠戦（93行）を除いた 100行（鳳凰戦 39・十段戦 31・小島武夫杯帝王戦 10・プロクイーン 8・紅龍戦 7・若獅子戦と桜蕾戦 3・WRC-Rリーグと新人王戦 2）
* この指示は表を作るだけ。公開の変更（上の決定の実装）は別の指示で出す

手順

1. #362 に着手中のコメントを書く（本文・状態・ラベルは変えない）。CHAT-1016-LVP-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1016-LVP-02` を足す。LVP-01 と同じ読み方で【2】【3】を読み、行数をログに書く
2. (a) 掲載が空欄の行から、日本シリーズ2大会と達人戦・昇龍戦・鳳匠戦を除いた行を取り出し、平野さんが【3】を埋めるための表を `## 経過` に書く。列・並び・チャット側の案の分け方（N 案〈冒頭〉・N 案〈範囲外〉・Y 案）・Y にしたとき落ちるかの判定は、CHAT-1016-LVP-01 の指示の手順3のとおり。案ごとの件数も書く
3. 上の「決定」を docs/decisions/live.md に足す（CLAUDE.md「作業ログ」節の完了時の手順。書き方は docs/decisions/README.md）

止まる条件

* 【2】【3】の読み込みが失敗する、または読み直すたびに行数が変わる（件数を書いて止まる）
* 取り出した行が 100 行から ±10 行を超えてずれる（タイトル戦ごとの件数を書いて止まる。見込みの出どころは上の「前提」）
* #362 に他セッションの着手中コメントがある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、案ごとの件数と、Y 案のうち Y にしても落ちる行（理由つき）を書く
* マージは冒頭の「マージ:」の行のとおり（LVP-01・LVP-02 のログと docs/decisions/live.md）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-LVP-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-LVP-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1016-LVP-02` のコミットは0件。`LVP` は同じセッションの続き（LVP-01）
- 0. 「指示」欄の末尾は指示文の最後の行と一致。LVP-01 の `## 報告` の状態は「判断待ち」
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- work/1016-lvp はリモートにあり、ローカルと一致（2bf94d99a）。`origin/cloudflare` は祖先でない（371e00365 まで進んでいる）ため、ログの push の後に merge で取り込む

## 報告

- 状態: 作業中
- ブランチ: work/1016-lvp
- ログ: https://github.com/retroeater/mj/blob/work/1016-lvp/docs/logs/CHAT-1016-LVP-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1016-lvp
- 確認用URL: なし
- マージ: 未
- issue: #362
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 371e0036）: https://github.com/retroeater/mj-logs/tree/main/guide/371e0036

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/371e0036/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/371e0036/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/371e0036/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/371e0036/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/371e0036/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/371e0036/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/91b5dd30.md
