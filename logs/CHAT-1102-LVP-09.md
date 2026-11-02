# CHAT-1102-LVP-09

- 着手日時: 2026-11-02
- 対象issue: #362
- ブランチ: work/1017-lvp
- 着手時HEAD: e1181557b

## 指示

【Claude作成】Claude Code 向け指示：pro/ が live/ の HTML を読まずに放送対局の行を作るように直し、行4244 を確かめて、live/ の作り直しを未公開のまま cloudflare へマージする Chat-Ref: CHAT-1102-LVP-09 マージ: 承認済み（チャットで） 貼る時機: CHAT-1102-LVP-08 の中断の後（同じセッションの続きでも新しいセッションでもよい。毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-lvp の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1017-lvp を続けて使う（CHAT-1017-LVP-06・CHAT-1102-LVP-08 の作業があるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。origin/work/1017-lvp に、CHAT-1102-LVP-08 の報告（e1181557b）より後のコミットが毎日の再生成（`chore: regenerate ...`）のほかにあれば、ログに書いて止まる。

目的
CHAT-1102-LVP-08 が止まった2つの理由を片付け、live/ の作り直しを cloudflare に入れる。live/ は noindex のまま、navbar・サイトマップにも出さない（公開は次の指示）。
決定（2026-11-02、平野さん）

* pro/ の放送対局の行は、live/ のトップの HTML を読まず、live/ の生成と同じ読み込み（`generate_live_pages.load()`）の結果から作る（LVP-08 の報告の案 (b)）。この指示で直す
* 【3】の行4244（`FXtYzZBEtXA`、【メンバー限定】第２期 昇龍戦）は平野さんが直した（2026-11-02）
* 直しと確かめが通れば、work/1017-lvp を cloudflare にマージしてよい（2026-11-02 の承認のまま。live/ は未公開のまま）

前提（チャット側。平野さんの決定ではない）

* 平野さんへは、行4244 をステージ「決勝」、まとめ単位「ベスト8、決勝」にする案（行4247 と同じ形）を伝えた。実際に入った値は要確認
* `generate_pro_pages.py` は今、`load_lives()`・`parse_lives()` で live/index.html のカード（`mj-video-card`）を正規表現で読み、`load_roles()` では既に `live_pages.load()` を呼んでいる（2026-11-02 の cloudflare で確かめた）。案 (b) は、カード（href・題・日付・サムネイル・検索の key）を `load()` の Page から作り、HTML を読む部分と「先に live_pages を生成する」順の依存を消す形。`load()` を2回呼ばないようにする
* 件数（`live_cards()` の count）は、live/ の検索（`?q=`）と同じ部分一致のまま（決定 K3）。key は `generate_live_pages.search_text()` から作る
* 2026-11-02 の時点で、`scripts/generate_pro_pages.py` を変えている未マージの work/ ブランチは無い（要確認）。pro/ の公開の準備（PRP）のチャットには、チャット側からこの直しを伝える
* houou/ の4ファイルの差は、LVP-08 の比べで比べる相手側の生成が一部のページを飛ばしたためとみられる（要確認）

手順

1. #362 に着手中のコメントを書く（本文・状態・ラベルは変えない）。CHAT-1102-LVP-08 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1102-LVP-09` を足す。origin/cloudflare を取り込む（衝突の扱いは LVP-08 の手順1と同じ。生成されたページは生成し直す。追記どうしの文書は両方を残す。それ以外は解かずに止まる）。【3】の行4244 を動画ID で読み、ステージ・まとめ単位の値と、`--check live_pages` で作られるページ（URL）と URL の重なりの有無を書く
2. 上の決定どおり `generate_pro_pages.py` を直し、テスト（`scripts/tests/`）を足す・直す。確かめは「pro/ の放送対局の欄の中身が、cloudflare の今の版（live/ が旧の形のとき）と同じ」こと: cloudflare の版で生成した pro/ と、直した後の版で生成した pro/ を全ファイル比べ、放送対局の欄（選手・実況・解説のカード、件数、`/live/?q=` のリンク）の差を0にする（差が出たら件数と例を書く。live/ の行の題・日付の書式が作り直しで変わったせいの差なら、その旨と全件の数を書く）。houou/ を含む全ページの新旧の比べをやり直し、live/・pro/ 以外の差の理由を書く。`python3 -m unittest discover -s scripts/tests` を回す
3. 作業ブランチに push し、プレビューで pro/ の個別ページ2つ（放送対局の多い人と、実況・解説だけの人）と `/live/shouryu/2.html` を確かめる（確認用の URL は最終報告にだけ書く）。上の「決定」を docs/decisions/live.md と docs/decisions/pros.md に足す。止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージする。マージ後、Workers Builds の check-run の結果と、本番の `/live/` の HTML に noindex が残っていることを確かめる（CLAUDE.md「判断・作業の原則」の確認範囲で書く）

止まる条件

* 0章の確かめに当たる
* 手順1の解き方に当てはまらない衝突がある
* 行4244 が公開から落ちる、または URL の重なりがある（理由を書いて、マージせずに止まる。直すのは平野さん）
* pro/ の放送対局の欄に、作り直しの書式で説明できない差が出る。live/・pro/ 以外の生成物に、取り込みで説明できない差が出る
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが、`scripts/generate_pro_pages.py` の `load_lives()`・`parse_lives()`・`live_cards()`・`load_roles()`、または LVP-08 の止まる条件のファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* #362 に他セッションの着手中コメントがある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、行4244 で作られたページと、pro/ の比べの結果（差の件数）を書く
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1102-LVP-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1102-LVP-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1102-LVP-09` のコミットは0件。`LVP` は同じセッションの続き
- 0. 「指示」欄の末尾は指示文の最後の行と一致。origin/work/1017-lvp に e1181557b より後のコミットは無い
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1017-lvp
- ログ: https://github.com/retroeater/mj/blob/work/1017-lvp/docs/logs/CHAT-1102-LVP-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-lvp
- 確認用URL: なし
- マージ: 未
- issue: #362
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1819ca0c）: https://github.com/retroeater/mj-logs/tree/main/guide/1819ca0c

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1819ca0c/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1819ca0c/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1819ca0c/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1819ca0c/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1819ca0c/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1819ca0c/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/29bf0a43.md
