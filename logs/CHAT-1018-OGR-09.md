# CHAT-1018-OGR-09

- 着手日時: 2026-10-18
- 対象issue: #517, #329, #581（コメント: #362, #591）
- ブランチ: work/1018-ogr
- 着手時HEAD: efdeccaf2

## 指示

【Claude作成】Claude Code 向け指示：apply_page_meta.py を消し（#517）、#329・#517・#581 を閉じる Chat-Ref: CHAT-1018-OGR-09 マージ: 承認済み（チャットで）。ただし「止まる条件」の差分の範囲を外れたら止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1018-ogr の作成と push、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1018-ogr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1018-ogr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。

目的
OGP の横断レビュー（#581）の締めくくり。OGP に関わる残りの issue のうち #329・#517 を片付け、#581 を閉じる。#581 に残る作業は担当の系列の issue に移す。
決定（2026-10-17〜18、平野さん）

* #329（帰り道の各話に twitter:player を入れる）は行わずに閉じる。og:video は入っていて画像のカードで足りる。再生用ページを作ると生成物・サイトマップ・ほかの動画のページへの広げ方の考えごとが増える。X で再生させたいときは YouTube の URL を直接貼る（2026-10-18）
* #517 は (a)。`scripts/apply_page_meta.py` を消す。生成ページの説明文の正は各生成スクリプトの `META`（`PageMeta`）、手書きのページは HTML を直接直す（2026-10-18）
* CHAT-1017-OGR-08 の後、平野さんが X・LINE で最強戦の対局のページ（`saikyo/2019/20190223.html`）と卓のページ（`saikyo/2019/20190223-s1-a.html`）を確かめた。対局は作品「麻雀最強戦」の画像、卓は回のサムネイルが出て、帯は「ベスト32 A卓 | 女流プレミアトーナメント 華麗な技 | 麻雀最強戦2019 | ryoei.pro」まで見える（2026-10-18）。#581 の論点 (e) の「最強戦の卓・対局のページのカードの見え方」はこれで済み
* #581 は閉じる。残る作業は担当の系列の issue へ移す: live/ のトップ・大会・期の og:image を最新話のサムネイルにする件（LVP の B9）は #362、ouka/ の「女流桜花」の OGP 画像（2026-10-17 の決定、OUK のチャットへ伝言済み）は #591。女流勉強会の og:title・og:image の確かめは #576 のチェックリストに既にある（2026-10-18）
* プレビューを見ずにマージしてよい（ページの差分は出ない見込み）

前提（チャット側。平野さんの決定ではない）

* `apply_page_meta.py` はワークフロー・ほかのスクリプトから呼ばれない手動の保守用スクリプト（#517 本文、2026-10-07 時点。要確認）。参照は `docs/notes/static-generation.md`「メンテナンス用スクリプトの詳細」の1行と、`generate_resource_dictionary.py`・`generate_video_wayhome.py` の「同じ文言にする（崩すと次の実行で差し戻る）」のコメント（#517 本文）。CHAT-1016-OGR-03・04 でこのスクリプトの `build_meta()` の og:locale・og:image を直した。#517 のコメント（CHAT-1009-SWP-09）に `PAGES` の `rh_links.html` の項目の件がある（消せば片付く）
* 手書きのページ（index・jpml_links・houou_ranking・houou_results・ouka_ranking・ouka_results・wrc_ranking・wrc_results の8ページ、CHAT-1016-OGR-03 のログ）は今の HTML のまま（このスクリプトを消しても変わらない）
* #329 の本文の「進め方」（1話だけ試す）は行わない

手順

1. `git grep apply_page_meta`（docs/logs・docs/decisions を除く）で参照を洗い出し、スクリプトを消して、参照（生成スクリプトのコメント・docs/notes・CLAUDE.md や handover にあればそれも）を「生成ページは生成スクリプトの `META` が正、手書きのページは HTML を直接直す」に直す。テストを通す（このスクリプトを読むテストがあれば消す）。再生成して生成物の差分が0であることを確かめる
2. 記録と issue: 上の「決定」を docs/decisions/seo-bing.md の OGR の節に足し、docs/notes/ogp.md の規則の節に「残りの作業の行き先」（#362・#591・#576）を1行で足す。#362 と #591 に、移した作業を1行ずつコメントする（#362: B9 は 2026-10-16 の LVP の決定のまま、OGP の規則〈docs/notes/ogp.md〉と矛盾しない旨。#591: 2026-10-17 の「女流桜花」の OGP 画像の決定と docs/notes/ogp.md の参照）。#329・#517・#581 に決定のコメントを書いて閉じる（「状況:」ラベルがあれば外す）
3. cloudflare へマージする

止まる条件

* `apply_page_meta.py` がワークフロー・`regenerate.py`・ほかのスクリプトから呼ばれていた（呼び出し元を書いて止まる）
* 差分（docs/ を除く）が、スクリプトの削除・コメントの直し・テストの変更の範囲を外れる。生成物（HTML・サイトマップなど）の差分が出たら、生成のたびに入るシートの変化で説明できるもの以外は止まる
* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の一覧）が `apply_page_meta.py` を変えている、または取り込みで衝突する
* #362・#591・#576 のいずれかが閉じていた（コメントは書かずに報告し、ほかは進めてよい）
* マージ後の check-run の失敗は、今回の変更によるものなら止まる。無関係なら原因を報告に書いたうえで残りの手順を進めてよい
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（閉じた issue とコメントした issue を書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-OGR-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-OGR-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

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

ガイド文書（この版を写した時点の最新、mj efdeccaf）: https://github.com/retroeater/mj-logs/tree/main/guide/efdeccaf

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/efdeccaf/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/efdeccaf/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/efdeccaf/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/efdeccaf/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/efdeccaf/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/efdeccaf/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/efdeccaf.md
