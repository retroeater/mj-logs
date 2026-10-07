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

- 未マージの `work/` ブランチは `work/1006-lgr-10` と自分だけ。lgr-10 は `resource_dictionary.html`・`dic/`・`scripts/lib/`・`scripts/regenerate.py` を変えていない
- 「辞書」タブを読み直した: 625 行、カテゴリは全件「麻雀用語」。2026-05-01 版と比べて追加2（取牌・打荘数）・「?九牌」→「么九牌」の修正1・ほかの 622 行は順番まで一致。RVW-07 の調べから増減・書き換えなし（止まる条件に当たらない）
- 「辞書」タブの冊は `scripts/lib/live.py` の `SPREADSHEET_ID`（連盟プロ以外と同じ冊）で、先頭のタブは「連盟プロ以外」。`fetch_records()` には `live.FIRST_SHEET` を渡す（別のタブ名を誤って読んだときに止まる）

### 手順2: 作ったもの（コミット 51c7895e・0b8e700c、決定 docs/decisions/features.md）

| ファイル | 内容 |
|---|---|
| `scripts/generate_resource_dictionary.py`（新規） | 「辞書」タブを見出しの名前（カテゴリ・よみ・単語・品詞・コメント。「備考」は読まない）で、「プロ」タブを `SELECT A,B WHERE Y = "Y" ORDER BY B ASC`（jpml_pros と同じ行と並び）で読み、`dic/pros.json`・`dic/mahjong.json`（`{"label","updated","rows":[[読み,語,品詞,コメント],…]}`）と `resource_dictionary.html` を書く。更新日はカテゴリごとで、行が前回の JSON と同じなら前回の日付（初回は生成日 2026-10-06） |
| `resource_dictionary.js`（新規） | 選んだカテゴリの JSON を同じオリジンから読み、（読み, 語, 品詞）が同じ行を1行にまとめ、Microsoft IME 用（UTF-16LE・BOM・CR+LF・3列）か Google 日本語入力用（UTF-8・LF・4列）で `Blob` にして保存させる。最後の行に改行は付けない（旧ファイルと同じ）。カテゴリが0個のときはボタンを押せなくして「カテゴリを1つ以上選んでください。」を出す |
| `resource_dictionary.html`（手書き → 生成） | head（title・description・og・Analytics）は旧ページとバイト単位で同じ。h1「リソース 辞書」はそのまま。本文はカテゴリのチェックボックス（既定は全選択、件数と更新日つき）・形式のラジオ（既定は Microsoft IME）・ダウンロードのボタン・状態の表示・`<noscript>` の案内・辞書登録方法（@IT の2本）。旧の閉じていない `<p>`・余分な `</a>` は無くなった。生成ページの決まりで末尾に description の文（`.mj-lead`）が出る |
| `dic/` | 旧ファイル4つを消し、`pros.json`（57,484 バイト）・`mahjong.json`（29,516 バイト）に置き換え |
| `scripts/regenerate.py` | `OUTPUT_OVERRIDES` に `"resource_dictionary": "resource_dictionary.html dic/"` を足した。ワークフローは変えていない（`generate_*.py` の有無で週次の `all` と push の判定の対象になる。`resource_dictionary.js` の変更でも再生成される） |
| `scripts/tests/test_resource_dictionary.py`（新規、13件） | 列の取り出し・順番・コメント、知らないカテゴリ、空欄・知らない品詞・（読み, 語）の重複・置換文字と制御文字（TAB・改行を含む）で止まること、同じ語で読みが違う行は通ること、更新日の保ち方、旧ファイルの削除、ページのチェックボックスと件数 |
| `docs/notes/static-generation.md` | 「ページの一覧」の静的なページを4 → 3 にし、生成の行を足した。「navbar.js と検索欄」の生成物・手書きの数を直した。HTML の総数（25）は変わらない |
| `scripts/apply_page_meta.py` | 変えていない。`PAGES["resource_dictionary.html"]` の title・description は生成スクリプトの値と同じ（video_wayhome と同じく、生成スクリプトにそろえる旨のコメントを置いた） |

- 不正な行の扱い: **生成を止める方**を選んだ（docs/notes/static-generation.md「生成を止める条件の設計」と、generate_title_pages・generate_houou_race の流儀に合わせた）。止まった場合、前回の生成物がそのまま残る
- 連盟プロ: 1,099 語。旧（1,067 語）と比べて、共通 1,044 語（読みも一致）、旧だけ 23 語、新だけ 55 語（RVW-05 の見込みどおり）。並びは読み順（旧は別の並びだったが、取り込みには関係しない）。名前・読みに空白は無かった
- 麻雀用語: 625 語。旧（`Google_mahjong_20260501.txt`）の 623 行と、読み・語・品詞・コメントの順番と中身が一致（「?九牌」を「么九牌」に直した1行を除く）し、取牌・打荘数の2行が増えた
- `python3 -m unittest discover -s scripts/tests`: 576 件 OK
- 全ページの再生成（`python3 scripts/regenerate.py all`）: 辞書ページと `dic/` のほかに出た差分は `houou_race/24-2.json` の1つだけ。中身は 24後 A1 の累計の列の変化で、「鳳凰」タブの値の変化によるもの（別セッションの未公開ページ。今回の変更は共有のコードに触れていない）。名指しで戻した
- 作業中、`git stash list` を含むコマンドを書いてしまい、hook（mj-git-guard）に拒否された（何も実行されていない。読むだけのつもりだったが CLAUDE.md の禁止事項。以後は使っていない）

### 手順3: 確かめた結果

ダウンロード（ローカルで配信し、Playwright の Chromium で操作。外部への接続は止めた）:

| 組み合わせ | 形式 | 保存名 | バイト数 | 先頭 | BOM の数 | 改行 | 最後の行の改行 | 行数 | 列数 | 重複 | 品詞 | 「髙」の語 | 「么九牌」 | 中身が期待どおり |
|---|---|---|---:|---|---:|---|---|---:|---|---:|---|---:|---:|---|
| 連盟プロ | MS-IME | MSIME_連盟プロ_20261006版.txt | 36,828 | FF FE | 1 | CR+LF | なし | 1,099 | 3 | 0 | 人名 | 2 | 0 | ○ |
| 連盟プロ | Google | Google日本語入力_連盟プロ_20261006版.txt | 46,436 | （BOM なし） | 0 | LF | なし | 1,099 | 4 | 0 | 人名 | 2 | 0 | ○ |
| 麻雀用語 | MS-IME | MSIME_麻雀用語_20261006版.txt | 18,452 | FF FE | 1 | CR+LF | なし | 625 | 3 | 0 | 名詞・固有名詞 | 0 | 1 | ○ |
| 麻雀用語 | Google | Google日本語入力_麻雀用語_20261006版.txt | 23,208 | （BOM なし） | 0 | LF | なし | 625 | 4 | 0 | 名詞・固有名詞 | 0 | 1 | ○ |
| 両方 | MS-IME | MSIME_連盟プロ・麻雀用語_20261006版.txt | 55,282 | FF FE | 1 | CR+LF | なし | 1,724 | 3 | 0 | 人名・名詞・固有名詞 | 2 | 1 | ○ |
| 両方 | Google | Google日本語入力_連盟プロ・麻雀用語_20261006版.txt | 69,645 | （BOM なし） | 0 | LF | なし | 1,724 | 4 | 0 | 人名・名詞・固有名詞 | 2 | 1 | ○ |

- 「中身が期待どおり」は、ダウンロードしたファイルを読み直し、`dic/*.json` の行（MS-IME は先頭3列）と並びまで一致することを比べた
- カテゴリを両方外すと、ボタンが押せなくなり「カテゴリを1つ以上選んでください。」が出る。JavaScript を切ると `<noscript>` の案内が出る
- 保存名: このコンテナの Chromium は、ロケールが UTF-8 でないと日本語の保存名を「download」にしてしまう（旧ページと同じ作りの、同じオリジンのファイルへの `download` 属性でも同じだった）。`LANG=C.UTF-8` と日本語のロケールで動かすと上の表の名前で保存された。実際のブラウザでの保存名は未確認
- 見た目: PC 幅（1280）と iPhone 13 の幅（390）でスクリーンショットを見た。カテゴリ（既定で2つとも選択）・形式（既定は Microsoft IME）・ボタン（高さ 38px、幅 142px 前後）・辞書登録方法が縦に並び、はみ出しは無い。ボタンに付けた下向き矢印のアイコンは青地に黒で見えにくかったので外した（0b8e700c）
- プレビュー: 「Workers Builds: mj」success（0b8e700c）。プレビューで `resource_dictionary.html`・`resource_dictionary.js`・`dic/pros.json`・`dic/mahjong.json` が 200、旧 `dic/Google_pros_20260501.txt` は 404（旧 URL への案内は作らない決定どおり）
- cloudflare との差分（ログ・決定を除く）: 生成された HTML 1、新しい JS 1、データ 2 追加・4 削除、生成スクリプト 1、テスト 1、`scripts/regenerate.py` 1、docs/notes 1。origin/cloudflare はこの間に進んでいる（取り込みはマージの指示のときに行う）

## 報告

- 状態: 判断待ち（続きは CHAT-1005-RVW-11）
- ブランチ: work/1006-rvw-377
- ログ: https://github.com/retroeater/mj/blob/work/1006-rvw-377/docs/logs/CHAT-1005-RVW-09.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1006-rvw-377
- 確認用URL: ターミナルの最終報告にだけ書いた（Workers Builds のプレビュー）
- マージ: しない（判断待ち）
- issue: なし（#377 にはマージの指示で結果を書く想定）
- 判断が必要なこと:
  - 保存名: 「MSIME_連盟プロ・麻雀用語_20261006版.txt」「Google日本語入力_麻雀用語_20261006版.txt」の形（旧の「MSIME_連盟プロ_20260501版.txt」に寄せ、選んだカテゴリを「・」でつなぎ、日付は選んだカテゴリの更新日の新しい方）でよいか
  - 文言: 見出し「カテゴリ」「形式」、ボタン「ダウンロード」、保存後の「1,724語の辞書ファイルを保存しました。」、カテゴリの「連盟プロ（1,099語、2026-10-06更新）」でよいか。生成ページの決まりで末尾に description の文が出るようになった
  - h1「リソース 辞書」の見直し: 案は「リソース 辞書」のまま／「麻雀プロ・麻雀用語の辞書」／「日本プロ麻雀連盟 麻雀プロ・麻雀用語の辞書」（resource_logs の「日本プロ麻雀連盟 麻雀プロが訪れた飲食店ログ」に合わせる形）
  - 既定の形式（今は Microsoft IME）でよいか
  - 平野さんの確認の手順（Windows 11 の Microsoft IME）: プレビューで ① 連盟プロのみ ② 麻雀用語のみ ③ 両方 をそれぞれ Microsoft IME 形式で保存し、IME の「ユーザー辞書ツール」→「ツール」→「テキストファイルからの登録」で取り込む。②・③ で「やおちゅーはい」→「么九牌」、①・③ で「髙」を含む名前（2名）が変換できること、③ が 1,724 語で登録エラーが無いことを見る。あわせて、ブラウザで保存したファイル名が上の形になっているかを見る（このコンテナでは日本語の保存名を確かめきれなかった）
  - 指示文の雛形の行に欠けは無い
- 未確認の項目: 実際のブラウザ（Windows・iPhone の Safari）での保存名と保存の動き、Microsoft IME・Google 日本語入力での取り込み（バイト列までは確かめた）。iPhone の Safari ではファイルの保存の扱いが PC と違う（共有シートやダウンロードの一覧に入る）
- エラー: hook の拒否1件（`git stash list` を含むコマンド。経過に記載。実行されていない）

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
