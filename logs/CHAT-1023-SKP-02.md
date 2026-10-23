# CHAT-1023-SKP-02

- 着手日時: 2026-10-23
- 対象issue: #626（作成）・#627（公開）
- ブランチ: work/1023-skp（CHAT-1023-SKP-01 の続き）
- 着手時HEAD: e0d42a804

## 指示

【Claude作成】Claude Code 向け指示：最強戦の個人成績・ランキング・質問欄・新トップ（SKP-01）の仮置きを確定し、4点を直して cloudflare にマージする（未公開のまま、#626） Chat-Ref: CHAT-1023-SKP-02 マージ: 承認済み（チャットで。平野さんが仮置き29件に1問ずつ答え、29件目で「直してそのままマージ」を選んだ。条件は「止まる条件」） 貼る時機: いつでも（CHAT-1023-SKP-01 の続き。同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1023-skp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1023-skp を続けて使う（CHAT-1023-SKP-01 の判断待ちの続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成されたページの衝突は CLAUDE.md「ブランチ運用」のとおり、取り込んだ後のスクリプトで生成し直して解く

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1023-SKP-01 の仮置き（一覧の1〜29）を確定する。そのうち4点を直し、1点を確かめたうえで、work/1023-skp を cloudflare にマージする。新しいページは noindex・どこからも辿れないまま（公開は #627）。
決定（2026-10-23、平野さん。SKP-01 の仮置きの番号で書く）

* そのまま確定するもの: 1・2・3・4・7・8・9・10・11・12・13・14・15・17・18・21・22・23・25・27
* 5: 年度ページの年度の選択の先頭の項目「歴代最強位」は、公開のときに行き先を年度別 `/saikyo/years/` へ替える
* 6: `/saikyo/?q=` は公開のときに次のようにする。jpml_pros と卓のページの「すべて見る」は個人成績 `/saikyo/players/?name=` へ直接張り替える。外で共有された `?q=` のリンクだけ、新トップの JS が個人成績へ移す（期限つき）。転送を外す条件は、公開から3か月後の転送の棚卸しで `?q=` の流入が無いと確かめたとき
* 16: 部門名「ファイナル出場」を「ファイナル」に縮める（題名・選ぶ部品・部門別のタブ・質問欄の部門名のすべて）。数え方は7のまま
* 19: 個人成績の名前の面の下に、対戦成績 `/saikyo/vs/?name=<選手名>` への「対戦成績を見る ›」の1行を置く。出すのは対戦成績の公開（#623）と同時で、公開までは出さない
* 20: 連盟外の人にも読みを付ける。「連盟プロ以外」の「名前かな」を使う（ひらがなで引けるようにする）
* 24: ファイナルの決勝の通過（最強位の判定〈`FINAL_MATCH_NAME` × 決勝 × 通過〉と同じ）のピルを「優勝」から「最強位」にする。予選の決勝の通過は「優勝」のまま。最強戦の全ページでそろえる（個人成績・年度ページ・対局のページ・卓のページ・対戦成績）。この指示では新しいページ（個人成績など）だけを直し、公開済みのページ（年度・対局・卓）と対戦成績（HTH の系列が担当）は公開（#627）のときに直す
* 26: 質問欄の歴代最強位の答えの出典の名前を「麻雀最強戦 歴代優勝者」から「麻雀最強戦 歴代最強位」に直す。決勝の出典「麻雀最強戦<年度> 決勝結果」はそのまま
* 28: 質問欄の答えに出た選手のカードの名簿の誤検出（短い名前が文の一部に当たる）は、この指示で確かめる
* 29: 上を直したうえで、このまま cloudflare にマージしてよい

前提（チャット側。平野さんの決定ではない）

* 「連盟プロ以外」の見出しは 名前・名前かな・所属団体・所属補足（docs/decisions/saikyo.md の 2026-10-12〈CHAT-1012-SNM-03〉）。名前かなが空欄の人がいるかは未確認（要確認）
* 公開（#627）の指示に入れる項目: 1（next/ を消す）・2 の4行目・5・6・19・24 の公開済みのページと対戦成績の分。この指示で #627 にコメントして残す
* マージの後の自動処理の見込み: Workers Builds の本番反映（サイト `mj` と、`workers/` が変わるので Worker `mj-ask`）。翌朝の `regenerate-saikyo.yml` と週次の `all` が新しいページも生成し直す（要確認。SKP-01 で `regenerate.py` の対象に足したかで変わる）
* 本番の質問欄での最強戦の試し（27）は、マージの後・公開の前に別の指示で行う

手順

1. CHAT-1023-SKP-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1023-SKP-02` を足す。上の「決定」を docs/decisions/saikyo.md に足す（SKP-01 の仮置きの一覧の番号と中身を引いて、1件1行で書く）。#627 に、公開の指示に入れる項目（「前提」の2つめ）をコメントする。#626 に着手中のコメントを書く
2. 16・20・24（新しいページの分）・26 を直す。20 は、読みが付かない出場者（「名前かな」が空欄の人と、「プロ」「連盟プロ以外」のどちらにもいない人）を全員名前つきで `## 経過` に表で書く（0人ならそう書く）。28 は、名簿の名前のうち、ほかの名簿の名前に含まれるものと2文字以下のものを全件書き出し、それぞれ質問欄の答えに出そうな語（年度・対局名・部門名・ステージ名など）に当たるかを確かめる。当たるものがあれば、その名前を突き合わせから外すか、長い名前を先に取る形に直してテストを足す（直した名前と理由を書く）。当たるものが無ければ直さずに一覧だけ書く
3. origin/cloudflare を取り込み（要れば）、`python3 -m unittest discover -s scripts/tests`・`node --test 'workers/ask/test/*.test.mjs'`・`regenerate-page.yml` の check（saikyo_pages・ask_data_saikyo）を流す。作業ブランチで生成し直し、origin/cloudflare との差分を種類ごとに数えて書く。CLAUDE.md「ブランチ運用」・docs/notes/branch-operations.md「マージするとき」のとおり `git push origin work/1023-skp:cloudflare` でマージする。本番で `https://ryoei.pro/saikyo/next/?v=<マージのSHA>`・`/saikyo/players/`・`/saikyo/ranking/champion/`・`/saikyo/ranking/final/` が 200 で noindex を含むこと、`/saikyo/` が作業ブランチの `saikyo/index.html`（マージ前と同じ）であること、Worker `mj-ask` のビルドが成功したことを確かめる（15分で本番が古いままなら「未確認の項目」に回して完了にする）。#626 にマージしたこと（公開は #627）をコメントし、閉じない。マージ後の作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」に従う（関係のない検証の成否に条件づけない）

止まる条件

* テスト・check が通らない（原因を書いて止まる）
* origin/cloudflare との差分に、SKP-01 と上の直しで説明できないものがある。許すのは、SKP-01 の `## 報告` と `## 経過` に書いたファイル・この指示の直し・docs/decisions・docs/logs、取り込みで入ったシートの変化で説明できる saikyo/ の生成物の差分（その差分は別に数えて書く）。公開済みのページ（`saikyo/index.html`・年度・対局・卓・`saikyo/vs/`）と `sitemap-saikyo.xml` が、シートの変化以外の理由で変わっていたら止まる
* 20 の読みが付かない人が出場者の1割（44人）を超える（人数と名前を書いて止まる。「名前かな」の入力を平野さんに頼むかを決めるため）
* 28 で直すと、ほかの大会（houou/・ouka/・jwrc/・wrcr/）の選手のカードの出力が変わる
* 未マージの work/ ブランチが `scripts/generate_saikyo_pages.py`・`assets/saikyo.js`・`assets/houou_ask.js`・`workers/ask/` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗: 今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いたうえで、残りの手順（本番の確かめ・#626 のコメント）を進めてよい。自分の変更で落ちると分かっているテストは直してよい

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-SKP-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-SKP-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: 無し（`git log --all --grep=CHAT-1023-SKP-02`・docs/logs）
- 作業ブランチ: ローカル・リモートとも work/1023-skp が e0d42a804 で一致。`origin/cloudflare` は祖先でない（後で merge で取り込む）
- 0章: 指示欄の末尾は指示文の最後の行「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠け無し

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1023-skp
- ログ: https://github.com/retroeater/mj/blob/work/1023-skp/docs/logs/CHAT-1023-SKP-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1023-skp
- 確認用URL: なし
- マージ: 未
- issue: #626・#627
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 560a6f35）: https://github.com/retroeater/mj-logs/tree/main/guide/560a6f35

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/560a6f35/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/560a6f35/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/560a6f35/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/560a6f35/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/560a6f35/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/560a6f35/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c49be44e.md
