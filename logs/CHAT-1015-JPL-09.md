# CHAT-1015-JPL-09

- 着手日時: 2026-10-15
- 対象issue: #523
- ブランチ: work/1015-jpl
- 着手時HEAD: 0f28e166c

## 指示

【Claude作成】Claude Code 向け指示：JPL-08 の続き。wrc_ranking・wrc_results の略記「JPML WRC」を「WRCリーグ」に直し、JPL-08 の変更と一緒に cloudflare へマージする（#523） Chat-Ref: CHAT-1015-JPL-09 マージ: 承認済み（チャットで）。条件は下の「止まる条件」に当たらないこと（テストが通り、差分が JPL-08 の差分と、下の略記の置き換えで説明できる範囲だけ） 貼る時機: いつでも。JPL-08 と同じセッションでよい 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-jpl の使用と push、cloudflare へのマージ（上の「マージ:」の行の範囲）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1015-jpl を続けて使う（JPL-08 の変更がある）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる。生成物の衝突は、取り込んだ後に生成し直して解いてよい（直した後の差分を種類に分けて書く）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1015-JPL-08.md の `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。JPL-08 の `## 報告` の状態の末尾に `/ 続き: CHAT-1015-JPL-09` を足す（`## 指示` 欄より後ろの見出しを相手にする）。#523 が Open で、他セッションの着手中コメントが無いことを確かめ、着手中コメントを残す。

目的
JPL-08 の「判断が必要なこと」に平野さんが答えた。略記の「JPML WRC」も新名称にそろえ、JPL-08 の変更（WRC 2大会の新名称の全面反映）と一緒に本番へ入れる。
決定（2026-10-15、平野さん）

* wrc_ranking.html・wrc_results.html の `<title>`・og:title・h1 と `scripts/apply_page_meta.py` の略記「JPML WRC」は「WRCリーグ」にする（例「ランキング | WRCリーグ | ryoei.pro」「成績詳細 | WRCリーグ | ryoei.pro」「WRCリーグ 成績詳細」）
* JPL-08 の変更のマージを承認する（翌朝の実行で予定表【2】・/live の【2】・mj_放送対局 の仮の予定が新名に書き換わることを含めて）

前提（チャット側。平野さんの決定ではない）

* チャット側が 2026-10-15 にブランチの別名のプレビュー（6427fca83）で、title/jwrc/・title/jwrc/19.html・title/jwrc-r/・title/jwrc-r/7.html・live/jwrc-r/1/f.html・rh_results_detail.html・video_live.html・index.html・wrc_ranking.html・wrc_results.html・title/ を読み、「JPML WRC(-R)リーグ」が0件、新名が出ていること、略記「JPML WRC」が wrc_ranking.html に2件・wrc_results.html に3件残っていることを確かめた
* `apply_page_meta.py` のコメント「# ---- JPML WRC ----」も「WRCリーグ」にそろえてよい（コメントなので表示には出ない）
* 見込み（JPL-08 の報告）: マージ後の最初の毎朝の実行で、予定表【2】257行・/live の【2】402行の大会名と、mj_放送対局 の今後の仮の予定 8件の件名が新名になる。チャット側がカレンダー「ryoei.pro」の 2026-10-16 に確認の予定を作った
* マージの push で全ページの再生成が動き、video_mtsuku が「Mつく」シートの件で飛ばされてジョブが失敗表示になるのは見込みどおり（MTK のチャットで対応中。この変更とは無関係）

手順

1. 直す: 上の決定の略記を置き換える（`apply_page_meta.py` を直して生成し直す形か、手書きのページを直接直すかは実物に合わせる）。リポジトリ全体で「JPML WRC」を検索し、入力の照合（旧名を読むための対応表・テスト）以外に残っていないことを確かめて件数を書く。`python3 -m unittest discover -s scripts/tests` を通す
2. マージする（CLAUDE.md「ブランチ運用」）。取り込み後に生成し直し、origin/cloudflare との差分を種類に分けて書く（JPL-08 の報告の差分〈生成物 22ファイル・コード・手書き〉と、この指示の略記の置き換え以外が無いこと）。マージで動く自動処理の結果を確かめてログに書く（上限15分。video_mtsuku の飛ばしは無関係として扱う）。本番の wrc_ranking.html・title/jwrc/ で新名が出ていることを確かめる（URL に `?v=<未使用の値>`）
3. 記録と片付け: docs/decisions/title.md に上の「決定」を足す。#523 に結果をコメントし、本文の「残り」の済んだ項目に印を付ける。#523 をクローズしてよいかの案（翌朝の反映の確認が残ること、他の残りの有無）を「判断が必要なこと」に書く（クローズはしない）。JPL-07 の `## 報告` の状態を「完了」にする（判断が必要なことは JPL-08・09 で扱った旨）。JPL-08 の状態も「完了」にする。作業ブランチを片付ける（片付けはほかの検証の成否に条件づけない）

止まる条件

* 0章で JPL-08 の状態が「判断待ち」でない、#523 が Closed、または他セッションの着手中コメントがある
* テストが通らない、または生成が止まる（video_mtsuku の飛ばしを除く）
* 差分に、JPL-08 の差分・略記の置き換え・生成日時など毎回変わるもの・シートの今日の変化で説明できるもの以外が出た（全件書いて止まる）
* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が、変えるファイルの同じ関数・行を変えている、または取り込みで衝突する（同じファイルの別の箇所なら止まらずログに書く）
* マージ後の check-run・再生成のジョブの失敗が今回の変更によるもの（無関係な失敗なら原因を報告に書いて残りを進めてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-JPL-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-JPL-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1015-jpl
- ログ: https://github.com/retroeater/mj/blob/work/1015-jpl/docs/logs/CHAT-1015-JPL-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-jpl
- 確認用URL:
- マージ:
- issue: #523
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b2da47b9）: https://github.com/retroeater/mj-logs/tree/main/guide/b2da47b9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2da47b9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2da47b9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2da47b9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2da47b9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2da47b9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b2da47b9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/66464371.md
