# CHAT-1008-DIC-02

- 着手日時: 2026-10-08
- 対象issue: #515
- ブランチ: work/1008-dic
- 着手時HEAD: 88d5ee4b

## 指示

【Claude作成】Claude Code 向け指示：辞書（#515）のカテゴリを4つにし、止まっている辞書ページの生成を直してマージする。動詞の品詞の扱いを調べる Chat-Ref: CHAT-1008-DIC-02 マージ: 承認済み（チャットで） 貼る時機: 平野さんが「辞書」タブの（よみ, 単語）の重複（「日本プロ麻雀協会」が2行）を直した後 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-dic の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 あわせて、「辞書」タブを生成と同じ経路で読み、同じカテゴリの中で（よみ, 単語）が重複する行があれば何もせず止まる（貼る時機の前提を満たしていない。重複した行を報告する）。

目的
CHAT-1008-DIC-01 は送る前に差し替えたため欠番。「辞書」タブに平野さんがカテゴリ「連盟用語」「Mリーグ」を足し、管理用の「サブカテゴリ」列を足した（「備考」列は無くなった）。`scripts/generate_resource_dictionary.py` はカテゴリを2つ（連盟プロ・麻雀用語）に固定しており、知らないカテゴリがあると `GenerationError` で止まるため、次の辞書ページの生成が失敗する（チャット側が 2026-10-08 に cloudflare の版で `CATEGORIES` と `rows_from_dict_tab()` を読んで確かめた）。カテゴリを4つにして直し、本番に入れる。あわせて、動詞の品詞の扱いを調べる（実装はしない）。
決定（2026-10-08、平野さん）

* #515 の中で進める
* 「辞書」タブのカテゴリは「麻雀用語」「連盟用語」「Mリーグ」の3つ。これに「プロ」タブから作る「連盟プロ」を足した4つをページに出す。並びは 麻雀用語 → 連盟用語 → 連盟プロ → Mリーグ
* 「辞書」タブのカテゴリの値も「連盟用語」にする（ページの表示名と同じ）
* 「辞書」タブの「サブカテゴリ」列はシートの並べ替え用で、ウェブサイトでは使わない（辞書ファイルにもページにも出さない）
* 同日の昼に決めた「団体・組織」「大会・イベント」のカテゴリは作らない（団体の語は「麻雀用語」のサブカテゴリ「団体」に置いた）
* 「Mリーグ」には全チーム名・全選手名、「セミファイナルシリーズ」などの用語、Mリーグ機構・チェアマンの氏名などを入れる。スポンサーは入れない。データは平野さんが「辞書」タブに入れる
* 「カブる」「喰い取る」は動詞として扱えるようにする（どう扱うかはこの指示の調べの後で決める）
* このカテゴリの追加は、プレビューを見ずにマージまで進めてよい
* スマホ向けは Android（Gboard）の形式を足す方向（平野さんが Android で試せる）。iPhone は採用を保留。Gboard の形式はこの指示では扱わず、次の指示で扱う

前提（チャット側。平野さんの決定ではない）

* スラッグの案: 麻雀用語 `mahjong`（今のまま）、連盟用語 `renmei`、連盟プロ `pros`（今のまま）、Mリーグ `mleague`。実物に合わせて変えてよい
* チャット側が 2026-10-08 23時台に読んだ「辞書」タブ: 743行（麻雀用語 543・連盟用語 127・Mリーグ 73）、品詞は全件「名詞」、見出しは カテゴリ・サブカテゴリ・よみ・単語・品詞・コメント（ほかに見出しの無い空の列が1つ）。その後も平野さんが直す
* 「辞書」タブに行が1つも無いカテゴリは、生成を止めず、ページに出さない（`dic/<スラッグ>.json` も書かない）案。行を足せば次の生成で出る
* 「Mリーグ」には連盟プロと同じ人（「プロ」タブの登録名と一致するのは20行）が入る。「日本プロ麻雀連盟」「一般社団法人Mリーグ機構」も2つのカテゴリにある。2つ以上のカテゴリを選んで保存するとき、ページの JS で（よみ, 単語）が同じ行を1つにまとめる（先に並ぶカテゴリの行を残す）案。今の JS がすでにまとめているなら変えない
* 品詞の確かめ（`KNOWN_POS` = 名詞・固有名詞・人名）は変えない。動詞はこの指示では足さない（平野さんにはシートの品詞を「名詞」のままにしてもらう）
* 平野さんは作業の途中でも「辞書」タブに行を足していく。行数は読み直すたびに変わりうる

手順

1. 確かめる: #515 の本文・コメントを読み、Open であること・他セッションの着手中コメントが無いことを確かめる。未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が `scripts/generate_resource_dictionary.py`・`resource_dictionary.js`・`resource_dictionary.html`・`dic/` を変えていないか確かめる。「辞書」タブを2回読み、カテゴリごとの行数を報告する（2回で行数が違えば止まる）。
2. 作る: カテゴリを4つにし（並びは決定のとおり。「サブカテゴリ」列は読まない）、行の無いカテゴリの扱いと、複数カテゴリを選んだときの重複のまとめ方を前提の案で入れる。`docs/notes/static-generation.md`「ページの一覧」の辞書の行と、`docs/decisions/` の決定を直す（追記先の今の内容を読んでから。同じ趣旨の記述は置き換え・拡張してよく、矛盾してどちらが正か判断が要るときだけ止まる）。全ページを再生成し、差分を種類に分けて報告する。ローカルの Chromium で、4つを全部選んだときと「連盟プロ」「Mリーグ」だけを選んだときの2形式の保存を確かめ、語数と重複が無いことを報告する。#515 に経過をコメントする。マージ: 承認済み（チャットで）。
3. 調べる（実装しない）: 動詞「カブる」「喰い取る」（ラ行五段）を Microsoft IME（UTF-16LE の取り込み用テキスト）と Google 日本語入力の辞書ファイルで登録するときの品詞の名前（活用の種類を含む）を、公式の資料か実際に書き出したファイルで確かめ、「辞書」タブの「品詞」列に何と書けば両形式と Gboard（次の指示）に出せるかの案を出す。確かめられない点は「未確認の項目」に書く。結果は報告の「判断が必要なこと」に書く。

止まる条件

* #515 が Closed、または他セッションの着手中コメントがある
* 未マージの work/ ブランチが上の4つのどれかを変えている
* 「辞書」タブに「麻雀用語」「連盟用語」「Mリーグ」以外のカテゴリの値がある（名前を報告する）。または行数が 600〜1,000 の範囲を外れる
* 全ページの再生成の差分に、決定と「辞書」タブ・ほかのシートの変化で説明できない変更がある（見込み: `resource_dictionary.html`・`dic/*.json` の変化と、マージ後の自動再生成と同じ種類の変化だけ。`sitemap` の lastmod はよい）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり。マージ後の Workers Builds と regenerate-page.yml の結果を待つ上限は15分（超えたらその時点の状態を書き「未確認の項目」に回す）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-DIC-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-DIC-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1008-dic
- ログ: https://github.com/retroeater/mj/blob/work/1008-dic/docs/logs/CHAT-1008-DIC-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-dic
- 確認用URL: なし
- マージ: 未
- issue: #515
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 88d5ee4b）: https://github.com/retroeater/mj-logs/tree/main/guide/88d5ee4b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/88d5ee4b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/88d5ee4b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/88d5ee4b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/88d5ee4b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/88d5ee4b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/88d5ee4b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/88d5ee4b.md
