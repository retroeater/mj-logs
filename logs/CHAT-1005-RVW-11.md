# CHAT-1005-RVW-11

- 着手日時: 2026-10-07
- 対象issue: #377
- ブランチ: work/1006-rvw-377
- 着手時HEAD: 59d17d6f

## 指示

【Claude作成】Claude Code 向け指示：#377 の続き。static-generation.md の衝突を両方の行を残して解いて cloudflare を取り込み、保存名を「<ダウンロード日>_<形式>_麻雀用語辞書.txt」に変え、プレビューを出して判断待ちで止まる Chat-Ref: CHAT-1005-RVW-11 マージ: 判断待ちで止まる（平野さんがプレビューで保存名と Microsoft IME への取り込みを確かめてから決める） 貼る時機: CHAT-1005-RVW-10 の後（取り込みの衝突で中断している。その続き） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1006-rvw-377 への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1006-rvw-377 を続けて使う（CHAT-1005-RVW-09 の辞書ページの実装があり、その直しのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない。衝突の扱いは下の「前提」）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1005-RVW-10 のログの `## 指示`・`## 経過`・`## 報告` を読み、状態が「中断」であることを確かめる（違えば何もせず止まる）。

目的
CHAT-1005-RVW-10 が止まった点（origin/cloudflare の取り込みで `docs/notes/static-generation.md` が衝突）を解き、RVW-10 の指示の手順2（保存名を直す）・手順3（確かめてプレビューを出す）を行う。仕様は RVW-10 の指示の「決定」「前提」のとおり。
決定（2026-10-07、平野さん。RVW-10 の指示と同じ）

* 保存名は「20261007_MSIME_麻雀用語辞書.txt」の形にする。先頭の日付はダウンロードした日。理由: 連盟プロは「プロ」タブを参照して随時更新されるため、「〜版」「時点」の日付はあまり意味がなくなる
* 文言・h1「リソース 辞書」・既定の形式（Microsoft IME）は RVW-09 のままでよい

前提（チャット側。平野さんの決定ではない）

* 衝突の解き方（チャット側の判断。平野さんがこの指示を貼ることで認める）: `docs/notes/static-generation.md`「ページの一覧」の表の、隣り合う2行の衝突（cloudflare 側は `490a610f` で houou_race の行を公開に合わせて書き換え、こちらは直後に辞書ページの行を足した）は、cloudflare 側の houou_race の行を採り、辞書の行を残して解いてよい。同じファイルの件数の記述（静的なページの数・生成物の数など）は、両方の変更を足した正しい数に直す（要確認: 取り込み後の実物で数える）
* 上の解き方を認めるのは、RVW-10 のログに書かれた衝突（`docs/notes/static-generation.md` の1ファイル、「ページの一覧」の隣り合う行）だけ。ほかのファイルや、同じファイルのほかの箇所が衝突したら、解かずに止まる
* 保存名の形（チャット側の読み）: `<YYYYMMDD>_MSIME_麻雀用語辞書.txt` と `<YYYYMMDD>_Google日本語入力_麻雀用語辞書.txt`。選んだカテゴリの名前は入れない。「版」は付けない。日付は利用者のブラウザの現地の日付（UTC ではない）
* ページの表示（カテゴリごとの「◯語、YYYY-MM-DD更新」）、`dic/*.json` の `updated`、更新日を保つ作りは変えない
* 決定は RVW-10 が docs/decisions/features.md に「保存名は未実装」と書いて足している。実装したら、その行を実装済みに直す（新しい見出しは、解き方の記録が要るときだけ足す）
* RVW-09 のログの `## 報告` の状態を「判断待ち（続きは CHAT-1005-RVW-11）」に、RVW-10 のログの状態を「中断（続きは CHAT-1005-RVW-11）」に直す（どちらも `## 指示` 欄は変えない）

手順

1. 取り込む: 0章の確認の後、未マージの `work/` ブランチが辞書ページ・`dic/`・生成スクリプトを変えていないか確かめる。origin/cloudflare を `git merge` で取り込み、上の前提の衝突だけを上の解き方で解く。解いた後の `docs/notes/static-generation.md` の該当の表と件数の記述を、ログに引用する。`python3 -m unittest discover -s scripts/tests` を通す
2. 直す: RVW-10 の指示の手順2のとおり（保存名の組み立てを上の形に変える。保存名に関わるテスト・コメント・docs を合わせる。再生成で、辞書ページと `dic/` 以外に差分が出ないこと、`dic/*.json` の中身と `updated` が意図せず変わらないことを確かめる。「プロ」タブ・「辞書」タブの値が変わっていれば、変わった件数を報告に書く）
3. 確かめてプレビューを出し、判断待ちで止まる: RVW-10 の指示の手順3のとおり（Chromium〈Playwright〉で、3通りの選択 × 2形式の保存名と、時計を別の日付にした場合を1つ確かめる。中身のバイト列が RVW-09 の確かめと同じ条件を満たすこと。表でログに書く）。報告に、確認用 URL、cloudflare との差分のファイル数（種類ごと）、平野さんの確認の手順（プレビューで保存し、保存名と Microsoft IME への取り込みを見る）を書く。cloudflare へは push しない

止まる条件

* RVW-10 の `## 報告` の状態が「中断」でない
* 取り込みの衝突が、前提に書いたもの（1ファイル・「ページの一覧」の隣り合う行）と違う
* 未マージの `work/` ブランチが辞書ページか `dic/`・生成スクリプトを変えている
* 保存名のほかに、ページの表示や辞書ファイルの中身を変える必要が出た（変えずに報告に書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）。なお、この指示は cloudflare へ push しない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-RVW-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-RVW-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-07 着手。CHAT-1005-RVW-11 のコミットなし。work/1006-rvw-377 はローカルとリモートが一致（59d17d6f）
- 0. 指示欄の末尾は指示文の最後の行と一致。RVW-10 の `## 報告` の状態は「中断」。雛形の行は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1006-rvw-377
- ログ: https://github.com/retroeater/mj/blob/work/1006-rvw-377/docs/logs/CHAT-1005-RVW-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1006-rvw-377
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
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
