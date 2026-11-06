# CHAT-1106-PFU-01

- 着手日時: 2026-11-06
- 対象issue: （起票予定）
- ブランチ: work/1106-pfu
- 着手時HEAD: 021cf833

## 指示

【Claude作成】Claude Code 向け指示：「良栄 > 牌譜」（rh_paifu.html）を廃止する（旧 URL は 404） Chat-Ref: CHAT-1106-PFU-01 マージ: 承認済み（チャットで）。ただし「止まる条件」に1つでも当たればマージせずに止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1106-pfu の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1106-pfu を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1106-pfu origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
メニュー「良栄」の「牌譜」（`rh_paifu.html`）を廃止する。ページ・生成・再生成の対象・サイト内のリンクをすべて外し、旧 URL は転送せず 404 にする。
決定（2026-11-06、平野さん）

* 「良栄 > 牌譜」（`rh_paifu.html`）を廃止する
* 旧 URL は転送せず 404 にする（「番組 > English」〈#617〉・「連盟 > リンク」の廃止と同じ扱い。`/r/` へは転送しない）
* 下の「止まる条件」の確かめがすべて通れば、判断待ちにせずそのまま cloudflare へマージしてよい

前提（チャット側。平野さんの決定ではない）

* 牌譜へのリンクがある場所（mj-logs のガイド・ログで読んだもの。実物は要確認）:
   * 共通ナビ「良栄」のドロップダウン（成績・成績詳細・牌譜。CHAT-1104-TOP-11 のログ）と、新しいナビのメニューのシートの「良栄」のまとまり
   * r/ の一覧の「成績」の節（成績・成績詳細・牌譜の3リンク、`docs/decisions/ryoei.md` の CHAT-1103-RYO-07）と、空間の対局室の「牌譜の紙」（`RESULTS`、`docs/notes/ryoei-world.md`「触れる」）
   * 再生成の対象 `rh_paifu`（`regenerate.py --list` の32件、CHAT-1105-MFQ-02 のログ）・生成スクリプト・毎朝の再生成の一覧・sitemap・`llms.txt`・テスト
   * 文書: `docs/notes/static-generation.md`（型A の表）・`docs/notes/site-findings.md`（`img.youtube.com` の依存先・`?name=` の表）・`docs/decisions/README.md` と `ryoei.md` の分野名「良栄（r/・成績・成績詳細・牌譜、#389）」
* 対局室の「牌譜の紙」は、物ごと消す（仮置き。チャット側の案。消すと場面が寂しくなる・置き場所の番号が空くなど気になる点があれば、触れる先だけ外して物を残す方に変えてよく、どちらにしたかを報告に書く）
* 「牌譜」という語はほかのページ（用語・説明文など）にも出る。変えるのは良栄の牌譜ページを指すものだけで、ほかの「牌譜」は変えない
* 牌譜のページの元データ（スプレッドシートのタブなど）は、この指示では消さない。どのブック・タブから読んでいたかを `scripts/` の定数名とタブ名で報告に書く（ID・URL は書かない）。消すかは平野さんがマージ後に決める
* issue: 同じ主題の issue が無ければ「良栄 > 牌譜（rh_paifu.html）の廃止」で起票し、関係する #389 にリンクのコメントを残す（#389 の状態は要確認）。マージと本番の確かめが済んだら、起票した issue をクローズする
* mj-logs の actions/morning.md（2026-11-06 20:25 JST 時点）に出ている異常は「run-weekly.yml の予約実行の未実行」だけ（今回の変更と無関係）

手順

1. 洗い出し: 同じ主題の issue（クローズ済みを含む。検索語「牌譜」「rh_paifu」）と、未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の各ブランチのコミットの件名とログ）を確かめ、同じ目的のものがあれば止まる。 無ければ issue を起票して着手中のコメントを残す。続けて `git grep -n -i -e 'rh_paifu' -e 'paifu'` と `git grep -n '牌譜'` をリポジトリ全体（CLAUDE.md・docs/handover.md を含む）で実行し、除外（`docs/decisions/` の過去の行・`docs/logs/`・`docs/notes/handover-archive-2026.md`）を除いた全件を「消す／書き換える／変えない（別の意味の牌譜）」に分けた表にしてログの `## 経過` に書く（件数と、ファイル名・行の一覧を全件）。
2. 削除と書き換え: 表の「消す／書き換える」を実施する（ページ・生成スクリプト・再生成の対象・毎朝の再生成の一覧・共通ナビとメニューのシート・r/ の一覧と対局室・sitemap・`llms.txt`・テスト・文書）。`_redirects` と `docs/notes/redirects.md` には足さない（404 のため）。 文書は `docs/notes/static-generation.md` の表に「旧 `rh_paifu.html` は廃止し転送しない（404、2026-11-06、#<起票した issue>）」の形で足し（`video_en` の行と同じ書き方）、`docs/decisions/` には「決定」節を足す。分野名の「牌譜」は外してよい（過去の行は書き換えない）。 共有の部品（ナビ・`scripts/lib/`）を変えたら CLAUDE.md「判断・作業の原則」のとおり全ページを再生成する。待つ時間は同じセッションで合計45分まで延長してよい。
3. 確かめとマージ: 下の「止まる条件」をすべて確かめ、通ればマージする。マージ後、本番で `/rh_paifu.html?v=<未使用の値>` が 404、トップ・r/・houou/ の共通ナビの「良栄」に牌譜が無いことを確かめる（本番への要求の UA に `mj-internal` を含める）。 15分で本番が古いままなら判断待ちにせず「未確認の項目」に回して完了にする。確かめが済んだら起票した issue をクローズする（「状況:」ラベルがあれば外す）。

止まる条件

* 同じ目的の issue か未マージの work/ ブランチがある
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）。対象が複数なら重なる対象だけ外して進め（外したものと理由を報告に書く）、全部が重なるときだけ止まる。この場合はマージしない
* 手順1の表の「消す／書き換える」を実施した後、`git grep` で良栄の牌譜ページへの参照が1件でも残る（除外は手順1と同じ）
* テストが1件でも落ちる（自分の変更で落ちると分かっているテストは直してよい）
* プレビューで `/rh_paifu.html` が 404 にならない（転送・200 になる）
* 差分に、牌譜の削除と、それによる再生成の変化（ナビ・r/ のリンクが外れたこと、sitemap・`llms.txt` の行が消えたこと）で説明できない行がある。 再生成のページの差分は、同じデータで origin/cloudflare のコードと作業ブランチのコードの両方で生成して比べ、牌譜の部分以外の差があれば全行をログに書いて止まる（シートの変化が混ざるため）
* r/ の重さが `docs/notes/ryoei-world.md`「重さ」の上限（最初の画面 600KB・`world.js` 150KB、gzip）を超える
* マージ後の check-run・手動実行が今回の変更で失敗する（無関係な失敗なら原因を報告に書いたうえで残りの手順を進めてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（元データのブック・タブの定数名とタブ名、対局室の「牌譜の紙」をどうしたかを含める）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1106-PFU-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1106-PFU-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1106-pfu
- ログ: https://github.com/retroeater/mj/blob/work/1106-pfu/docs/logs/CHAT-1106-PFU-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1106-pfu
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 82fe7190）: https://github.com/retroeater/mj-logs/tree/main/guide/82fe7190

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/82fe7190/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/82fe7190/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/82fe7190/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/82fe7190/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/82fe7190/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/82fe7190/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/82fe7190.md
