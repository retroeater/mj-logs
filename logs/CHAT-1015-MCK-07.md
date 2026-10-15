# CHAT-1015-MCK-07

- 着手日時: 2026-10-15
- 対象issue: なし（関係: #365）
- ブランチ: work/1015-mck-alias
- 着手時HEAD: 281628d5b

## 指示

【Claude作成】Claude Code 向け指示：成績のページで「別名」を通して今の登録名で表示する直しの下調べ（どのページが別名を通しているか・WRC で通す案） Chat-Ref: CHAT-1015-MCK-07 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1015-MCK-06 と並行してよい。ブランチは別） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-mck-alias の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1015-mck-alias を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-mck-alias origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
WRC 第18・19期の TSV（CHAT-1015-MCK-05）で、公式と JWRC の既存の行は「樫野凪」、「ryoei.pro_選手」の「別名」タブでは「樫野凪 → かしのなぎ（登録名変更）」だった。平野さんの決定は「成績データは当時の表示のまま、別名を通して全部の期を『かしのなぎ』で表示する」。この指示では実装せずに、今どのページが「別名」を通しているか、WRC のページ（Google Charts でシートを直接読む）で通すにはどうするかを調べ、実装の指示を作れる案をログに書く。
決定（2026-10-15、平野さん）

* 成績データ（JWRC タブなど）は当時の表示のまま残す（シートの名前は書き換えない）。表示のときに「別名」を通して、全部の期を今の登録名（例: 「かしのなぎ」）で出す
* 第18・19期の TSV は「樫野凪」のまま貼る（MCK-05 のとおり）

前提（チャット側。平野さんの決定ではない）

* MCK-05 のログ「名前の表記」: 「別名」タブは `lib/pro_sheet.py` のブック（ryoei.pro_選手）にあり、「樫野凪 → かしのなぎ（区分: 登録名変更）」の行がある。WRC のページ（`wrc_results.js`・`league_ranking.js` の JWRC）は JWRC タブを直接読み、「別名」を通さない（要確認）
* ほかのページ（title/ など Python で生成するページ）は生成のときに「別名」を通している見込み（要確認。CHAT-1014-JPL 系列で WRC の旧名の読み替えの話が出ている。JPL の最新のログも読む）
* 決定は「全部の期を今の登録名で」なので、ページの名前の絞り込み（`?name=`）・ランキングの集計も、今の登録名で1人にまとまるのが望ましい（チャット側の解釈。要確認の点として案に書く）
* WRC だけでなく、同じ形のページ（鳳凰・桜花の成績とランキング）にも同じ問題があるかもしれない。対象を広げるかは平野さんが案を見て決める

手順

1. 「別名」タブの読み方（`lib/pro_sheet.py` など）と、「別名」を通しているページ・通していないページを洗い出し、表にする（ページ・生成の方式〈Python の生成／JS でシートを直接読む〉・別名を通すか・通し方）。「別名」タブの区分ごとの件数と、成績のタブ（鳳凰・桜花・JWRC など）に別名の左側（旧名）が実際に出る件数をタブごとに数え、名前の一覧も書く。CHAT-1014-JPL 系列の最新のログを読み、同じことを進めている指示があれば、その範囲と状態を書く
2. JS でシートを直接読むページ（少なくとも WRC の2ページ）で別名を通す案を2つ以上書く（例: 生成のときに別名の対応表を JSON にしてページが読む、JS が「別名」タブも gviz で読む、など）。案ごとに、外部ドメインや CSP（#9）への影響、名前の絞り込み・ランキングの集計がどう変わるか、変わるファイル、テストの足し方、良い点・悪い点を書き、推奨とその理由を書く
3. 対象を WRC だけにするか、同じ問題のある鳳凰・桜花のページにも広げるかの判断材料（手順1の件数）を「判断が必要なこと」に書く。#365 に着手中のコメントは不要（この指示は #365 の作業ではない）。関係しそうな open issue があれば番号を書く（起票はしない）

止まる条件

* 別のセッションが同じ直し（JS のページで別名を通す）を進めていて、未マージのブランチがある（範囲と状態を書いて止まる）
* 「別名」タブを読めない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-MCK-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-MCK-07 を書く

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

ガイド文書（この版を写した時点の最新、mj 281628d5）: https://github.com/retroeater/mj-logs/tree/main/guide/281628d5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/281628d5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/281628d5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/281628d5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/281628d5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/281628d5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/281628d5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/66464371.md
