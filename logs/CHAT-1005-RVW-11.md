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
- 1. 未マージの `work/` ブランチのうち、辞書ページ・`dic/`・生成スクリプトを変えているものは無い（ほかは `work/1007-wkr-09` だけで、該当ファイルの変更なし）
- `git merge origin/cloudflare` の衝突は `docs/notes/static-generation.md` の1ファイル・1箇所（「ページの一覧」の表の houou_race の行と辞書の行）だけで、前提のとおり。cloudflare 側の houou_race の行を採り、辞書の行を残して解いた（f846134d）
- 件数の記述: 表は合計 26 ページ（index 1・型A 1＋7・型A' 2・型D 1・型C 2・houou_race 1・辞書 1・video_wayhome 1・Google Charts 6・静的 3）で、冒頭の「HTMLは26ページ」と `ls *.html` の 26 と合う。「navbar.js と検索欄」の `data-search="off"` のトップ階層の数は、houou_race が入ったため実物（`grep -l 'data-search="off"' *.html` で9）に合わせて直した

解いた後の該当箇所（引用、長い行は省略）:

```
| ビルド時生成（独自: 順位表の数え上げ+期ごとのJSON） | 1 | `houou_race.html`（鳳凰戦 リーグ別成績推移）。…（cloudflare 側の文言のまま）
| ビルド時生成（独自: カテゴリを選んで辞書ファイルを組み立てる） | 1 | `resource_dictionary.html`。`scripts/generate_resource_dictionary.py` が … （#377）
| ビルド時生成（独自: 全画面ヒーロー+横スクロールカード列） | 1 | `video_wayhome.html`。…
…
| 静的なページ | 3 | `404.html` / `jpml_links.html` / `rh_links.html` |
…
  トップ階層の9ページ（`404` / `houou_race` / `jpml_links` / `resource_dictionary` /
  `resource_efficiency` / `rh_links` / `rh_results` / `rh_results_detail` /
  `video_wayhome`）と、`title/`・`live/`・`saikyo/`・`books/`・`wayhome/` の全ページ。
  トップ階層の生成物6ページ（`houou_race` / `resource_dictionary` / `resource_efficiency` / `rh_results` /
  `rh_results_detail` / `video_wayhome`）とサブディレクトリの生成物は
…
  残り3ページ（手書きHTML: `404` / `jpml_links` / `rh_links`）を新規に追加するときは手で付ける
```

- `python3 -m unittest discover -s scripts/tests`: OK（取り込み後・直した後とも）
- 2. `resource_dictionary.js` の保存名の組み立てを `<YYYYMMDD>_<MSIME|Google日本語入力>_麻雀用語辞書.txt` に変えた（コミット「feat: name dictionary downloads by download date」）。日付は `new Date()` の現地の年・月・日。選んだカテゴリの名前と「版」は入れない。生成スクリプト・テスト・docs に保存名の記述は無かったため、変えたのは JS だけ
  - ページの `data-label`・`data-updated` 属性は保存名に使わなくなったが、ページを変えないため残した（害は無い。次にページを触るときに外せる）
- 再生成（`python3 scripts/generate_resource_dictionary.py`）: `resource_dictionary.html`・`dic/*.json` とも差分なし。「プロ」タブ 1,099 行・「辞書」タブ 625 行で、RVW-09 から変わっていない。更新日は 2026-10-06 のまま（今日 10-07 に生成しても日付が保たれることを確かめた）。生成スクリプトも共有のコードも変えていないため、全ページの再生成はしていない
- 決定: docs/decisions/features.md の RVW-10 の行の「（未実装）」を「（実装は CHAT-1005-RVW-11、未マージ）」に直した。RVW-09 の状態を「判断待ち（続きは CHAT-1005-RVW-11）」、RVW-10 の状態を「中断（続きは CHAT-1005-RVW-11）」に直した（`## 指示` 欄は変わっていない）

### 手順3: 確かめた結果

保存名（Playwright の Chromium、`LANG=C.UTF-8`・ロケール ja-JP・タイムゾーン Asia/Tokyo）:

| 時計 | ブラウザの現地の日付（UTC） | 組み合わせ | 形式 | 保存名 |
|---|---|---|---|---|
| 今 | 2026-10-07（2026-10-07T01:43Z） | 連盟プロ | MS-IME | 20261007_MSIME_麻雀用語辞書.txt |
| 今 | 同上 | 連盟プロ | Google | 20261007_Google日本語入力_麻雀用語辞書.txt |
| 今 | 同上 | 麻雀用語 | MS-IME | 20261007_MSIME_麻雀用語辞書.txt |
| 今 | 同上 | 麻雀用語 | Google | 20261007_Google日本語入力_麻雀用語辞書.txt |
| 今 | 同上 | 両方 | MS-IME | 20261007_MSIME_麻雀用語辞書.txt |
| 今 | 同上 | 両方 | Google | 20261007_Google日本語入力_麻雀用語辞書.txt |
| 固定（`clock.setFixedTime`） | 2027-01-01（2026-12-31T23:30Z） | 両方 | MS-IME | 20270101_MSIME_麻雀用語辞書.txt |
| 同上 | 同上 | 両方 | Google | 20270101_Google日本語入力_麻雀用語辞書.txt |

固定の時計は、UTC では 12-31・日本時間では 01-01 になる時刻にした。保存名は現地（日本時間）の日付になった。

中身（RVW-09 と同じ確かめ）:

| 時計 | 組み合わせ | 形式 | バイト数 | 先頭 | BOM の数 | 改行 | 最後の行の改行 | 行数 | 列数 | 重複 | 品詞 | 「髙」の語 | 「么九牌」 | 中身が期待どおり |
|---|---|---|---:|---|---:|---|---|---:|---|---:|---|---:|---:|---|
| 今 | 連盟プロ | MS-IME | 36,828 | FF FE | 1 | CR+LF | なし | 1,099 | 3 | 0 | 人名 | 2 | 0 | ○ |
| 今 | 連盟プロ | Google | 46,436 | （BOM なし） | 0 | LF | なし | 1,099 | 4 | 0 | 人名 | 2 | 0 | ○ |
| 今 | 麻雀用語 | MS-IME | 18,452 | FF FE | 1 | CR+LF | なし | 625 | 3 | 0 | 名詞・固有名詞 | 0 | 1 | ○ |
| 今 | 麻雀用語 | Google | 23,208 | （BOM なし） | 0 | LF | なし | 625 | 4 | 0 | 名詞・固有名詞 | 0 | 1 | ○ |
| 今 | 両方 | MS-IME | 55,282 | FF FE | 1 | CR+LF | なし | 1,724 | 3 | 0 | 人名・名詞・固有名詞 | 2 | 1 | ○ |
| 今 | 両方 | Google | 69,645 | （BOM なし） | 0 | LF | なし | 1,724 | 4 | 0 | 人名・名詞・固有名詞 | 2 | 1 | ○ |
| 固定 | 両方 | MS-IME | 55,282 | FF FE | 1 | CR+LF | なし | 1,724 | 3 | 0 | 人名・名詞・固有名詞 | 2 | 1 | ○ |
| 固定 | 両方 | Google | 69,645 | （BOM なし） | 0 | LF | なし | 1,724 | 4 | 0 | 人名・名詞・固有名詞 | 2 | 1 | ○ |

- プレビュー: 「Workers Builds: mj」success（17e0d86e）。プレビューの `resource_dictionary.js` が新しい保存名の組み立てになっていることを確かめた
- cloudflare との差分（docs・ログを除く）: 生成された HTML 1、JS 1、データ 2 追加・4 削除、生成スクリプト 1、テスト 1、`scripts/regenerate.py` 1。docs は `docs/notes/static-generation.md`・`docs/decisions/features.md` とログ（RVW-07・09・10・11）

## 報告

- 状態: 完了（判断が出た: マージしてよい。マージは CHAT-1005-RVW-13）
- ブランチ: work/1006-rvw-377
- ログ: https://github.com/retroeater/mj/blob/work/1006-rvw-377/docs/logs/CHAT-1005-RVW-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1006-rvw-377
- 確認用URL: ターミナルの最終報告にだけ書いた（Workers Builds のプレビュー）
- マージ: しない（判断待ち）
- issue: なし
- 判断が必要なこと:
  - 平野さんの確認の手順（Windows 11）: プレビューの辞書ページで ① 連盟プロのみ ② 麻雀用語のみ ③ 両方 をそれぞれ Microsoft IME 形式で保存し、保存名が「20261007_MSIME_麻雀用語辞書.txt」の形（日付はその日）になっているかを見る。IME の「ユーザー辞書ツール」→「ツール」→「テキストファイルからの登録」で取り込み、②・③ で「やおちゅーはい」→「么九牌」、①・③ で「髙」を含む名前（2名）が変換できること、③ が 1,724 語で登録エラーが無いことを見る。必要なら Google 日本語入力の形式も1つ保存して名前を見る
  - 同じ日に同じ形式を2回保存すると、ブラウザが「(1)」などを付ける（組み合わせを変えても名前は同じため）。気になるかどうか
  - 取り込みの衝突は前提の解き方で解いた（経過に引用）。houou_race の公開に合わせ、`data-search="off"` のトップ階層の数を 8 → 9 に直したのは、両方の変更を足した結果の数の直し
  - 指示文の雛形の行に欠けは無い
- 未確認の項目: 実際のブラウザ（Windows・iPhone の Safari）での保存名と、Microsoft IME・Google 日本語入力での取り込み（バイト列までは確かめた）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ccdefd6b）: https://github.com/retroeater/mj-logs/tree/main/guide/ccdefd6b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ccdefd6b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ccdefd6b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ccdefd6b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ccdefd6b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ccdefd6b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ccdefd6b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1257323c.md
