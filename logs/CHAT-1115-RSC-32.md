# CHAT-1115-RSC-32

- 着手日時: 2026-11-15
- 対象issue: なし（設計案のみ。#686・#676・#630 の扱いの案を書く）
- ブランチ: work/1115-rsc-3layer
- 着手時HEAD: 8fcdb053c

## 指示

【Claude作成】Claude Code 向け指示：「最強戦」を別のブックに切り出して【1】【2】【3】の3層にする設計案を出す（コード・シートは変えない）
Chat-Ref: CHAT-1115-RSC-32
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（CHAT-1115-RSC-31 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1115-rsc-3layer の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1115-rsc-3layer を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1115-rsc-3layer origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）
取り込みの衝突: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1115-RSC-31 のログ（work/1115-rsc-autowrite 上）の `## 報告` の状態の末尾に ` / 続き: CHAT-1115-RSC-32` を足す（このログと同じコミットでなくてよい。work/1115-rsc-autowrite に push する。ほかのファイルは変えない）。

## 目的
CHAT-1115-RSC-31（判断待ち）の続き。速報から自動で書く先を、正本のブック（R_タイトル戦_成績）の「最強戦」タブではなく、新しく切り出すブックにする。そのブックを /live・SNS ブック・ABEMA ブックと同じ【1】【2】【3】の3層にする設計案を出し、平野さんが一問一答で決められる形にする。この指示ではコード・シート・issue の本文を変えない。

### 決定（2026-11-15、平野さん）
- 「最強戦」のシートを別のブックに切り出し、【1】【2】【3】の3層の構造にする（平野さんの提案）。書き込み用のアカウントは、新しいブックだけに共有する。2026-10-10・10-12 の決定（正本には書き込み用のアカウントを共有しない。docs/notes/live-channel-write.md 8行目付近）はそのまま守る
- RSC-31 の今のブックへの書き込みの仕組み（work/1115-rsc-autowrite、未マージ）はマージしない。速報の読み方・照合・書く前後の確かめなど、使える部品は3層の設計で使ってよい
- 2026-11-15 の RSC-31 の決定（照合済みの卓だけ自動で書く・予選は空いた順位と結果・決勝は4行・照合が合わない卓は候補タブ・事後の確認は常設 issue #686 にコメント・書くのは live-channel-writer）は、書く先を新しいブックに変えて引き継ぐ
- （報告）今、「最強戦」の予選の順位・結果は、平野さんが対局の後に手で入れている

### 前提（チャット側。平野さんの決定ではない）
- 3層の手本: docs/notes/live-channel-write.md（/live。【1】元データ・【2】自動変換後・【3】手動補正）、docs/notes/sns-book.md、docs/notes/abema-book.md（【2】【3】を鍵の5列で引き、【3】の空でないセルが【2】を上書きする）
- RSC-31 で分かったこと（同じログの `## 経過`）: 「最強戦」は 2744行、見出しは 対局日・年度・対局・ステージ・卓・順位・結果・名前・表示・備考。新しい対局が上。決勝の「卓」は「-」、1位の「結果」は「通過」（優勝は生成で導く）。3層にすると、行の並びを機械が決める【2】で「足す場所」（RSC-31 の判断待ち2）は要らなくなる見込み（要確認）
- 過去の対局（2013年度〜、平野さんが手で入れた行）をどの層に置くかが、設計の一番の分かれ目と見込む（例: 【1】に一度だけ移して以後は速報の行を足す／【3】に置いて手の正とする／旧タブを【1】の元として読み続ける。案はこれに限らない）
- 新しいブックは、ほかの本番のブックと同じく専用フォルダ「【本番環境】」に置き、`sheets-reader` はフォルダの閲覧者、`live-channel-writer` は編集者（docs/notes/sheets-reader.md「段取りと今の状態」。要確認）。作るのは平野さん
- 移行のときの旧タブの扱いは /live の前例（切り替えの後に「(旧)…」に名前を変えて残し、後で消す。docs/notes/live-channel-write.md）にそろえる案がある（チャット側の案）

## 手順
1. 調べ: 「最強戦」のタブ（`generate_saikyo_pages.SPREADSHEET_ID` の「最強戦」）を読む・書く・指すものを、`scripts/`・`.github/workflows/`・`workers/` など リポジトリ全体（docs を除く）で全件洗い出し、ファイル・関数・読む列・用途の表をログの `## 経過` に書く（AI コンシェルジュ・ABEMA ブックの【2】・転記案の B と D・個人成績・ランキング・`check_unused_names.py` などが当たる見込み。要確認）。docs の中で「最強戦」シートを今の場所として書いている文書も、ファイル名の一覧で書く
2. 設計案: 次の点ごとに、案を2〜3つ（推奨に印）と、それぞれの利点・欠点・移行の手間を書く。(a) 各層の中身と書き手（過去の対局をどの層に置くか）、(b) 生成が読むもの（【2】と【3】の重ね方と鍵の列）、(c) 速報から書く先の層と、予選の空いたセル・決勝の4行の書き方（RSC-31 の判断待ち1・2がどうなるか）、(d) 平野さんが手で直すときに触る層、(e) 移行の順（新しいブックの用意 → 写す → 読む側の切り替え → 旧タブの扱い）と、そのあいだ毎朝の生成を止めない方法。各案は手本（/live・SNS ブック・ABEMA ブック）のどれに近いかを書く
3. 進め方: 推奨の案で進めたときの、指示の分け方（1本3項目程度まで）と順、平野さんの手作業（新しいブックの作成・フォルダ・共有。画面ごとに直接のリンクを付ける。非公開の URL はターミナルへの最終報告にだけ書く）、#686・#676・#630 の扱いの案を書く。「判断が必要なこと」には、手順2の点を平野さんに一問ずつ聞ける形（問い・選択肢・推奨）で並べる

## 止まる条件
- 差分がこのログと、RSC-31 のログの状態の行（work/1115-rsc-autowrite 上）以外に出る
- 手順1で、「最強戦」のタブに書き込んでいる本番のコード（RSC-31 の未マージの分を除く）が見つかる（場所を書いて止まる）
- 同じ論点（「最強戦」の別ブック化・3層化）の open issue がある（クローズ済みも含めて検索し、あれば番号を書いて止まる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- コミットの件名・本文に、閉じるキーワード（close・fix・resolve とその活用形）の直後の `#番号` を書かない（push の前に確かめる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1115-RSC-32.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1115-RSC-32 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1115-rsc-3layer
- ログ: https://github.com/retroeater/mj/blob/work/1115-rsc-3layer/docs/logs/CHAT-1115-RSC-32.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1115-rsc-3layer
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8fcdb053）: https://github.com/retroeater/mj-logs/tree/main/guide/8fcdb053

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8fcdb053/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8fcdb053/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8fcdb053/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8fcdb053/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8fcdb053/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8fcdb053/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/d911246f.md
