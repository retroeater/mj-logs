# CHAT-1115-RSC-31

- 着手日時: 2026-11-15
- 対象issue: #676（リンクのコメント）・新しく起票する常設 issue
- ブランチ: work/1115-rsc-autowrite
- 着手時HEAD: d911246f6

## 指示

【Claude作成】Claude Code 向け指示：最強戦の転記案（D）の照合済みの卓を「最強戦」に自動で書き、再生成して常設 issue で事後の確認を頼む仕組みを作る（マージは判断待ち）
Chat-Ref: CHAT-1115-RSC-31
マージ: 判断待ちで止まる
貼る時機: いつでも（CHAT-1115-RSC-30 とは別のセッションに貼る。作業ブランチが別なので並行してよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1115-rsc-autowrite の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1115-rsc-autowrite を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1115-rsc-autowrite origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）
取り込みの衝突: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
最強戦の転記案（D、`scripts/research_candidates.py`）は、今は照合済みの卓も「R_候補」の候補タブに書くだけで、平野さんが「最強戦」に手で入れている。照合済みの卓は、速報から「最強戦」を直接書き、saikyo/ の再生成まで済ませてから、平野さんに事後の確認を頼む形に変える。この指示では実装と書かない試し（dry-run）までを行い、マージ・本番での書き込みは平野さんの判断を待つ。
決定（2026-11-15、平野さん）

* 最強戦の結果は、速報から「最強戦」シートを直接更新し、再生成まで済ませたうえで、平野さんに事後の確認を頼む運用にする（平野さんの提案）
* 自動で書く範囲: 照合済みの卓だけ。予選の卓は、すでにある行の空いている順位・結果を埋める。決勝の卓は4行を足す。照合が合わない卓は今どおり候補タブに書く（照合済みの条件は今の docs/notes/research-candidates.md「D のテキスト速報」のまま）
* 書き込みのアカウントは `live-channel-writer`。「最強戦」のタブがあるブック（R_タイトル戦_成績）に、平野さんが編集者で共有する（共有はマージの前に平野さんが行う。この指示の間は共有されていない前提）
* 事後の確認は、新しく起票する常設 issue に、書いた朝だけコメントする（書いた卓ごとの4人・順位・結果、速報の URL、公開のページの URL、戻し方）
* （報告）今、「最強戦」の予選の順位・結果は、平野さんが対局の後に手で入れている

前提（チャット側。平野さんの決定ではない）

* 2026-11-04 の決定（docs/decisions/automation.md の CHAT-1104-RSC-10 の節）の「決勝の卓は、候補の行を見て平野さんが『最強戦』に4行を足す」「本体の『最強戦』は変えない」を、上の決定で置き換える
* docs/notes/live-channel-write.md の8行目付近（要確認）に「正本・/live 用スプレッドシートは共有せず、書ける先をこの2つのブックに限る」（2026-10-10・10-12 の決定）とある。R_タイトル戦_成績 がこの「正本」に当たるかを、docs と `scripts/` の定数で確かめてログに書く（当たっても止まらない。マージの判断のときにチャット側が平野さんに確かめる）
* 書き込みはコードで「最強戦」のタブだけに限る（range にタブ名を付ける。ほかのタブ名への書き込みは例外で止める）。予選の卓は、空いているセルにだけ書く。すでに値があって速報と同じなら書かない。違えば書かずに未照合（今の照合と同じ）
* 決勝の4行は、同じ対局の予選の行と同じ列の形にする（表示=Y など）。各列に何を入れるかは Code が予選の行と既存の決勝の行から決め、ログに列ごとの表で書く（チャット側の案。表を見て平野さんが決める）
* 平野さんが先に手で入れていた卓は、シートの値が速報と同じなので「書かない」になる見込み。その朝は事後の確認のコメントも出さない（チャット側の案）
* 自動で書いた卓の候補タブの行は、今どおり書く。「状態」は「採用」、「備考」に「自動で書いた（日時）」と書く（チャット側の案。docs/notes/research-candidates.md の「状態は平野さんが書く」の例外になる）
* 書いた朝は、書いた後に saikyo/ を再生成する。run-daily（`.github/workflows/run-daily.yml`）のジョブ `sokuho` と saikyo/ の再生成（`regenerate-saikyo.yml`、#537）の順が今どうなっているかは要確認
* 既定は書かない（dry-run）。本番の書き込みは入力・定数で明示したときだけにし、マージの後に平野さんの共有と承認を待ってから有効にする
* 形の違う対局（全日本プロ選手権・アマチュア・ファイナル）は、今どおり「見つからない」で書く（形を足すかは 2026-11-16 にチャット側が平野さんと決める。#676）
* 常設 issue の名前・ラベルは既存の常設 issue（#426・#506 など）の形にそろえる。本文に仕組み・戻し方（シートの変更履歴）・止め方を書く

手順

1. 調べ（コード・シートは変えない）: 同じ論点の open issue（クローズ済みも含めて）を検索する。run-daily のジョブ `sokuho` と saikyo/ の再生成の順、「最強戦」の列（見出し）と 2026-11-01・11-08 の予選・決勝の行の形、`LIVE_SHEETS_SA_KEY` を `research-candidates.yml` から使えるか、上の「正本」の確かめ。結果をログの `## 経過` に書く
2. 実装と記録: 書き込み（dry-run が既定。タブの限定・空きセルだけ・決勝の4行）、書いた後の再生成、常設 issue へのコメント。常設 issue を起票し、#676 にリンクのコメントをする（#676 の本文・状態・ラベルは変えない）。docs/notes/research-candidates.md・docs/notes/live-channel-write.md（書ける先のブックの一覧）・docs/decisions を直す。テストを足して通す。差分は docs/notes/self-review.md のとおりサブエージェントにレビューさせる
3. 書かない試し: 作業ブランチのコードで、2026-11-01（超技巧合戦）と 2026-11-08（義兄弟合戦）の速報を dry-run で読み、「書く予定」をシートの今の値（平野さんが手で入れたもの）と並べた表をログに全件で書く。予選は「書かない（同じ）」、決勝の4行は既存の行と列ごとに同じになるはず。本番のシートと候補タブには書かない（読み取りは `sheets-reader`）

止まる条件

* 同じ論点の open issue がある、または #676 に他のセッションの着手中のコメントがある
* 手順3で、予選に「書く」と出る卓がある、または決勝の4行が既存の行と1列でも違う（違いを表に書いて止まる）
* dry-run のつもりの実行で、本番のシート・候補タブに書き込みが起きた（すぐ止まって報告する）
* 「最強戦」の行数が、読み直すたびに変わる（件数を書いて止まる）
* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` の一覧）が `scripts/research_candidates.py`・`research-candidates.yml`・`run-daily.yml` の同じ関数・ジョブを変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* コミットの件名・本文に、閉じるキーワード（close・fix・resolve とその活用形）の直後の `#番号` を書かない（push の前に確かめる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、マージの可否・決勝の4行の列の表・「正本」の確かめの結果・平野さんの共有の手順（直接のリンク付き）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1115-RSC-31.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1115-RSC-31 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1115-rsc-autowrite
- ログ: https://github.com/retroeater/mj/blob/work/1115-rsc-autowrite/docs/logs/CHAT-1115-RSC-31.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1115-rsc-autowrite
- 確認用URL: なし
- マージ: 未
- issue: #676
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a7723aff）: https://github.com/retroeater/mj-logs/tree/main/guide/a7723aff

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7723aff/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7723aff/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7723aff/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7723aff/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7723aff/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7723aff/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1bb9d125.md
