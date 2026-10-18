# CHAT-1018-PRV-01

- 着手日時: 2026-10-18
- 対象issue: #557
- ブランチ: work/1018-prv
- 着手時HEAD: 0ae083e44

## 指示

【Claude作成】Claude Code 向け指示：スプレッドシートを非公開にする前の調べ（#557）。読み手の洗い出しと、認証付きの読み取り・Google Charts のページの案 Chat-Ref: CHAT-1018-PRV-01 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1018-prv を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1018-prv origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
ryoei.pro が読むスプレッドシートを、すべて非公開（一般的なアクセスを「制限付き」）にしたい（#557）。非公開にすると、ログインせずに gviz・CSV エクスポートで読む今の生成と、ブラウザから直接シートを読むページが止まる。切り替えの前に、何がどのスプレッドシートを読んでいるかを全部洗い出し、認証付きで読む形と Google Charts のページの扱いの案を出す。この指示は調べだけで、コード・シート・共有の設定は変えない。
決定（2026-10-18、平野さん）

* ryoei.pro のスプレッドシートは、すべて非公開にして、非公開のフォルダへ移す（#557。2026-10-13 の CHAT-1013-AIC-05「基本は非公開のフォルダに置く。gviz の公開読み取りの扱いもあわせて決める」の続き）
* 非公開への切り替えで開発の進め方に問題が出る点は、先に手当てする。その調べをこの指示で始める

前提（チャット側。平野さんの決定ではない）

* 平野さんは、仕組みの切り替えが済むまで一般的なアクセスを変えない（チャット側からお願いした。要確認ではなく、この調べの間は今の共有のままとして扱う）
* 生成は `scripts/lib/sheets.py` からログインせずに gviz と CSV エクスポートで読む（docs/notes/static-generation.md）。「Google Charts 依存」のページが6つあり、ブラウザから直接シートを読む（同じ文書の表、#7）（数・名前は要確認）
* サービスアカウントは `live-channel-writer`・`birthday-calendar`・`gsc-export`・`mj-ask` などがある（docs/notes/ask-worker.md・live-channel-write.md・birthday-calendar.md）。鍵は GitHub Actions のシークレットにだけあり、コンテナには無い（docs/notes/cloud-sessions.md「鍵」）。「ryoei.pro_AIボット」はすでに「制限付き」で、Worker がサービスアカウントで読み書きしている
* 移行先のフォルダはチャット側も Code も読めない。どのスプレッドシートがどのフォルダにあるかは、平野さんが Drive で照合する前提で、こちらは題と ID 定数名の一覧を出す
* シートの ID・URL はログに書かない（CLAUDE.md「作業ログ」節）。定数名（`live.SPREADSHEET_ID` など）と題で呼ぶ

手順

1. 同じ主題の issue（#557 を含む。クローズ済みも）の本文・コメントを読み、決まっていることをログに書く。#557 に着手中のコメントを残す（本文・状態・ラベルは変えない）。未マージの work/ ブランチで `scripts/lib/sheets.py`・シートの ID の定数を変えているものを確かめて書く（`git branch -r --no-merged origin/cloudflare`）。
2. 読み手の洗い出し: scripts/・.github/workflows/・workers/・ブラウザで動く JS（ルートの `*.js`・`assets/` など）から、Google のスプレッドシートを読む・書く箇所を全部洗い出し、表にする（列: スプレッドシートの題〈htmlview の title で引く〉・ID の定数名・シート名・読む経路〈gviz／CSV エクスポート／htmlview／Sheets API／ブラウザの google.visualization〉・認証の有無とアカウント・呼び出し元〈スクリプト・ワークフロー・Worker・ページ〉・頻度〈定期実行・push・手動〉）。名簿（`lib/meibo.py`）など平野さんのものでないかもしれないスプレッドシートは、持ち主が分からない印を付ける。数は名前の一覧と一緒に書く（略さない）。
3. 案を出す（実装はしない）:
   * (a) 生成を認証付きで読む形。gviz・CSV エクスポートがサービスアカウントのアクセストークンで読めるか（Google の公式の文書で確かめ、出典を書く）、Sheets API に替える場合との違い（gviz の SELECT 構文・フィルタの検知 `check_not_filtered()`・数の型の扱いへの影響）。どのサービスアカウントを使うか（今のものを使うか、読み取り専用を新しく作るか）の案と、メリット・デメリット
   * (b) Google Charts 依存のページ（ブラウザから読むもの）を1つずつ、静的な生成に作り直すか・新しいページ（houou/ など）への転送で廃止するかの案
   * (c) 開発の進め方への影響: コンテナで直接シートを読めなくなった後、`--check`・新旧の生成物の比較・シートの照合をどう行うか（ワークフローの dry-run を足す等）の案。チャット側の読み取りへの影響も書く
   * (d) 切り替えの順番の案（1ファイルずつ非公開にして確かめる等）と、各段で止める条件 結果の要点を #557 にコメントする（このログの `## 報告` と `## 経過` の表から引用する）。

止まる条件

* #557 が閉じている、または同じ主題の open issue・未マージの work/ ブランチがあり、作業が重なる（重なるものを書いて止まる）
* 他のセッションの着手中のコメントが #557 にある
* 手順2で、読むたびに結果が変わる（シートの編集中の可能性）
* 調べの途中で、コード・シート・共有の設定を変えないと確かめられないことが出た（変えずに、確かめ方の案を「判断が必要なこと」に書く）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、手順3の (a)〜(d) で平野さんが選ぶことを、選択肢ごとのメリット・デメリットと推奨を添えて書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1018-PRV-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1018-PRV-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複確認（`--unshallow` 後）: `CHAT-1018-PRV-01` のコミット・`PRV` の識別子のコミット・`docs/logs/CHAT-*-PRV-*.md` の履歴ともに 0 件
- 作業ブランチ: ローカル・リモートとも `work/1018-prv` が無いため `git checkout -b work/1018-prv origin/cloudflare` で作成
- 0. 「指示」欄の末尾の行は「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」で、指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1018-prv
- ログ: https://github.com/retroeater/mj/blob/work/1018-prv/docs/logs/CHAT-1018-PRV-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1018-prv
- 確認用URL: なし
- マージ: 未
- issue: #557
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0ae083e4）: https://github.com/retroeater/mj-logs/tree/main/guide/0ae083e4

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0ae083e4/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0ae083e4/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0ae083e4/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0ae083e4/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0ae083e4/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0ae083e4/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0ae083e4.md
