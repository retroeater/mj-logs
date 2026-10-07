# CHAT-1005-RVW-10

- 着手日時: 2026-10-07
- 対象issue: #377
- ブランチ: work/1006-rvw-377
- 着手時HEAD: 5f8fd017

## 指示

【Claude作成】Claude Code 向け指示：#377 辞書ページの保存名を「<ダウンロード日>_<形式>_麻雀用語辞書.txt」に変え、プレビューを出して判断待ちで止まる Chat-Ref: CHAT-1005-RVW-10 マージ: 判断待ちで止まる（平野さんがプレビューで保存名と Microsoft IME への取り込みを確かめてから決める） 貼る時機: CHAT-1005-RVW-09 の後（判断待ちで止まっている。その続き） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1006-rvw-377 への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1006-rvw-377 を続けて使う（CHAT-1005-RVW-09 の辞書ページの実装があり、その直しのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1005-RVW-09 のログの `## 報告` を読み、状態が「判断待ち」であることを確かめる（違えば何もせず止まる）。

目的
CHAT-1005-RVW-09 で作った辞書ページ（`resource_dictionary.html`・`resource_dictionary.js`）の、保存するファイル名だけを平野さんの決定に合わせて変える。ほかの文言・h1・既定の形式は RVW-09 のままで決まった。
決定（2026-10-07、平野さん）

* 保存名は「20261007_MSIME_麻雀用語辞書.txt」の形にする。先頭の日付はダウンロードした日（利用者がボタンを押した日）。理由: 連盟プロは「プロ」タブを参照して随時更新されるため、「〜版」「時点」の日付はあまり意味がなくなる
* 文言（見出し「カテゴリ」「形式」、ボタン「ダウンロード」、保存後の「1,724語の辞書ファイルを保存しました。」、カテゴリの「連盟プロ（1,099語、2026-10-06更新）」）は RVW-09 のままでよい
* h1 は「リソース 辞書」のままでよい
* 既定の形式は Microsoft IME のままでよい

前提（チャット側。平野さんの決定ではない）

* 保存名の形（チャット側の読み。平野さんの例は Microsoft IME 用の1つだけ）: `<YYYYMMDD>_MSIME_麻雀用語辞書.txt` と `<YYYYMMDD>_Google日本語入力_麻雀用語辞書.txt`。選んだカテゴリの名前は保存名に入れない（どの組み合わせでも「麻雀用語辞書」）。「版」は付けない
* ダウンロードした日は、利用者のブラウザの現地の日付（`Date` の現地の年月日。UTC ではない）で、8桁の数字
* ページの表示（カテゴリごとの「◯語、YYYY-MM-DD更新」）と、`dic/*.json` の `updated`、更新日を保つ作りは変えない（文言は RVW-09 のままでよいという決定のため）
* RVW-09 で、コンテナの Chromium はロケールが UTF-8 でないと日本語の保存名を「download」にした。確かめ方は RVW-09 と同じ（`LANG=C.UTF-8` と日本語のロケール）でよい。実際のブラウザでの保存名は平野さんが確かめる
* RVW-09 のログは `## 報告` の状態だけを「判断待ち（続きは CHAT-1005-RVW-10）」に直す（`## 指示` 欄は変えない）

手順

1. 確かめる: 0章の確認の後、未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`）が `resource_dictionary.html`・`resource_dictionary.js`・`dic/`・`scripts/generate_resource_dictionary.py` を変えていないか確かめる。origin/cloudflare を取り込む（衝突したら止まる）
2. 直す: 保存名の組み立てを上の形に変える（JS。生成スクリプトが保存名の部品を持っていれば、そちらも）。保存名に関わるテスト・コメント・docs（`docs/notes/static-generation.md` などに保存名の記述があれば）を合わせる。決定を docs/decisions/features.md に足す。`python3 -m unittest discover -s scripts/tests` を通す。再生成で、辞書ページと `dic/` 以外に差分が出ないこと、`dic/*.json` の中身と `updated` が意図せず変わらないことを確かめる（「プロ」タブ・「辞書」タブの値が変わっていれば、変わった件数を報告に書く）
3. 確かめてプレビューを出し、判断待ちで止まる: Chromium（Playwright）で、3通りの選択 × 2形式の保存名（日付がブラウザの現地の日付であること。時計を別の日付にした場合も1つ確かめる）と、中身のバイト列が RVW-09 の確かめと同じ条件を満たすことを確かめ、表でログに書く。報告に、確認用 URL、cloudflare との差分のファイル数（種類ごと）、平野さんの確認の手順（プレビューで保存し、保存名と Microsoft IME への取り込みを見る）を書く。cloudflare へは push しない

止まる条件

* RVW-09 の `## 報告` の状態が「判断待ち」でない
* origin/cloudflare の取り込みで衝突した
* 未マージの `work/` ブランチが辞書ページか `dic/`・生成スクリプトを変えている
* 保存名のほかに、ページの表示や辞書ファイルの中身を変える必要が出た（変えずに報告に書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）。なお、この指示は cloudflare へ push しない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-RVW-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-RVW-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-07 着手。CHAT-1005-RVW-10 のコミットなし。work/1006-rvw-377 はローカルとリモートが一致（5f8fd017）
- 0. 指示欄の末尾は指示文の最後の行と一致。RVW-09 の `## 報告` の状態は「判断待ち」。雛形の行は揃っている
- 1. 未マージの `work/` ブランチのうち辞書ページ・`dic/`・生成スクリプトを変えているのは自分の `work/1006-rvw-377` だけ（ほかは無い）
- origin/cloudflare を `git merge` で取り込んだところ、`docs/notes/static-generation.md` が衝突した。止まる条件「origin/cloudflare の取り込みで衝突した」に当たるため、`git merge --abort` で取り込みを取り消し（作業ツリーは取り込み前の 5f8fd017 のまま）、手順2に入らず止まった
  - 衝突の箇所: 「ページの一覧」の表。cloudflare 側は `490a610f feat: publish houou_race`（CHAT-1006-LGR-10、#508）で houou_race の行の文言を公開に合わせて書き換え、こちら（RVW-09）はその直後の行に辞書ページの行を足していた。隣り合う行の衝突で、内容は両立する（cloudflare 側の houou_race の行を採り、辞書の行を残せば解ける見込み）。生成物ではないため、CLAUDE.md「ブランチ運用」でも止まる扱い
  - 同じ取り込みのほかのファイルは衝突しなかった（衝突は1ファイルだけ）
- 指示の「決定」節を docs/decisions/features.md に「2026-10-07（CHAT-1005-RVW-10）」として足した（保存名は未実装と書いた）。RVW-09 のログの状態の直しは手順2に含まれるため、行っていない

## 報告

- 状態: 中断（続きは CHAT-1005-RVW-11）
- ブランチ: work/1006-rvw-377
- ログ: https://github.com/retroeater/mj/blob/work/1006-rvw-377/docs/logs/CHAT-1005-RVW-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1006-rvw-377
- 確認用URL: なし（コードは変えていない。RVW-09 のプレビューのまま）
- マージ: しない
- issue: なし
- 判断が必要なこと:
  - 衝突は docs/notes/static-generation.md「ページの一覧」の隣り合う2行（cloudflare 側の houou_race の行の書き換えと、こちらの辞書の行の追加）。cloudflare 側の houou_race の行を採り、辞書の行を残す解き方で取り込んでよいか（続きの指示に「docs/notes/static-generation.md のこの衝突は、両方の行を残して解いてよい」と書けば進められる）
  - 続きは新しい番号の指示で（このログにコミットがあるため）。保存名の変更・RVW-09 のログの状態の直しはまだ行っていない
  - 指示文の雛形の行に欠けは無い
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 84f7dfcf）: https://github.com/retroeater/mj-logs/tree/main/guide/84f7dfcf

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/84f7dfcf/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/84f7dfcf/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/84f7dfcf/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/84f7dfcf/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/84f7dfcf/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/84f7dfcf/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2fd75cd3.md
