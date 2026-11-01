# CHAT-1101-PRP-04

- 着手日時: 2026-11-01
- 対象issue: #641・#159・#139
- ブランチ: work/1101-prp-pub
- 着手時HEAD: 34d74074e

## 指示

【Claude作成】Claude Code 向け指示：pro/ の公開の準備（その2: 旧 URL の転送・選手のリンクの向け替え・検知の付け替え）を同じ作業ブランチに足し、プレビューで止める（マージ＝公開はまだしない）
Chat-Ref: CHAT-1101-PRP-04
マージ: 判断待ちで止まる（このブランチのマージが pro/ の公開になる。live/〈#362〉の公開と告知動画の後に、別の指示でマージする）
貼る時機: CHAT-1101-PRP-03 の完了報告（判断待ち）の後。PRP-03 と同じセッションでよい
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1101-prp-pub を続けて使う（PRP-03 の公開の準備に足すため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしが衝突したら両方を残して解く。生成されたページだけの衝突は、取り込んだ後のスクリプトで生成し直して解く。両方の変更が両立するそのほかの衝突（隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1101-PRP-03 のログの `## 報告` の状態が「判断待ち」であることを確かめ（違えば止まる）、その状態の末尾に ` / 続き: CHAT-1101-PRP-04` を足して push する。

## 目的
pro/ の公開（#641）のうち、PRP-03 で残した転送・リンクの向け替え・検知の付け替えを、同じ作業ブランチに足してプレビューで確かめられる状態にする。番号は CHAT-1101-PRP-01 のログの `## 経過`「2. 棚卸しの表」（以下「表」）の番号。決定の丸数字は docs/decisions/pros.md の 2026-11-01 の決定（CHAT-1101-PRP-02 で記録）。

### 決定（2026-11-01、平野さん）
- PRP-03 の「判断が必要なこと」の並べ替えのボタンの幅: 今の形（高さだけ 44px、幅は列の幅）でよい
- 前の決定から（pros.md に記録済み）:
  - ③ `jpml_pros.html` は公開と同時に `/pro/` へ 301。X の画像のリンク切れの検知（#139、`check_image_links.py`）の対象も同時に pro/ へ移す。要らなくなるものの片付けは #645（この指示ではしない）
  - ④ `?name=` の旧 URL は Worker の 301。範囲は `jpml_pros.html?name=` だけ。旧名は「別名」の表から引き、在籍者でない名前は `/pro/` のトップへ
  - ⑨ 順位変動の `playerUrl()` と title/ の優勝者のリンクを、公開のときに pro/ の個別ページへ向け替える
  - K8: pro/ のトップの質問欄の答えに出た選手のカードの行き先を、pro/ の個別ページに（個別ページには質問欄が無いのでトップだけ。CHAT-1031-PRO-09 の読み替え）

### 前提（チャット側。平野さんの決定ではない）
- 表7・8・15・17・18 と PRP-01 のログの「入るリンク」の一覧に、今の場所がある（例: Worker `mj` は `wrangler.jsonc` に `main` が無く静的アセットだけ。名前→slug は `pro/search.json` の1つ目と9つ目の値。`assets/houou_ask.js:130` は `data-players-page` + `?name=`。`assets/league_race.js:65` の `playerUrl()` は X。`generate_title_pages.py:740-742` の優勝者は X。`scripts/check_image_links.py:31` の `TARGET_HTML`）。要確認。食い違えば実物に従ってログに書き、どこを変えるか決められなければ止まる
- Worker の 301 は、**Worker が `/jpml_pros.html` へのリクエストだけで動く形**（静的アセットの設定で Worker を先に通すパスを絞れるか。`docs/notes/cloudflare.md` の配信設定・`html_handling`・`_redirects` との順番）を先に調べる（要確認。チャット側は確かめていない）。全リクエストで Worker が動く形しか無いときは、作らずに止まり、Workers の課金と速さへの影響の見込みを報告する
- 名前→slug の対応表は、pro/ の生成と同じ流れで毎回作り直す（シートの変化に追いつくため）。旧名は「別名」の表から。在籍者でない名前・表に無い名前は `/pro/`。`?name=` の無い `jpml_pros.html` も `/pro/` へ 301（`_redirects` で行うか Worker で行うかは順番を調べて決め、`docs/notes/redirects.md` の一覧と `scripts/tests/test_redirects_inventory.py` に合わせる）
- title/ の優勝者・順位変動のアイコンは、在籍者（pro/ に個別ページがある人）だけを個別ページへ向け、ほかは今の行き先のまま（チャット側の案。`docs/decisions/title.md` の優勝者の行は PRP-02 が注記した）
- `houou_ask.js` は5大会の質問欄の共通部品。pro/ のトップ以外の質問欄の動き（行き先）は変えない
- 本番（ryoei.pro）へのリクエストは、この指示の全体で20件まで、1分に10件以下（2026-10-31 に Rate limiting rule〈1分30回〉に約1,180回当たったため）。転送の確かめはローカルの `wrangler dev`（CLAUDE.md「禁止事項」の起動の形）とプレビューで行う

## 手順
1. 転送: Worker `mj` に `jpml_pros.html?name=` の 301 を足す（前提の調べを先に行い、`## 経過` に結果を書く）。対応表の生成とテスト（`node --test 'workers/<名前>/test/*.test.mjs'`、ほかに Python の生成のテスト）。`?name=` 無しの `jpml_pros.html` の 301。`docs/notes/redirects.md`・`docs/notes/cloudflare.md`・#159 の設計の記述を直す
2. 向け替え: K8（pro/ のトップの質問欄のカード）・`playerUrl()`（`lib/houou_race.py` のデータに slug が要るなら足す）・title/ の優勝者。関係するページを再生成し、pro/ と順位変動と title/ 以外の生成物に差分が出ないことを確かめる
3. 検知の付け替えと確かめ: `check_image_links.py`・`check-image-links.yml` の対象を pro/ に移す（ワークフローを変えるので、CLAUDE.md「ブランチ運用」のとおり作業ブランチで手動実行して確かめる）。そのうえで次を `## 経過` に書く:
   - テスト（`python3 -m unittest discover -s scripts/tests` と workers のテスト）がすべて通る。足したテストは修正前のコードで失敗することを確かめる
   - ローカルとプレビューで、`/jpml_pros.html`・`/jpml_pros.html?name=<在籍者>`・`?name=<旧名>`（別名の表から1件）・`?name=<在籍者でない名前>`・`?name=<存在しない名前>`・`?name=` に記号や空白を含む値の6通りの状態コードと行き先。Worker が `/jpml_pros.html` 以外のリクエストで動いていないこと
   - 向け替えた3か所で、在籍者は個別ページ・在籍者でない人は今の行き先になっている例を各1件以上（名前つき）
   - 生成物の差分を種類ごとに数えた表（pro/・houou/race/・title/ のほかに差分が出たら止まる）
   - 新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）

## 止まる条件
- 0章の確かめで、PRP-03 のログの状態が「判断待ち」でない
- Worker を `/jpml_pros.html` だけで動かす形が無い（作らずに見込みを報告する）
- 新しい依存（npm・pip のパッケージ、外部 API）が要る
- 前提の場所が食い違い、どこを変えればよいか決められない
- pro/・houou/race/・title/ 以外のページの生成物に、この指示の変更で説明できない差分が出た
- 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象が複数なら重なる対象だけ外して進め、全部が重なるときだけ止まる）
- ワークフローの手動実行が失敗した（一時的な接続の失敗は、失敗したジョブだけを1回再実行して success なら止まらない）
- この指示では `cloudflare` へマージしない（完了の報告は「判断待ち」）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューの URL はターミナルへの最終報告の「確認用:」の行にだけ書く
- マージは冒頭の「マージ:」の行のとおり（しない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-PRP-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-PRP-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1101-prp-pub
- ログ: https://github.com/retroeater/mj/blob/work/1101-prp-pub/docs/logs/CHAT-1101-PRP-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-prp-pub
- 確認用URL: 作業中
- マージ: 未（判断待ち）
- issue: #641
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f1d4a0a0）: https://github.com/retroeater/mj-logs/tree/main/guide/f1d4a0a0

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f1d4a0a0/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/bdc234c5.md
