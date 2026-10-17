# CHAT-1017-HOU-71

- 着手日時: 2026-10-17
- 対象issue: #541（関係: #582）
- ブランチ: work/1017-hou
- 着手時HEAD: ae962df83

## 指示

【Claude作成】Claude Code 向け指示：#541 の段3 — 平野さんが書き換えた「鳳凰」シートの照合のあと、通期のリーグで「後」を受け付ける分岐と画面の読み替えを外し、cloudflare へマージして #541 をクローズする Chat-Ref: CHAT-1017-HOU-71 マージ: 承認済み（チャットで。2026-10-17 に平野さんが、下の「止まる条件」〈シートの照合が合わない・生成物の差・画面の文字の差・固定の5問の違い〉に当たらないときだけマージする形で承認） 貼る時機: いつでも（平野さんは 2026-10-17 20:48 JST までに「鳳凰」シートの書き換えを終えた。新しいセッションは任意） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-hou の作成と push と、cloudflare へのマージの push と、#541・#582 へのコメントと #541 のクローズを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1017-hou を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-hou origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確かめ、一致しなければ作業せず報告する。

目的
#541 を案2 で進める3段のうちの段3。段1（CHAT-1017-HOU-70、マージ済み）で内部を「通期」で持つ形にし、段2 で平野さんが「鳳凰」シートの A1・A2・鳳凰位（以下「通期のリーグ」）の C列「前後」を「-」に書き換えた。段3 では、書き換えを照合したうえで、通期のリーグの「後」を受け付ける分岐と、画面・質問欄の、リーグ名で通期を判断する読み替えを外す。生成物と画面の文字は変えない。
決定（2026-10-17、平野さん）

* #541 は案2、3段で進める（HOU-70 の「決定」のとおり）。段3 では通期のリーグで「後」を受け付ける分岐と、画面の読み替え（`assets/houou.js` の `termShort()`・`termLong()` ほか）を外す
* 旧ページ（#582 で後始末するもの）は案2 に合わせて直さず、読むときに「-」を「後」として扱うつなぎ（HOU-70 の `legacy_half()`・`legacy_load()`）は #582 まで残す
* 段3 のマージは承認済み（冒頭の「マージ:」の行の条件つき）

前提（チャット側。平野さんの決定ではない）

* 書き換える前の「鳳凰」シートの値は CHAT-1017-HOU-69 のログ「2. シートの今の値」のとおり（16,011 行、通期のリーグは全部「後」で A1 325・A2 421・鳳凰位 22 の計 768 行）。B〜E リーグの行数はそのログに書かれていない（「略」）ので、B〜E が変わっていないことは行数ではなく下の手順1 の生成物の比較で確かめる
* 段1 のコードは「後」と「-」を同じ値にするので、シートの書き換えだけでは生成物は変わらない（HOU-70 のログ「3. 確かめ」）
* 段3 の後は、通期のリーグの行で「後」が来たら生成が止まる。これからシートに 44期以降の通期のリーグの行を足すときは「-」で入れる（平野さんには 2026-10-17 にチャットで伝え済み）。止める理由の文言は、シートのどの行をどう直せばよいかが分かる形にする
* 画面・質問欄の通期の判断は、リーグ名（`SPAN_LEAGUES`）ではなくデータの通期の印（成績 JSON・`data.json` の期の番号 0、順位変動の期のキー `<期>-0`）で行う形にする。リーグ名の一覧が通期の印を作る所（`lib/houou_tab.py` の `term_half()` など、シートを読む所）に残るのはよい
* `work/1017-amp`（AMP、未マージ）が `scripts/generate_houou_pages.py` を変えている（CHAT-1017-AMP-09 のログ。判断待ち）。着手時に未マージのままか、マージされたかを確かめる

手順

1. シートの照合（直す前に。ここで合わなければ手順2 に入らない）:
   * #541 に他のセッションの着手中のコメントが無いか確かめ、着手中のコメントを書く。CHAT-1017-HOU-70 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1017-HOU-71` を足す
   * 「鳳凰」シートを生成と同じ経路で2回読み、全行数と、リーグ × C列の値ごとの行数を表にする（2回で違ったら両方の数を書いて止まる）。通期のリーグは「-」が 768 行（A1 325・A2 421・鳳凰位 22）、「後」「前」その他が 0 行であること。全行数が 16,011 でなければ、増減とその行（期・リーグ・名前）を書いて止まる
   * 着手時の origin/cloudflare のコード（段1 の後）で、今のシートから houou_pages・houou_race・houou_leagues・jpml_pros・ask_data を生成し、origin/cloudflare にコミット済みの生成物と比べる。差が 0 件であること（B〜E リーグの「前」「後」が変わっていれば、ここで差が出る）。差があれば種類と件数を書いて止まる
2. 段3 を直す: 通期のリーグで「後」を受け付ける分岐を外し（「後」は止める理由つきで止まる）、`assets/houou.js`・`assets/houou_race.js`（houou/ 側）・`workers/ask/src/query.mjs` のリーグ名で通期を判断する所をデータの通期の印で判断する形にする。旧ページのつなぎは残す。`git grep` で `SPAN_LEAGUES` とリーグ名（「鳳凰位」「A1」「A2」）で通期を判断している所を洗い、直した所・残した所と理由を表にしてログに書く。テストは、通期のリーグの「後」で止まる、「-」で今と同じになる、を足し・直す。`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/ask/test/*.test.mjs'` を通す。`docs/notes/houou-top.md`「通期のリーグ」、`docs/decisions/houou.md`、シートの入れ方を書いている文書（`git grep` で「前後」の入れ方を書いた所。要確認）に、通期のリーグは「-」で入れることを書く
3. 確かめてマージする:
   * 段3 のコードで同じ5本を生成し、手順1 の生成物と比べて差が 0 件であること
   * 作業ブランチのプレビューと本番を headless Chrome で比べ、HOU-70 と同じ所（houou/ トップ、ランキング3つ、順位変動の A1・B1 の最新、白鳥翔・佐々木寿人・二階堂亜樹・一井慎也の期別・通算・リーグのタブの吹き出し・部門別）の文字が同じであること。ページのエラーが 0 であること
   * origin/cloudflare を取り込み（`work/1017-amp` などがマージされていれば、取り込んだ後に手順3 の生成の比べとテストをやり直す）、push の直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD` を確かめ、`git push origin work/1017-hou:cloudflare` でマージする。マージ後の `regenerate-page.yml` の結果と、本番の白鳥翔・佐々木寿人の画面を確かめ、`docs/notes/ask-worker.md` の固定の5問を本番の質問欄に送って答えを `## 経過` に書く
   * #582 に、旧ページの「-」を「後」として読むつなぎ（関数名とファイル）が後始末の対象に加わったことをコメントする。#541 に段3 の完了と、通期のリーグを「-」で入れること、旧ページのつなぎは #582 で消すことをコメントしてクローズする（コメントの末尾に `Chat-Ref: CHAT-1017-HOU-71` の行）

止まる条件

* #541 に他のセッションの着手中のコメントがある
* 手順1 のシートの照合が合わない（全行数・通期のリーグの値・2回の読み込み）、または手順1 の生成物に差がある（手順2 に入らず、止まった時点でログを cloudflare へ入れてよい。ドキュメントのみ）
* 未マージの work/ ブランチ（`work/1017-amp` を含む）が手順2 で変える関数と同じ関数を変えていて、取り込みで衝突する。両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよく、解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* 手順3 の生成物に差がある、画面の文字が本番と違う、ページのエラーが出る
* 固定の5問の答えが `docs/notes/ask-worker.md` の期待と違う（マージ後なので戻さずに止まり、違った答えを書く）
* テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の失敗は、今回の変更による失敗と無関係な失敗を分ける。無関係なら原因を報告に書き、残りの手順（本番の確かめ・固定の5問・issue のコメントとクローズ）を進めてよい。今回の変更による失敗なら、それ以上進めずに報告する

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、手順2 で残した通期のリーグ名の判断（あれば）とその理由を書く
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たればマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-HOU-71.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-HOU-71 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 68d72850）: https://github.com/retroeater/mj-logs/tree/main/guide/68d72850

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/68d72850/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/68d72850/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/68d72850/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/68d72850/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/68d72850/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/68d72850/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/42c384b5.md
