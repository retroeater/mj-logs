# CHAT-1005-RVW-07

- 着手日時: 2026-10-06
- 対象issue: #377
- ブランチ: work/1006-rvw-377
- 着手時HEAD: d66a5861

## 指示

【Claude作成】Claude Code 向け指示：#377 辞書データをカテゴリごとに分け、選んだカテゴリを1つの辞書ファイルにまとめてダウンロードできるようにする（シートの「辞書」タブと「プロ」タブから生成）。プレビューを出して判断待ちで止まる Chat-Ref: CHAT-1005-RVW-07 マージ: 判断待ちで止まる（プレビューを平野さんが見て決める） 貼る時機: CHAT-1005-RVW-06（h1 を11ページに足す）が cloudflare へマージされた後（`resource_dictionary.html` を両方が変えるため）。マージ前に貼られたら、手順1の「未マージのブランチ」の確認で止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1006-rvw-377 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1006-rvw-377 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1006-rvw-377 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
辞書ページ（`resource_dictionary.html`）を、手書きの静的ページから、シートを元に生成するページへ変える。利用者がカテゴリ（今は「連盟プロ」「麻雀用語」）を選び、選んだカテゴリを1つにまとめた辞書ファイルを、Microsoft IME 用・Google 日本語入力用の2種のどちらかでダウンロードできるようにする。調査は CHAT-1005-RVW-05 のログ（`docs/logs/CHAT-1005-RVW-05.md`）にある。
決定（2026-10-06、平野さん）

* #377 の仕様は RVW-04 で記録済み（カテゴリごとに分け、好きなカテゴリを選んで、ひとつの辞書ファイルにまとめてダウンロード）。カテゴリは今の2つで始め、Mリーガー氏名などはデータを用意してから足す。出す形式は今と同じ2種
* Microsoft IME 用の文字コードは UTF-16LE（BOM 付き・CR+LF・TAB 区切り）にする（RVW-05 の案2）。平野さんが Windows 11 の Microsoft IME で、この形式のテストファイル（3語。「髙」を含む）を取り込めることを確かめた
* スプレッドシートに「辞書」タブ（列: カテゴリ・読み・語・品詞・コメント）を作成済み。「プロ」タブとは別の冊（シート ID: `10g_Xub35Od6vg8zFlKuB-9Kwgyg-HapWWsnfMur7J34`）。中身は麻雀用語 623 行（RVW-05 のログの TSV）。コメントは Google 日本語入力用だけに出す
* 「連盟プロ」は「辞書」タブに写さず、「プロ」タブ（`Y = "Y"` の行。A 名前・B 読み）から生成する。品詞は全件「人名」
* 旧ファイル（`dic/*_20260501.txt` の4つ）は消す。旧 URL への案内（`_redirects` など）は作らない

前提（チャット側。平野さんの決定ではない）

* 生成物は、カテゴリごとに1つのデータファイル（例: `dic/<カテゴリのスラッグ>.json`。行は読み・語・品詞・コメント）にして、ページの JS が選ばれたカテゴリの行を集めて、2形式のテキストを組み立てて `Blob` で保存させる。UTF-16LE への変換は JS でできる（`Uint16Array` 等。ライブラリ・外部ドメインは足さない）。形式の細部（JSON か TSV か）は既存の生成物の作りに合わせてよい
* 選んだカテゴリをまとめるとき、（読み, 語, 品詞）が同じ行は1行にする。Google 日本語入力用は今のファイルと同じ UTF-8・LF・BOM なし・最後の行に改行なし、4列（読み・語・品詞・コメント）。Microsoft IME 用は UTF-16LE・BOM・CR+LF・3列（読み・語・品詞）。見出し行・コメント行は付けない
* 保存するファイル名の案: `MSIME_辞書_<生成日>.txt`・`Google_辞書_<生成日>.txt`（今の「MSIME_連盟プロ_20260501版.txt」の形に寄せてよい。選んだカテゴリ名を入れるかは実物で決めてよい）。件数・更新日（生成日）はページに焼き込む（今の「1067語、2026-05-01更新」の手書きを置き換える）
* 生成の元は、「辞書」タブ（上の冊）と「プロ」タブ（`generate_jpml_pros.py` が読む冊）の2つ。ほかの `generate_*.py` の読み方（gviz・シート名・CSV の読み・エラーの扱い・定期実行の仕組み）に合わせる。「辞書」タブを読めるか（共有の設定）は実物で確かめる
* 「連盟プロ」の語の形は、今の `dic/*_pros_20260501.txt` と同じにする（名前の空白の扱いを含む。RVW-05 は空白を除いて照合した。要確認）。RVW-05 の見込み: 今の辞書と比べて 23 語減り、55 語増える（共通の1,044語は読みが全件一致）。違えば理由を報告に書く
* RVW-05 が見つけた `resource_dictionary.html` の HTML の崩れ（閉じていない `<p>`・余分な `</a>`）は、作り直すときに解消する。h1 の無いページなので、RVW-06 が足した h1 を生かす（RVW-06 のマージ後に着手するため、cloudflare にあるはず。要確認）。既存の見た目・説明文・「辞書登録方法」（@IT の記事2本へのリンク）は保つ
* 公開について: 辞書の中身はすでにサイトで公開しているデータ。ログに貼る件数・差分は件数だけにし、個人の出入りの一覧は書かない
* 生成を定期実行・シートの更新に追随させる仕組み（GitHub Actions のワークフロー）が要る場合、ワークフローの変更は、既存の生成と同じ扱いで行ってよいかを実物（CLAUDE.md・docs/notes/static-generation.md）で確かめる。変えてはいけない決まりがあれば変更せず、案として報告に書く（この場合、生成物は一度手で生成して入れる）

手順

1. 確かめる: 未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`）が `resource_dictionary.html`・`dic/`・`scripts/lib/page.py`・生成の仕組みを変えていないか。RVW-06 がマージされ、`resource_dictionary.html` に h1 があるか。`docs/notes/static-generation.md` と既存の生成スクリプト（`generate_jpml_pros.py` など）の作り、定期実行の仕組み、`dic/` や `resource_dictionary.html` へのリンク（サイト内の他のページ・sitemap・`_redirects`・OGP 等）を洗い出し、「どこをどう変えるか」の表をログに書く。「辞書」タブと「プロ」タブがそれぞれ読めるかを確かめる
2. 作る: (a) 生成スクリプト（例: `scripts/generate_resource_dictionary.py`）とそのテスト（`scripts/tests/`。読み・品詞・コメントの列、重複の扱い、空行・空白の扱い）。カテゴリごとのデータファイル、`resource_dictionary.html`（生成。カテゴリのチェックボックス〈既定は全選択〉、形式の選択〈Microsoft IME／Google 日本語入力〉、ダウンロードのボタン、件数と生成日、辞書登録方法のリンク。1つも選ばれていないときはボタンを押せなくするか、案内を出す。JS が無効のときの案内を1行出す）。(b) 旧ファイル4つを消し、旧ファイルへのリンクを直す。(c) docs: `docs/notes/static-generation.md` の「ページの一覧」の系統を手書きから生成へ直す。決定を `docs/decisions/features.md` に足す（CLAUDE.md「作業ログ」節）。`python3 -m unittest discover -s scripts/tests` を通す。全ページの再生成で、辞書ページ以外に差分が出ないことを確かめる（共有の関数を変えた場合は CLAUDE.md のとおり）
3. 確かめてプレビューを出し、判断待ちで止まる: (a) 生成したデータの確認: 麻雀用語が 623 語で、今の `Google_mahjong_20260501.txt` と（読み・語・品詞・コメントの順番・中身が）一致すること。連盟プロの件数と、旧との差（増減の件数）。(b) JS の確認: Node か Chromium（Playwright。`PLAYWRIGHT_BROWSERS_PATH` が設定済み。ブラウザのダウンロードをしない）で、選択の組み合わせ（連盟プロのみ・麻雀用語のみ・両方）× 2形式をダウンロードし、バイト列を確かめる（UTF-16LE の BOM が先頭に1つだけ・CR+LF・最後の行の扱い・「髙」を含む語が正しく出る・重複なし・品詞名。Google 用は UTF-8・LF・4列）。結果を表でログに書く。(c) 見た目: PC 幅とスマホ幅（iPhone の Safari の幅）で、ページの見た目・ボタンの押しやすさ・既定の選択を確かめる。報告に、確認用 URL（ブランチのプレビュー。取り方は CLAUDE.md・docs/notes/ のとおり）、cloudflare との差分のファイル数（種類ごと）、平野さんに決めてほしい点（ファイル名・ボタンの文言・説明文・生成の定期実行の扱いなど）を書く。cloudflare へは push しない

止まる条件

* 未マージの `work/` ブランチが `resource_dictionary.html`・`dic/`・`scripts/lib/page.py` を変えている（ブランチ名と要点を書いて止まる）
* RVW-06 が cloudflare へ未マージ（`resource_dictionary.html` に h1 が無い）
* 「辞書」タブか「プロ」タブがこのセッションから読めない（共有の設定・ネットワーク。別の手段を試さず、状況を書いて止まる。コードは書いてよいが、実データでの確認は報告に「未確認」と書く）
* 今の辞書（2026-05-01 版）と「辞書」タブの麻雀用語が、件数・中身で食い違う（食い違いの件数と例を、公開してよい範囲で書いて止まる）
* 外部ドメインかライブラリを足す必要が出た
* 全ページの再生成で、辞書ページ以外に意図しない差分が出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）。なお、この指示は cloudflare へ push しない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は判断待ち）。「判断が必要なこと」には、プレビューを見て決める点と、平野さんが Windows 11 の Microsoft IME で、生成したファイルを実際に取り込んで確かめる手順（どのカテゴリの組み合わせで試すか）を書く
* マージは冒頭の「マージ:」の行のとおり（しない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-RVW-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-RVW-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-06 着手。CHAT-1005-RVW-07 のコミットなし。work/1006-rvw-377 はローカル・リモートとも無く、origin/cloudflare（d66a5861）から作成
- 0. 指示欄の末尾は指示文の最後の行と一致。雛形の行は揃っている

### 手順1: 確認

- 未マージの `work/` ブランチは自分の `work/1006-rvw-377` だけ（`resource_dictionary.html`・`dic/`・`scripts/lib/`・`scripts/regenerate.py`・`regenerate-page.yml` を変えるものは無い）
- RVW-06 はマージ済み。`resource_dictionary.html` に `<h1 class="visually-hidden">リソース 辞書</h1>` がある
- 「辞書」タブ（冊 `10g_Xub…J34`）・「プロ」タブ（冊 `1h4-…TeP0`、`SELECT A,B WHERE Y = "Y"` で 1,099 行）とも、このセッションの gviz で読めた

どこをどう変えるか（案。止まったため作っていない）:

| 対象 | 今 | 変え方 |
|---|---|---|
| `scripts/generate_resource_dictionary.py` | 無い | 新規。「辞書」タブを `fetch_records()`（見出しで読む）、「プロ」タブを `generate_jpml_pros.py` と同じ冊・`WHERE Y = "Y"` で読み、カテゴリごとのデータと `resource_dictionary.html` を書く（`render_content()`、`has_search_boxes=False`・`wrap_main=True`） |
| 定期実行 | `regenerate-page.yml` が `scripts/generate_*.py` の有無で対象を決める（週次 `all` と push の判定） | **ワークフローは変えない**（生成スクリプトを1本足せば自動で対象になる、`regenerate.py` の docstring）。出力がディレクトリを含むため `scripts/regenerate.py` の `OUTPUT_OVERRIDES` に `"resource_dictionary": "resource_dictionary.html dic/"` を1行足す |
| 生成日の焼き込み | ページに「1067語、2026-05-01更新」と手書き | 毎週の `all` で日付だけが変わってコミットが出ないよう、データが前回と同じなら前回の日付を保つ作りにする案 |
| `dic/` | 4ファイル（旧形式） | 消して、カテゴリごとのデータ（例: `dic/pros.json`・`dic/mahjong.json`）に置き換える |
| `resource_dictionary.html` | 手書き（閉じていない `<p>`・余分な `</a>`） | 生成に変える。h1・title・description・og は今の値を保つ。ページの JS（例: `resource_dictionary.js`）で集めて Blob で保存 |
| `scripts/apply_page_meta.py` | title・description の表に `resource_dictionary.html` の行がある | 生成ページでも同じ値を `PageMeta` に入れる（表の行は残すか外すかを実装時に確かめる） |
| `docs/notes/static-generation.md` | 「ページの一覧」で静的なページ（4）に入っている。「navbar.js と検索欄」で手書き4ページに数えられている | 系統を生成へ移し、件数を直す |
| サイト内のリンク | navbar.js（`/resource_dictionary.html`）・`llms.txt`・`sitemap-pages.xml` はページへのリンクだけ。`dic/` を直接指すのはページ本体だけ（ほかに docs/gsc の記録と `docs/review-followup-instructions.md` の古い記述） | ページの URL は変わらないので直さない。`llms.txt` の説明文（「辞書ファイル（Microsoft IME・Google日本語入力）」）は今のままで合う |

「辞書」タブの実物（2026-10-06 取得）:

- 見出し: カテゴリ・**よみ**・**単語**・品詞・コメント・**備考**（RVW-05 の案の「読み」「語」と名前が違い、「備考」列がある。生成は見出しの名前で読むので、実物の名前に合わせればよい）
- 625 行。カテゴリは全件「麻雀用語」。空の行・読みや語の空欄は無い

### 止まった理由: 「辞書」タブの麻雀用語が今の辞書（2026-05-01 版）と食い違う

止まる条件「今の辞書と『辞書』タブの麻雀用語が、件数・中身で食い違う」に当たるため、手順2（作る）に入らず止まった。

| 食い違い | 件数 | 例 | 見立て |
|---|---:|---|---|
| タブにだけある語 | 2 | しゅはい／取牌（備考「20260605追加」）、だちゃんすう／打荘数（備考「20260713追加」） | 2026-05-01 版の後に平野さんが元データに足した語。公開ファイルには入っていない |
| 語の文字が違う | 1 | やおちゅーはい: タブは「么九牌」、`Google_mahjong_20260501.txt` は「?九牌」 | 今の公開ファイルの文字化け。「么」は cp932（Microsoft IME 用の今の文字コード）に無い字で、変換で「?」になったと見られる（Google 用の UTF-8 のファイルも「?」）。UTF-16LE にすれば表せる |

ほかの 622 行は、読み・語・品詞・コメントの順番と中身が一致した（タブ 625 行 = 一致 622 + 追加 2 + 修正 1）。どれも誤りではなくタブ側が新しいと見られるが、指示の止まる条件どおり、判断を待つ。

## 報告

- 状態: 中断（止まる条件に当たった。「辞書」タブの麻雀用語が今の辞書と食い違う）
- ブランチ: work/1006-rvw-377
- ログ: https://github.com/retroeater/mj/blob/work/1006-rvw-377/docs/logs/CHAT-1005-RVW-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1006-rvw-377
- 確認用URL: なし（コードは書いていない）
- マージ: しない
- issue: なし（#377 にはコメントしていない）
- 判断が必要なこと:
  - 「辞書」タブの麻雀用語は 625 行で、今の辞書（623 語）と3件違う: 追加2語（取牌・打荘数、備考に追加日）と、今の公開ファイルの文字化け「?九牌」がタブでは「么九牌」。タブを正として進めてよいか（進めると麻雀用語は 625 語になる）
  - 「辞書」タブの見出しは「よみ」「単語」で、「備考」列がある。生成は見出しの名前で読み、「備考」は出さない（管理用）でよいか
  - 続きは新しい番号の指示で（このログにコミットがあるため、同じ Chat-Ref では貼り直せない）。作業ブランチ work/1006-rvw-377 はこのログだけで、そのまま続けて使える
  - 指示文の雛形の行に欠けは無い
- 未確認の項目: 連盟プロの件数と旧との差（「プロ」タブは読めたが、作る手前で止まったため数えていない。RVW-05 の見込みは −23・+55）
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
