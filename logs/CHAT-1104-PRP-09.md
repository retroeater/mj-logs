# CHAT-1104-PRP-09

- 着手日時: 2026-11-04
- 対象issue: #641
- ブランチ: work/1101-prp-pub
- 着手時HEAD: 3cc4a8a83

## 指示

【Claude作成】Claude Code 向け指示：pro/ の公開の準備（その7）。PRP-08 の判断待ちへの回答（トップの既定を「よみ: すべて」に・放送対局の「すべて」を1つに・明るい配色の★を紺に）を入れ、プレビューで止める（マージ＝公開はまだしない） Chat-Ref: CHAT-1104-PRP-09 マージ: 判断待ちで止まる（このブランチのマージが pro/ の公開になる。live/〈#362〉の公開と告知動画の後に、別の指示でマージする） 貼る時機: CHAT-1104-PRP-08 の完了報告（判断待ち）の後。PRP-08 と同じセッションでも、新しいセッションでもよい 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1101-prp-pub を続けて使う（PRP-03〜08 の公開の準備に足すため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしが衝突したら両方を残して解く。生成されたページだけの衝突は、取り込んだ後のスクリプトで生成し直して解く。両方の変更が両立するそのほかの衝突（隣り合う行・別の目的の変更が同じ関数に入ったもの）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1104-PRP-08 のログの `## 報告` の状態が「判断待ち」であることを確かめ（違えば止まる）、その状態の末尾に `/ 続き: CHAT-1104-PRP-09` を足して push する。

目的
PRP-08 の「判断が必要なこと」への平野さんの回答を入れ、プレビューで平野さんが確かめられる状態にする。
決定（2026-11-04、平野さん）

* トップ（`pro/`）を開いた直後の既定は「よみ: すべて」（絞り込みなし、全員）にする。PRP-08 で入れた「既定の『よみ: あ』をほかのカテゴリを押したら外す」扱いは、この決定で要らなくなるので外す（よみで「あ」を選んだときは、ほかの選択と同じに重ね掛けする）
* 誕生日の絞り込みは今の形（個別ページのチップからだけ。トップでは状態の1行と「解除」）のままにする。シートのカテゴリには入れない（一覧に誕生日の列が無く、絞り込んでも正しいか分からないため）。「解除」で全員に戻るのは、上の既定とそろう
* 放送対局の「すべて（N件）」は1つにし、2つの欄の最後（「放送対局（実況・解説）」の欄の下）にだけ置く（live/ の検索は役で絞れず、行き先も件数も同じため）
* 選んだ後の★: 暗い配色では名前の面の上の白い★（今のまま）。明るい配色では丸を付けず、★を紺（ナビ・鳳凰位のチップと同じ系統）にする

前提（チャット側。平野さんの決定ではない）

* 既定を全員にしても、トップの HTML はもとから全 1,098行を持ち（約685KB、gzip 約78KB）、写真 857枚はすべて `loading="lazy"`（チャット側が origin/work/1101-prp-pub で数えた。要確認）。読み込む量は変わらない見込み。既定が変わることで `?all=1` の意味が「既定と同じ」になるので、受け続けるか・状態の1行の書き方（例「すべて」）をどうするかは既存の形に合わせて決め、報告に書く
* 明るい配色の★の紺は、白い面の上でアイコンの基準のコントラスト（3:1 以上、docs/notes/design.md）を満たす値にする（測った値を書く）
* PRP-08 のコミットの Workers Builds のプレビューは、2026-11-04 00:40 JST の時点で check-suite が `queued` のままだった（チャット側が REST で見た）。この指示の push でビルドが付くかを確かめ、15分で付かなければ「未確認の項目」に回してローカルで確かめる（平野さんのダッシュボードの確認が要るかを報告に書く）
* 本番（ryoei.pro）へのリクエストは、この指示の全体で20件まで、1分に10件以下。まとめて送るときは UA に `mj-internal` を含める（CLAUDE.md）

手順

1. トップの既定と誕生日の「解除」・`?all=1` の扱い: `scripts/generate_pro_pages.py`・`assets/pro.js` を直し、PRP-08 の組み合わせの検査（または手で確かめた表）を新しい既定に合わせて直す。pro/ を再生成する
2. 放送対局の「すべて（N件）」を1つにし、明るい配色の★を紺にする（`style.css`）。テストを直す・足す
3. 確かめと記録:
   * テスト（`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/*/test/*.test.mjs'`）がすべて通る。足したテストは修正前のコードで失敗することを確かめる
   * プレビュー（付かなければローカル）で、トップを開いた直後に全員が出る → 「所属: 東京」→「入会: 23期」を重ねる → よみで「あ」を重ねる → 「すべて」で戻す、の流れと、`pro/sho.shiratori.23/` の☆→★（明暗）・放送対局の2つの欄と1つの「すべて（N件）」を、360px と 1280px・明暗で見る
   * 生成物の差分を種類ごとに数えた表（pro/ のほかに差分が出たら止まる）
   * 新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）
   * 上の「決定」を docs/decisions/pros.md に足し、`docs/notes/pro-pages.md` を今の形に直す

止まる条件

* 0章の確かめで、PRP-08 のログの状態が「判断待ち」でない
* pro/ 以外のページの生成物に、この指示の変更（と取り込み）で説明できない差分が出た
* 新しい依存（npm・pip のパッケージ、外部 API・外部ドメイン）が要る
* 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* この指示では `cloudflare` へマージしない（完了の報告は「判断待ち」）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューの URL はターミナルへの最終報告の「確認用:」の行にだけ書く
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-PRP-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-PRP-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1101-prp-pub
- ログ: https://github.com/retroeater/mj/blob/work/1101-prp-pub/docs/logs/CHAT-1104-PRP-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-prp-pub
- 確認用URL: 作業中
- マージ: 未（判断待ち）
- issue: #641
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 667be053）: https://github.com/retroeater/mj-logs/tree/main/guide/667be053

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/667be053/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/59c4b82d.md
