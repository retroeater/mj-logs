# CHAT-1017-HOU-70

- 着手日時: 2026-10-17
- 対象issue: #541（関係: #586）
- ブランチ: work/1017-hou
- 着手時HEAD: 98ff33c91

## 指示

【Claude作成】Claude Code 向け指示：#541 の段1 — 鳳凰戦の A1・A2・鳳凰位を内部でも「通期」として持つ形に生成・画面・質問欄を直し、シートの「前後」が「後」でも「-」でも同じ結果になるようにする Chat-Ref: CHAT-1017-HOU-70 マージ: 承認済み（チャットで。2026-10-17 に平野さんが、この段1 を「見た目が変わらないことを確かめるだけ」の段として承認済みでマージする進め方に同意。ただしテストが通り、下の「止まる条件」に当たらないときだけ） 貼る時機: いつでも（新しいセッションは任意。HOU-69 と同じセッションでもよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-hou の作成と push と、cloudflare へのマージの push と、#541・#586 へのコメントを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1017-hou を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-hou origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。

目的
#541 を案2 で進める3段のうちの段1。「鳳凰」シートの A1・A2・鳳凰位（以下「通期のリーグ」）を、生成・成績データ・画面・質問欄の内部で「通期」として持つ形に直す。シートの C列「前後」が今の「後」でも、これから平野さんが書き換える「-」でも、同じ生成結果になるようにする。画面の見た目（文字）は変えない。
決定（2026-10-17、平野さん）

* #541 は案2: 内部も「通期」として持ち、成績データ・順位変動の期のキーと URL・質問欄・画面の JS を通期に合わせる。理由は、後で見たときに「なぜ後期扱いなのか」が分からなくなるのを避けるため（HOU-69 の案1〈読むときに「後」として扱う〉は採らない）
* 鳳凰位の行も、A1・A2 と同じく「-」にする（書き換えは A1 325・A2 421・鳳凰位 22 の 768 行。数は CHAT-1017-HOU-69 のログ「2. シートの今の値」）
* 旧ページ（旧リーグ推移 houou_leagues・旧順位変動 houou_race・`houou_results.js`・`check_leagues_dropped.py` など、#582 で後始末するもの）は案2 に合わせて直さない。読むときに「-」を「後」として扱うつなぎだけ入れ、出力は今と変えない
* 順位変動の古い URL（A リーグの `?term=42-2` の形）は読み替えない（公開したばかりで共有済みのリンクが無いため）
* 進め方は3段: 段1（この指示。「後」と「-」の両方を受け付け、内部は通期）をマージ → 段2（平野さんが 768 行を「-」に書き換える。1回でも分けてもよい）→ 段3（通期のリーグで「後」を受け付ける分岐と、画面の読み替え〈`assets/houou.js` の `termShort()`・`termLong()` ほか〉を外す）
* 段1 のマージは承認済み（冒頭の「マージ:」の行）

前提（チャット側。平野さんの決定ではない）

* 影響する所の一覧は CHAT-1017-HOU-69 のログ「3. 影響する所」の表が正。着手時の cloudflare で `git grep` し直し、増えた所があれば足す
* 内部の通期の印（成績 JSON の `half` の値、順位変動の期のキー〈`<期>-<1/2>` に対する通期の形〉、`workers/ask/data.json` の値）は実物に合わせて決めてよい。決めた形と理由をログに書き、`docs/notes/houou-top.md`（無ければ近い節）と `docs/decisions/houou.md` に書く
* 期を数える所（リーグのタブ・積み上げの人数・順位）では、今と同じく通期のリーグを前期・後期の両方に数える。今の `fill_front_half()`・`standings()`・`leaguePositions()` の「後期から前期へ写す」は、「通期を前期・後期の両方に出す」意味の名前と書き方にする（後期の行と誤解されない形）。数える結果は今と同じ
* jpml_pros の「鳳凰<最新の期>」の値と、Ampai のリンクは今と同じにする。Ampai のブックは通期のリーグも「前」「後」の2行を持つ（HOU-69 のログ「2.」）ので、通期のリーグは今と同じ「後」の行を引く（要確認。ほかに合う行の選び方が無いか確かめ、無ければこの形）
* 旧ページのつなぎは、旧ページだけが使う関数か、旧ページの生成の入口に置く。houou/ 側と共有する関数に「-」→「後」の変換を入れない（入れると内部が「後」に戻る）
* 通期のリーグの行で「前」が来たら止める（今のシートに0件。HOU-69）。「後」と「-」以外の値も止める
* `workers/ask/` の答えの期の書き方（`termLabel()`）は、データの通期の印から「第42期」と書く形にする。AI への指示（`ANSWER_SYSTEM`）を変える場合は、`docs/notes/ask-worker.md`「変更の指示を作るときの前提: 本番で送る固定の問い」の5問を、マージ後に本番で送って期待と比べる（送るのはこの5問だけ。1日の上限の範囲）。`ANSWER_SYSTEM` を変えない場合も、data.json の期の値が変わるので同じ5問を送る
* マージで生成スクリプトが変わるので、`regenerate-page.yml` が対象のページを作り直す。その時点のシート（名簿の退会など）の変化が生成物に混ざることがある

手順

1. 着手の確かめ: #541 に他のセッションの着手中のコメントが無いか確かめ、着手中のコメントを書く。`git branch -r --no-merged origin/cloudflare` の各ブランチが、HOU-69 の「3.」の表のファイルの同じ関数を変えていないか、取り込みで衝突しないかを確かめてログに書く。`docs/notes/ask-worker.md` の固定の5問の節を読む。CHAT-1017-HOU-69 のログの `## 報告` の「状態」の行の末尾に `/ 続き: CHAT-1017-HOU-70` を足す。
2. 直す: 生成（`scripts/` の HOU-69 の表のもの）・成績データ・`assets/houou.js`・`assets/houou_race.js` のうち houou/ 側・`workers/ask/` を、前提のとおり通期を内部の値として持つ形にする。旧ページのつなぎを入れる。テストは「後」の入力と「-」の入力の両方で同じ結果になるもの、通期のリーグの「前」・ほかの値で止まるものを足す（`scripts/tests/`・`workers/ask/test/`）。`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を通す。
3. 確かめる（マージの前）:
   * 今のシート（「後」）で生成し、cloudflare の生成物との差を種類に分けてログに書く。許すのは、通期のリーグの期の値（`half` など）・期のキー・順位変動の A リーグの URL の形・ask の data.json の期の値・それに伴う部品の名前の変更だけ。旧ページの生成物は差が無いこと
   * 「鳳凰」シートの通期のリーグの「前後」を手元で「-」に置き換えた入力（シートは変えない。読み込んだ後の値を置き換えるテスト用の経路で）で生成し、上の「後」の生成物と同じであること（旧ページも同じであること）
   * 作業ブランチのプレビューを headless Chrome で開き、cloudflare の本番と画面の文字を比べる。見る所: houou/ トップ、個人成績の白鳥翔（鳳凰位）・A1 の選手1人・A2 の選手1人・B リーグの選手1人のそれぞれ「推移」「期別」タブと吹き出し、リーグのタブ（最新の期と第22期）、ランキングの3つ、順位変動の A1 の最新の期と B1 の最新の期。違いが URL の形だけであることを書く（選んだ選手の名前を書く）
   * jpml_pros の A1・A2・鳳凰位の選手の「鳳凰<最新の期>」と Ampai のリンクが cloudflare と同じこと
   * 直した後の docs（`docs/notes/houou-top.md`・`docs/decisions/houou.md`・必要なら `docs/notes/ask-worker.md`）をコミットする
   * push の直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD` を確かめ、`git push origin work/1017-hou:cloudflare` でマージする。マージ後の `regenerate-page.yml` の結果と、本番で上の見る所のうち白鳥翔と A1 の選手の画面を確かめる。固定の5問を本番の質問欄に送り、答えを `## 経過` に書く。#541 に段1 の完了と、段2（平野さんの書き換え）に入ってよいことをコメントする（コメントの末尾に `Chat-Ref: CHAT-1017-HOU-70` の行）

止まる条件

* #541 に他のセッションの着手中のコメントがある
* 未マージの work/ ブランチが HOU-69 の表のファイルの同じ関数を変えている、または取り込みで衝突する
* 「後」の生成物に、上で許す種類の外の差が出る（旧ページの差を含む）
* 「-」に置き換えた入力の生成物が、「後」の生成物と同じにならない
* 画面の文字が本番と違う（URL の形の違いを除く）。jpml_pros の値・Ampai のリンクが変わる
* Ampai のリンクを今と同じにする引き方が「後」の行以外に要る、または決められない
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の失敗は、今回の変更による失敗と無関係な失敗を分ける。無関係なら原因を報告に書き、残りの手順（本番の確かめ・固定の5問・#541 のコメント）を進めてよい。今回の変更による失敗なら、それ以上進めずに報告する

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、決めた通期の印の形と、固定の5問で期待と違った答え（あれば）を書く
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たればマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-HOU-70.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-HOU-70 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミットなし。HOU-69 と同じセッション。origin/work/1017-hou はマージ済みで、ローカルの work/1017-hou も origin/cloudflare の祖先なので `git merge --ff-only origin/cloudflare` で進めた（既に最新）。雛形の行は揃っている。「指示」欄の末尾は指示文の最後の行と一致

## 報告

- 状態: 作業中
- ブランチ: work/1017-hou
- ログ: https://github.com/retroeater/mj/blob/work/1017-hou/docs/logs/CHAT-1017-HOU-70.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-hou
- 確認用URL: なし
- マージ: 未
- issue: #541
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 98ff33c9）: https://github.com/retroeater/mj-logs/tree/main/guide/98ff33c9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/98ff33c9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/98ff33c9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/98ff33c9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/98ff33c9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/98ff33c9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/98ff33c9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e61b4b99.md
