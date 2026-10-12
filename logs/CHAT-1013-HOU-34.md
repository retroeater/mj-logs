# CHAT-1013-HOU-34

- 着手日時: 2026-10-13
- 対象issue: #518
- ブランチ: work/1013-hou
- 着手時HEAD: 1e009af3

## 指示

【Claude作成】Claude Code 向け指示：HOU-33（個人成績の直し5点：現在のリーグのチップ・期別の余白・鳳凰位の行は開かない・通算のグラフの大きさ・リーグの吹き出しの3行）を cloudflare へマージする（houou/ は未公開のまま） Chat-Ref: CHAT-1013-HOU-34 マージ: 承認済み（チャットで。平野さんが HOU-33 のプレビューを見て「よい」と 2026-10-13 に返答した） 貼る時機: CHAT-1013-HOU-33 の判断待ちの後（いつでも。新しいセッションに貼る） 作業ブランチ: クラウドセッションで実行する。未マージの work/1013-hou を続けて使う（HOU-33 の変更のマージのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。生成されたページの衝突は CLAUDE.md「ブランチ運用」のとおり生成し直して解く。それ以外の衝突は解かずに止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1013-hou の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1013-HOU-33 のログの `## 報告` を読み、状態が「判断待ち」であることを確かめる（でなければ何もせず止まる）。HOU-33 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1013-HOU-34` を足す。 この指示は新しいセッションで貼る前提。このセッションが新しく始めたものかをログの経過に書く。

目的
HOU-33 の変更（判断待ち）を、平野さんのプレビューの確認を受けて cloudflare へ入れる。houou/ は未公開（noindex・メニュー未掲載）のまま。新しい変更は入れない。
決定（2026-10-13、平野さん）

* HOU-33 のプレビューの状態でマージしてよい（HOU-33 の報告の「仮置きにしたもの」〈現在のリーグの決め方・チップの大きさと間・期のカードの左右の余白・鳳凰位の行の印の欄・吹き出しの形〉、「前提から変えたこと」〈A1・A2・鳳凰位はリーグのタブの第43期の段を使う〉、「指示との違い」〈チップの出ない在籍者の名前の一覧はログに書かない〉を含む）

前提（チャット側。平野さんの決定ではない）

* 変わるのは HOU-33 のコミット（`scripts/generate_houou_pages.py`・`assets/houou.js`・`style.css` の `.mj-houou-*`・`houou/players/index.html`・docs〈decisions/houou.md・notes/houou-top.md〉・docs/logs とスクリーンショット）と、origin/cloudflare の取り込みの分だけ（要確認）
* マージの後に自動の再生成（regenerate-page.yml）が走ると、houou/ 以外のページにもシートの変化の分の差分が出ることがある。今回の変更と関係なければ止めない（ログに書く）

手順

1. 取り込みと再検証: origin/cloudflare を取り込み（上の衝突の扱い）、取り込んだ後に生成し直して `generate_houou_pages.py --check` とテストを通す。`git diff --stat origin/cloudflare...HEAD` を種類（生成スクリプト・JS・CSS・houou/ の生成物・docs・docs/logs）に分けてログに書き、上の「前提」の範囲の外のファイルが無いことを確かめる
2. マージ: CLAUDE.md「ブランチ運用」「マージするとき」のとおり（push 直前に再 fetch して祖先を確かめ、`git push origin work/1013-hou:cloudflare`）。HOU-33 のログの状態を CLAUDE.md「作業ログ」節に従って扱う
3. 本番の確かめ: Workers Builds の check-run の成否、マージ後の regenerate-page.yml の差分（houou/ 以外が出たらその中身と理由）。本番の `https://ryoei.pro/houou/players/?v=<未使用の値>` を headless Chrome の 375px で開き、`?name=白鳥翔`・`?name=平野良栄&view=total`・`?name=平野良栄&view=league`・`?name=東幸一郎` で HOU-33 と同じ見え方であること、JS のエラー 0、noindex が付いたままであることを確かめる。#518 に、ログの `## 報告` から引用して完了のコメントを書く（本文・状態は変えない）

止まる条件

* `git diff --stat` に上の「前提」の範囲の外のファイルがある（取り込みの分と区別して報告する）
* 取り込みの後の `--check` やテストが今回の変更で落ちて直せない（無関係な失敗は原因を書いて進めてよい）
* 生成物でない文書の衝突で、両立しないもの
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
* マージ後の check-run の失敗は、今回の変更によるものなら止まって報告する。無関係なら原因を報告に書いたうえで残りの手順を進めてよい

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（承認済み）。作業ブランチの削除は delete-merged-branches.yml に任せる
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1013-HOU-34.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1013-HOU-34 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e3b1b713）: https://github.com/retroeater/mj-logs/tree/main/guide/e3b1b713

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3b1b713/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3b1b713/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3b1b713/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3b1b713/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3b1b713/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3b1b713/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/3ce5d549.md
