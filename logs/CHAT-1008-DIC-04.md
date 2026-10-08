# CHAT-1008-DIC-04

- 着手日時: 2026-10-08
- 対象issue: #515
- ブランチ: work/1008-dic
- 着手時HEAD: 734f7ed0

## 指示

【Claude作成】Claude Code 向け指示：DIC-03 の続き。辞書ページの CSS を別ファイルにして、Gboard 形式の追加と見た目の比較ページを作る（判断待ちで止まる） Chat-Ref: CHAT-1008-DIC-04 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1008-DIC-03 は判断待ちで止まっている） 作業ブランチ: クラウドセッションで実行する。未マージの work/1008-dic を続けて使う（CHAT-1008-DIC-03 の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-dic の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 あわせて、CHAT-1008-DIC-03 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。

目的
CHAT-1008-DIC-03 は、未マージの work/1008-hou（#518）が `style.css` を変えているため手順1で止まった。その答えを受けて、DIC-03 の手順2（作る）・手順3（報告する）を行う。仕様（目的・決定・前提・止まる条件）は DIC-03 のログの「指示」欄のとおりで、下の決定だけを足す。
決定（2026-10-09、平野さん）

* 辞書ページの新しい見た目の CSS は `style.css` に入れず、辞書用の別ファイル（例 `resource_dictionary.css`）に置く。比較ページの案ごとの CSS はページ内の `<style>` でよい（DIC-03 の報告の案のとおり）

前提（チャット側。平野さんの決定ではない）

* 別ファイルの CSS は自ドメインの静的ファイルとして読み込む（外部ドメインは使わない）。`.assetsignore` に載せない（公開する）。色の値は `style.css` の変数・値をそのまま使い、別ファイルで新しい色を作るときはコントラストを WCAG 2.x の式で計算して報告する
* work/1008-hou がこの作業中にマージされて `style.css` が変わっても、辞書の CSS は別ファイルのまま進めてよい

手順

1. 確かめる: CHAT-1008-DIC-03 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1008-DIC-04` を足す。上の決定を `docs/decisions/` に足す。未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）を一覧にし、`scripts/generate_resource_dictionary.py`・`resource_dictionary.js`・`resource_dictionary.html`・`dic/`・新しく作る CSS のファイル名を変えているものが無いことを確かめる（`style.css` だけを変えているものは止まる理由にしない）。
2. 作る: DIC-03 の手順2のとおり（本番の辞書ページに Gboard 形式を足す〈見た目は今のまま〉・4つの軸を切り替えられる比較ページ・PC 幅とスマホ幅のスクリーンショット・Gboard の zip の中身の確かめ・キーボード操作とフォーカスの確かめ）。
3. 報告する: DIC-03 の手順3のとおり（比較ページと本番ページのプレビュー URL、相性の良い組み合わせ2〜3個の提案）。判断待ちで止まる。

止まる条件

* CHAT-1008-DIC-03 の状態が「判断待ち」でない
* 未マージの work/ ブランチが上の手順1のファイル（`style.css` を除く）を変えている
* DIC-03 の「止まる条件」に当たった（未マージのブランチの条件は上の手順1に置き換える）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-DIC-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-DIC-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0章・ブランチ

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・作業ブランチ・共通手順）は揃っている
- CHAT-1008-DIC-03 の `## 報告` の状態は「判断待ち」
- `git log --all --grep="CHAT-1008-DIC-04"` は0件
- ローカルの work/1008-dic は origin/work/1008-dic と同じ（734f7ed0）。`git merge-base --is-ancestor origin/cloudflare HEAD` は真で、取り込みは不要だった

### 手順1（確かめ）

- DIC-03 のログの状態に ` / 続き: CHAT-1008-DIC-04` を足した。`docs/decisions/features.md` に 2026-10-09（DIC-04）の決定を足した（DIC-03 の決定は DIC-03 の最後の push で足してある）
- 未マージの work/ ブランチ: `origin/work/1008-dic`（この作業）・`origin/work/1008-hou`（`style.css` だけ）・`origin/work/1009-nen`（`style.css` だけ）。`scripts/generate_resource_dictionary.py`・`resource_dictionary.js`・`resource_dictionary.html`・`dic/`・`resource_dictionary.css`・`resource_dictionary_compare.html` を変えているものは無い
- #515: Open、他セッションの着手中コメントなし
- issue の分け方: #515 は「Mリーグの追加と既存のカテゴリの見直し」なので、Gboard 形式（2026-10-08 の決定で #515 の中）は #515 に残し、見た目の刷新は新しい #522「辞書ページの見た目を刷新する（比較ページで案を選ぶ）」に分けた。起票前にクローズ済みを含めて題に「辞書」「Gboard」「見た目」を含む issue を探し、#515・#377・#26（リンクの見た目、無関係）だけだった。#522 の本文に「元: #515」、#515 に #522 へのコメントを残した

### 手順2（作る）

本番の辞書ページ（見た目は今のまま）:

- 形式に「Gboard（Android）」のラジオボタンを足した
- `resource_dictionary.js`: Gboard 形式を足した。見出し行 `# Gboard Dictionary version:1`、以下「読み TAB 語 TAB ja-JP」、UTF-8・LF・末尾改行なし（ほかの2形式に合わせた）。品詞・コメントは出さない。`dictionary.txt` を無圧縮（stored）の zip に入れる（CRC-32 は自前、外部ライブラリなし）。保存名は `YYYYMMDD_Gboard_麻雀用語辞書.zip`（今の保存名の形のまま。ほかの2形式は `.txt`）
- JSON の読み込みを1回だけにした（カテゴリごとにキャッシュ）。語数の表示（`#dicTotal`）があるページだけ、選ぶたびに重複をまとめた後の語数を出す（本番のページには無いので動きは変わらない）
- 「辞書登録方法」のリンク（@IT の2記事）と META の description（「Microsoft IME・Google日本語入力」）は変えていない。description は `scripts/apply_page_meta.py` と揃える必要がある

比較ページ `resource_dictionary_compare.html`（#522）:

- 同じ生成スクリプトが書く（`render_compare_page()`）。noindex、navbar・サイトマップ・`llms.txt`・既存のページからリンクしない。`scripts/regenerate.py` の出力の一覧に足した（Actions の再生成で語数がずれないように）
- 共通の CSS は `resource_dictionary.css`（新規。`.assetsignore` に載せない。`assets-check.yml` の許可リストは `*.css`・`*.html` を含むので変更不要）。案ごとの CSS はページ内の `<style>`。`style.css` は変えていない
- 4つの軸と「Gboard の公式リンク（あり/なし）」を、ページ上部のラジオボタンで別々に切り替える（`body:has(#…:checked)`、JS なし）。切り替えの欄は `<details open>` で、スマホでは畳める
- 色は `style.css`・Bootstrap の値（本文 #212529・補足 #495057・リンク/選択 #14459b・部品の境界 #868e96・区切り #dee2e6）。新しく足したのは選択時の淡い地 #eef3fb だけ。WCAG 2.x の式で計算したコントラスト比:

| 文字・部品 | 地 | 比 |
|---|---|---|
| 本文 #212529 | 白 / #eef3fb | 15.43 / 13.85 |
| 補足 #495057 | 白 / #eef3fb / P1 の地 #f1f3f5 | 8.18 / 7.34 / 7.35 |
| リンク・選択 #14459b | 白 / #eef3fb | 8.92 / 8.01 |
| 白（選択した部品・ボタンの文字） | #14459b / ホバー #0d2f6e | 8.92 / 12.74 |
| 部品の境界 #868e96 | 白 | 3.32（#eef3fb・#f1f3f5 の上では 2.98・2.99 のため、白の上でだけ使っている） |

- チャット側の案から直した点:
  - P3「ミニマル」のボタンは画面下に固定（fixed）でなく、本文の末尾に付く sticky にした（ページが短く、固定すると末尾の余白が要るため）。P2 のスマホは案のとおり画面下に固定
  - H2 のチップの文言は「<形式> の登録方法 ↗」。「Gboard の公式リンク: なし」のとき、H2 は Gboard のチップを出さず、取り込みの3手順を1行の文で出す。H1・H3 では公式ヘルプの行を消す
  - カテゴリの短い説明（タイル・スイッチで出す）はこちらで書いた: 麻雀用語「役・牌・ルールなどの用語」、連盟用語「タイトル戦・大会・番組など」、連盟プロ「日本プロ麻雀連盟のプロの名前」、Mリーグ「チーム・選手・Mリーグの用語」
  - 吹き出しは HTML の `popover` 属性（JS なし。Esc・外側のタップで閉じる）。中身は選んだ形式の3手順と公式ヘルプ
  - 形式の図形は線だけの SVG（モニター・ノートPC・スマホ）。ロゴは使っていない
- 外部ドメインのリソースは使っていない（公式ヘルプはリンクだけ）。外部が要る案は無かった
- `style.css` の全体の規則 `input { width: 100px }` が比較の切り替えのラジオボタンを崩したので、比較ページの `<style>` で `width: auto` にした

全ページの再生成（`python3 scripts/regenerate.py all`、1分40秒、エラーなし）の差分は `resource_dictionary.html`（Gboard のラジオボタン、麻雀用語 547→546語）・`resource_dictionary_compare.html`（新規）・`dic/mahjong.json` だけ。`dic/mahjong.json` は「カブる」「喰い取る」が消え（決定のとおり）、「Mリーグ機構」（えむりーぐきこう）が増えた（平野さんの「辞書」タブの変更）。

ローカルの Chromium（Playwright、`python3 -m http.server`）での確かめ:

- スクリーンショット: PC 幅 1280px とスマホ幅 390px で、各軸の3案（12案）と吹き出しを撮った（scratchpad。リポジトリには入れていない）。P2・P3 はスマホの画面内（上端・下端）も撮った
- Gboard の zip（比較ページと本番ページの両方、4つ全部と「連盟プロ」「Mリーグ」だけ）: Python の `zipfile` で開け、CRC の検査（`testzip()`）は通る。中は `dictionary.txt` 1つ・無圧縮。先頭行 `# Gboard Dictionary version:1`、BOM なし UTF-8、CR なし（LF）、各行3列で3列目は `ja-JP`、行数は 1,822（4つ）・1,152（2つ）で（読み, 語）の重複は0。見込み（`dic/*.json` から数えた重複をまとめた後の数）と一致
- ほかの2形式（本番ページ）: Microsoft IME（BOM 付き UTF-16LE・CR+LF・3列）・Google 日本語入力（UTF-8・LF・4列）とも 1,822・1,152 行、重複0。DIC-02 から形は変わっていない
- 比較ページの語数の表示: 4つで 1,822、1つ外すと 1,279、全部外すと 0 になりボタンを押せなくなり「カテゴリを1つ以上選んでください。」が出る
- キーボード: Tab でカテゴリ4つ → 「登録方法」チップ → 形式 → ダウンロードの順に移り、どれもフォーカスの輪（白2px＋#14459b 2px）が出る。Space でカテゴリを外せる、矢印キーで形式を選べる、Enter で吹き出しが開き Esc で閉じる
- プレビュー（Workers Builds のバージョン URL。URL は最終報告にだけ書く）でも、比較ページ・本番ページ・CSS・JS・`dic/` が 200、比較ページに noindex、Gboard の zip を保存して中身が同じであることを確かめた
- テスト: `ComparePageTest` と Gboard のラジオボタンのテストを足した。`python3 -m unittest discover -s scripts/tests` は OK
- ヘッドレスの Playwright では保存名が `download` と報告される（DIC-02 と同じ）。保存名を作る処理は拡張子を足しただけ

## 報告

- 状態: 判断待ち / 続き: CHAT-1008-DIC-05
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/work/1008-dic/docs/logs/CHAT-1008-DIC-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: プレビューあり（URL は最終報告）。比較ページ `resource_dictionary_compare.html` と、Gboard 形式を足した `resource_dictionary.html`
- マージ: 未（指示どおり判断待ち）
- issue: #515（Gboard 形式）・#522（見た目の刷新。新規）
- 判断が必要なこと:
  - 比較ページで見た目の組み合わせを選んでほしい。見る数を減らす提案（相性の良い3つ）:
    - A「P1 ステップカード＋C1 タイル＋R2 アイコン付きカード＋H1 チップ＋吹き出し」: 段と選んだものが一番はっきりする。PC でもスマホでも崩れにくい。形式の図形で Android（Gboard）が分かりやすい
    - B「P2 サマリー＋C3 スイッチ＋R1 セグメント＋H1 チップ＋吹き出し」: スマホでは語数とボタンが画面下に固定され、アプリに近い。スイッチの行は短く、カテゴリが増えても縦に伸びるだけ
    - C「P3 ミニマル＋C2 チップ＋R3 ピル＋H2 チップから公式へ」: 一番軽く短い。カテゴリの説明は出ない
  - Gboard の公式リンク（あり/なし）。公式ヘルプは1語ずつの追加までで、ファイルの取り込みは書いていない
  - Android の実機で Gboard の取り込みを試してほしい（比較ページでも本番ページのプレビューでも保存できる）。取り込めなかったときは、Gboard の「単語リスト」→「書き出し」で出る zip の中のファイル名と先頭の数行を教えてほしい
  - Gboard 形式の本番への公開: 本番ページは見た目を変えずに Gboard を足しただけなので、実機で取り込めたら見た目の判断と別に先にマージしてよいか。あわせて、本番ページの「辞書登録方法」に Gboard の行を足すか（今は @IT の2記事だけ）、META の description（「Microsoft IME・Google日本語入力」）に Gboard を足すか（`scripts/apply_page_meta.py` も直す）
  - カテゴリの短い説明（経過に書いた4つの文）の文言でよいか
- 未確認の項目:
  - Gboard の実機での取り込み（見出し行・`ja-JP`・zip の中のファイル名 `dictionary.txt`・末尾改行なし・読みの長さの上限）。形はチャット側の調べ（非公式の解説3つ）に合わせただけで、Gboard の実際の書き出しファイルとは比べていない
  - 3つの形式の取り込みの手順の画面名（指示文の文案のまま。実機の画面と比べていない）
  - Android の Chrome と PC の実際のブラウザでの見え方（ローカルの Chromium とプレビューのヘッドレスでだけ見た）。実際のブラウザでの保存名（ヘッドレスでは `download` と出る）
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
