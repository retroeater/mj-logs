# CHAT-1016-OGR-01

- 着手日時: 2026-10-16
- 対象issue: 新規起票（#165 にコメント）
- ブランチ: work/1016-ogr
- 着手時HEAD: 91b5dd304

## 指示

【Claude作成】Claude Code 向け指示：OGP 画像の横断レビューの issue を起票する Chat-Ref: CHAT-1016-OGR-01 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1016-ogr の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1016-ogr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1016-ogr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
ryoei.pro の各ページの OGP 画像（og:image）を横断的にレビューし、整合性・統一感のある設定にするための issue を起票する。この指示は起票と現状の一覧まで。レビュー・方針決め・直しは起票した issue で後日行う。
決定（2026-10-16、平野さん）

* ryoei.pro で採用している OGP 画像を横断的にレビューし、整合性・統一感のある設定にする。その issue を起票する
* 方針の例として「YouTube 動画（のページ）は、その動画のサムネイルを使う」等を挙げた（例であり、全ページの方針はこの issue のレビューで決める）

前提（チャット側。平野さんの決定ではない）

* 2026-10-16 にチャット側が REST で issue の題・本文を見た範囲では、同じ目的（現行サイトの og:image の横断レビュー）の issue は見当たらない（要確認）。近いもの: #165（open・保留、新サイトで OGP 画像をページ別に出し分けるか。選手個別ページが主）、#267（open、og:locale）、#517（open、apply_page_meta.py）、#78・#232・#339・#340・#343・#353・#546（closed、各区分の OGP 画像）
* 今わかっている区分ごとの決め方（docs/decisions・docs/notes/ogp.md より。実物は手順2で確かめる）: 共通の `img/ogp.png`（#78、「ryoei.pro」の文字だけ）／帰り道の一覧は合成画像（三田さんの許可を得て作成、#339・#340）／Mつくの一覧は最新話の YouTube サムネイルそのまま（docs/decisions/mtsuku.md。合成は許可が無いため作らない）／title/ と houou/ は黒地・白字の画像（#232、houou/ は #540）／最強戦の年度・対局・卓は ABEMA の作品の og:image（docs/decisions/saikyo.md の 5、#549）
* 平野さんは 2026-10-02 頃、映画「マインドアスリート」の宣材写真で（共通の）OGP 画像を作ることを検討していた（チャット側の控え。issue・docs にあるかは要確認。あれば番号を本文の「関連」に書く）
* 本文の論点の案（チャット側の案。issue には「チャット側の案」と明記して書く）: (a) 動画1本のページは YouTube（ABEMA は ABEMA）のサムネイルをそのまま使うか、帰り道のような合成画像との関係をどうするか (b) 一覧・入口のページの意匠（黒地・白字の型にそろえるか）と、共通の `img/ogp.png` の扱い (c) og:title・og:description・og:type・og:image:width/height・alt の付け方の平仄（#267 の og:locale と一緒に見るか） (d) 対象外（凍結・廃止予定・作り直し予定のページ。2026-10-06 のサイト全体の横断レビューの範囲の決め方にならうか） (e) 確かめ方（docs/notes/ogp.md「試作は比較用の画像だけで判断しない」〈1ページを本番に出し X・LINE で見てから広げる〉）

手順

1. 同じ目的の issue（クローズ済み・コメントの決定を含む。検索語に「OGP」「og:image」「サムネイル」「マインドアスリート」を入れる）と、未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）で OGP を変えているものを確かめる。同じ目的の open issue があれば起票せず止まる。関連する issue・ブランチは番号と題をログの `## 経過` に書く
2. origin/cloudflare の生成済み HTML の og:image（と og:title・og:type の有無）を全ページ分集め、区分（共通画像／区分ごとの画像／YouTube・ABEMA のサムネイル／og:image なし 等）ごとのページ数と代表のページを表にしてログの `## 経過` に書く。凍結中（`regenerate.py` の `FROZEN_FROM_ALL`）・未公開（noindex）のページは印を付ける。区分に入らないものは名前の一覧も書く
3. issue を起票する。題の案「OGP 画像を横断的にレビューし、ページの種類ごとにそろえる」。本文は、目的と平野さんの決定（上の「決定」の2点、2026-10-16）、現状（手順2の表を引用。どのログのどの節かを書く）、論点（上の (a)〜(e) を「チャット側の案」として）、関連（手順1の issue）。ラベルは既存のものから「分野: SEO/AIO」「対象: 全ページ」。期日は書かない。#165 には「現行サイトの横断レビューは #<新番号>」の1行のコメントを書く（本文・状態・ラベルは変えない）

止まる条件

* 同じ目的の open issue がある（番号と題を書いて止まる）
* 手順2で og:image を集められないページの種類がある（生成の仕組みが想定と違う）ときは、集められた分で起票してよい。集められなかった分は名前の一覧を本文とログに書く
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（新しい issue の番号を書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-OGR-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-OGR-01 を書く

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

ガイド文書（この版を写した時点の最新、mj 55ef1c42）: https://github.com/retroeater/mj-logs/tree/main/guide/55ef1c42

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/55ef1c42/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/55ef1c42/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/55ef1c42/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/55ef1c42/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/55ef1c42/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/55ef1c42/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/91b5dd30.md
