# CHAT-1101-PRP-06

- 着手日時: 2026-11-01
- 対象issue: #641・#568
- ブランチ: work/1101-prp-pub
- 着手時HEAD: 410409287

## 指示

【Claude作成】Claude Code 向け指示：pro/ の公開の準備（その4）。cloudflare を取り込み、転送の Worker `mj-pros-redirect` の 301 を転送の月次の集計（棚卸し）の対象に入れる。同じ作業ブランチに足して止める（マージ＝公開はまだしない）
Chat-Ref: CHAT-1101-PRP-06
マージ: 判断待ちで止まる（このブランチのマージが pro/ の公開になる。live/〈#362〉の公開と告知動画の後に、別の指示でマージする）
貼る時機: CHAT-1101-PRP-05 の完了報告（判断待ち）の後。PRP-05 と同じセッションでよい
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1101-prp-pub を続けて使う（PRP-03〜05 の公開の準備に足すため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしが衝突したら両方を残して解く。生成されたページだけの衝突は、取り込んだ後のスクリプトで生成し直して解く。両方の変更が両立するそのほかの衝突（隣り合う行・別の目的の変更が同じ関数に入ったもの）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1101-PRP-05 のログの `## 報告` の状態が「判断待ち」であることを確かめ（違えば止まる）、その状態の末尾に ` / 続き: CHAT-1101-PRP-06` を足して push する。

## 目的
公開の前の最後の直しとして、cloudflare に入ったほかの系列の変更を取り込み、転送の Worker の 301 を転送の月次の集計（#568、棚卸しの基準「直近90日でボット以外の転送0件かつ Search Console の着地0件」）の対象に入れる。Worker を外す時機も、ほかの転送と同じ基準で決められるようにするため。

### 決定（2026-11-01、平野さん）
- PRP-05 の判断が必要なことへの回答:
  - 転送の Worker `mj-pros-redirect` の 301 を、転送の月次の集計の対象に入れる（PRP-05 のログの「転送の月次の集計」の案: 「棚卸しの対象」へ移し、`test_redirects_inventory.py` で「仕組み」が Worker の行だけ `_redirects` との照合を外す）。Worker の 301 が Cloudflare の集計に出るかは、公開の後の最初の月次（2026-12-01）で確かめる
  - 旧 `jpml_pros.html` の `?place=`・`?league=`・`?ouka=` などは、pro/ のトップのクエリへ引き継がず `/pro/` へ送る（今の形のまま）
  - Worker の Workers Builds のプロジェクト（P1）は、公開のマージの後に平野さんが作る（PRP-05 の報告のとおり）
- 「鳳凰」シートの第38期後期の行の名前の空白（こばり　しん・水口 崚・泉　裕斗）は、平野さんがシートで直す（この指示では何もしない）

### 前提（チャット側。平野さんの決定ではない）
- cloudflare には、このブランチと同じファイルを変えた変更が入っている見込み（要確認）: CHAT-1101-RDR-08（`scripts/check_image_links.py` の UA に目印 `mj-internal`）と CHAT-1101-RDR-05・07（集計から自分たちのアクセスの UA を外す）。PRP-04 は同じファイルで検知の対象を pro/ に移している。目的が別なので、両方を残して解く見込み
- 集計の仕組みの正は `docs/notes/redirects.md`・`scripts/fetch_redirect_counts.py`・`scripts/tests/test_redirects_inventory.py`（RDR 系列の作業。入れてから90日未満の行は判定しない・#568 の表に判定できる日、の規則も RDR が入れた。要確認）。それに合わせて Worker の行を足す。集計の仕組み（UA の除外など）そのものは変えない
- `docs/notes/static-generation.md` の pro/ の「未公開」の記述は、未マージの work/1029-lgn-page と同じ行で衝突するので、この指示では直さない（公開のマージの指示で、LGN の進み具合を見て直す）
- 本番（ryoei.pro）へのリクエストは、この指示の全体で20件まで、1分に10件以下（2026-10-31 に Rate limiting rule〈1分30回〉に約1,180回当たったため）

## 手順
1. `origin/cloudflare` を取り込む。衝突したファイルと解き方を `## 経過` に引用する。取り込んだ後に、pro/・順位変動（4大会）・title/ を再生成する必要があれば `regenerate-page.yml` で生成し直す
2. 集計の対象: `docs/notes/redirects.md` の Worker の行を「棚卸しの対象」へ移し（入れた日・判定できる日を既存の行の形で書く）、`scripts/tests/test_redirects_inventory.py` が「仕組み」が Worker の行だけ `_redirects` との照合を外し、代わりに `workers/pros-redirect/wrangler.jsonc` のルートと照合するようにする。`scripts/fetch_redirect_counts.py` がこの行の転送元（`/jpml_pros.html`。クエリ付きを含めるか）を数える対象に入ることを、関数を実データの一覧で動かして確かめる（Cloudflare の API は呼ばなくてよい。理由: 実際に数えられるかは公開の後の Worker のデプロイまで確かめられないため）。#568 に、2026-12-01 の月次で Worker の 301 が数に出るかを確かめる旨をコメントする
3. 確かめ: テスト（`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/*/test/*.test.mjs'`）がすべて通る。足したテストは修正前のコードで失敗することを確かめる。生成物の差分を種類ごとに数えた表（取り込みと再生成で変わったもの）。新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）。上の「決定」を docs/decisions/pros.md に足す

## 止まる条件
- 0章の確かめで、PRP-05 のログの状態が「判断待ち」でない
- 取り込みで、両方を残して解けない衝突が出た
- 集計の仕組みそのもの（`fetch_redirect_counts.py` の数え方・UA の除外）を変えないと、Worker の行を対象に入れられない（変えずに案を書いて止まる）
- 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- この指示では `cloudflare` へマージしない（完了の報告は「判断待ち」）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（しない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-PRP-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-PRP-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1101-prp-pub
- ログ: https://github.com/retroeater/mj/blob/work/1101-prp-pub/docs/logs/CHAT-1101-PRP-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-prp-pub
- 確認用URL: 作業中
- マージ: 未（判断待ち）
- issue: #641・#568
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
