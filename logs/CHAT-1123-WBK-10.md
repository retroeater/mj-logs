# CHAT-1123-WBK-10

- 着手日時: 2026-11-22
- 対象issue: #408, #684
- ブランチ: work/1123-wbk
- 着手時HEAD: 8f508ee04

## 指示

【Claude作成】Claude Code 向け指示：WBK の締めとして、平野さんの決定を記録し、「タイトル」に手で足した第8期麻雀マスターズの2〜5位を生成と同じ経路で確かめ、#408・#684 に WBK-09 の結果をコメントする（WBK-09 の続き） Chat-Ref: CHAT-1123-WBK-10 マージ: ドキュメントのみ（ログ・決定の追記）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1120-WBK-09 のマージの後。マージは済んでいる） セッション: 任意（クラウドのセッション。WBK の PC のローカルのセッションには貼らない） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1123-wbk を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1123-wbk origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」） 取り込みの衝突: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1120-WBK-09（判断待ち）の続きで、Wayback Machine の調査（系列 WBK）の締め。WBK-09 の判断待ちへの平野さんの回答を記録し、平野さんが「タイトル」に手で足した第8期麻雀マスターズの行を、生成と同じ読み取りの経路で確かめる。#408・#684 に WBK-09 の結果をコメントする。シート・候補タブ・コード・ページは変えない。
決定（2026-11-23、平野さん）

* WBK-09 の (1)（第8期麻雀マスターズの2〜4位を「タイトル」に書くか）: 平野さんが「タイトル」に手で追記した。ただし「表示」は N にした（第8期しか分からず、前後の期と書き方をそろえられないため）。荒正義を何位にしたかは、平野さんが書いた行のとおり（この指示で読んで記録する）
* WBK-09 の (2) の食い違い1行（王位戦 第24期 1位）: 「タイトル」の「井光一郎」が正しい（平野さんが今の公式の王位戦のページ https://www.ma-jan.or.jp/title-fight/ouisen.html で確かめた）。「タイトル」・「別名」は変えない。2000年のページの「井光一朗」は古いページの表記の違いとして扱う
* WBK-09 の (3)（#408・#684 へのコメント）: 推奨の (a)（(1) の結果とあわせて、この指示で1回でコメントする）

前提（チャット側。平野さんの決定ではない）

* 「タイトル」の「表示」が N の行は、ページ・入口・sitemap に出ない（docs/notes/title-pages.md）。なので、手で足した行で第8期麻雀マスターズの期のページは変わらない見込み（要確認）
* 確かめは、生成と同じ読み取りの経路（`generate_title_pages` がシートを読む関数）で「タイトル」を読み、第8期麻雀マスターズの行（表示 Y・N のすべて）を全件の表にして、WBK-09 のログの `## 経過` の第8期麻雀マスターズの表（河野高志 +43.3・多井隆晴 -5.3・小林剛 -47.4・荒正義〈足切り〉）と突き合わせる。名前は「別名」で現在名に直して比べる。点の列があれば点も比べる
* 小林剛（麻将連合）は連盟の選手ではない。「別名」・選手の一覧に無くても、それだけでは止めない（ログに書く）
* 系列 WBK はこの指示で締める。#408・#684 は閉じない（#408 は日付が年だけの期が残り、#684 は1位だけの期が残るため。残りは外の資料待ち）

手順

1. 確かめ（書かない）
   * CHAT-1120-WBK-09 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1123-WBK-10` を足す
   * 前提のとおり「タイトル」を読み、第8期麻雀マスターズの行を全件の表（順位・名前・点・表示・そのほか平野さんが書いた列）にし、WBK-09 の表との一致・食い違いを書く。荒正義の順位がどう書かれたかも書く
   * `regenerate-page.yml` を入力 `check` で手動実行し、「タイトル」を読むページに、手で足した行による差分が出ないこと（表示 N のため）を確かめる。差分が出れば、中身を全件書く（再生成はしない）
2. 記録とコメント
   * docs/decisions/automation.md の WBK-01 の節に、上の決定3つと、確かめの結果、系列 WBK を締めたことを追記する（今の内容を読んでから）
   * #408 に、WBK-09 の結果（6ページを読み、325行のうち一致・表記の違い・食い違い・シートに無い・両方に5位なしの数。食い違い1行は「タイトル」が正しいと平野さんが確かめた）をコメントする。#684 に、第8期麻雀マスターズの1〜4位と点・足切りの荒正義、平野さんが「タイトル」に表示 N で足したこととその理由、#684 の残りの期の数をコメントする。数はこのログと WBK-09 のログの `## 経過` のどの表から引用したかを書く。どちらも閉じない

止まる条件

* 「タイトル」に第8期麻雀マスターズの2位以下の行が無い、または行の表示が N でない（書かずに、読んだ表を書いて止まる）
* 手で足した行の名前・点が WBK-09 の表と食い違う（食い違いを全件書いて止まる。シートは直さない）
* `regenerate-page.yml` の `check` で、手で足した行による差分が出た（中身を書いて止まる。再生成しない）
* 差分が docs（decisions・ログ）を超える
* 追記先（docs/decisions/automation.md）の今の内容と矛盾していて、どちらが正か判断が要る（同じ趣旨の記述があるだけなら止めず、置き換え・拡張して報告に書く）
* 未マージの work/ ブランチが、変えるファイルの同じ行・節を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる。同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1123-WBK-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1123-WBK-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手時の確かめ: `CHAT-1123-WBK-10` のコミットは全ブランチに無い。`work/1123-wbk` はローカル・リモートとも無いので、`git checkout -b work/1123-wbk origin/cloudflare` で作った（8f508ee04）。CHAT-1120-WBK-09 は cloudflare にマージ済み（7498ed2a3）。
- 0章: 「指示」欄の末尾は指示文の最後の行と一致。
- 雛形の行（Chat-Ref・マージ・貼る時機・セッション・共通手順）はすべてある。
- 指示文の日付: Chat-Ref は 1123、決定は「2026-11-23」と書かれているが、このセッションの日付は 2026-11-22。チャット側の日付の書き方として扱い、決定の日付は指示文のまま写す。

## 報告

- 状態: 作業中
- ブランチ: work/1123-wbk
- ログ: https://github.com/retroeater/mj/blob/work/1123-wbk/docs/logs/CHAT-1123-WBK-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1123-wbk
- 確認用URL: なし
- マージ: 未
- issue: #408, #684
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7498ed2a）: https://github.com/retroeater/mj-logs/tree/main/guide/7498ed2a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7498ed2a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7498ed2a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7498ed2a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7498ed2a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7498ed2a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7498ed2a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/8f508ee0.md
