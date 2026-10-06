# CHAT-1005-RVW-05

- 着手日時: 2026-10-06
- 対象issue: #377（調査のみ）
- ブランチ: work/1006-rvw-dic
- 着手時HEAD: fad9eb53

## 指示

【Claude作成】Claude Code 向け指示：#377 辞書データの今の形を調べ、シートの「辞書」タブの列の案と貼り付け用のデータを用意する（調査だけ） Chat-Ref: CHAT-1005-RVW-05 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい〈調査だけの指示〉 貼る時機: いつでも（CHAT-1005-RVW-06 と並行してよい。別のセッションに貼る） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1006-rvw-dic の作成と push、cloudflare へのマージ（上の「マージ:」の行の範囲）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1006-rvw-dic を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1006-rvw-dic origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#377（辞書データのカテゴリ）の実装の前に、今の辞書ファイルの形を確かめ、元データをスプレッドシートへ移すための列の案と、貼り付けられるデータを用意する。この指示ではコード・シートを変えない（変わるのはログだけ）。
決定（2026-10-06、平野さん）

* #377 は、辞書データをカテゴリごとに分け、利用者が好きなカテゴリを選んで、ひとつの辞書ファイルにまとめてダウンロードできるようにする（RVW-04 で記録済み）
* カテゴリは今の2つ（連盟プロ・麻雀用語）で始める。Mリーガー氏名などは平野さんがデータを用意してから足す
* 出す形式は今と同じ2種（Microsoft IME 用・Google 日本語入力用）
* 作り方は平野さんの案: スプレッドシートに「カテゴリ」列を持つ元データを置き、生成でカテゴリごとのファイルを用意し、画面で選んだカテゴリをページの JS で結合して渡す

前提（チャット側。平野さんの決定ではない）

* RVW-04 の調べ: `resource_dictionary.html` は静的で、`dic/` の4ファイル（連盟プロ・麻雀用語 × Microsoft IME〈UTF-8 でない文字コード〉・Google 日本語入力〈UTF-8〉、2026-05-01 版）へのリンクだけ。生成スクリプトもシートも無い
* チャット側の見立て: 外部ドメインもライブラリも足さずに作れる。ただし Microsoft IME 用の文字コードが Shift_JIS だと、ブラウザの標準の機能では Shift_JIS で書き出せない（ライブラリが要る）。UTF-16LE（BOM 付き）なら JS で書き出せ、Microsoft IME も読み込める（要確認: 今のファイルの文字コードと、Microsoft IME の対応する文字コード〈公式の説明で確かめる〉）
* チャット側の見立て: 「連盟プロ」の語は「プロ」タブ（氏名と読み）から生成できるかもしれない。その場合は「辞書」タブに人の名前を写さずに済み、入退会にも追いつく（要確認: 「プロ」タブに読みの列があるか、今の連盟プロの辞書と件数・中身が合うか）
* 公開について: 辞書はすでにサイトで公開しているデータなので、ログ（mj-logs は public）に中身を貼ってよい。ただし今の公開ファイルに無い個人の情報（「プロ」タブの公開していない列など）は書かない

手順

1. 今のファイルを調べる: `dic/` の4ファイルの文字コード・BOM の有無・改行・列（読み・語・品詞・コメント）・件数・品詞の種類を表にする。2形式の中身（語と読み）が一致しているか、カテゴリをまたいで同じ語があるかも書く。`resource_dictionary.html` の今の作り（リンク・説明文・h1 の有無）も書く
2. 案を作る: (a) Microsoft IME 用に出す文字コードの案（Shift_JIS のままなら何が要るか、UTF-16LE にするなら IME が読めることの根拠）(b) 「辞書」タブの列の案（例: カテゴリ・読み・語・品詞・コメント）と、2形式の品詞名の対応 (c) 「連盟プロ」を「プロ」タブから生成できるか（読みの列の有無と、今の辞書との差の件数）(d) 生成物の置き場所とファイルの形（カテゴリごとの JSON など）と、ページの JS で結合・書き出す流れ (e) 平野さんの手（タブを作る・貼る）の手順
3. 貼り付け用のデータ: 「麻雀用語」の今の中身を、(b) の列の形の TSV にしてログに貼る（コードブロック）。「連盟プロ」は (c) で「プロ」タブから生成できるなら貼らず、できないなら同じ形で貼る。ログに書いて、マージして完了で終える

止まる条件

* `dic/` が前提の4ファイルと違う（ファイル名と中身の要約を書いて止まる）
* 未マージの `work/` ブランチ（`git branch -r --no-merged origin/cloudflare`）が `dic/` か `resource_dictionary.html` を変えている
* コード・シートを変える必要が出た（変えずに報告に書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」には、手順2の案のうち平野さんが選ぶ点（文字コード・列・連盟プロの出どころ）を書く
* マージは冒頭の「マージ:」の行のとおり（ログだけを cloudflare へ入れて「完了」）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-RVW-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-RVW-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-06 着手。CHAT-1005-RVW-05 のコミットなし。work/1006-rvw-dic はローカル・リモートとも無く、origin/cloudflare（fad9eb53）から作成
- 0. 指示欄の末尾は指示文の最後の行と一致。雛形の行は揃っている
- 「貼る時機」は「別のセッションに貼る」だが、RVW-04 と同じセッションに貼られた（RVW-06 の実行先はこのセッションからは分からない）

## 報告

- 状態: 作業中
- ブランチ: work/1006-rvw-dic
- ログ: https://github.com/retroeater/mj/blob/work/1006-rvw-dic/docs/logs/CHAT-1005-RVW-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1006-rvw-dic
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj fad9eb53）: https://github.com/retroeater/mj-logs/tree/main/guide/fad9eb53

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/fad9eb53/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/96fa2201.md
