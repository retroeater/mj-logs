# CHAT-1103-TOP-03

- 着手日時: 2026-11-03
- 対象issue: #101, #287
- ブランチ: work/1103-top
- 着手時HEAD: 8b462f533

## 指示

【Claude作成】Claude Code 向け指示：新トップ top/ の直し（「新着の番組」・説明文）と、TOP-02 の仮置きへの回答の記録（#101） Chat-Ref: CHAT-1103-TOP-03 マージ: 判断待ちで止まる（直した後のプレビューを平野さんが見て決める） 貼る時機: CHAT-1103-TOP-02 の後（判断待ちで止まっていること） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1103-top への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1103-top を続けて使う（TOP-02 の top/ を直すため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1103-TOP-02 のログの `## 報告` の状態が「判断待ち」で、origin/work/1103-top が cloudflare に未マージであることを確かめる（違えば止まる）。TOP-02 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1103-TOP-03` を足す。

目的
TOP-02 のプレビューを見た平野さんの回答（仮置き13項目のうち番組と説明文、大会日程の判定の確かめ）を反映する。あわせて、PRP 系列から届いた「お気に入り」★と予定の JSON の形を #287 に書き写す。
決定（2026-11-03、平野さん。TOP 系列のチャットで）

* TOP-02 の「番組」の面（帰り道・プロテスト・Mつくの3行、2行目が「番組」）をやめ、見出し「新着の番組」の面にする。3番組（帰り道・プロテスト・Mつく）を横断し、公開日の新しい順に3件まで出す
* ページ末尾の説明文（description も同じ文なら同じ）に WRC-R を足す。大会日程のチップは title/ の大会名（「WRC-Rリーグ」）のまま
* h1「ryoei.pro」、大会日程の5件と「ほかN件」、誕生日の面（名前・所属・入会期、前後3日を焼く）は TOP-02 のまま
* 大会日程の段の判定（TOP-02 の仮置き）は今の規則のままでよい。平野さんが確かめた結果: 2026-11-14・15 の「麻雀グランプリMAX」は、実際は別の大会「コスモビューティーグランプリ」（掲載対象外）。2026-11-08 の「第4期達人戦」は予選（外すのが正しい）
* 鳳匠戦をタイトル戦（title/）に加える。表示順は鸞和戦の次、slug は `hosho`（作業はこの指示に含めない。次の指示で `_redirects` などを先に入れてから、平野さんが「タイトル戦」タブに行を足す）

前提（チャット側。平野さんの決定ではない）

* 「コスモビューティーグランプリ」が「麻雀グランプリMAX」として読まれているのは、連盟の予定表の取り込み（`yotei.event_of()`・`yotei.EVENTS` の語。docs/notes/yotei-sheet.md）の読み違いの見込み（要確認）。公開カレンダー「mj_放送対局」の 2026-11-14 の予定の件名は「麻雀グランプリMAX」、対局者は女流プロ12名と「大阪予選1位」（チャット側が 2026-11-03 にカレンダーで読んだ）。トップでは段の語が無いので出ないが、カレンダー・pro/ の今後の予定・live/ には誤った大会名で出る。この指示では直さず、issue に残す（直すのは予定表・カレンダーの系列）
* 「新着の番組」の元: 各番組の生成が読む回の一覧と公開日（帰り道は `lib/wayhome.py` の `fetch_rows()` と `data/youtube_meta.json` の `publishedAt`、Mつく・プロテストも同じ形の見込み。docs/notes/video-wayhome.md・video-mtsuku.md・video-exam.md。要確認）。行の形（仮置き）: 日付（「2026年8月27日」の形。帰り道のカードと同じ書き方）・番組のチップ（帰り道・プロテスト・Mつく）・回の題。行き先は各話のページ。面の位置は「今日が誕生日の選手」と「各セクション」の間（仮置き）。導線に「番組」の行は置かない（仮置き。navbar の「番組」のドロップダウンはそのまま）
* 女流勉強会（`study/`、未公開 #576）は3番組に含めない
* PRP 系列からの伝言（2026-11-03、CHAT-1103-PRP-07 のログ `## 報告` が根拠）: ★と予定の JSON は pro/ の公開（#641）に含め、作業ブランチ work/1101-prp-pub にある（cloudflare に未マージ）。pro/ のトップにも「お気に入り」の絞り込みを置く。localStorage のキー `mj:favorites`、値は `[{"slug": "<slug>", "name": "<足したときの現在名>"}, …]` の JSON の配列（足した順・上限なし・壊れた値は空として読む）。予定の JSON は `/pro/plans.json`、形は `{"plans": [{slug, name, date, kind, role, title, href}, …]}`（`date` は日本時間の `YYYY-MM-DD` か `YYYY-MM-DDTHH:MM`、`kind` は 放送・最強戦・道場部、`role` は放送の実況・解説のときだけ、日付の近い順、過ぎた行は読む側が隠す、毎朝の `pro_pages` の生成で更新、2026-11-03 は 24,341 バイト・124行・85名）。★の読み上げの名前は「お気に入り」で一定、状態は `aria-pressed`。TOP は `generate_pro_pages.py`・`assets/pro.js`・pro/ 配下を変えない

手順

1. 記録: `docs/decisions/top.md` に上の「決定」を足す。#287 に、上の PRP からの伝言の「形」をそのままコメントで書き写す（出典として「CHAT-1103-PRP-07 のログ `## 報告`」を書く。本文・状態・ラベルは変えない。TOP-01 の着手中コメントがまだ残っていれば解除の一文も）。「コスモビューティーグランプリ」の読み違いは、同じ論点の issue（「グランプリ」「予定表」「event_of」「大会名の読み違い」）をクローズ済みも含めて検索し、あればコメント、無ければ起票する（題の案「予定表の取り込みで『コスモビューティーグランプリ』が『麻雀グランプリMAX』と読まれる」、2026-11-14・15 の件名・対局者・影響する画面〈カレンダー・pro/ の今後の予定・live/〉を本文に。ラベルは `分野: 自動化` と `対象: live` があれば付ける）。直しはしない
2. top/ の直し: 「番組」の面を「新着の番組」に置き換える（元のデータの所在を `## 経過` に書いてから。3番組の回を公開日で並べ、新しい3件。行の形と位置は上の仮置き）。説明文に WRC-R を足す。テスト（`scripts/tests/test_top_page.py`）に新着の番組の並び・件数・行き先を足す。`docs/notes/top-page.md` を直す。毎朝の再生成で鮮度を保てるか（3番組の回の一覧が毎朝の再生成のどこで更新されるか、トップの生成がそれより後か）を確かめ、保てなければ止まらず「判断が必要なこと」に書く
3. 検査・自己レビュー（docs/notes/self-review.md）・`regenerate-page.yml` の作業ブランチでの手動実行（`target_page: top_page`）・画面の確かめ（360px・400px。新着の番組のいちばん長い題）を流し、ブランチ名の別名 URL でプレビューできる状態にして止まる

止まる条件

* TOP-02 が判断待ちでない、または work/1103-top がマージ済み
* 3番組の回の一覧・公開日を、各番組の生成と同じ元から読めない（読む元を一覧にして止まる）
* 予定表の読み違いと同じ論点の issue に、他セッションの着手中コメントがある（コメントだけ足して進めてよい。起票はしない）
* 未マージの work/ ブランチが `scripts/generate_top_page.py`・`docs/notes/top-page.md`・`scripts/tests/test_top_page.py` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* top/ 以外のページの生成物に差分が出た（見込みは0件）
* cloudflare への push が権限判定で拒否された（この指示ではマージしないので、起きれば手順の誤り。別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-TOP-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-TOP-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d2277ebe）: https://github.com/retroeater/mj-logs/tree/main/guide/d2277ebe

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e4049b27.md
