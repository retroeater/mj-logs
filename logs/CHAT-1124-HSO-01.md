# CHAT-1124-HSO-01

- 着手日時: 2026-11-24
- 対象issue: なし
- ブランチ: work/1124-hso
- 着手時HEAD: d005b2a

## 指示

【Claude作成】Claude Code 向け指示：放送対局 鳳匠戦の期のページのステージの並び（決勝→ベスト16→ベスト8）の原因を調べ、STAGE_ORDER に鳳匠戦を足す Chat-Ref: CHAT-1124-HSO-01 マージ: 承認済み（チャットで） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1124-hso を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1124-hso origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
本番の https://ryoei.pro/live/housho/1.html （第1期鳳匠戦）で、ステージが「決勝 → ベスト16 → ベスト8」の順に並んでいる。原因を確かめ、今後の期も「ベスト16 → ベスト8 → 決勝」の順になるよう恒久的に直す。
決定（2026-11-24、平野さん）

* 原因の切り分けは Claude Code に任せる
* 直し方は恒久策（コード側で鳳匠戦のステージの並びを決める）。【3】に手で番号を入れる暫定策は取らない

前提（チャット側。平野さんの決定ではない）

* 2026-11-24 17:24 JST にチャット側が Chrome で見た本番のページの並び: 決勝（2026年11月20日更新）→ ベスト16 A〜D卓 → ベスト8 A・B卓
* チャット側の推測: 鳳匠戦が `scripts/lib/live_extract.py` の `STAGE_ORDER` に無く（要確認）、【2】の「ステージの並び」が空か、決勝にだけ別の値が入っているため（要確認）。根拠は docs/notes/live-channel-write.md の「表に無い段階は確認=Y・理由『ステージの並びの表に無い段階』」と、鳳匠戦が 2026-11-03 ごろに title/ に加わった新しい大会であること（docs/decisions/title.md・top.md）。コードは開いていない
* 期のページの中はキーの順（docs/notes/live-page-design.md、判断 LV-03）
* CHAT-1123-LVP-36（work/1123-lvp-ns、マージ済み 31b9c5359）が `STAGE_ORDER` に日本シリーズの段階を足している。LVP の系列は別のチャットが進めている
* 鳳匠戦の段階の並びの案: ベスト16 → ベスト8 → 決勝（第1期の段階はこの3つ。ほかの段階が【1】【2】にあれば同じ考え方〈早い段階が先〉で入れ、報告に書く）

手順

1. 【2】【3】の鳳匠戦の全行（ステージ・ステージの並び・卓・確認・理由）を読み、`STAGE_ORDER` と期のページの並びの決め方（関数名と行番号）から、並びが「決勝 → ベスト16 → ベスト8」になる原因を確かめてログに書く。原因が上の推測と違えば、手順2に進まず止まる
2. `STAGE_ORDER` に鳳匠戦（ベスト16 → ベスト8 → 決勝）を足し、テストを足す。今の【1】から【2】を作り直したと仮定した結果で、値が変わる【2】の行を全件書き、live_pages を生成して変わるページの URL を全件書く（live/housho/1.html のステージの並びも）。テストを回す
3. 止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、Workers Builds の check-run の結果を確かめる。本番への反映が翌朝の毎朝の取り込み（update-live-channel）を待つのか、今日のうちに反映する手段があるのかを報告に書く（取り込みの手動実行はしない）。「決定」を docs/decisions/live.md に足し、docs/notes/live-channel-write.md の `STAGE_ORDER` の記述に要るなら鳳匠戦を足す（追記先の今の内容を読み、同じ趣旨の記述があれば置き換え・拡張してよい）

スプレッドシートを読み書きする実行（ほかのセッションの分を含む）が無いことを見てから起動する。読み書きするかどうかは Code がワークフローとスクリプトで確かめる。
止まる条件

* 原因がチャット側の推測（`STAGE_ORDER` に鳳匠戦が無い）と違う（原因と直し方の案を書いて、変更せずに止まる）
* 【3】の鳳匠戦の行に「ステージの並び」の手の補正が入っている（値を全件書いて止まる。平野さんが消すかどうかを決める）
* 規則の変更で、鳳匠戦のほかに【2】の値が変わる行がある、または live/housho/ のほかにページが変わる・増える・減る（全件を書いて、マージせずに止まる）
* 【2】【3】の読み込みが失敗する
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが `scripts/lib/live_extract.py` の `STAGE_ORDER` の同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1124-HSO-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1124-HSO-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認
- 同じ Chat-Ref のコミット・`HSO` の重複（コミット・docs/logs の履歴）: 無し。work/1124-hso はローカル・リモートとも無く、origin/cloudflare（d005b2a）から作った
- 0章: 指示欄の末尾が指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠け無し
- 対象 issue: 指示文に無し（着手中コメントは不要）
- 0章ゲート: 未マージの work/ ブランチで `scripts/lib/live_extract.py`・`docs/decisions/live.md`・`docs/notes/live-channel-write.md` を変えるものは無い。`scripts/tests/` は work/1101-prp-pub・work/1103-ryo5・work/1115-rsc-autowrite が別のテストファイルを変えている（test_live_extract.py は無し）
- CLAUDE.md 23,299 B・handover.md 23,619 B（この指示では触らない）
- 実行中・待ちのワークフロー: 無し（08:54 UTC に確かめてから起動）。起動したのは regenerate-page.yml の dry_run だけ（シートは読むだけ）

### 手順1: 原因
- 期のページの並び: `scripts/generate_live_pages.py` の `build_pages()`（405行〜）が `Page.key`（194行、`(title_order, 期の並び, stage_order, table_key(卓))`）で並べ、`build_period()`（924行〜）がその順でステージごとに最初に出た順にまとめる。`Page.stage_order`（187行）は【2】【3】を合わせた「ステージの並び」を `live.number_key()`（scripts/lib/live.py 133行）で数にする。空は数でないので `(1, 0.0, "")` になり、全ステージが同じ値になる
- そのため卓で決まり、卓の無い決勝（`table_key("")` = `(0, 0, "")`）が先頭、続いてベスト16・ベスト8（どちらも A 卓から。同じ値のときは並べ替え前の順で、ベスト16 が先に出る）→「決勝 → ベスト16 → ベスト8」
- 【2】の値は `scripts/lib/live_extract.py` の `analyze()`（330行）が `STAGE_ORDER.get(war, {}).get(stage, "")` で入れる。`STAGE_ORDER`（93行〜）に鳳匠戦が無い
- 【2】【3】の鳳匠戦の全行（run 45360223046、一時のコミット b8c4e24c5 で行を出した → 6f2edf092 で戻した）:
  - 【3】15行（4304・4305・4307・4309・4318・4319・4322・4323・4340・4341・4345・4347・4350・4396・4397行目）: 補正の列（タイトル戦・期・ステージ・ステージの並び・卓・まとめ単位）はすべて空。掲載は Y 7行・N 6行・空 2行（決勝の 4G2HocWBcic・M5h0gbcCvt0。自動の値を使う）→ 「ステージの並び」の手の補正は無い
  - 【2】15行: ステージの並びはすべて空、確認=Y、理由は全行「ステージの並びの表に無い段階」（うち7行は「掲載（自動）N: 同じ対局のメンバー限定版がある（冒頭のみ無料の公開版）」も）。ステージはベスト16（A〜D 各2本）・ベスト8（A 2本・B 3本）・決勝（2本）で、ほかの段階は無い
- 原因はチャット側の推測（STAGE_ORDER に鳳匠戦が無く、【2】の並びが空）と一致 → 手順2へ

### 手順2: 実装（3b657c6c6）
- `STAGE_ORDER` に `"鳳匠戦": {"ベスト16": "1", "ベスト8": "2", "決勝": "3"}` を足した
- テスト `StageOrderTest.test_housho_best16_best8_final`（実際の題 3本: ベスト16A卓・ベスト８A卓・第１期…決勝戦）。変更前のコード（鳳匠戦を表から除いた状態）で 3件とも落ちることを確かめた
- `python3 -m unittest discover -s scripts/tests`: 2,314件 OK

### 【2】の値の比べ（リポジトリの層1 = 【1】の全件、変更前後のコード、名前の辞書なし）
- 14,414行のうち値が変わるのは鳳匠戦の15行だけ。掲載（自動）は変わらない
  - ベスト16 8行（dFOIYiIeQy4・lRfK1G89L-M・YkDnyZIYa3c・hEcZKGw62pA・Zw-a4z12f1k・mUManqtka4c・FOC5NloMEoI・P4r2rzuAKSk）: ステージの並び 空→1、確認 Y→空、理由「ステージの並びの表に無い段階」→空
  - ベスト8 5行（-oAfJvStOIo・0kARNlbHNKU・WqTGuY4SW-Y・vvnxp53q8Fw・urnHaqf-6mw）: 空→2、同上
  - 決勝 2行（4G2HocWBcic・M5h0gbcCvt0）: 空→3、同上
  - （理由に「掲載（自動）N: …」が付く7行は、その部分は残る）

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d005b2a1）: https://github.com/retroeater/mj-logs/tree/main/guide/d005b2a1

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d005b2a1/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d005b2a1/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d005b2a1/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d005b2a1/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d005b2a1/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d005b2a1/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/d005b2a1.md
