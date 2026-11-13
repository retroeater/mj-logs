# CHAT-1113-SKP-27

- 着手日時: 2026-11-13
- 対象issue: なし
- ブランチ: work/1113-skp-abema-regen
- 着手時HEAD: 474170e3c（着手時に origin/cloudflare を merge で取り込んだ）

## 指示

【Claude作成】Claude Code 向け指示：（CHAT-1113-SKP-26 の続き）最強戦シートの「表示」を直した後に再生成し、義兄弟合戦の3卓とアマ最強位決定戦のライブ枠を本番で確かめる Chat-Ref: CHAT-1113-SKP-27 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（このセッションのコミットはログだけ。ページの再生成のコミットは regenerate-saikyo.yml が cloudflare へ入れる） 貼る時機: いつでも（CHAT-1113-SKP-26 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチの push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1113-skp-abema-regen を続けて使う（CHAT-1113-SKP-26 のログがあり、判断待ちの続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」） 取り込みの衝突: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1113-SKP-26 のログ（docs/logs/CHAT-1113-SKP-26.md）の `## 報告` の状態の末尾に `/ 続き: CHAT-1113-SKP-27` を足す。

目的
CHAT-1113-SKP-26 は「最強戦」シートに義兄弟合戦の決勝の卓・アマ最強位決定戦の対局が無い（と見えた）ため、本番に反映せずに止まった。平野さんがシートを直したので、SKP-26 の手順2・3をやり直して本番に出し、確かめる。
決定（2026-11-13、平野さん）

* シート側を直してから再生成し、1回の update で出す（SKP-26 の判断待ちへの回答。A卓・B卓だけ先に出す案は採らない）
* ザ・リベンジは対局者が未定なので、「最強戦」シートの「表示」を N のままにする。今回はザ・リベンジのページ・ライブ枠を出さない

前提（チャット側。平野さんの決定ではない）

* 平野さんの申告（未確認、2026-11-13）: 義兄弟合戦の決勝と、全国アマチュア最強位決定戦・ザ・リベンジの行は「最強戦」シートに前から入っていたが、対局者未定で表示されないよう「表示」を N にしていた。義兄弟合戦の決勝とアマ最強位決定戦は「表示」を Y にした（対局者を入れたかは要確認）。ザ・リベンジは N のまま
* 「最強戦」シートは見出し「表示」が Y の行だけ読む（docs/notes/saikyo-page-design.md「1. データ源」）
* SKP-26 の dry_run（ログの「手順2」の表）: 回が当たった卓 620 / 647・なし 68（当たる卓なし 63）。【1】の 2026 の最大の回は p36。なしの1回は義兄弟合戦の決勝卓の回と推定（未確認）
* ザ・リベンジの【3】の行は、今回はどの卓にも当たらないのが想定どおり（止まる条件にしない）
* 卓のページが増えるので、saikyo/2026/ の対局・卓のページ、年度のページ、sitemap-saikyo.xml、関連の卓のカードが変わる見込み。ほかのシート側の変化が混ざることもある

手順

1. シートの読み直しと check: `gh run list` で再生成どうしが並走しないことを確かめる（実行中があれば待つ。15分を超えたら止まる）。regenerate-page.yml を作業ブランチの ref・入力 check で、対象を最強戦のページに絞って手動実行し、止まる理由・警告を全部ログに書く（止まる理由があれば止まる）。あわせて、作業ブランチ側の生成の出力から、義兄弟合戦の決勝の卓・アマ最強位決定戦の対局と卓が読めたか（対局日・ステージ・卓・対局者の人数）、年度・対局・卓のページの件数（SKP-26 では 882）がいくつ増えるかを表にする。対局者が空の卓があれば、生成がどう扱ったか（警告・ページの見え方）を書く
2. 確かめの実行: regenerate-saikyo.yml を ref cloudflare、abema: dry_run、再生成なしで手動実行し、SKP-26 の dry_run と並べて表にする。義兄弟合戦の決勝卓の回が卓に当たり、「なし」が SKP-26 から 1 減っていなければ止まる（回のIDと題名を、注釈・出力から分かる範囲で書く）
3. 本番への反映: regenerate-saikyo.yml を ref cloudflare、abema: update、再生成ありで手動実行し、終わるまで待つ（合計30分まで延長してよい）。再生成のコミットの変更ファイルを全部一覧にし、「義兄弟合戦」「アマ最強位決定戦」「シート側のほかの変化」「説明できない」に分ける（同じ形の行が多いときは機械で照合して数で書く）。本番を `.html?v=<未使用の値>` 付き・UA に `mj-internal` を含めて読み、次を表にする:
   * 義兄弟合戦の3卓（A卓・B卓・決勝）: 「▶ 再生」が卓ごとの動画（abema.tv/video/episode/…）を指し、回のIDと題名の卓が合っている。ライブ枠のリンクが無い。サムネイルが HTTP 200
   * アマ最強位決定戦の各卓: 「▶ 再生」がライブ枠を指している。【3】の行のうち卓に当たらなかったもの（ザ・リベンジ以外）があれば、鍵のどの列が違うか

止まる条件

* 手順1の check に止まる理由がある、または義兄弟合戦の決勝の卓・アマ最強位決定戦が読めていない（「表示」が Y になっていない見込み。読めた行と読めない行を書く）
* 手順2で、義兄弟合戦の決勝卓の回が卓に当たらない、または「なし」が 1 減らない
* 手動実行のどれかが失敗した（手順3の前なら update は実行しない。原因をジョブのログから書く）
* 手順3の後で、義兄弟合戦の卓のどれかが卓ごとの動画以外を指す、またはアマ最強位決定戦にライブ枠が出ない卓がある。このときはコミットを戻さず、どの層（【2】か【3】）から来た値か、当たらない理由を書いて判断待ちにする（【3】の行は行番号でなく卓の鍵〈年度・対局日・対局・ステージ・卓〉で示す）
* 「説明できない」変更ファイルがある（戻さず、一覧と理由の推定を書いて判断待ちにする）
* 未マージの work/ ブランチが saikyo/ の生成・scripts/lib/abema_book.py・scripts/update_abema_book.py の同じ関数を変えている（`git branch -r --no-merged origin/cloudflare` から確かめる。別の箇所なら止まらずログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。issue: なし（issue は変えない）
* マージは冒頭の「マージ:」の行のとおり（SKP-26 のログも一緒に cloudflare へ入る）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-SKP-27.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-SKP-27 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj be8d366d）: https://github.com/retroeater/mj-logs/tree/main/guide/be8d366d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/be8d366d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/be8d366d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/be8d366d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/be8d366d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/be8d366d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/be8d366d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/613f050c.md
