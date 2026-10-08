# CHAT-1008-DIC-06

- 着手日時: 2026-10-08
- 対象issue: #522・#515
- ブランチ: work/1008-dic
- 着手時HEAD: d07e3321

## 指示

【Claude作成】Claude Code 向け指示：DIC-05 の続き。辞書ページを選んだ見た目で本実装し（Gboard は画面に出さない）、比較ページを消す（判断待ちで止まる） Chat-Ref: CHAT-1008-DIC-06 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1008-DIC-05 は判断待ちで止まっている） 作業ブランチ: クラウドセッションで実行する。未マージの work/1008-dic を続けて使う（CHAT-1008-DIC-05 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-dic の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 あわせて、CHAT-1008-DIC-05 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
CHAT-1008-DIC-05 は、未マージの work/1008-hou（#518）が `scripts/regenerate.py` の辞書の行の隣に2行を足しているため手順1で止まった。その答えを受けて、DIC-05 の手順2（作る）・手順3（報告する）を行う。仕様（目的・決定・前提・止まる条件）は DIC-05 のログの「指示」欄のとおりで、下の決定で置き換える・足す。
決定（2026-10-09、平野さん）

* DIC-05 の報告の案のとおり進める（比較ページの行を消せば `scripts/regenerate.py` の差分は0行になり、work/1008-hou と重ならない。取り込みで隣り合う行が衝突したら、両方の行を残して解いてよい）
* 「Gboard 形式だけを先に本番へ入れない」（DIC-05 の決定）を置き換える: 新しい見た目を先に本番へ入れ、Gboard は平野さんの Android 実機での確認（2026-10-09 予定）の後に足す。この指示の本番ページでは、形式の選択に Gboard を出さない（Microsoft IME・Google 日本語入力の2つ）

前提（チャット側。平野さんの決定ではない）

* Gboard の zip を作る JS（DIC-04 で作ったもの）は消さずに残し、画面のセグメントに Gboard を出さないだけにする。後で Gboard を戻すときは、セグメントに1つ足すだけで済む形にする（例: 形式の一覧の定義で Gboard の行を無効にしておく）
* Gboard の吹き出しの3手順の文も、戻すときのために残してよい（画面には出さない）
* 平野さんの Android 実機での確認は、DIC-04 のプレビュー（Gboard を選べる）で行う。そのプレビューは消さない

手順

1. 確かめる: CHAT-1008-DIC-05 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1008-DIC-06` を足す。上の決定を `docs/decisions/` に足す（DIC-05 の決定のうち置き換えたものに「→ 置き換え」を付ける）。DIC-05 の手順1の残り（#522・#515 の状態、未マージの work/ ブランチの確かめ。`style.css` と `scripts/regenerate.py` の辞書以外の行だけを変えているものは止まる理由にしない）を行う。
2. 作る: DIC-05 の手順2のとおり（決定の見た目で本番ページを作り直す・比較ページと使わない案を消す・全ページの再生成と差分の報告・PC とスマホのスクリーンショット・保存の確かめ・キーボードと吹き出しの確かめ・`docs/notes/static-generation.md` の辞書の行・#522 と #515 へのコメント）。ただし保存の確かめは画面に出る2形式（Microsoft IME・Google 日本語入力）で行い、Gboard は画面に出ていないことを確かめる。
3. 報告する: 本番ページのプレビュー URL と、平野さんに見てほしい点（PC とスマホでの見た目）を書いて、判断待ちで止まる。META の description は変えず、Gboard を足すときの文案を「判断が必要なこと」に書く（DIC-05 の前提のとおり）。

止まる条件

* CHAT-1008-DIC-05 の状態が「判断待ち」でない
* 未マージの work/ ブランチが `scripts/generate_resource_dictionary.py`・`resource_dictionary.js`・`resource_dictionary.html`・`resource_dictionary.css`・`dic/` を変えている、または `scripts/regenerate.py` の辞書の行（比較ページの行を含む）そのものを変えている
* DIC-05 の「止まる条件」に当たった（未マージのブランチの条件は上に置き換える）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-DIC-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-DIC-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/work/1008-dic/docs/logs/CHAT-1008-DIC-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: なし
- マージ: 未
- issue: #522・#515
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e1cabc36）: https://github.com/retroeater/mj-logs/tree/main/guide/e1cabc36

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e1cabc36/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cd4e3d2c.md
