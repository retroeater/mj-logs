# CHAT-1023-SKP-03

- 着手日時: 2026-10-23
- 対象issue: #626（作成）・#627（公開）
- ブランチ: work/1023-skp（CHAT-1023-SKP-01・02 の続き）
- 着手時HEAD: 61db3c7b1

## 指示

【Claude作成】Claude Code 向け指示：最強戦の個人成績・ランキング・質問欄・新トップ（SKP-01・02）を、読みの無い124名を残したまま cloudflare にマージする（未公開のまま、#626）
Chat-Ref: CHAT-1023-SKP-03
マージ: 承認済み（チャットで。2026-10-23 に平野さんが仮置き29件の29番目で「直してそのままマージ」を選び、SKP-02 の止まりへの回答で「読みは後で入れる」を選んだ。条件は「止まる条件」）
貼る時機: いつでも（CHAT-1023-SKP-02 の続き。同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1023-skp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1023-skp を続けて使う（CHAT-1023-SKP-02 の判断待ちの続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。生成されたページの衝突は CLAUDE.md「ブランチ運用」のとおり、取り込んだ後のスクリプトで生成し直して解く

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1023-SKP-02 は、読みの無い出場者が124名いて止まる条件に当たり、止まった。その124名を残したまま、SKP-02 の手順3（生成し直し・差分の確かめ・マージ・本番の確かめ）から再開する。新しいページは noindex・どこからも辿れないまま（公開は #627）。

### 決定（2026-10-23、平野さん）
- 読みの無い124名（全員「連盟プロ以外」の「名前かな」が空欄。名前は CHAT-1023-SKP-02 のログの `## 経過`「20 の読みが付かない出場者」の表）は、後で平野さんが「名前かな」を入れる。それまでは名前だけで引ける形のまま、この版をマージしてよい
- 入力は公開（#627）までに行う。入れた分は翌朝の再生成で反映される
- SKP-02 の止まる条件「読みが付かない人が44人を超える」は、この指示では外す。そのほかの止まる条件は SKP-02 のまま

### 前提（チャット側。平野さんの決定ではない）
- 16・24（新しいページの分）・26 の直しと 28 の確かめは済んでおり、作業ブランチに push 済み（SKP-02 の `## 報告`。コミット cdb4c4294・955267472）
- マージの後の自動処理の見込み: Workers Builds の本番反映（サイト `mj` と、`workers/` が変わるので Worker `mj-ask`）。翌朝の `regenerate-saikyo.yml` と週次の `all` が新しいページも生成し直す（要確認。SKP-01 で対象に足したかで変わる。報告に書く）

## 手順
1. CHAT-1023-SKP-02 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1023-SKP-03` を足す。上の「決定」を docs/decisions/saikyo.md に足す。#627 に、公開の前に平野さんが「連盟プロ以外」の「名前かな」を124名分入れること（名前の一覧は CHAT-1023-SKP-02 のログを指す）を、公開の指示に入れる項目としてコメントする
2. origin/cloudflare を取り込み（要れば）、`python3 -m unittest discover -s scripts/tests`・`node --test 'workers/ask/test/*.test.mjs'`・`regenerate-page.yml` の check（saikyo_pages・ask_data_saikyo）を流す。作業ブランチで生成し直し、origin/cloudflare との差分を種類ごとに数えて書く。読みの無い人の数を名前つきで書き、SKP-02 の124名との差（増えた人・減った人）だけを表にする
3. CLAUDE.md「ブランチ運用」・docs/notes/branch-operations.md「マージするとき」のとおり `git push origin work/1023-skp:cloudflare` でマージする。本番で `https://ryoei.pro/saikyo/next/?v=<マージのSHA>`・`/saikyo/players/`・`/saikyo/ranking/champion/`・`/saikyo/ranking/final/` が 200 で noindex を含むこと、`/saikyo/ranking/final/` の題名が「ファイナル」であること、`/saikyo/` が作業ブランチの `saikyo/index.html`（マージ前と同じ）であること、Worker `mj-ask` のビルドが成功したことを確かめる（15分で本番が古いままなら「未確認の項目」に回して完了にする）。#626 にマージしたこと（公開は #627）をコメントし、閉じない。マージ後の作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」に従う（関係のない検証の成否に条件づけない）

## 止まる条件
- テスト・check が通らない（原因を書いて止まる）
- origin/cloudflare との差分に、SKP-01・SKP-02 の直しで説明できないものがある。許すのは、SKP-01・SKP-02 の `## 報告` と `## 経過` に書いたファイル・docs/decisions・docs/logs、取り込みで入ったシートの変化で説明できる saikyo/ の生成物の差分（その差分は別に数えて書く）。公開済みのページ（`saikyo/index.html`・年度・対局・卓・`saikyo/vs/`）と `sitemap-saikyo.xml` が、シートの変化以外の理由で変わっていたら止まる
- 読みの無い人が SKP-02 の124名から5名を超えて増えた（「プロ」や名簿の側が原因の人が出たとみなし、名前を書いて止まる）
- 未マージの work/ ブランチが `scripts/generate_saikyo_pages.py`・`assets/saikyo.js`・`assets/houou_ask.js`・`workers/ask/` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージ後の check-run の失敗: 今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いたうえで、残りの手順（本番の確かめ・#626 のコメント）を進めてよい。自分の変更で落ちると分かっているテストは直してよい

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-SKP-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-SKP-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: 無し（`git log --all --grep=CHAT-1023-SKP-03`・docs/logs）
- 作業ブランチ: ローカル・リモートとも work/1023-skp が 61db3c7b1 で一致。`origin/cloudflare`（7c1c0c3ba、12コミット先）は祖先でない（merge で取り込む）
- 0章: 指示欄の末尾は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠け無し

## 報告

- 状態: 中断（作業中）
- ブランチ: work/1023-skp
- ログ: https://github.com/retroeater/mj/blob/work/1023-skp/docs/logs/CHAT-1023-SKP-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1023-skp
- 確認用URL: なし
- マージ: 未
- issue: #626・#627
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 067eacf2）: https://github.com/retroeater/mj-logs/tree/main/guide/067eacf2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/067eacf2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/067eacf2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/067eacf2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/067eacf2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/067eacf2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/067eacf2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c49be44e.md
