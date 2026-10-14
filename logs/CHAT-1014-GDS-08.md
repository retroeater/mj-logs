# CHAT-1014-GDS-08

- 着手日時: 2026-10-14
- 対象issue: #565・#564
- ブランチ: work/1014-gds-ref2
- 着手時HEAD: a1747ce98

## 指示

【Claude作成】Claude Code 向け指示：CLAUDE.md の曖昧な「同『…』」参照と chrome-reading.md の壊れた参照を直し、#565 を #564 に結び付ける Chat-Ref: CHAT-1014-GDS-08 マージ: 承認済み（チャットで。この指示文を貼ることが承認。直す箇所と直し方は下の「決定」に全部書いた） 貼る時機: いつでも（GDS と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1014-gds-ref2 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-gds-ref2 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
GDS-07 の調べ（ログ「手順2」の表）で見つかった参照の曖昧さ・壊れを直す。GDS-01 で CLAUDE.md「ブランチ運用」の「同『ワークフローを変更したとき』」を `docs/notes/github-actions.md` に変えたため、その後ろの「同『…』」が branch-operations.md ではなく github-actions.md を指すように読める。あわせて #565 を WFL 系列の親 #564 に結び付ける。
決定（2026-10-14、平野さん）

* CLAUDE.md の次の「同『…』」を、意図した文書名 `docs/notes/branch-operations.md` に書き換える（GDS-07 のログの表の5件。行番号はチャット側が guide の版で見たもの）:
   * 「ブランチ運用」の「同『マージするとき』」（2か所）
   * 同「生成物を含む作業ブランチを取り込む・マージするとき」
   * 同「ブランチを削除するとき」
   * 「作業ログ」の「`_template.md`と同『作業ログの寿命』」→「`_template.md`と docs/notes/branch-operations.md『作業ログの寿命』」
* docs/notes/chrome-reading.md の `docs/notes/chat-side-operations.md「確認対象ごとの手段」`（見出しが無い）を、今の見出し `docs/notes/chat-side-operations.md「読み方」` に直す
* #565 は WFL 系列で扱う: #564 に「#565（文書参照の検査テスト）は WFL の改名の段でワークフローのコメントの参照を直した後に、戻らないことを確かめる用途で扱う」とコメントし、#565 にも「親: #564」とコメントする
* ほかの「同『…』」・見出しの一部だけの参照（GDS-07 の表の残り）は今回直さない

前提（チャット側。平野さんの決定ではない）

* 直した後の「同」の向き: 1つ目の「同」の手前に `docs/notes/branch-operations.md` が出るなら、2つ目以降は「同」のままでもよい（同じ文書が直前に出ていれば曖昧でない）。ただし間に別の文書名が挟まるなら文書名を書く。受け手は読み手が迷わない最小の直しを選んでよい
* CLAUDE.md は +100〜200 バイトの見込み（文書名1つで約 34 バイト）。警告域 26,624 の外
* マージで動く自動処理: `assets-check.yml`（CLAUDE.md を含む push）、Workers Builds の check-run（CLAUDE.md が watch に含まれる。表示への影響は無い）。`regenerate-page.yml` は走らない
* WFL の作業ブランチが CLAUDE.md の同じ行を変えている可能性がある（要確認。未マージの work/ ブランチの差分で確かめる）

手順

1. 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が CLAUDE.md の「ブランチ運用」「作業ログ」節の同じ行、または chrome-reading.md の同じ行を変えていないかを確かめる。#564・#565 の今のコメントを読む
2. 決定のとおりに直す。直す前と後の行をログの `## 経過` に引用し、CLAUDE.md の `wc -c` の前後を書く。`python3 -m unittest discover -s scripts/tests` が全件通ることを確かめる
3. CLAUDE.md「ブランチ運用」のとおりマージし、cloudflare の check-run（assets-check・Workers Builds）の結果をログに書く。#564・#565 にコメントを書く

止まる条件

* 未マージの work/ ブランチが同じ行を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 直す箇所が見つからない・文面が決定と違う
* テストが落ちた（今回の変更と無関係な失敗なら、原因を報告に書いたうえでマージしてよい）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-GDS-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-GDS-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1014-GDS-08` は全ブランチのコミット・docs/logs/ に無い。`GDS` は同じセッションの GDS-01〜07 の続き
- 作業ブランチ: `origin/work/1014-gds-ref2` はリモートに無く、ローカルにも無いため `git checkout -b work/1014-gds-ref2 origin/cloudflare` で作成
- 0. 指示欄の末尾は指示文の最後の行と一致
- 指示文の冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- CLAUDE.md の「ガイド文に規則を足す指示に『知見 → 選んだ層 → 理由』の表が無ければ止まる」には当たらない（規則を足さず、既存の参照の文書名を書き換えるだけ）

## 報告

- 状態: 作業中
- ブランチ: work/1014-gds-ref2
- ログ: https://github.com/retroeater/mj/blob/work/1014-gds-ref2/docs/logs/CHAT-1014-GDS-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-gds-ref2
- 確認用URL: なし
- マージ: 未
- issue: #565・#564
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a1747ce9）: https://github.com/retroeater/mj-logs/tree/main/guide/a1747ce9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1747ce9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1747ce9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1747ce9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1747ce9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1747ce9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1747ce9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/59e33bdf.md
