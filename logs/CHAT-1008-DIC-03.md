# CHAT-1008-DIC-03

- 着手日時: 2026-10-08
- 対象issue: #515
- ブランチ: work/1008-dic
- 着手時HEAD: cd4e3d2c

## 指示

【Claude作成】Claude Code 向け指示：辞書ページに Gboard（Android）形式を足し、新しい見た目の案を比較ページで見られるようにする（判断待ちで止まる） Chat-Ref: CHAT-1008-DIC-03 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1008-DIC-02 はマージ済み） 作業ブランチ: クラウドセッションで実行する。work/1008-dic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1008-dic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-dic の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
辞書ページ（`resource_dictionary.html`）で、(1) Android の Gboard に取り込める形式を選べるようにし、(2) ページを新しい見た目にする。見た目は文章で決めず、比較ページで平野さんが Android と PC で見比べて選ぶ。この指示では本番のページは見た目を変えず、比較ページは noindex・どこからもリンクしない・sitemap に載せない。
決定（2026-10-09、平野さん）

* CHAT-1008-DIC-02 の「判断が必要なこと」への答え: 動詞の2語（カブる・喰い取る）は「辞書」タブから削除した（動詞の品詞の対応は作らない）。カテゴリをまたぐ（よみ, 単語）の重複（一般社団法人Mリーグ機構・日本プロ麻雀連盟）は「辞書」タブでこのままにする
* スマホ向けは Android（Gboard）の形式を足す。iPhone は採用を保留（2026-10-08）。平野さんが Android の実機で取り込みを試す
* ページをモダンな見た目にしたい。シンプルで直感的に。ページ全体をスタイリッシュに、チェックボックス・ラジオボタンもスタイリッシュな案を複数見たい
* 「辞書登録方法」は、できれば公式のページを参照したい（Android も）。リンクは目立たないインフォメーションチップのような形にしたい

前提（チャット側。平野さんの決定ではない）

* Gboard 形式（チャット側の調べ。公式の仕様書は見つからなかった）: Gboard の単語リストの取り込みは zip を選ぶ。中身は UTF-8 のテキスト1つで、先頭行 `# Gboard Dictionary version:1`、以下「よみ TAB 単語 TAB ja-JP」（LF）。品詞・コメントの欄は無い（出さない）。経路は Gboard の設定 → 単語リスト → 単語リスト → 日本語 → 右上のメニュー → インポート。出典: dohack「Gboardの辞書機能（単語リスト）の使い方」（2024-11 更新）、小技チョコレート「Google日本語入力の辞書ツールに登録した単語を Android 版の Gboard にインポートする方法」（2025-05 更新）、@IT「Gboard で単語をユーザー登録する方法」。実物（Gboard の書き出しの形・zip の中のファイル名）と食い違えば実物に合わせ、どこが違ったかを報告する
* zip はページの JS で作る（無圧縮〈stored〉の zip で足りる。CRC-32 を自前で計算。外部ライブラリ・外部ドメインは使わない〈CSP、#9〉）。保存名は今の形に合わせ「YYYYMMDD_Gboard_<カテゴリ>辞書.zip」（中のテキスト名は Gboard の書き出しに合わせる。分からなければ `dictionary.txt`）
* 公式の「登録方法」の資料（チャット側が 2026-10-09 に確かめた）: どれもファイルからの一括取り込みの手順は書いていない。そのため、チップを押すと出る小さな吹き出し（ポップオーバー）に取り込みの3手順を書き、その下に公式ヘルプへのリンクを置く案
   * Microsoft IME: 「Microsoft 日本語 IME」 https://support.microsoft.com/ja-jp/windows/hardware/input-devices/microsoft-japanese-ime （「Microsoft IME 設定」の節。ユーザー辞書ツールの開き方まで）
   * Google 日本語入力: 「辞書 - 日本語入力 ヘルプ」 https://support.google.com/ime/japanese/answer/166765?hl=ja （辞書ツールの開き方まで）
   * Gboard: 公式ヘルプに単語リストの取り込みの記事は無い。「入力候補を表示して間違いを修正する」 https://support.google.com/gboard/answer/7068415?hl=ja&co=GENIE.Platform%3DAndroid （1語ずつの追加まで）を置くか、公式リンクを置かないかは比較ページで両方を見せる
   * 3手順の文案（実物の画面名と違えば直してよい）: Microsoft IME「タスクバーの［あ］/［A］を右クリック →［単語の追加］→［ユーザー辞書ツール］→［ツール］→［テキストファイルからの登録］」。Google 日本語入力「［プロパティ］→［辞書］→［編集］で辞書ツールを開く →［管理］→［新規辞書にインポート］」。Gboard「Gboard の設定 →［単語リスト］→［単語リスト］→［日本語］→ 右上の︙ →［インポート］で zip を選ぶ」
* 比較ページの案（チャット側の提案。4つの軸を、ページ上部のラジオボタンで別々に切り替えられるようにする。作り方は docs/notes/title-pages.md の比較ページ〈`:has(#…:checked)` で切り替え〉に倣う）:
   * 軸1 ページ全体: P1「ステップカード」（1カラム・中央の白いカードに ①カテゴリ ②形式 ③ダウンロード の番号付きの段）／P2「サマリー」（PC は左に選択・右に「選んだ語数・形式・ダウンロード」の固定パネル、スマホは画面下に固定のバー）／P3「ミニマル」（区切り線と余白だけ。ボタンは下部に固定）
   * 軸2 カテゴリ（チェックボックス）: C1「タイル」（カテゴリごとのカード。語数と短い説明。選ぶと枠の色とチェックの印）／C2「チップ」（丸いピル。選ぶと塗り）／C3「スイッチ」（行の右にトグルスイッチ）
   * 軸3 形式（ラジオボタン）: R1「セグメント」（横並びの切替。Microsoft IME | Google 日本語入力 | Gboard）／R2「アイコン付きカード」（Windows・PC/Mac・Android を表す簡単な図形。ロゴは使わない）／R3「ピル」
   * 軸4 登録方法: H1「ⓘ チップ＋吹き出し」（形式の見出しの横に小さなチップ「登録方法」。押すと3手順と公式ヘルプへのリンク）／H2「チップから公式へ直接」（選んだ形式の下に小さなチップ「登録方法 ↗」）／H3「折りたたみ」（ページ下部に `<details>`）
   * どの組み合わせでも、選んだ語数の合計（重複をまとめた後の数）をボタンの近くに出し、何も選ばないとボタンを押せなくする案
* 色・文字の大きさは今の `style.css`（リンク色 `#14459b`、文字のコントラスト AAA〈7:1〉、UI 部品の境界・フォーカス 3:1〈1.4.11〉）に合わせる。リポジトリに DESIGN.md があれば読んで合わせる。外部のフォント・アイコン・CDN は使わない
* 比較ページは実際にダウンロードできる（Gboard 形式を含む）ようにする。平野さんはそのページで Android の取り込みも試す

手順

1. 確かめる: #515 の本文・コメントを読み、Open であること・他セッションの着手中コメントが無いことを確かめる。この指示の UI の作業を #515 で扱うか、新しい issue（「辞書ページの見た目の刷新」など）に分けるかは、#515 の範囲を読んで決め、分けたときは起票して #515 と結ぶ。未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が `scripts/generate_resource_dictionary.py`・`resource_dictionary.js`・`resource_dictionary.html`・`dic/`・`style.css` を変えていないか確かめる。CHAT-1008-DIC-02 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1008-DIC-03` を足し、上の決定（動詞の2語の削除・重複はこのまま）を `docs/decisions/` に足す。
2. 作る: 本番の辞書ページの形式に「Gboard（Android）」を足す（見た目は今のまま）。前提の案で比較ページを作る（4つの軸を別々に切り替え。チャット側の案が実物に合わないところは直してよく、直した点を報告する）。ローカルの Chromium で、PC 幅とスマホ幅（390px 程度）の両方で各案を開いてスクリーンショットを撮り、Gboard 形式の zip を保存して中身（先頭行・行数・文字コード・改行・重複の無いこと）を確かめる。キーボードだけで選べること・フォーカスが見えることも確かめる。
3. 報告する: プレビュー URL（PC とスマホで開ける比較ページの URL と、Gboard 形式を足した本番ページのプレビュー）と、平野さんに見てほしい組み合わせの数を減らす提案（相性の良い組み合わせ2〜3個）を報告に書いて、判断待ちで止まる。

止まる条件

* #515 が Closed、または他セッションの着手中コメントがある
* 未マージの work/ ブランチが上の5つのどれかを変えている
* 全ページの再生成で、辞書ページ・比較ページ・`dic/` 以外に、ほかのシートの変化で説明できない差分が出た
* 外部ドメインのリソースを使わないと作れない案があった（その案は作らずに報告する）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-DIC-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-DIC-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0章・ブランチ

- 「指示」欄の末尾は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・作業ブランチ・共通手順）は揃っている
- `git log --all --grep="CHAT-1008-DIC-03"` は0件
- ローカルの work/1008-dic は origin/cloudflare の祖先、origin/work/1008-dic もマージ済み。`docs/notes/cloud-sessions.md`「作業ブランチの用意」のとおり `git merge --ff-only origin/cloudflare` で進めた（着手時 HEAD cd4e3d2c）

### 手順1（確かめ）で止まった

- #515: Open。着手中コメントは CHAT-1008-DIC-02（このセッション）のものだけで、他セッションのものは無い
- 未マージの work/ ブランチのうち `origin/work/1008-hou`（CHAT-1008-HOU-02、#518 鳳凰戦の新ページ houou/ の未公開の見本、先頭 e25f04f4）が `style.css` を変えている。止まる条件「未マージの work/ ブランチが上の5つのどれかを変えている」に当たるため、手順2に入らず止まった
  - 中身: `style.css` の末尾に `/* ==== 鳳凰戦の新ページ houou/(#518、未公開) ==== */` 以下 424 行を足すだけ（既存の行は変えていない）。`.mj-houou*` のクラスで、辞書ページのクラスとは重ならない
  - ほかの4つ（`scripts/generate_resource_dictionary.py`・`resource_dictionary.js`・`resource_dictionary.html`・`dic/`）を変えている未マージのブランチは無い
- 止まる前に、手順1のうち文書の2点は済ませた: CHAT-1008-DIC-02 のログの状態に ` / 続き: CHAT-1008-DIC-03` を足した。`docs/decisions/features.md` に 2026-10-09 の決定を足し、2026-10-08 の「動詞として扱えるようにする」に「→ 置き換え」を付けた
- #515 で扱うか別の issue に分けるかは、手順2に入らないため決めていない（起票もしていない）。#515 への着手コメントもしていない（DIC-02 の着手中コメントは残っている）

## 報告

- 状態: 判断待ち / 続き: CHAT-1008-DIC-04
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/work/1008-dic/docs/logs/CHAT-1008-DIC-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: なし（手順2に入っていない）
- マージ: 未（指示どおり判断待ち。今のブランチの変更は docs/logs・docs/decisions だけ）
- issue: #515
- 判断が必要なこと:
  - 未マージの work/1008-hou（#518）が `style.css` の末尾に houou/ 用の 424 行を足しているため止まった。進め方の案: 比較ページと辞書ページの新しい見た目の CSS は `style.css` に入れず、辞書用の別ファイル（例 `resource_dictionary.css`、比較ページはページ内の `<style>`）に置く。Gboard 形式の追加は `style.css` を使わない。これなら work/1008-hou と触るファイルが重ならない。この案で進めてよいか（または work/1008-hou のマージを待つか）
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
