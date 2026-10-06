# CHAT-1005-RVW-09

- 着手日時: 2026-10-06
- 対象issue: #377
- ブランチ: work/1006-rvw-377
- 着手時HEAD: ed2f05bc

## 指示

【Claude作成】Claude Code 向け指示：#377 の続き。「辞書」タブを正として、辞書ページをシートからの生成に変え、選んだカテゴリを1つの辞書ファイルでダウンロードできるようにする。プレビューを出して判断待ちで止まる Chat-Ref: CHAT-1005-RVW-09 マージ: 判断待ちで止まる（プレビューを平野さんが見て決める） 貼る時機: CHAT-1005-RVW-07 の後（止まる条件で中断している。その続き） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1006-rvw-377 への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1006-rvw-377 を続けて使う（CHAT-1005-RVW-07 のログと docs/decisions/features.md の追記があり、その続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1005-RVW-07 のログ（`docs/logs/CHAT-1005-RVW-07.md`）の `## 指示`・`## 経過`・`## 報告` を読み、状態が「中断」であることを確かめる（違えば何もせず止まる）。

目的
CHAT-1005-RVW-07 が止まった点（「辞書」タブと今の公開ファイルの食い違い）に判断が出たので、RVW-07 の指示の手順2（作る）・手順3（確かめてプレビューを出す）を行う。仕様は RVW-07 の指示の「目的」「決定」「前提」のとおりで、下の決定で上書きする。
決定（2026-10-06、平野さん）

* 「辞書」タブを正として進める。麻雀用語は 625 語になる（今の公開ファイル 623 語に、取牌・打荘数の2語を足し、文字化けの「?九牌」を「么九牌」に直した形）
* 「辞書」タブの見出しは実物（カテゴリ・よみ・単語・品詞・コメント・備考）の名前で読む。「備考」は管理用で、辞書ファイルにもページにも出さない
* RVW-07 の決定（UTF-16LE〈BOM 付き・CR+LF・TAB 区切り〉、連盟プロは「プロ」タブから生成、旧ファイル4つは消す、`_redirects` は作らない、カテゴリは今の2つ、形式は今と同じ2種）は変えない

前提（チャット側。平野さんの決定ではない）

* 作り方は、RVW-07 のログの「どこをどう変えるか」の表（Code の案）のとおりでよい: `scripts/generate_resource_dictionary.py` を足す、ワークフローは変えない（`scripts/generate_*.py` の有無で対象になる）、`scripts/regenerate.py` の `OUTPUT_OVERRIDES` に辞書の出力先を足す、`dic/` はカテゴリごとのデータ（例: `dic/pros.json`・`dic/mahjong.json`）に置き換える、ページの JS で集めて `Blob` で保存する、h1・title・description・og は今の値を保つ、`scripts/apply_page_meta.py` の表の行の扱いは実物で決める、`docs/notes/static-generation.md` の系統と件数を直す
* 更新日は、データが前回と同じなら前回の日付を保つ（毎週の再生成で日付だけが変わるコミットを出さない）。初回の日付は生成日
* 「么」は cp932 に無い字。Microsoft IME 用（UTF-16LE）・Google 日本語入力用（UTF-8）の両方で「么九牌」が正しく出ることを、バイト列で確かめる
* 「辞書」タブに、カテゴリ・よみ・単語のどれかが空の行、知らない品詞（名詞・固有名詞・人名のほか）、（よみ, 単語）の重複が入ったときの扱い: 生成を失敗させず、その行を出さずに警告を出す案と、失敗させる案がある。既存の生成スクリプトの流儀に合わせ、選んだ方を報告に書く（要確認）
* 連盟プロの件数と旧との差は RVW-05 の見込み（−23・+55）。数えて報告に書く（個人の出入りの一覧は書かず、件数だけ）
* RVW-07 のログは中断のままにし、`## 報告` の状態だけを「中断（続きは CHAT-1005-RVW-09）」に直す（`## 指示` 欄は変えない）

手順

1. 確かめる: 0章の確認の後、未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`）が `resource_dictionary.html`・`dic/`・`scripts/lib/`・`scripts/regenerate.py` を変えていないか確かめる。「辞書」タブを読み直し、RVW-07 の調べ（625 行 = 旧と一致 622 + 追加 2 + 修正 1）から変わっていないか確かめる（平野さんが語を足していれば、増えた件数を報告に書いて進める。減った・書き換わった行があれば止まる）
2. 作る: RVW-07 の指示の手順2のとおり（生成スクリプトとテスト、カテゴリごとのデータ、生成する `resource_dictionary.html`〈カテゴリのチェックボックス・既定は全選択、形式の選択、ダウンロードのボタン、件数と更新日、辞書登録方法のリンク、1つも選ばれていないときと JS が無効のときの案内〉、旧ファイル4つの削除、docs の更新）。決定を docs/decisions/features.md に足す。`python3 -m unittest discover -s scripts/tests` を通す。全ページの再生成で、辞書ページ以外に差分が出ないことを確かめる
3. 確かめてプレビューを出し、判断待ちで止まる: RVW-07 の指示の手順3のとおり（選択の組み合わせ〈連盟プロのみ・麻雀用語のみ・両方〉× 2形式を Chromium〈Playwright。ブラウザのダウンロードをしない〉か Node でダウンロードしてバイト列を確かめる: BOM が先頭に1つだけ・CR+LF・最後の行の扱い・「髙」「么」を含む語・重複なし・品詞名・列の数。PC 幅とスマホ幅〈iPhone の Safari の幅〉の見た目）。結果を表でログに書く。報告に、確認用 URL、cloudflare との差分のファイル数（種類ごと）、平野さんに決めてほしい点（保存するファイル名・ボタンや説明の文言・h1「リソース 辞書」の見直しの案）、平野さんが Windows 11 の Microsoft IME で生成したファイルを取り込んで確かめる手順（どの組み合わせで試すか）を書く。cloudflare へは push しない

止まる条件

* RVW-07 の `## 報告` の状態が「中断」でない
* 未マージの `work/` ブランチが `resource_dictionary.html`・`dic/`・`scripts/lib/`・`scripts/regenerate.py` を変えている
* 「辞書」タブの麻雀用語が RVW-07 の調べから減った・書き換わった（増えただけなら進める）
* 「辞書」タブか「プロ」タブがこのセッションから読めない（別の手段を試さずに止まる）
* 外部ドメインかライブラリを足す必要が出た。ワークフロー（`.github/workflows/`）を変える必要が出た（変えずに案を報告に書く）
* 全ページの再生成で、辞書ページ以外に意図しない差分が出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）。なお、この指示は cloudflare へ push しない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-RVW-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-RVW-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-06 着手。CHAT-1005-RVW-09 のコミットなし。work/1006-rvw-377 はローカルとリモートが一致（ed2f05bc）、origin/cloudflare は HEAD の祖先
- 0. 指示欄の末尾は指示文の最後の行と一致。RVW-07 の `## 報告` の状態は「中断」。雛形の行は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1006-rvw-377
- ログ: https://github.com/retroeater/mj/blob/work/1006-rvw-377/docs/logs/CHAT-1005-RVW-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1006-rvw-377
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 569dab20）: https://github.com/retroeater/mj-logs/tree/main/guide/569dab20

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/569dab20/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/569dab20/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/569dab20/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/569dab20/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/569dab20/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/569dab20/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/824dc807.md
