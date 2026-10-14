# CHAT-1014-SKD-13

- 着手日時: 2026-10-14
- 対象issue: #566
- ブランチ: work/1014-skd
- 着手時HEAD: 1c5d1b861

## 指示

【Claude作成】Claude Code 向け指示：SKD-12（最強戦の新しい年度・対局のページの白地のナビと「麻雀最強戦2025 ▾」の題名）を cloudflare へマージする（#566）
Chat-Ref: CHAT-1014-SKD-13
マージ: 承認済み（チャットで。チャット側が SKD-12 のプレビューを見て崩れが無いことを確かめ、平野さんにプレビューのリンクを送った。この指示文を貼ることが SKD-12 の版の承認）
貼る時機: いつでも（CHAT-1014-SKD-12 は判断待ち。同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-skd の push と、cloudflare へのマージの push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1014-skd を続けて使う（SKD-12 の成果物をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成されたページだけが衝突したら、取り込んだ後のスクリプトで生成し直して解く。生成物でない文書の衝突は、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。マージの前に CLAUDE.md「ブランチ運用」と docs/notes/branch-operations.md「マージするとき」「生成物を含む作業ブランチを取り込む・マージするとき」を読む。

## 目的
SKD-12 の変更（新しい年度・対局のページの共通ナビを白地にし、絞り込みの固定バーをやめ、年度ページの題名を「麻雀最強戦2025 ▾」〈下から出るシートで年度を選ぶ〉、対局のページの題名を対局名にする。navbar.js の変更を含む）を本番に入れる。ページは未公開のまま（公開は #549）。

### 決定（2026-10-14、平野さん）
- SKD-12 の決定（C・対局のページの題名・白地のナビの範囲・残りの順）は SKD-12 のログ・docs/decisions に記録済みなら重ねて書かない
- SKD-12 の版をマージしてよい（この指示文を貼ることが承認）

### 前提（チャット側。平野さんの決定ではない）
- チャット側は SKD-12 の版（85d423c62）を手元で 375px の明・暗で見て、ログの「確認用URL」の印（白地のナビ・28px の「麻雀最強戦2025 ▾」・濃色の固定バーが無い）、対局のページの題名と日付、暗い配色で開いたナビの「最強戦」の印、今の年度ページ `saikyo/2025.html` のナビと固定バーが変わっていないことを確かめた
- マージの前の再検証: origin/cloudflare を取り込んだ後に `python3 scripts/regenerate.py saikyo_pages` で生成し直し、`python3 -m unittest discover -s scripts/tests` を通す。生成物の差（取り込み後の origin/cloudflare との比較）を種類に分ける。許すのは、新しい年度ページ・対局のページの差（SKD-12 のログの差の表）と `navbar.js`・`style.css` の SKD-12 の変更と、シートの変化・X の画像の URL の変化で説明できる差だけ。ほかの saikyo/ のページに、シートの変化と X の画像の URL の変化以外の差が出たら止まる（出た種類と件数を書く）
- 未マージの work/ ブランチ（`work/1014-plb` など）が `navbar.js`・`scripts/generate_saikyo_pages.py`・`style.css`・`assets/saikyo.js`・`assets/houou_race.js` の同じ行・同じ関数を変えていないことを、取り込み・マージの前に確かめ直す
- マージ後: 走った run と `chore: regenerate …` のコミットの差の種類、Workers Builds の本番の check-run の成否を書く。本番の `https://ryoei.pro/saikyo/2025/index.html?v=<マージの SHA>` の HTML に SKD-12 の印（題名の「麻雀最強戦2025」とシート、固定バーが無い）があり、noindex が残っていることを確かめる。`navbar.js` を変えたので、本番の houou/・今の年度ページ・live/ の1ページずつで、ナビの出し分けが前と同じであることを headless Chrome で確かめる（`navbar.js` は30日キャッシュでないか `_headers` を確かめ、古い版が返るなら書く）
- ログの状態: SKD-12 のログの状態の末尾に ` / 続き: CHAT-1014-SKD-13` を足す（`docs/logs/_template.md` と docs/notes/branch-operations.md「作業ログの寿命」のとおり）
- #566 にマージ済みのコメントを書く（本文・状態・ラベルは変えない。閉じない）
- 作業ブランチ work/1014-skd のマージ後の片付けは、SKD-09 と同じく `delete-merged-branches.yml` に任せてよい（自分で消すなら docs/notes/branch-operations.md「ブランチを削除するとき」を読み、消す前の先頭 SHA をログに書く）

## 手順
1. origin/cloudflare を取り込み、生成し直してテストを通し、生成物の差を分類する（上の許す範囲を外れたら止まる）
2. ログの状態を直して、`git merge-base --is-ancestor origin/cloudflare HEAD` を確かめてから `git push origin work/1014-skd:cloudflare` でマージする
3. マージ後の自動処理・check-run・本番の HTML を確かめ、#566 にコメントする

## 止まる条件
- 生成物の差が上の許す範囲を外れる、またはテストが落ちる
- 取り込みで、生成されたページ以外の衝突のうち、両立しないものが出る
- 未マージの work/ ブランチが、この指示で入れるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージ後の check-run の失敗は、この変更によるものなら止まって報告する。無関係なら原因を報告に書いたうえで、残りの手順を進めてよい

## 完了条件
- 取り込みの結果、生成物の差の分類と件数、テストの結果、マージの SHA、マージ後の run と `chore: regenerate` の差、check-run の成否、本番の HTML の印と noindex、ログの状態の直し、#566 のコメントの URL（消したならブランチの先頭 SHA）がログにある
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-SKD-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-SKD-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 0章: 「指示」欄の末尾と指示文の最後の行が一致。Chat-Ref の既存コミットなし。マージの前に CLAUDE.md「ブランチ運用」と docs/notes/branch-operations.md「マージするとき」「生成物を含む作業ブランチを取り込む・マージするとき」を読んだ
- ブランチ: origin/work/1014-skd（1c5d1b861）を続けて使用。ログ先行 push 86bc8eecd（[sync-logs]）
- 取り込み: origin/cloudflare（dc67195f7）は HEAD の祖先で、SKD-12 の後に進んでいない（取り込むコミット0件、衝突なし）
- 未マージの work/ ブランチ（work/1009-swp-526・1011-swp-hnd・1011-swp-nav・1014-wfl-s3・MTK）: `navbar.js`・`scripts/generate_saikyo_pages.py`・`style.css`・`assets/saikyo.js`・`assets/houou_race.js` を変えているものはなし（`work/1014-plb` はリモートに未マージのものとして無い）
- 再検証: `python3 scripts/regenerate.py saikyo_pages` で生成し直した結果、作業ツリーの差は0（コミット済みの生成物と一致）。`python3 -m unittest discover -s scripts/tests`: OK（1098件）
- 生成物の差（origin/cloudflare との比較）:

| 種類 | 件数 |
|---|---|
| 新しい年度ページ `saikyo/<年度>/index.html` | 16 |
| 対局のページ `saikyo/<年度>/<対局>.html`（すべて `mj-saikyo-n-page`） | 219 |
| ほかの saikyo/ のページ（卓のページ・今の年度ページ・トップ） | 0 |

  ほかは `navbar.js`・`style.css`・`assets/saikyo.js`・`scripts/generate_saikyo_pages.py`・テストと docs。許す範囲どおり
- SKD-12 のログの状態を「判断待ち / 続き: CHAT-1014-SKD-13」に直した

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6ae903ee）: https://github.com/retroeater/mj-logs/tree/main/guide/6ae903ee

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6ae903ee/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6ae903ee/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6ae903ee/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6ae903ee/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6ae903ee/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6ae903ee/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/67ac863e.md
