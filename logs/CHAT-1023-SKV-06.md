# CHAT-1023-SKV-06

- 着手日時: 2026-10-23
- 対象issue: #577・#572
- ブランチ: work/1023-skv-play（ログ）・work/1023-skv-detect
- 着手時HEAD: work/1023-skv-play f9758350c・work/1023-skv-detect b46885ed6

## 指示

【Claude作成】Claude Code 向け指示：SKV-03（ライブ枠の卓も「▶ 再生」）と SKV-04（毎週土曜の ABEMA の新しい回の検知）を cloudflare へマージする Chat-Ref: CHAT-1023-SKV-06 マージ: 承認済み（チャットで）。対象は work/1023-skv-play（CHAT-1023-SKV-03）と work/1023-skv-detect（CHAT-1023-SKV-04）の成果物。下の「止まる条件」に当たったブランチはマージしない 貼る時機: いつでも（SKV-03・SKV-04 は判断待ちで止まっている。2026-10-23 にログで確かめた）。できれば 2026-10-24（土）04:00 JST の毎朝の運行の前に 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1023-skv-play・work/1023-skv-detect への push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。この指示のログは work/1023-skv-play に置く。 作業ブランチ: クラウドセッションで実行する。未マージの work/1023-skv-play と work/1023-skv-detect を続けて使う（SKV-03・SKV-04 の成果物をマージするため）。それぞれ `git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
CHAT-1023-SKV-03・SKV-04 の判断待ちに答え、2本を cloudflare へマージする。SKV-04 の検知の初回は、マージ後の最初の土曜の 04:00 JST の毎朝の運行（run-daily.yml）。
決定（2026-10-23、平野さん）

* SKV-03 はこのままマージしてよい（ライブ枠の卓のヒーローの画像はリンクにしないまま、ボタンだけ「▶ 再生」。チャット側がプレビューの 20261025-s1-a.html で「▶ 再生」・行き先がライブ枠の URL であることを確かめ、平野さんに示した）
* SKV-04 はマージしてよい（作業ブランチの手動実行 run 39934617712 で、既にある回 p27 が「ある」、p28〜p33 が「無い」と出た結果を示した）

前提（チャット側。平野さんの決定ではない）

* SKV-03 の「判断が必要なこと」の短縮 URL（`abema.go.link`）の卓は今のまま「ABEMA で見る」とし、#577 に残す（チャット側の案。今の【3】に短縮 URL の卓は無い）
* SKV-04 の前提との違い2点（regenerate-saikyo.yml の workflow_call に入力 `scheduled` を足して run-daily から渡した／回のページは国外から読めないので題を出さず、サムネイルの有り無しで回の ID と URL だけを知らせる）は、SKV-04 の報告のとおりで受け入れる（チャット側の案。平野さんにはマージの可否だけを聞いた）
* 取り込みで生成物でない文書が衝突したとき: `docs/decisions/*.md`・`docs/logs/`・docs/notes/github-actions.md の一覧の表など、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* 生成物（saikyo/・sitemap*.xml）が取り込みで衝突したときは、origin/cloudflare の側を採って解いてよい（コンテナでは生成できない）。SKV-03 のマージで `scripts/generate_saikyo_pages.py` が cloudflare に入ると `regenerate-page.yml` が push を検知して saikyo_pages を生成し直す（docs/notes/github-actions.md「regenerate-page.yml」、要確認）ので、ボタンの変更はそのコミットで本番に入る
* SKV-04 は `scripts/lib/**` を変えるので、マージの push で `regenerate-page.yml` が動く見込み（対象は `--changed` の判定による）。ページの差分は無いか、シートの変化だけで説明できるものの見込み
* run-daily.yml は手で起動しない（`scheduled` で起動すると各本が書く。SKV-04 のログのとおり）

手順

1. work/1023-skv-play に origin/cloudflare を取り込み、`python3 -m unittest discover -s scripts/tests` を流して cloudflare へマージする。マージの push で動いた `regenerate-page.yml` の run と結論、コミット（`chore: regenerate ...`）があればその `--stat`（ファイル名を全件、saikyo/・sitemap*.xml・その他に分ける）、Workers Builds の本番の check-run の結論を書く。本番の saikyo/2026/20261025-s1-a.html・20261025-s1-b.html・20261101-s1-a.html・20261101-s1-b.html を `?v=<未使用の値>` を付けて取得し、ボタンの行を引用する（4ページとも「▶ 再生」で行き先がライブ枠の URL なら済み）。#577 にマージの結果をコメントする（本文・状態・ラベルは変えない）
2. work/1023-skv-detect に origin/cloudflare（手順1の後）を取り込み、テストを流して cloudflare へマージする。マージの push で動いたワークフローの run と結論、生成のコミットがあればその `--stat` と変わった理由、Workers Builds の check-run の結論を書く。#572 に、マージしたことと、初回は次の土曜の 04:00 JST の毎朝の運行であることをコメントする（本文・状態・ラベルは変えない。#572 は知らせ先なので閉じない）
3. CHAT-1023-SKV-03・SKV-04 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1023-SKV-06` を足し、マージの行を直す。docs/decisions/saikyo.md に今回の決定を足す。作業ブランチの片付けは docs/notes/branch-operations.md に従う（ワークフローの結果の成否に条件づけない）

止まる条件

* SKV-03・SKV-04 のログの状態が判断待ちでない、またはブランチの先頭が両ログの報告の後に変わっている（そのブランチだけ外し、もう片方は進めてよい）
* 取り込みで、上の「前提」で解いてよいとした以外の衝突が出た（そのブランチだけ外して進め、両方なら止まる）
* テストが失敗した。今回の変更による失敗と無関係な失敗を分けて書く（今回の変更による失敗なら、そのブランチはマージしない）
* マージ後の生成のコミットに saikyo/・sitemap*.xml 以外の変更、または今回の変更とシートの変化で説明できない saikyo/ の変更がある（戻さずに内訳を書いて止まる）
* マージ後の check-run の失敗は、今回の変更による失敗と無関係な失敗を分けて書く。無関係なら原因を書いたうえで残りの手順を進めてよい
* 手順1の後、4ページのどれかが「ABEMA で見る」のまま（生成の run の結論とボタンの行を書いて止まる。もう一度の手動実行はしない）
* 未マージの work/ ブランチ（work/1022-hth・work/1023-skp・work/1023-skv など）が、2本の変えた同じ関数・同じ行を変えている、または取り込みで衝突する（衝突する側のブランチだけ外して進め、両方なら止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-SKV-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-SKV-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0fe6b19b）: https://github.com/retroeater/mj-logs/tree/main/guide/0fe6b19b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0fe6b19b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0fe6b19b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0fe6b19b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0fe6b19b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0fe6b19b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0fe6b19b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c49be44e.md
