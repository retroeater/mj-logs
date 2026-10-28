# CHAT-1028-LPR-04

- 着手日時: 2026-10-28
- 対象issue: なし（LPR-01 の指摘 S12・S13・S14・S16・S17）
- ブランチ: work/1028-lpr-sk
- 着手時HEAD: bf4ef24c8

## 指示

【Claude作成】Claude Code 向け指示：成績ページ群の横断レビュー（LPR-01）の最強戦の指摘5件を直す（S12・S13・S14・S16・S17） Chat-Ref: CHAT-1028-LPR-04 マージ: 承認済み（チャットで）。条件は下の「止まる条件」。1つでも当たればマージせず止まる 貼る時機: CHAT-1028-LPR-03 のマージの後（style.css を同じく変えるため）。LPR-03 と同じセッションでよい 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1028-lpr-sk〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1028-lpr-sk の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1028-lpr-sk を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1028-lpr-sk origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1028-LPR-03 のコミットが origin/cloudflare に入っていること（`git log origin/cloudflare --grep 'CHAT-1028-LPR-03'` が1件以上で、docs/logs/CHAT-1028-LPR-03.md の `## 報告` の「マージ」が済）を確かめ、満たさなければ何もせず止まる。

目的
LPR-01（docs/logs/CHAT-1028-LPR-01.md）の指摘のうち、最強戦（saikyo/）の5件を直す。見出しの大会名（S12）、押せる大きさ（S13）、フォーカス枠（S14）、JS の写しの削除（S16）、検索の正規化のテストと対戦成績の絞り込みのかな・カナの区別（S17）。各指摘の内容と証拠は LPR-01 のログ `## 経過`「指摘の一覧」の元の番号の行が正。
決定（2026-10-28、平野さん）

* 直すのは S12（①-2）・S13（③-4）・S14（③-6）・S16（②-10）・S17（②-11）
* 最強戦（saikyo/）は告知済みとみなしてよい（docs/notes/page-announcement.md の告知の期間の守り方は当てない）
* マージは、テストが通り、再生成の差分が指摘で説明できる種類だけで、プレビューでの計測が通れば行ってよい

前提（チャット側。平野さんの決定ではない）

* 各件の場所（LPR-01 のログの記載。要確認: 実物と食い違えばその件だけ外して進め、外した件と理由を報告に書く）:
   * S12: saikyo/years/・saikyo/vs/ の h1 が「年度別」「対戦成績」。saikyo/players・ranking と4大会は「<大会> <ページ>」の形なので、「最強戦 年度別」「最強戦 対戦成績」にそろえる（title・パンくずは変えない）
   * S13: saikyo/players/?name=… の「対戦成績を見る ›」（`a.mj-houou-player-span`、inline）が約100×15px。24px 以上にする。`mj-houou-player-span` は4大会の個人成績でも使う名前なので、規則は最強戦の該当のリンクだけに当て、4大会の見た目を変えない
   * S14: saikyo/vs/・saikyo/years/ のパンくずの「最強戦」と vs/ の「もっと見る」ボタンのフォーカス枠が Chrome 既定（auto 1px）。他ページの focus-visible の規則（solid 2px #14459b）を当てる
   * S16: assets/saikyo.js 290〜305行の vs の節の el()・chevron() が、同じファイル46〜60行のトップレベルの関数と字句まで同じ。写しを消してトップレベルの関数を使う
   * S17: 検索の正規化（assets/saikyo.js:39・635、assets/league.js:65、scripts/lib/results.py:51〜54）に JS と Python の一致を見るテストが無い（saikyo.js の playerKey・playerMatches は test_saikyo_vs.py の JS_FUNCTIONS に無い）。また対戦成績の絞り込み（saikyo.js:251・273・514）は normalize だけでカタカナとひらがなを区別し、個人成績の検索は区別しない。絞り込みを個人成績の検索と同じ折り畳み（playerKey の側）にそろえ、一致のテストを足す（例: 「たろう」で「タロウ」が出る）
* 生成物の比べ方: 同じセッションで、origin/cloudflare の状態と変更の後の状態をそれぞれ `python3 scripts/regenerate.py saikyo_pages` で生成して比べる（シートの変化を除くため。共通の関数・定数を変えたら CLAUDE.md「判断・作業の原則」のとおり全ページ）。見込みの差分は saikyo/years/・saikyo/vs/ の HTML の h1（と、S14・S13 で HTML にクラスを足すならその属性）だけ
* プレビューは docs/notes/cloud-sessions.md「ローカル確認の代わりにプレビュー」のとおり。プレビューの URL はログ・issue に書かない
* マージの後、`regenerate-page.yml` が scripts の変更の push で走る。最強戦は毎朝 `regenerate-saikyo.yml` でも再生成される

手順

1. 5件を直す。テストを全部走らせる（`scripts/tests/` と、変えたものに関係する workers のテストがあればそれも）。origin/cloudflare と変更の後で生成して比べ、差分を種類ごとに全件ログに書く
2. 作業ブランチを push してプレビューで計測し、ログに書く: (a) years/・vs/ の h1 が「最強戦 年度別」「最強戦 対戦成績」 (b) 375px と 1280px で「対戦成績を見る ›」の大きさが高さ24px 以上、かつ4大会の個人成績（LPR-01 の手順1の表の URL、選手を選んだ後）の `a.mj-houou-player-span`・`.mj-houou-player-span` の大きさが origin/cloudflare の本番と同じ (c) vs/・years/ のパンくずと「もっと見る」に Tab でフォーカスしたときの outline が solid 2px #14459b (d) 対戦成績の絞り込みでひらがなとカタカナの入力が同じ結果になる（使った入力と件数を書く）。あわせて、24px 未満のタップの対象が saikyo/ の代表のページ（LPR-01 の表）に増えていないことを書く
3. LPR-01 の決定の続きとして、上の「決定」を docs/decisions/README.md の書き方に従って分野のファイルに足し、ログを仕上げる。マージは冒頭の「マージ:」の行のとおり（push 直前の確かめは CLAUDE.md「ブランチ運用」）。マージ後、本番で (a) を `?v=<未使用の値>` 付きで確かめる。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、そのとおりに行う

止まる条件

* 0章の確かめが食い違う
* テストが1つでも落ちる（自分の変更で落ちると分かっているテストは直してよい。直した理由をログに書く）
* origin/cloudflare と変更の後の生成の差分に、years/・vs/ の h1 と、S13・S14 のために足したクラス・属性以外がある
* 手順2の (a)〜(d) のどれかが通らない、または4大会の個人成績の見た目（(b) の大きさ）が変わる
* 変えたファイルが、scripts/generate_saikyo_pages.py・assets/saikyo.js・style.css・scripts/tests/ の関係するテスト・saikyo/ の生成物・docs/decisions/・docs/logs/ 以外に及ぶ（S17 で assets/league.js や scripts/lib/results.py を変える必要が出たら、変えずに止まって理由を報告する）
* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が上のファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所なら止まらずログに書く。重なる件だけ外して進め、全部が重なるときだけ止まる）
* マージ後の check-run の失敗が今回の変更による（無関係な失敗なら原因をログに書いて、本番の確かめを進めてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1028-LPR-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1028-LPR-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7e8e5a5a）: https://github.com/retroeater/mj-logs/tree/main/guide/7e8e5a5a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7e8e5a5a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7e8e5a5a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7e8e5a5a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7e8e5a5a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7e8e5a5a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/7e8e5a5a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cfad705f.md
